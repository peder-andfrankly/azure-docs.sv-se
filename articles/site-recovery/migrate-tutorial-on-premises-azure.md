---
title: Migrera lokala datorer med Azure Site Recovery
description: Den här artikeln beskriver hur du migrerar lokala datorer till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 24015810a295ef88b7d3e63bfc464ddddef6b55f
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73939627"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrera lokala datorer till Azure


I den här artikeln beskrivs hur du migrerar lokala datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md). I allmänhet används Site Recovery för att hantera och dirigera haveri beredskap för lokala datorer och virtuella Azure-datorer. Det kan dock också användas för migrering. Migreringen använder samma steg som haveri beredskap med ett undantag. I en migrering är det sista steget att redundansväxla datorer över från din lokala plats. Till skillnad från haveri beredskap kan du inte växla tillbaka till lokalt i ett scenario med migrering.


Den här kursen visar hur du migrerar lokala virtuella datorer och fysiska servrar till Azure. Lär dig att:

> [!div class="checklist"]
> * Konfigurera käll-och mål miljön för migrering
> * Konfigurerar en replikeringsprincip
> * Aktivera replikering
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat
> * Köra en engångs redundansväxling till Azure


> [!TIP]
> Nu kan du migrera lokala servrar till Azure med hjälp av tjänsten Azure Migrate. [Läs mer](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Innan du börjar

Observera att enheter som exporteras av paravirtualiserade-drivrutiner inte stöds.


## <a name="prepare-azure-and-on-premises"></a>Förbered Azure och lokalt

1. Förbered Azure enligt beskrivningen i [den här artikeln](tutorial-prepare-azure.md). Även om den här artikeln beskriver förberedelse steg för haveri beredskap, är stegen också giltiga för migrering.
2. Förbered lokala [VMware](vmware-azure-tutorial-prepare-on-premises.md)- eller [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-servrar. Om du migrerar fysiska datorer behöver du inte förbereda något. Verifiera bara [support mat ris](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Välj ett skyddsmål

Välj vad och vart du vill replikera.
1. Klicka på **Recovery Services-valv** > valv.
2. På Resurs-menyn klickar du på **Site Recovery** > **Förbered infrastrukturen** > **Skyddsmål**.
3. I **Skyddsmål** väljer du vad du vill migrera.
    - **VMware**: Välj **Till Azure** > **Ja, med VMWare vSphere Hypervisor**.
    - **Fysisk dator**: Välj **Till Azure** > **Inte virtualiserad/övrigt**.
    - **Hyper-V**: Välj **Till Azure** > **Ja, med Hyper-V**. Om de virtuella Hyper-V-datorerna hanteras av VMM väljer du **Ja**.


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

**Scenario** | **Detaljer**
--- | --- 
VMware | Konfigurera [käll miljön](vmware-azure-set-up-source.md)och konfigurera [konfigurations servern](vmware-azure-deploy-configuration-server.md).
Fysisk dator | [Konfigurera](physical-azure-set-up-source.md) käll miljön och konfigurations servern.
Hyper-V | Konfigurera [käll miljön](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Konfigurera [käll miljön](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) för Hyper-V distribuerad med System Center VMM.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange Resource Manager-distributionsmodellen.
3. Site Recovery kontrollerar Azure-resurserna.
    - Om du migrerar virtuella VMware-datorer eller fysiska servrar, verifierar Site Recovery att du har ett Azure-nätverk där de virtuella Azure-datorerna ska finnas när de har skapats efter redundansväxlingen.
    - Om du migrerar virtuella Hyper-V-datorer verifierar Site Recovery att du har ett kompatibelt Azure Storage-konto och-nätverk.
4. Konfigurera [nätverks mappning](hyper-v-vmm-azure-tutorial.md#configure-network-mapping)om du migrerar virtuella Hyper-V-datorer som hanteras av System Center VMM.

## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

**Scenario** | **Detaljer**
--- | --- 
VMware | Konfigurera en [replikeringsprincip](vmware-azure-set-up-replication.md) för virtuella VMware-datorer.
Fysisk dator | Konfigurera en [replikeringsprincip](physical-azure-disaster-recovery.md#create-a-replication-policy) för fysiska datorer.
Hyper-V | Konfigurera en [replikeringsprincip](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Konfigurera en [replikeringsprincip](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) för Hyper-V distribuerad med System Center VMM.

## <a name="enable-replication"></a>Aktivera replikering

**Scenario** | **Detaljer**
--- | --- 
VMware | [Aktivera replikering](vmware-azure-enable-replication.md) för virtuella VMware-datorer.
Fysisk dator | [Aktivera replikering](physical-azure-disaster-recovery.md#enable-replication) för fysiska datorer.
Hyper-V | [Aktivera replikering](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Aktivera replikering](hyper-v-vmm-azure-tutorial.md#enable-replication) för Hyper-V distribuerad med System Center VMM.


## <a name="run-a-test-migration"></a>Kör en testmigrering

Kör en [testredundansväxling](tutorial-dr-drill-azure.md) till Azure för att kontrollera att allt fungerar som förväntat.


## <a name="migrate-to-azure"></a>Migrera till Azure

Kör en redundansväxling för de datorer som du vill migrera.

1. I **Inställningar** > **Replikerade objekt** klickar du på datorn > **Redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Välj den senaste återställningspunkten.
3. Inställningen för krypteringsnyckeln är inte relevant för det här scenariot.
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker att stänga av virtuella datorer innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**.
5. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
6. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Slutför migrering**. Det här gör följande:

   - Avslutar migreringsprocessen, stoppar replikeringen för den lokala virtuella datorn och stoppar Site Recovery debitering för den virtuella datorn.
   - Det här steget rensar replikeringsdata. Men det raderar inte de migrerade virtuella datorerna.

     ![Slutföra migrering](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Replikeringen av den virtuella datorn stoppas innan redundansen startas. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kan lägga märke till längre redundanstest för fysiska servrar, VMware Linux-datorer, virtuella VMware-datorer som inte har aktiverat DHCP-tjänsten och virtuella VMware-datorer som inte har följande start driv rutiner: storvsc, VMBus, storflt, intelide, ATAPI.

## <a name="after-migration"></a>Efter migrering

När datorerna har migrerats till Azure, finns det ett antal steg du bör utföra.

Vissa steg kan automatiseras som en del av migreringsprocessen med den inbyggda funktionen automationsskript i [återställningsplanerna](site-recovery-runbook-automation.md)   


### <a name="post-migration-steps-in-azure"></a>Eftermigreringssteg i Azure

- Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer. 
- Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
- [Azure VM-agenten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) hanterar VM-interaktion med Azure-infrastrukturkontrollanten. Det krävs för vissa Azure-tjänster, som Azure Backup, Site Recovery och Azure Security.
    - Om du migrerar VMware-datorer och fysiska servrar, installerar installationsprogrammet för Mobilitetstjänsten tillgängliga Azure VM-agenter på Windows-datorer. På virtuella Linux-datorer rekommenderar vi att du installerar agenten efter redundansväxling.
    - Om du migrerar virtuella Azure-datorer till en sekundär region, måste Azure VM-agenten etableras på den virtuella datorn före migreringen.
    - Om du migrerar virtuella Hyper-V-datorer till Azure, ska du installera Azure VM-agenten på den virtuella Azure-datorn efter migreringen.
- Ta bort alla Site Recovery-providers/agenter manuellt från den virtuella datorn. Om du migrerar virtuella VMware-datorer eller fysiska servrar avinstallerar du mobilitets tjänsten från den virtuella datorn.
- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](azure-to-azure-quickstart.md).
- För ökad säkerhet:
    - Lås och begränsa åtkomsten för inkommande trafik med Azure Security Centers [just-in-time-administration]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuera [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/ ).
- För övervakning och hantering:
    - Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.

### <a name="post-migration-steps-on-premises"></a>Lokala eftermigreringssteg

- Flytta över apptrafiken till den app som körs på den migrerade Azure-VM-instansen.
- Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
- Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
- Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna.




## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen migrerade du lokala virtuella datorer till virtuella Azure-datorer. Vidare

> [!div class="nextstepaction"]
> [Konfigurera katastrof återställning](azure-to-azure-replicate-after-migration.md) till en sekundär Azure-region för virtuella Azure-datorer.

  
