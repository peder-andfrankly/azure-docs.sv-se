---
title: Uppdateringshantering lösning i Azure
description: Den här artikeln beskriver hur du använder Azure Uppdateringshantering-lösningen för att hantera uppdateringar för dina Windows-och Linux-datorer.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 06d7ede1e9b91832f908c87a22cca37ec2866365
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806549"
---
# <a name="update-management-solution-in-azure"></a>Uppdateringshantering lösning i Azure

Du kan använda Uppdateringshantering lösning i Azure Automation för att hantera operativ system uppdateringar för dina Windows-och Linux-datorer i Azure, i lokala miljöer och i andra moln leverantörer. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera installationsprocessen för nödvändiga uppdateringar för servrar.

Du kan aktivera Uppdateringshantering för virtuella datorer (VM) direkt från ditt Azure Automation-konto. Mer information finns i [Hantera uppdateringar för flera virtuella datorer](manage-update-multi.md). Du kan också aktivera Uppdateringshantering för en virtuell dator från sidan virtuell dator i Azure Portal. Det här scenariot är tillgängligt för virtuella [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) -och [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) -datorer.

> [!NOTE]
> Uppdateringshantering-lösningen kräver att du länkar en Log Analytics arbets yta till ditt Automation-konto. En slutgiltig lista över regioner som stöds finns i [mappningar för Azure-arbetsytor](./how-to/region-mappings.md). Region mappningarna påverkar inte möjligheten att hantera virtuella datorer i en separat region från ditt Automation-konto.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Lösningsöversikt

Datorer som hanteras med Uppdateringshantering använder följande konfigurationer för att utföra utvärderingen och för att uppdatera distributioner:

* Microsoft Monitoring Agent (MMA) för Windows eller Linux
* Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
* Automation Hybrid Runbook Worker
* Microsoft Update eller Windows Server Update Services (WSUS) för Windows-datorer

Följande diagram illustrerar hur lösningen utvärderar och tillämpar säkerhets uppdateringar på alla anslutna Windows Server-och Linux-datorer i en arbets yta:

![Uppdateringshantering process flöde](./media/automation-update-management/update-mgmt-updateworkflow.png)

Du kan använda Uppdateringshantering till att internt registrera datorer i flera prenumerationer hos samma klientorganisation.

När ett paket har frigjorts tar det 2 till 3 timmar innan korrigeringen visas för Linux-datorer för utvärdering. För Windows-datorer tar det 12 till 15 timmar innan korrigeringen visas för utvärderingen när den har släppts.

När en dator har slutfört en sökning efter uppdateringens efterlevnad vidarebefordrar agenten informationen i bulk till Azure Monitor loggar. På en Windows-dator körs kompatibilitetskontroll var 12: e timme som standard.

Förutom genomsöknings schemat initieras genomsökningen av kompatibiliteten inom 15 minuter från den MMA som startas om, innan installationen av uppdateringen och efter installationen av uppdateringen.

För en Linux-dator utförs genomsökningen varje timme som standard. Om MMA-agenten startas om initieras en kompatibilitetskontroll inom 15 minuter.

Lösningen rapporterar hur uppdaterad datorn baseras på vilken källa du är konfigurerad att synkronisera med. Om Windows-datorn är konfigurerad för att rapportera till WSUS, beroende på när WSUS senast synkroniserades med Microsoft Update, kan resultatet skilja sig från vad Microsoft Update visar. Detta är detsamma för Linux-datorer som kon figurer ATS för att rapportera till en lokal lagrings platsen i stället för till en offentlig lagrings platsen.

