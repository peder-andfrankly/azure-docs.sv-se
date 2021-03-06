---
title: Lagrings konfiguration för SQL Server virtuella datorer | Microsoft Docs
description: I det här avsnittet beskrivs hur Azure konfigurerar lagring för SQL Server virtuella datorer under etableringen (Resource Manager distributions modell). Det förklarar också hur du kan konfigurera lagring för befintliga SQL Server virtuella datorer.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: mathoma
ms.openlocfilehash: a91098d06f481afaae75eb497d5a076c3eb42c07
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72896960"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Lagrings konfiguration för SQL Server virtuella datorer

När du konfigurerar en SQL Server avbildning av virtuella datorer i Azure kan portalen automatisera lagrings konfigurationen. Detta innefattar att koppla lagring till den virtuella datorn, göra lagringen tillgänglig för SQL Server och konfigurera den för att optimera för dina särskilda prestanda krav.

I det här avsnittet beskrivs hur Azure konfigurerar lagring för dina SQL Server virtuella datorer både under etablering och för befintliga virtuella datorer. Den här konfigurationen baseras på [metod tips för prestanda](virtual-machines-windows-sql-performance.md) för virtuella Azure-datorer som kör SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Krav

Om du vill använda de automatiserade konfigurations inställningarna för lagring måste den virtuella datorn ha följande egenskaper:

