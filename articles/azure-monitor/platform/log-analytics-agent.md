---
title: Samla in loggdata med Azure Log Analytics-agenten | Microsoft Docs
description: Det här avsnittet hjälper dig att samla in data och övervaka datorer i Azure, lokala eller andra moln med Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: 33ba07ac8d89546856666cc7ab94fae650020001
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74306529"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Samla in loggdata med Log Analytics agent

Azure Log Analytics agent, som tidigare kallades Microsoft Monitoring Agent (MMA) eller OMS Linux-agenten, utvecklades för omfattande hantering på lokala datorer, datorer som övervakas av [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)och virtuella datorer i valfritt moln. Windows-och Linux-agenterna ansluter till en Azure Monitor och lagrar insamlade loggdata från olika källor i Log Analytics arbets ytan, samt eventuella unika loggar eller mått som definieras i en övervaknings lösning. 

Den här artikeln innehåller en detaljerad översikt över agenten, system och nätverkskrav och olika distributionsmetoder.

## <a name="overview"></a>Översikt

![Log Analytics-agenten kommunikation diagram](./media/log-analytics-agent/log-analytics-agent-01.png)

Innan du analyserar och arbetar med insamlade data måste du först installera och ansluta agenter för alla datorer som du vill skicka data till Azure Monitor tjänsten. Du kan installera agenter på virtuella datorer i Azure med hjälp av Azure Log Analytics VM-tillägg för Windows och Linux, och för datorer i en hybridmiljö med hjälp av installationsprogrammet, kommandorad, eller med Desired State Configuration (DSC) i Azure Automation. 

Agenten för Linux och Windows kommunicerar utgående till Azure Monitor tjänsten via TCP-port 443, och om datorn ansluter via en brand vägg eller proxyserver för att kommunicera via Internet kan du läsa igenom kraven nedan för att förstå nätverks konfigurationen kunna. Om dina IT-säkerhetsprinciper inte tillåter datorer i nätverket att ansluta till Internet, kan du konfigurera en [Log Analytics Gateway](gateway.md) och sedan Konfigurera agenten så att den ansluter genom gatewayen till Azure Monitor loggar. Agenten kan sedan ta emot konfigurations information och skicka insamlade data beroende på vilka data insamlings regler och övervaknings lösningar som du har aktiverat i din arbets yta. 

När du använder Log Analyticss agenter för att samla in data måste du förstå följande för att kunna planera agent distributionen:

* Om du vill samla in data från Windows-agenter kan du [Konfigurera varje agent så att den rapporterar till en eller flera arbets ytor](agent-windows.md), även om den rapporterar till en System Center Operations Manager hanterings grupp. Windows-agenten kan rapportera upp till fyra arbets ytor.
* Linux-agenten stöder inte Multi-värdar och kan bara rapportera till en enda arbets yta.
* Windows-agenten stöder [FIPS 140-standarden](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), medan Linux-agenten inte stöder det.  

Om du använder System Center Operations Manager 2012 R2 eller senare:

* Varje Operations Manager hanterings grupp kan [endast anslutas till en arbets yta](om-agents.md).
* Linux-datorer som rapporterar till en hanterings grupp måste konfigureras att rapportera direkt till en Log Analytics-arbetsyta. Om Linux-datorerna redan rapporterar direkt till en arbets yta och du vill övervaka dem med Operations Manager följer du dessa steg för att [rapportera till en Operations Manager hanterings grupp](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Du kan installera Log Analytics Windows-agenten på Windows-datorn och låta den rapportera till båda Operations Manager integrerade med en arbets yta och en annan arbets yta.

Agenten för Linux och Windows är inte bara för att ansluta till Azure Monitor. den har också stöd för Azure Automation som värd för Hybrid Runbook Worker-rollen och andra tjänster som [ändringsspårning](../../automation/change-tracking.md), [uppdateringshantering](../../automation/automation-update-management.md)och [Azure Security Center](../../security-center/security-center-intro.md). Mer information om Hybrid Runbook Worker-rollen finns i [Azure Automation hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Windows-operativsystem som stöds

Följande versioner av Windows-operativsystemet stöds officiellt för Windows-agenten:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, version 1709 och 1803
* Windows 7 SP1, Windows 8 Enterprise och Pro och Windows 10 Enterprise och Pro

>[!NOTE]
>Log Analytics agenten för Windows har utformats för att stödja server övervaknings scenarier, vi inser att du kan köra Windows-klienten för att stödja arbets belastningar som kon figurer ATS och optimerats för serveroperativ systemet. Agenten har stöd för Windows-klienten, men våra övervaknings lösningar fokuserar inte på klient övervaknings scenarier om inget annat anges explicit.

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds

Det här avsnittet innehåller information om Linux-distributioner som stöds.

Från och med versioner som lanseras efter augusti 2018 har gör vi följande ändringar i vår supportmodell:  

* Endast de server-versioner stöds, inte klienten.  
* Nya versioner av [Azure Linux-distributioner](../../virtual-machines/linux/endorsed-distros.md) stöds alltid.  
* Alla mindre versioner stöds för varje större version i listan.
* Versioner som har klarat sina tillverkarens support upphör datum stöds inte.  
* Nya versioner av AMI stöds inte.  
* Endast de versioner som kör SSL 1.x som standard som stöds.

>[!NOTE]
>Om du använder en distribution eller en version som stöds för närvarande inte och inte justeras till vår supportmodell, rekommenderar vi att du Förgrena lagringsplatsen uppmärksammades att Microsoft-supporten inte kommer ge hjälp med förgrenade agent versioner.

* Amazon Linux 2017.09 (x 64)
* CentOS Linux 6 (x86/x64) och 7 (x 64)  
* Oracle Linux 6 och 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) och 7 (x 64)
* Debian GNU/Linux 8 och 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) och 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) och 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 stöds bara på x86_x64 plattformar (64-bitars) och OpenSSL tidigare än 1.x inte stöds på valfri plattform.
>

### <a name="agent-prerequisites"></a>Krav för agent

I följande tabell visas de paket som krävs för Linux-distributioner som stöds av-agenten som agenten ska installeras på.

|Nödvändigt paket |Beskrivning |Lägsta version |
|-----------------|------------|----------------|
|Glibc |    GNU C-bibliotek | 2.5-12 
|Openssl    | OpenSSL-bibliotek | 1.0. x eller 1.1. x |
|Klammerparentes | webb klient för sväng | 7.15.5 |
|Python – ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Antingen rsyslog eller syslog-ng krävs för att samla in syslog-meddelanden. Standard syslog-daemon på version 5 av Red Hat Enterprise Linux, CentOS och Oracle Linux-version (sysklog) stöds inte för syslog-händelseinsamling. Om du vill samla in syslog-data från den här versionen av dessa distributioner ska rsyslog daemon installeras och konfigureras för att ersätta sysklog.

## <a name="tls-12-protocol"></a>TLS 1.2-protokollet