> [!NOTE]
> För att rapporten ska kunna rapporteras till tjänsten måste vissa URL: er och portar vara aktiverade för Uppdateringshantering. Mer information om dessa krav finns i [nätverks planering för Hybrid arbetare](automation-hybrid-runbook-worker.md#network-planning).

Du kan distribuera och installera programuppdateringar på datorer som kräver uppdateringarna genom att skapa en schemalagd distribution. Uppdateringar som klassificeras som *valfria* ingår inte i distributions omfånget för Windows-datorer. Endast nödvändiga uppdateringar ingår i distributions omfånget.

Den schemalagda distributionen definierar vilka mål datorer som får tillämpliga uppdateringar. Det gör det antingen genom att uttryckligen ange vissa datorer eller genom att välja en [dator grupp](../azure-monitor/platform/computer-groups.md) som baseras på loggs ökningar av en specifik uppsättning datorer (eller på en [Azure-fråga](automation-update-management-query-logs.md) som dynamiskt väljer virtuella Azure-datorer baserat på angivna kriterier). Dessa grupper skiljer sig från [omfattnings konfigurationen](../azure-monitor/insights/solution-targeting.md), som endast används för att avgöra vilka datorer som får hanterings paketen som aktiverar lösningen.

Du kan också ange ett schema för att godkänna och ange en tids period under vilken uppdateringar kan installeras. Den här perioden kallas underhålls perioden. En 20 minuters period i underhålls perioden är reserverad för omstarter, förutsatt att en krävs och du har valt lämpligt alternativ för omstart. Om korrigeringen tar längre tid än förväntat och det finns mindre än 20 minuter i underhålls perioden görs ingen omstart.

Uppdateringar installeras av runbooks i Azure Automation. Du kan inte visa dessa Runbooks och de kräver inte någon konfiguration. När en uppdaterings distribution skapas, skapar den ett schema som startar en huvud uppdaterings-Runbook vid den angivna tiden för de datorer som ingår. Huvud-Runbook startar en underordnad Runbook på varje agent för att installera nödvändiga uppdateringar.

Vid det datum och den tid som anges i uppdaterings distributionen kör mål datorerna distributionen parallellt. Före installationen körs en genomsökning för att verifiera att uppdateringarna fortfarande krävs. För WSUS-klientdatorer, om uppdateringarna inte är godkända i WSUS, Miss lyckas uppdaterings distributionen.

En dator som är registrerad för Uppdateringshantering i fler än en Log Analytics arbets yta (multihoming) stöds inte.

## <a name="clients"></a>Klienter

### <a name="supported-client-types"></a>Stöds klienttyper

I följande tabell visas de operativ system som stöds för utvärdering av uppdateringar. Uppdatering kräver en Hybrid Runbook Worker. Information om Hybrid Runbook Worker krav finns i installations guiderna för att installera en [Windows-hybrid Runbook Worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) och en [Linux-hybrid Runbook Worker](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2019 (Data Center/Data Center Core/standard)<br><br>Windows Server 2016 (Data Center/Data Center Core/standard)<br><br>Windows Server 2012 R2 (Data Center/standard)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM och SP1 standard)||
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats. Klassificerings-baserad uppdatering kräver `yum` för att returnera säkerhets data som CentOS inte har i sina RTM-versioner. Mer information om klassificerings-baserad uppdatering på CentOS finns i [uppdaterings klassificeringar i Linux](#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14,04 LTS, 16,04 LTS och 18,04 (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

> [!NOTE]
> Skalnings uppsättningar för virtuella Azure-datorer kan hanteras via Uppdateringshantering. Uppdateringshantering fungerar på själva instanserna och inte på bas avbildningen. Du måste schemalägga uppdateringarna på ett stegvist sätt så att alla VM-instanser inte uppdateras samtidigt.
> Du kan lägga till noder för skalnings uppsättningar för virtuella datorer genom att följa stegen under [publicera en icke-Azure-dator](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Klientappar typer

I följande tabell visas operativ system som inte stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows-klient     | Klientoperativsystem (till exempel Windows 7 och Windows 10) stöds inte.        |
|Windows Server 2016 Nano Server     | Stöds ej.       |
|Azure Kubernetes service-noder | Stöds ej. Använd korrigerings processen som beskrivs i [tillämpa säkerhets-och kernel-uppdateringar på Linux-noder i Azure Kubernetes service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Klientkrav

Följande information beskriver OS-särskilda klient krav. Ytterligare vägledning finns i [nätverks planering](#ports).

#### <a name="windows"></a>Windows

Windows-agenter måste konfigureras för att kommunicera med en WSUS-server, eller så måste de ha åtkomst till Microsoft Update.

Du kan använda Uppdateringshantering med System Center Configuration Manager. Mer information om integrations scenarier finns i [integrera System Center Configuration Manager med uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md#configuration). [Windows-agenten](../azure-monitor/platform/agent-windows.md) krävs. Agenten installeras automatiskt om du registrerar en virtuell Azure-dator.

Som standard är virtuella Windows-datorer som distribueras från Azure Marketplace inställda på att ta emot automatiska uppdateringar från Windows Update-tjänsten. Det här beteendet ändras inte när du lägger till den här lösningen eller lägger till virtuella Windows-datorer i din arbets yta. Om du inte aktivt hanterar uppdateringar med hjälp av den här lösningen gäller standard beteendet (för att tillämpa uppdateringar automatiskt).

> [!NOTE]
> En användare kan ändra grupprincip så att omstarter av datorn bara kan utföras av användaren, inte av systemet. Hanterade datorer kan fastna om Uppdateringshantering inte har behörighet att starta om datorn utan manuell interaktion från användaren.
>
> Mer information finns i [konfigurera grupprincip inställningar för automatiska uppdateringar](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

För Linux måste datorn ha åtkomst till en uppdaterings lagrings plats. Uppdaterings lagrings platsen kan vara privat eller offentlig. TLS 1,1 eller TLS 1,2 krävs för att interagera med Uppdateringshantering. En Log Analytics-agent för Linux som är konfigurerad att rapportera till fler än en Log Analytics arbets yta stöds inte med den här lösningen. Datorn måste också ha python 2. x installerat.

Information om hur du installerar Log Analytics-agenten för Linux och laddar ned den senaste versionen finns i [Log Analytics agent för Linux](https://github.com/microsoft/oms-agent-for-linux). Information om hur du installerar Log Analytics agent för Windows finns i [ansluta Windows-datorer till Azure Monitor](../log-analytics/log-analytics-windows-agent.md).

Virtuella datorer som har skapats från RHEL-avbildningar (på begäran Red Hat Enterprise Linux) som är tillgängliga på Azure Marketplace är registrerade för att få åtkomst till [Red Hat Update-infrastrukturen (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) som har distribuerats i Azure. Alla andra Linux-distributioner måste uppdateras från distributionens online-fillagringsplats med distributionens metoder som stöds.

## <a name="permissions"></a>Behörigheter

Om du vill skapa och hantera uppdaterings distributioner måste du ha vissa behörigheter. Mer information om dessa behörigheter finns i [rollbaserad åtkomst – uppdateringshantering](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Lösningskomponenter

Lösningen består av följande resurser. Resurserna läggs till i ditt Automation-konto. De är antingen direktanslutna agenter eller i en Operations Manager ansluten hanterings grupp.

### <a name="hybrid-worker-groups"></a>Hybrid Worker-grupper

När du har aktiverat den här lösningen konfigureras en Windows-dator som är direkt ansluten till din Log Analytics-arbetsyta automatiskt som en Hybrid Runbook Worker för att stödja Runbooks som ingår i den här lösningen.

Varje Windows-dator som hanteras av lösningen visas i rutan **hybrid Worker-grupper** som en **system hybrid Worker-grupp** för Automation-kontot. Lösningarna använder *värdnamn FQDN_GUID* namngivnings konvention. Du kan inte rikta de här grupperna med Runbooks i ditt konto. Om du försöker, Miss lyckas försöket. De här grupperna är avsedda att endast stödja hanterings lösningen.

Du kan lägga till Windows-datorer i en Hybrid Runbook Worker grupp i ditt Automation-konto för att stödja Automation-runbooks om du använder samma konto för både lösningen och Hybrid Runbook Worker grupp medlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

### <a name="management-packs"></a>Hanteringspaket

Om din hanteringsgrupp för System Center Operations Manager är ansluten till Log Analytics-arbetsytan installeras därefter följande hanteringspaket i Operations Manager. Dessa hanteringspaket installeras också på direktanslutna Windows-datorer när du lägger till lösningen. Du behöver inte konfigurera eller hantera dessa hanteringspaket.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Uppdatera distributions-MP

> [!NOTE]
> Anta att du har en hanterings grupp för Operations Manager 1807 eller 2019 med agenter som kon figurer ATS på hanterings grupps nivå för att koppla dem till en arbets yta. Den aktuella lösningen för att få dem att visas är att åsidosätta **IsAutoRegistrationEnabled** till **Sant** i regeln **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Mer information om hur hanterings paket för lösningar uppdateras finns i [anslut Operations Manager till Azure Monitor loggar](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> För system med Operations Manager-agenten: för att en agent ska kunna hanteras fullständigt av Uppdateringshantering måste agenten uppdateras till MMA. Information om hur du uppdaterar agenten finns i [så här uppgraderar du en Operations Manager-agent](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). I miljöer som använder Operations Manager måste du köra System Center Operations Manager 2012 R2 UR 14 eller senare.

## <a name="data-collection"></a>Datainsamling

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Lösningen samlar in information om system uppdateringar från Windows-agenter och initierar sedan installationen av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Lösningen samlar in information om system uppdateringar från Linux-agenter och initierar sedan installationen av nödvändiga uppdateringar på distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Lösningen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp.<br/><br/>En direkt anslutning från Operations Manager agent till Azure Monitor loggar krävs inte. Data vidarebefordras från hanteringsgruppen till Log Analytics-arbetsytan. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

En sökning utförs två gånger per dag för varje hanterad Windows-dator. Var 15: e minut anropas Windows API för att fråga efter den senaste uppdaterings tiden för att fastställa om statusen har ändrats. Om statusen har ändrats initieras en kompatibilitetskontroll.

En sökning utförs varje timme för varje hanterad Linux-dator.

Det kan ta mellan 30 minuter och 6 timmar innan instrumentpanelen visar uppdaterade data för de datorer som hanteras.

Den genomsnittliga data användningen per Azure Monitor loggar för en dator som använder Uppdateringshantering är cirka 25 megabyte (MB) per månad. Det här värdet är bara en uppskattning och kan komma att ändras, beroende på din miljö. Vi rekommenderar att du övervakar din miljö för att hålla koll på din exakta användning.

## <a name="ports"></a>Nätverks planering

Följande adresser krävs specifikt för Uppdateringshantering. Kommunikationen med de här adresserna sker via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

För Windows-datorer måste du också tillåta trafik till alla slut punkter som krävs av Windows Update. Du hittar en uppdaterad lista med nödvändiga slut punkter i [problem som rör http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Om du har en lokal [Windows Update-Server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)måste du också tillåta trafik till servern som anges i [WSUS-nyckeln](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

För Red Hat Linux-datorer, se [IP-adresser för RHUI Content Delivery servers](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) för obligatoriska slut punkter. Andra Linux-distributioner finns i leverantörens dokumentation.

Mer information om portar som Hybrid Runbook Worker kräver finns i [hybrid Worker roll portar](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Vi rekommenderar att du använder de adresser som anges när du definierar undantag. För IP-adresser kan du hämta [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och den återspeglar de för tillfället distribuerade intervallen och eventuella kommande ändringar i IP-intervallen.

Följ anvisningarna i [ansluta datorer utan Internet åtkomst](../azure-monitor/platform/gateway.md) för att konfigurera datorer som inte har Internet åtkomst.

## <a name="view-update-assessments"></a>Visa uppdateringsutvärderingar

I ditt Automation-konto väljer du **uppdateringshantering** för att visa status för dina datorer.

Den här vyn innehåller information om dina datorer, uppdateringar som saknas, uppdaterings distributioner och schemalagda uppdaterings distributioner. I kolumnen **efterlevnad** kan du se den senaste gången datorn utvärderades. I kolumnen **Uppdatera agent beredskap** kan du kontrol lera hälso tillståndet för uppdaterings agenten. Om ett problem uppstår väljer du länken för att gå till fel söknings dokumentation som kan hjälpa dig att åtgärda problemet.

Om du vill köra en loggs ökning som returnerar information om datorn, uppdateringen eller distributionen väljer du motsvarande objekt i listan. Fönstret **loggs ökning** öppnas med en fråga för det valda objektet:

![Uppdateringshantering standardvy](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visa saknade uppdateringar

Välj **saknade uppdateringar** om du vill visa en lista med uppdateringar som saknas på dina datorer. Varje uppdatering visas och kan väljas. Information om antalet datorer som kräver uppdateringen, operativ systemet och en länk för mer information visas. I fönstret **loggs ökning** visas mer information om uppdateringarna.

![Uppdateringar som saknas](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Uppdatera klassificeringar

I följande tabeller visas uppdaterings klassificeringarna i Uppdateringshantering, med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett särskilt problem som åtgärdar en kritisk, ej säkerhetsrelaterad bugg.        |
|Säkerhetsuppdateringar     | En uppdatering för en produktspecifik, säkerhetsrelaterad fråga.        |
|Samlade uppdateringar     | En kumulativ uppsättning snabb korrigeringar som är paketerade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produkt funktioner som distribueras utanför en produkt lansering.        |
|Service pack     | En kumulativ uppsättning snabb korrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virus-eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper till att slutföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering av ett program eller en fil som är installerad för närvarande.        |

### <a name="linux-2"></a>Linux

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett enskilt problem eller ett produktspecifik, säkerhetsrelaterat problem.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är kritiska eller som inte är av säkerhets uppdateringar.        |

För Linux kan Uppdateringshantering skilja mellan kritiska uppdateringar och säkerhets uppdateringar i molnet och Visa utvärderings data på grund av data berikning i molnet. Vid uppdatering Uppdateringshantering förlitar sig på klassificerings data som är tillgängliga på datorn. Till skillnad från andra distributioner har CentOS inte den här informationen tillgänglig i RTM-versionen. Om du har CentOS-datorer som har kon figurer ATS för att returnera säkerhets data för följande kommando kan Uppdateringshantering korrigeras baserat på klassificeringar.

```bash
sudo yum -q --security check-update
```

Det finns för närvarande ingen metod som stöds för att aktivera intern klassificerings data tillgänglighet på CentOS. För tillfället tillhandahålls kunder som kanske har aktiverat detta på egen hand support.

## <a name="integrate-with-system-center-configuration-manager"></a>Integrera med System Center Configuration Manager

Kunder som har investerat i System Center Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av styrka och mognad för Configuration Manager för att hjälpa dem att hantera program uppdateringar. Configuration Manager ingår i sin program uppdaterings hanterings cykel (SUM).

Information om hur du integrerar hanterings lösningen med System Center Configuration Manager finns i [integrera System Center Configuration Manager med uppdateringshantering](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-patches-on-windows"></a>Uppdateringar från tredje part på Windows

Uppdateringshantering använder den lokalt konfigurerade uppdaterings databasen för att korrigera Windows-system som stöds. Detta är antingen WSUS eller Windows Update. Med verktyg som [System Center Updates Publisher](/sccm/sum/tools/updates-publisher) (Updates Publisher) kan du publicera anpassade uppdateringar i WSUS. Med det här scenariot kan Uppdateringshantering korrigera datorer som använder System Center Configuration Manager som sitt uppdaterings lager med program vara från tredje part. Information om hur du konfigurerar Updates Publisher finns i [Installera Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="patch-linux-machines"></a>Korrigera Linux-datorer

I följande avsnitt beskrivs eventuella problem med Linux-korrigeringar.

### <a name="unexpected-os-level-upgrades"></a>Oväntade uppgraderingar på operativ Systems nivå

På vissa Linux-varianter, till exempel Red Hat Enterprise Linux, kan uppgraderingar av operativ Systems nivå ske via paket. Detta kan leda till att Uppdateringshantering körs där versions numret för operativ systemet ändras. Eftersom Uppdateringshantering använder samma metoder för att uppdatera paket som en administratör använder lokalt på Linux-datorn, är detta avsiktligt avsiktligt.

Använd **undantags** funktionen om du vill undvika att uppdatera OS-versionen genom uppdateringshantering körs.

I Red Hat Enterprise Linux är paket namnet som ska undantas RedHat-release-Server. x86_64.

![Paket som ska undantas för Linux](./media/automation-update-management/linuxpatches.png)

### <a name="criticalsecurity-patches-arent-applied"></a>Kritiska/säkerhets uppdateringar tillämpas inte

När du distribuerar uppdateringar till en Linux-dator kan du välja uppdaterings klassificeringar. Med det här alternativet filtreras de uppdateringar som tillämpas på den dator som uppfyller de angivna villkoren. Det här filtret används lokalt på datorn när uppdateringen distribueras.

Eftersom Uppdateringshantering utför uppdaterings berikning i molnet, kan vissa uppdateringar flaggas i Uppdateringshantering som en säkerhets påverkan, även om den lokala datorn inte har den informationen. Det innebär att om du använder kritiska uppdateringar på en Linux-dator kan det finnas uppdateringar som inte har marker ATS som en säkerhets påverkan på den datorn och därför tillämpas inte uppdateringarna. Uppdateringshantering kan dock fortfarande rapportera datorn som icke-kompatibel, eftersom den innehåller ytterligare information om den relevanta uppdateringen.

Distribution av uppdateringar efter uppdaterings klassificering fungerar inte på RTM-versioner av CentOS. Om du vill distribuera uppdateringar för CentOS korrekt väljer du alla klassificeringar för att se till att uppdateringarna tillämpas. För SUSE väljer du *bara* **andra uppdateringar** som klassificering kan orsaka att vissa säkerhets uppdateringar också installeras om säkerhets uppdateringar som rör zypper (paket hanteraren) eller dess beroenden krävs först. Det här beteendet är en begränsning i zypper. I vissa fall kan du behöva köra uppdaterings distributionen igen. Verifiera uppdaterings loggen genom att kontrol lera uppdaterings loggen.

### <a name="multi-tenant"></a>Uppdaterings distributioner mellan klienter

Om du har datorer i en annan Azure-klient rapporterar för att Uppdateringshantering att du behöver korrigera, måste du använda följande lösning för att få dem schemalagda. Du kan använda cmdleten [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) med växeln `-ForUpdate` för att skapa ett schema och använda cmdleten [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) och skicka datorerna i den andra klienten till `-NonAzureComputer`-parametern. I följande exempel visas hur du gör detta:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Aktivera Uppdateringshantering

Om du vill börja korrigera system måste du aktivera Uppdateringshantering-lösningen. Det finns många sätt att publicera datorer på Uppdateringshantering. Följande är de rekommenderade och de sätt som stöds för att publicera lösningen:

* [Från en virtuell dator](automation-onboard-solutions-from-vm.md)
* [Från att bläddra flera datorer](automation-onboard-solutions-from-browse.md)
* [Från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md)
* [Med en Azure Automation Runbook](automation-onboard-solutions.md)

## <a name="next-steps"></a>Nästa steg

Använd följande självstudie för att lära dig att hantera uppdateringar för dina virtuella Windows-datorer:

> [!div class="nextstepaction"]
> [Hantera uppdateringar och korrigeringar för dina virtuella Windows-datorer i Azure](automation-tutorial-update-management.md)

* Använd loggs ökningar i [Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md) om du vill visa detaljerade uppdaterings data.
* [Skapa aviseringar](automation-tutorial-update-management.md#configure-alerts) för status för uppdaterings distribution.

* Information om hur du interagerar med Uppdateringshantering via REST API finns i [konfigurationer för program uppdatering](/rest/api/automation/softwareupdateconfigurations).
* Information om hur du felsöker Uppdateringshantering finns i [fel sökning uppdateringshantering](troubleshoot/update-management.md).
