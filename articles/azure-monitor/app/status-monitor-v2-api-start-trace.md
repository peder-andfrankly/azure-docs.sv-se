---
title: API-referens för Azure Application Insights-agent
description: Application Insights Agent-API-referens. Starta spårning. Samla in ETW-loggar från Statusövervakare och Application Insights SDK.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: c97315b3a215f10e5b8f9533bf09fa5ac30ee16f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899659"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Application Insights Agent-API: Start-ApplicationInsightsMonitoringTrace

Den här artikeln beskriver en cmdlet som är medlem i [PowerShell-modulen AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning

Samlar in [ETW-händelser](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) från koden för kod bundna bilagor. Denna cmdlet är ett alternativ till att köra [PerfView](https://github.com/microsoft/perfview).

Insamlade händelser skrivs ut till konsolen i real tid och sparas i en ETL-fil. Den utgående ETL-filen kan öppnas av [PerfView](https://github.com/microsoft/perfview) för ytterligare undersökning.

Denna cmdlet körs tills den når timeout-tiden (standard 5 minuter) eller stoppas manuellt (`Ctrl + C`).

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörs behörighet.

## <a name="examples"></a>Exempel

### <a name="how-to-collect-events"></a>Samla in händelser

Normalt skulle vi be dig att samla in händelser för att undersöka varför ditt program inte instrumenteras.

Koden för kod bundna bilagor genererar ETW-händelser när IIS startas och när programmet startas.

Så här samlar du in följande händelser:
1. I en cmd-konsol med administratörs behörighet kör du `iisreset /stop` om du vill stänga av IIS och alla webbappar.
2. Kör denna cmdlet
3. I en cmd-konsol med administratörs behörighet kör du `iisreset /start` för att starta IIS.
4. Försök att bläddra till din app.
5. När appen har lästs in kan du stoppa den manuellt (`Ctrl + C`) eller vänta tills tids gränsen har passerats.

### <a name="what-events-to-collect"></a>Vilka händelser som ska samlas in

Du har tre alternativ när du samlar in händelser:
1. Använd växeln `-CollectSdkEvents` om du vill samla in händelser som har avsänts från Application Insights SDK.
2. Använd växeln `-CollectRedfieldEvents` om du vill samla in händelser som avsänts av Statusövervakare och Redfield Runtime. Dessa loggar är användbara när du ska diagnostisera IIS och program start.
3. Använd båda växlar för att samla in båda händelse typerna.
4. Som standard, om ingen växel har angetts, kommer båda händelse typerna att samlas in.


## <a name="parameters"></a>Parametrar

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Valfritt.** Använd den här parametern för att ange hur länge det här skriptet ska samla in händelser. Standardvärdet är 5 minuter.

### <a name="-logdirectory"></a>-LogDirectory
**Valfritt.** Använd den här växeln för att ange den resulterande katalogen för ETL-filen. Som standard skapas den här filen i katalogen PowerShell-moduler. Den fullständiga sökvägen visas när skript körningen körs.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Valfritt.** Använd den här växeln för att samla in Application Insights SDK-händelser.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Valfritt.** Använd den här växeln om du vill samla in händelser från Statusövervakare och Redfield-körningsmiljön.

### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att skriva ut detaljerade loggar.



## <a name="output"></a>Resultat


### <a name="example-of-application-startup-logs"></a>Exempel på program start loggar
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>Nästa steg

Ytterligare fel sökning:

- Granska ytterligare fel söknings steg här: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Läs [API-referensen](status-monitor-v2-overview.md#powershell-api-reference) om du vill veta mer om parametrar som du kan ha missat.
- Om du behöver ytterligare hjälp kan du kontakta oss på [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Gör mer med Application Insights agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights-agenten.
 - [Hämta konfigurationen](status-monitor-v2-api-get-config.md) för att bekräfta att inställningarna har registrerats korrekt.
 - [Hämta status](status-monitor-v2-api-get-status.md) för att inspektera övervakning.
