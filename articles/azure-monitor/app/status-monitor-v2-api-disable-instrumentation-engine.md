---
title: API-referens för Azure Application Insights-agent
description: Application Insights Agent-API-referens. Disable-InstrumentationEngine. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dd546107164632c821c1ee9a1629fe0a1ca071fc
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899764"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Application Insights Agent-API: Disable-InstrumentationEngine

Den här artikeln beskriver en cmdlet som är medlem i [PowerShell-modulen AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning
Inaktiverar Instrumentation-motorn genom att ta bort vissa register nycklar.
Starta om IIS för att ändringarna ska börja gälla.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörs behörighet.

## <a name="examples"></a>Exempel

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametrar 

### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att skriva ut detaljerade loggar.

## <a name="output"></a>Resultat


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Exempel på utdata från att inaktivera Instrumentation-motorn

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Nästa steg

 Gör mer med Application Insights agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights-agenten.