* Etablerad med en [SQL Server galleri bild](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Använder [distributions modellen för Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Använder [Premium-SSD](../disks-types.md).

## <a name="new-vms"></a>Nya virtuella datorer

I följande avsnitt beskrivs hur du konfigurerar lagring för nya SQL Server virtuella datorer.

### <a name="azure-portal"></a>Azure portal

När du konfigurerar en virtuell Azure-dator med hjälp av en SQL Server Galleri avbildning väljer du **ändra konfiguration** på fliken **SQL Server inställningar** för att öppna konfigurations sidan Prestandaoptimerad lagring. Du kan antingen lämna värdena som standard eller ändra vilken typ av disk konfiguration som passar dina behov bäst utifrån din arbets belastning. 

![SQL Server VM lagrings konfiguration under etableringen](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Välj den typ av arbets belastning som du distribuerar SQL Server för under **lagrings optimering**. Med alternativet **allmän** optimering får du som standard en datadisk med 5000 högsta IOPS och du kommer att använda samma enhet för dina data, transaktions logg och tempdb-lagring. Om du väljer antingen transaktions **bearbetning** (OLTP) eller **data lager** skapas en separat disk för data, en separat disk för transaktions loggen och Använd lokal SSD för tempdb. Det finns inga lagrings skillnader mellan **transaktions bearbetning** och **data lager**hantering, men det ändrar [stripe-konfigurationen och spårnings flaggor](#workload-optimization-settings). Om du väljer Premium Storage konfigureras cachelagringen till *ReadOnly* för data enheten, och *ingen* för logg enheten enligt [SQL Server VM bästa metoder för prestanda](virtual-machines-windows-sql-performance.md). 

![SQL Server VM lagrings konfiguration under etableringen](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Disk konfigurationen är helt anpassningsbar så att du kan konfigurera lagrings miljön, disktyp och IOPs som du behöver för din SQL Server VM arbets belastning. Du kan också använda UltraSSD (för hands version) som ett alternativ för **disk typen** om din SQL Server VM finns i någon av de regioner som stöds (USA, östra 2, Sydostasien och Europa) och du har aktiverat [Ultra disks för din prenumeration](/azure/virtual-machines/windows/disks-enable-ultra-ssd).  

Dessutom kan du ange cachelagring för diskarna. Virtuella Azure-datorer har en teknik för cachelagring på flera nivåer som kallas [BLOB-cache](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) vid användning med [Premium-diskar](/azure/virtual-machines/windows/disks-types#premium-ssd). BLOB-cachen använder en kombination av den virtuella datorns RAM-minne och lokal SSD för cachelagring. 

Diskcachelagring för Premium SSD kan vara *ReadOnly*, *readwrite* eller *none*. 

- *ReadOnly* -cachelagring är mycket bra för SQL Server datafiler som lagras på Premium Storage. *ReadOnly* -cachelagring ger låg Läs latens, hög Läs-IOPS och data flöde eftersom läsningar utförs från cache, vilket operativ system i det virtuella dator minnet och lokal SSD. Dessa läsningar är mycket snabbare än läsningar från datadisk, som är från Azure Blob Storage. Premium Storage räknar inte vilka läsningar som hanteras från cachen mot disken IOPS och data flödet. Därför kan du använda det totala antalet IOPS-ANT data flöden. 
- *Ingen* cache-konfiguration ska användas för diskarna som är värd för SQL Server logg filen eftersom logg filen skrivs sekventiellt och inte drar nytta av *ReadOnly* -cachelagring. 
- *Readwrite* -cachelagring ska inte användas som värd för SQL Server filer eftersom SQL Server inte stöder data konsekvens med *readwrite* -cachen. Skriver avfalls kapaciteten för den *ReadOnly* BLOB-cachen och fördröjningen ökar om skrivningar går genom *ReadOnly* BLOB cache-lager. 


   > [!TIP]
   > Se till att lagrings konfigurationen matchar de begränsningar som angetts av den valda virtuella dator storleken. Att välja lagrings parametrar som överskrider prestanda gränsen för VM-storleken resulterar i fel: `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`. Sänk antingen IOPs genom att ändra disk typen eller öka begränsningen för prestanda begränsning genom att öka storleken på den virtuella datorn. 


Baserat på dina val utför Azure följande konfigurations åtgärder för lagring när du har skapat den virtuella datorn:

* Skapar och kopplar Premium-SSD till den virtuella datorn.
* Konfigurerar data diskarna så att de kan nås SQL Server.
* Konfigurerar data diskarna i en lagringspool baserat på de angivna kraven för storlek och prestanda (IOPS och data flöde).
* Kopplar lagringspoolen till en ny enhet på den virtuella datorn.
* Optimerar den här nya enheten baserat på din angivna arbets belastnings typ (data lager, transaktionell bearbetning eller allmänt).

Mer information om hur Azure konfigurerar lagrings inställningar finns i [avsnittet lagrings konfiguration](#storage-configuration). En fullständig genom gång av hur du skapar en SQL Server VM i Azure Portal finns i [vägledningen för etablering](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Resurs hanterings mallar

Om du använder följande Resource Manager-mallar bifogas två Premium-datadiskar som standard, utan konfiguration av lagringspool. Du kan dock anpassa dessa mallar om du vill ändra antalet Premium-datadiskar som är anslutna till den virtuella datorn.

* [Skapa en virtuell dator med automatisk säkerhets kopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Skapa en virtuell dator med automatisk uppdatering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Skapa en virtuell dator med AKV-integrering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Snabbstartsmall

Du kan använda följande snabb starts mall för att distribuera en SQL Server VM med lagrings optimering. 

* [Skapa en virtuell dator med lagrings optimering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Skapa en virtuell dator med UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga SQL Server virtuella datorer kan du ändra vissa lagrings inställningar i Azure Portal. Öppna din [resurs för virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)och välj **Översikt**. På sidan SQL Server översikt visas den aktuella lagrings användningen för den virtuella datorn. Alla enheter som finns på den virtuella datorn visas i det här diagrammet. För varje enhet visas lagrings utrymmet i fyra delar:

* SQL-data
* SQL-logg
* Annat (icke-SQL-lagring)
* Tillgänglig

Om du vill ändra lagrings inställningarna väljer du **Konfigurera** under **Inställningar**. 

![Konfigurera lagring för befintliga SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Du kan ändra disk inställningarna för de enheter som konfigurerades under SQL Server VM skapande processen. Om du väljer **utöka enhet** öppnas sidan ändring av enhet, så att du kan ändra disk typen, samt lägga till ytterligare diskar. 

![Konfigurera lagring för befintliga SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)



## <a name="storage-configuration"></a>Storage-konfiguration

Det här avsnittet innehåller en referens för de lagrings konfigurations ändringar som Azure utför automatiskt vid etablering eller konfiguration av SQL VM i Azure Portal.

* Azure konfigurerar en lagringspool från det lagrings utrymme som valts från den virtuella datorn. Nästa avsnitt av det här avsnittet innehåller information om konfigurationen av lagringspoolen.
* Automatisk lagrings konfiguration använder alltid [Premium SSD](../disks-types.md) P30-datadiskar. Det finns därför en 1:1-mappning mellan det valda antalet terabyte och antalet data diskar som är anslutna till den virtuella datorn.

Information om priser finns på sidan för [lagrings priser](https://azure.microsoft.com/pricing/details/storage) på fliken **disklagring** .

### <a name="creation-of-the-storage-pool"></a>Skapandet av lagringspoolen

Azure använder följande inställningar för att skapa lagringspoolen på SQL Server virtuella datorer.

| Inställning | Värde |
| --- | --- |
| Rand storlek |256 KB (data lager hantering); 64 KB (transaktion) |
| Diskstorlekar |1 TB varje |
| Cache |Läsa |
| Fördelnings storlek |storlek för 64 KB NTFS-allokeringsenhet |
| Återställning | Enkel återställning (ingen återhämtning) |
| Antal kolumner |Antal data diskar upp till 8<sup>1</sup> |


<sup>1</sup> när lagringspoolen har skapats kan du inte ändra antalet kolumner i lagringspoolen.


## <a name="workload-optimization-settings"></a>Inställningar för arbets belastnings optimering

I följande tabell beskrivs de tre tillgängliga alternativen för arbets belastnings typer och deras motsvarande optimeringar:

| Arbets belastnings typ | Beskrivning | Optimeringar |
| --- | --- | --- |
| **Allmänt** |Standardinställning som stöder de flesta arbets belastningar |Inget |
| **Transaktionell bearbetning** |Optimerar lagringen för traditionella databas OLTP-arbetsbelastningar |Spårnings flagga 1117<br/>Spårnings flagga 1118 |
| **Data lager hantering** |Optimerar lagringen för analys-och rapporterings arbets belastningar |Spårnings flagga 610<br/>Spårnings flagga 1117 |

> [!NOTE]
> Du kan bara ange arbets belastnings typen när du etablerar en virtuell SQL-dator genom att välja den i steget lagrings konfiguration.

## <a name="next-steps"></a>Nästa steg

Andra avsnitt om att köra SQL Server i virtuella Azure-datorer finns [SQL Server på Azure-Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
