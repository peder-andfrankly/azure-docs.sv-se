---
title: SQL Server på Virtuella Windows-datorer i vanliga frågor och svar om Azure | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om att köra SQL Server på virtuella Azure-datorer.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: c3b4fabb319a3ea76ee62c8c699d4613184a4e76
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791041"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Vanliga frågor och svar om SQL Server som körs på virtuella Windows-datorer i Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [SQL Server på virtuella Windows-datorer i Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Den här artikeln fokuserar på problem som är specifika för SQL Server på virtuella Windows-datorer. Om du kör SQL Server på Linux virtuella datorer kan du läsa [vanliga frågor och svar om Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Avbildningar

1. **Vilka SQL Server Galleri avbildningar för virtuella datorer är tillgängliga?** 

   Azure upprätthåller avbildningar av virtuella datorer för alla stödda större versioner av SQL Server på alla utgåvor för både Windows och Linux. Mer information finns i den fullständiga listan över virtuella [Windows-avbildningar](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) och [virtuella Linux-avbildningar](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Uppdateras befintliga SQL Server Galleri avbildningar för virtuella datorer?**

   Varannan månad uppdateras SQL Server avbildningar i galleriet för virtuella datorer med de senaste Windows-och Linux-uppdateringarna. För Windows-avbildningar inkluderar detta alla uppdateringar som marker ATS som viktiga i Windows Update, inklusive viktiga SQL Server säkerhets uppdateringar och Service Pack. För Linux-avbildningar omfattar detta de senaste system uppdateringarna. SQL Server kumulativa uppdateringar hanteras på olika sätt för Linux och Windows. SQL Server kumulativa uppdateringar för Linux ingår också i uppdateringen. Men för tillfället uppdateras inte virtuella Windows-datorer med SQL Server eller Windows Server kumulativa uppdateringar.

1. **Kan SQL Server avbildningar av virtuella datorer ta bort från galleriet?**

   Ja. Azure upprätthåller bara en bild per huvud version och utgåva. Till exempel, när en ny SQL Server service pack lanseras, lägger Azure till en ny avbildning till galleriet för den service pack. SQL Server avbildningen för föregående service pack tas omedelbart bort från Azure Portal. Men det är fortfarande tillgängligt för etablering från PowerShell under de kommande tre månaderna. Föregående service packs avbildning är inte tillgänglig längre efter tre månader. Den här borttagnings principen gäller även om en SQL Server-version inte stöds när den når slutet av livs cykeln.


1. **Är det möjligt att distribuera en äldre avbildning av SQL Server som inte är synlig i Azure Portal?**

   Ja, med hjälp av PowerShell. Mer information om hur du distribuerar SQL Server virtuella datorer med hjälp av PowerShell finns i [så här etablerar du SQL Server virtuella datorer med Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Kan jag skapa en generaliserad Azure SQL Server Marketplace-avbildning av min SQL Server VM och använda den för att distribuera virtuella datorer?**

   Ja, men du måste [registrera varje SQL Server VM med SQL Server VM Resource Provider](virtual-machines-windows-sql-register-with-resource-provider.md) för att kunna hantera dina SQL Server VM i portalen, samt använda funktioner som automatisk uppdatering och automatisk säkerhets kopiering. När du registrerar dig hos resurs leverantören måste du också ange licens typen för varje SQL Server VM. 

1. **Kan jag använda min egen virtuella hård disk för att distribuera ett SQL Server VM?**

   Ja, men du måste [registrera varje SQL Server VM med SQL Server VM Resource Provider](virtual-machines-windows-sql-register-with-resource-provider.md) för att kunna hantera dina SQL Server VM i portalen, samt använda funktioner som automatisk uppdatering och automatisk säkerhets kopiering.

1. **Är det möjligt att konfigurera konfigurationer som inte visas i galleriet för virtuella datorer (till exempel Windows 2008 R2 + SQL Server 2012)?**

   Nej. För Galleri avbildningar för virtuella datorer som innehåller SQL Server måste du välja en av de angivna avbildningarna antingen via Azure Portal eller via [PowerShell](virtual-machines-windows-ps-sql-create.md). Du kan dock distribuera en virtuell Windows-dator och själv installera SQL Server till den. Du måste sedan [Registrera din SQL Server VM med SQL Server VM Resource Provider](virtual-machines-windows-sql-register-with-resource-provider.md) för att kunna hantera dina SQL Server VM i portalen, samt använda funktioner som automatisk uppdatering och automatisk säkerhets kopiering. 


## <a name="creation"></a>Flikar

1. **Hur gör jag för att skapar du en virtuell Azure-dator med SQL Server?**

   Det enklaste sättet är att skapa en virtuell dator som innehåller SQL Server. En själv studie kurs om hur du registrerar dig för Azure och skapar en SQL Server VM från portalen finns i [etablera en SQL Server virtuell dator i Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Du kan välja en avbildning av en virtuell dator som använder SQL Server licens för betala per sekund, eller så kan du använda en avbildning som gör att du kan använda en egen SQL Server-licens. Du kan också välja att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad utgåva (utvecklare eller Express) eller genom att återanvända en lokal licens. Se till att [Registrera din SQL Server VM med SQL Server VM Resource Provider](virtual-machines-windows-sql-register-with-resource-provider.md) för att hantera dina SQL Server VM i portalen, samt använda funktioner som automatisk uppdatering och automatisk säkerhets kopiering. Om du använder din egen licens måste du ha [licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Mer information finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. **Hur kan jag migrera min lokala SQL Server-databas till molnet?**

   Skapa först en virtuell Azure-dator med en SQL Server instans. Migrera sedan dina lokala databaser till den instansen. Information om strategier för datamigrering finns i [Migrera en SQL Server databas till SQL Server i en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licensiering

1. **Hur kan jag installera min licensierade version av SQL Server på en virtuell Azure-dator?**

   Det finns tre sätt att göra detta på. Om du är en kund med Enterprise Agreement (EA) kan du etablera en av de [avbildningar för virtuella datorer som har stöd för licenser](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), som även kallas för att ta din egen licens (BYOL). Om du har [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)kan du aktivera [Azure Hybrid-förmån](virtual-machines-windows-sql-ahb.md) på en befintlig PAYG-avbildning (betala per användning). Du kan också kopiera SQL Server installationsmedia till en virtuell Windows Server-dator och sedan installera SQL Server på den virtuella datorn. Se till att registrera SQL Server VM med [resurs leverantören](virtual-machines-windows-sql-register-with-resource-provider.md) för funktioner som till exempel portal hantering, automatisk säkerhets kopiering och automatisk uppdatering. 

1. **Måste jag betala för att licensiera SQL Server på en virtuell Azure-dator om den endast används för vänteläge/redundans?**

   Om du vill ha en kostnads fri passiv licens för en sekundär tillgänglighets grupp i vänte läge eller en instans av redundanskluster måste du uppfylla alla följande kriterier som beskrivs i [licens guidens PDF](https://download.microsoft.com/download/7/8/C/78CDF005-97C1-4129-926B-CE4A6FE92CF5/SQL_Server_2017_Licensing_guide.pdf):

   1. Du har [License Mobility](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) genom [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Den passiva SQL Server-instansen hanterar inte SQL Server data till klienter eller kör aktiva SQL Server arbets belastningar. Den används endast för att synkronisera med den primära servern och upprätthålla annars den passiva databasen i ett varmt vänte läge. Om den betjänar data, till exempel rapporter till klienter som kör aktiva SQL Server arbets belastningar eller utför något "arbete", till exempel ytterligare säkerhets kopior från den sekundära servern, måste det vara en betald licensierad SQL Server-instans. 
   1. Den aktiva SQL Server-licensen omfattas av Software Assurance och tillåter **en** passiv sekundär SQL Server instans, med upp till samma beräknings mängd som den licensierade aktiva servern. 
   1. I den sekundära SQL Server VM används [licens modellen](virtual-machines-windows-sql-ahb.md)för att hämta din egen licens (BYOL) eller Azure Hybrid-förmån (AHB). 

1. **Kan jag ändra en virtuell till att använda min egen SQL Server-licens om den skapades från en av galleriavbildningarna med betala per användning?**

   Ja. Du kan enkelt byta en PAYG (betala per användning)-Galleri avbildningen (BYOL) genom att aktivera [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Mer information finns i [så här ändrar du licensierings modellen för en SQL Server VM](virtual-machines-windows-sql-ahb.md). Den här funktionen är för närvarande endast tillgänglig för kunder med offentliga moln.

1. **Kräver byte av licensieringsmodeller någon avbrottstid för SQL Server?**

   Nej. Att [ändra licensierings modellen](virtual-machines-windows-sql-ahb.md) kräver ingen stillestånds tid för SQL Server eftersom ändringen börjar gälla omedelbart och inte kräver omstart av den virtuella datorn. Men för att registrera SQL Server VM med SQL Server VM Resource Provider, är [SQL IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md) en förutsättning och installation av SQL IaaS-tillägget i _full_ läge startar om SQL Server tjänsten. Om SQL IaaS-tillägget behöver installeras kan du antingen installera det i _Lightweight_ -läge för begränsade funktioner eller installera det i _fullständigt_ läge under en underhålls period. SQL IaaS-tillägget som installeras i _Lightweight_ -läge kan uppgraderas till _fullständigt_ läge när som helst, men kräver en omstart av SQL Servers tjänsten. 

1. **Kan jag använda Azure Portal för att hantera flera instanser på samma virtuella dator?**

   Nej. Portal hantering är en funktion som tillhandahålls av SQL Server VM Resource Provider, som förlitar sig på SQL Server IaaS agent-tillägg. Samma begränsningar gäller för resurs leverantören som tillägg. Portalen kan antingen bara hantera en standard instans, eller en namngiven instans, så länge den har kon figurer ATS korrekt. Mer information om dessa begränsningar finns i [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Kan CSP-prenumerationer aktivera Azure Hybrid-förmån?**

   Ja, Azure Hybrid-förmån är tillgängligt för CSP-prenumerationer. CSP-kunder bör först distribuera en avbildning med betala per användning och sedan [ändra licensierings modellen](virtual-machines-windows-sql-ahb.md) till egen licens.

1. **Kommer den virtuella datorn att registreras med den nya SQL Server VM resurs leverantören att få ytterligare kostnader?**

   Nej. SQL Server VM Resource Provider möjliggör bara ytterligare hanterbarhet för SQL Server på virtuella Azure-datorer utan extra kostnad. 

1. **Är SQL Server VM Resource Provider tillgänglig för alla kunder?**
 
   Ja, så länge som SQL Server VM distribuerades i det offentliga molnet med Resource Manager-modellen och inte den klassiska modellen. Alla andra kunder kan registrera sig för den nya SQL Server VM resurs leverantören. Men endast kunder med [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) -förmånen kan använda sin egen licens genom att aktivera [Azure Hybrid-förmån (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) på en SQL Server VM. 

1. **Vad händer med resurs leverantörs resursen (_Microsoft. SqlVirtualMachine_) om den virtuella dator resursen flyttas eller tas bort?** 

   När resursen Microsoft. Compute/VirtualMachine släpps eller flyttas, meddelas den associerade Microsoft. SqlVirtualMachine-resursen för att asynkront replikera åtgärden.

1. **Vad händer med den virtuella datorn om resurs leverantörs resursen (_Microsoft. SqlVirtualMachine_) tas bort?**

    Resursen Microsoft. Compute/VirtualMachine påverkas inte när resursen Microsoft. SqlVirtualMachine släpps. Licensierings ändringarna kommer dock att återställas till den ursprungliga avbildnings källan. 

1. **Är det möjligt att registrera distribuerade SQL Server virtuella datorer med SQL Server VM Resource Provider?**

    Ja. Om du har distribuerat SQL Server från ditt eget medium och installerat SQL IaaS-tillägget kan du registrera dina SQL Server VM med resurs leverantören för att få de hanterings förmåner som ges av tillägget för SQL-IaaS. Du kan dock inte konvertera en självdistribuerad SQL Server VM för att betala per användning.

1. **Är det möjligt att växla licensierings modell på en SQL Server VM distribuerad med hjälp av den klassiska modellen?**

   Nej. Det finns inte stöd för att ändra licensierings modell på en klassisk virtuell dator. Du kan migrera den virtuella datorn till Azure Resource Manager-modellen och registrera dig hos SQL Server VM Resource Provider. När den virtuella datorn har registrerats med SQL Server VM Resource Provider, blir licensierings modellens ändringar tillgängliga på den virtuella datorn. 
   


## <a name="administration"></a>Administration

1. **Kan jag installera en andra instans av SQL Server på samma virtuella dator? Kan jag ändra installerade funktioner för standard instansen?**

   Ja. Installations mediet för SQL Server finns i en mapp på **C** -enheten. Kör **Setup. exe** från den platsen för att lägga till nya SQL Server instanser eller ändra andra installerade funktioner i SQL Server på datorn. Observera att vissa funktioner, till exempel automatisk säkerhets kopiering, automatisk uppdatering och Azure Key Vault-integrering, endast fungerar mot standard instansen eller en namngiven instans som kon figurer ATS korrekt (se fråga 3). 

1. **Kan jag avinstallera standardinstansen av SQL Server?**

   Ja, men det finns vissa överväganden. Först kan SQL Server-kopplad fakturering fortsätta att inträffa beroende på licens modellen för den virtuella datorn. För det andra, enligt vad som anges i föregående svar, finns det funktioner som förlitar sig på [SQL Server IaaS agent-tillägg](virtual-machines-windows-sql-server-agent-extension.md). Om du avinstallerar standard instansen utan att ta bort IaaS-tillägget, fortsätter tillägget att söka efter standard instansen och kan generera händelse logg fel. Felen är från följande två källor: **Microsoft SQL Server hantering av autentiseringsuppgifter** och **Microsoft SQL Server IaaS-agenten**. Ett av felen kan vara något av liknar följande:

      Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig.

   Om du väljer att avinstallera standard instansen avinstallerar du även [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md) . 

1. **Kan jag använda en namngiven instans av SQL Server med IaaS-tillägget**?
   
   Ja, om den namngivna instansen är den enda instansen på SQL Server, och om den ursprungliga standard instansen [avinstallerades korrekt](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Om det inte finns någon standard instans och det finns flera namngivna instanser på en enda SQL Server VM, kommer SQL Server IaaS agent-tillägget inte att installeras. 

1. **Kan jag ta bort SQL Server helt från en SQL Server VM?**

   Ja, men du kommer att fortsätta att debiteras för din SQL Server VM enligt beskrivningen i [pris vägledningen för SQL Server virtuella Azure-datorer](virtual-machines-windows-sql-server-pricing-guidance.md). Om du inte längre behöver SQL Server kan du distribuera en ny virtuell dator och migrera data och program till den nya virtuella datorn. Sedan kan du ta bort den virtuella SQL Server-datorn.
   
## <a name="updating-and-patching"></a>Uppdatering och uppdatering

1. **Hur gör jag för att ändra till en annan version/utgåva av SQL Server på en virtuell Azure-dator?**

   Kunder kan ändra sin version/utgåva av SQL Server med hjälp av installationsmediet som innehåller den önskade versionen eller utgåvan av SQL Server. När utgåvan har ändrats använder du Azure-portalen för att ändra versionsegenskapen för den virtuella datorn så att faktureringen för den virtuella datorn återspeglas korrekt. Mer information finns i avsnittet [om ändrings versioner av en SQL Server VM](virtual-machines-windows-sql-change-edition.md). Det finns ingen fakturerings skillnad för olika versioner av SQL Server, så när versionen av SQL Server har ändrats krävs ingen ytterligare åtgärd.

1. **Var kan jag få installations mediet att ändra versionen eller versionen av SQL Server?**

  Kunder som har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kan hämta sina installations medier från [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Kunder som inte har Software Assurance kan använda installations mediet från en Marketplace SQL Server VM avbildning som har önskad utgåva.

1. **Hur tillämpas uppdateringar och service packs på en SQL Server VM?**

   Virtuella datorer ger dig kontroll över värddatorn, inklusive när och hur du tillämpar uppdateringar. För operativ systemet kan du manuellt tillämpa Windows-uppdateringar, eller så kan du aktivera en schemaläggnings tjänst som kallas [automatiserad uppdatering](virtual-machines-windows-sql-automated-patching.md). Automatisk uppdatering installerar alla uppdateringar som markeras som viktiga, inklusive SQL Server-uppdateringar i den kategorin. Andra valfria uppdateringar till SQL Server måste installeras manuellt.

1. **Kan jag uppgradera min SQL Server 2008/2008 R2-instans när den har registrerats med SQL Server VM Resource Provider?**

   Ja. Du kan använda installations medier för att uppgradera versionen och versionen av SQL Server och sedan kan du uppgradera ditt [SQL IaaS-tillägg](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)) från _ingen agent_ till _full_. På så sätt får du till gång till alla fördelar med SQL IaaS-tillägget, till exempel portal hantering, automatiserade säkerhets kopieringar och automatiserad uppdatering. 

## <a name="general"></a>Allmänt

1. **Stöds SQL Server FCI (failover Cluster instances) på virtuella Azure-datorer?**

   Ja. Du kan [skapa ett Windows-redundanskluster på Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) och använda LAGRINGSDIRIGERING (S2D) för kluster lagringen. Du kan också använda klustring eller lagrings lösningar från tredje part enligt beskrivningen i [hög tillgänglighet och haveri beredskap för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > För närvarande stöds inte det _fullständiga_ [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) för SQL Server FCI på Azure. Vi rekommenderar att du avinstallerar det _fullständiga_ tillägget från virtuella datorer som ingår i FCI och installerar tillägget i _Lightweight_ -läge i stället. Det här tillägget har stöd för funktioner, till exempel automatisk säkerhets kopiering och uppdatering och vissa Portal funktioner för SQL Server. Dessa funktioner fungerar inte för SQL Server virtuella datorer när den _fullständiga_ agenten har avinstallerats.

1. **Vad är skillnaden mellan SQL Server virtuella datorer och tjänsten SQL Database?**

   Att köra SQL Server på en virtuell Azure-dator är inte detsamma som att köra SQL Server i ett fjärranslutet Data Center. [SQL Database](../../../sql-database/sql-database-technical-overview.md) erbjuder däremot databas som en tjänst. Med SQL Database har du inte till gång till de datorer som är värdar för dina databaser. En fullständig jämförelse finns i [Välj ett moln SQL Server alternativ: Azure SQL (PaaS) Database eller SQL Server på virtuella Azure-datorer (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Hur gör jag för att installera SQL Data Tools på min virtuella Azure-dator?**

    Hämta och installera SQL Data Tools från [Microsoft SQL Server data verktyg – Business Intelligence för Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Stöds distribuerade transaktioner med MSDTC på SQL Server virtuella datorer?**
   
    Ja. Lokal DTC stöds för SQL Server 2016 SP2 och senare. Program måste dock testas när du använder Always on-tillgänglighetsgrupper, eftersom transaktioner under flygningen inte går att redundansväxla och måste göras om. Klustrad DTC är tillgänglig från och med Windows Server 2019. 

## <a name="resources"></a>Resurser

**Virtuella Windows-datorer**:

* [Översikt över SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md).
* [Etablera en SQL Server virtuell Windows-dator](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrera en databas till SQL Server på en virtuell Azure-dator](virtual-machines-windows-migrate-sql.md)
* [Hög tillgänglighet och haveri beredskap för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Prestandametodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Program mönster och utvecklings strategier för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuella Linux-datorer**:

* [Översikt över SQL Server på en virtuell Linux-dator](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Etablera en virtuell SQL Server Linux-dator](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Vanliga frågor och svar (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentation om SQL Server på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