För att säkerställa säkerheten för data som överförs till Azure Monitor loggar, rekommenderar vi starkt att du konfigurerar agenten att använda minst Transport Layer Security (TLS) 1,2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har befunnits vara sårbara och även om de fortfarande arbetar för att tillåta bakåtkompatibilitet, rekommenderas de **inte**.  Mer information finns i [skicka data på ett säkert sätt med TLS 1,2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Krav på brandvägg

Informationen nedan visar den konfigurations information för proxy och brand vägg som krävs för att Linux-och Windows-agenten ska kunna kommunicera med Azure Monitor loggar.  

|Agentresurs|Portar |Riktning |Kringgå HTTPS-kontroll|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.blob.core.windows.net |Port 443 |Utgående|Ja |  

För brand Väggs information som krävs för Azure Government, se [Azure Government hantering](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Om du planerar att använda Azure Automation Hybrid Runbook Worker för att ansluta till och registrera med Automation-tjänsten för att använda Runbooks eller hanterings lösningar i din miljö, måste den ha åtkomst till port numret och de URL: er som beskrivs i [Konfigurera ditt nätverk för Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Windows-och Linux-agenten stöder kommunikation antingen via en proxyserver eller Log Analytics Gateway för att Azure Monitor med HTTPS-protokollet.  Både anonyma och grundläggande autentisering (användarnamn/lösenord) stöds.  För Windows-agenten som är ansluten direkt till tjänsten anges proxykonfigurationen under installationen eller [efter distributionen](agent-manage.md#update-proxy-settings) från kontroll panelen eller med PowerShell.  

För Linux-agenten anges proxyservern under installationen eller [efter installationen](agent-manage.md#update-proxy-settings) genom att ändra konfigurations filen proxy. conf.  Konfigurationsvärdet för proxyn för Linux-agenten har följande syntax:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Om proxyservern inte kräver att autentisera, kräver Linux-agenten fortfarande ett pseudo/användarlösenord. Detta kan vara valfri användarnamn eller lösenord.

|Egenskap| Beskrivning |
|--------|-------------|
|Protokoll | https |
|Användare | Valfritt användarnamn för proxy-autentisering |
|lösenord | Lösenord för proxyautentisering |
|proxyhost | Adressen eller FQDN för proxy server/Log Analytics-gateway |
|port | Valfritt portnumret för proxy server/Log Analytics-gateway |

Exempel: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Om du använder specialtecken som "\@" i lösen ordet får du ett anslutnings fel på grund av att värdet parsas felaktigt.  Undvik det här problemet genom att koda lösen ordet i URL: en med ett verktyg som [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Installera och konfigurera agenten

Att ansluta datorer i din Azure-prenumeration eller hybrid miljö direkt med Azure Monitor loggar kan utföras med olika metoder beroende på dina behov. Följande tabell visar varje metod för att avgöra vilken som fungerar bäst i din organisation.

|Källa | Metod | Beskrivning|
|-------|-------------|-------------|
|Azure VM| – Log Analytics VM-tillägg för [Windows](../../virtual-machines/extensions/oms-windows.md) eller [Linux](../../virtual-machines/extensions/oms-linux.md) med hjälp av Azure CLI eller med en Azure Resource Manager-mall<br>- [manuellt från Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)<br>- [Azure Security Center automatisk etablering](../../security-center/security-center-enable-data-collection.md)| -Tillägget installerar Log Analytics agent på virtuella Azure-datorer och registrerar dem i en befintlig Azure Monitor arbets yta.<br>-Azure Security Center kan etablera Log Analytics agent på alla virtuella Azure-datorer som stöds och eventuella nya som skapas om du aktiverar den för att övervaka säkerhets problem och hot. Om den aktive ras kommer alla nya eller befintliga virtuella datorer utan en installerad agent att tillhandahållas.|
| Windows-hybriddator|- [manuell installation](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Resource Manager-mall med Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installera Microsoft Monitoring Agent från kommando raden eller Använd en automatiserad metod som Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)eller med en Azure Resource Manager mall om du har distribuerat Microsoft Azure Stack i data centret.| 
| Linux-hybriddator| [Manuell installation](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installera agenten för Linux som anropar ett wrapper-skript som finns på GitHub. | 
| System Center Operations Manager|[Integrera Operations Manager med Log Analytics](../../azure-monitor/platform/om-agents.md) | Konfigurera integration mellan Operations Manager och Azure Monitor loggar för att vidarebefordra insamlade data från Windows-datorer som rapporterar till en hanterings grupp.|  

## <a name="next-steps"></a>Nästa steg

* Granska [data källorna](../../azure-monitor/platform/agent-data-sources.md) för att förstå de data källor som är tillgängliga för att samla in data från Windows-eller Linux-systemet. 

* Lär dig mer om [logg frågor](../../azure-monitor/log-query/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar. 

* Lär dig mer om att [övervaka lösningar](../../azure-monitor/insights/solutions.md) som lägger till funktioner i Azure Monitor och även samla in data i arbets ytan Log Analytics.
