---
title: Skapa en ny Azure Application Insights-resurs | Microsoft Docs
description: Konfigurera Application Insights övervakning manuellt för ett nytt Live-program.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/02/2019
ms.openlocfilehash: 11b581649e6d7a048899394d69e574e8f9a4d22c
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689213"
---
# <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

Azure Application Insights visar data om ditt program i en Microsoft Azure *resurs*. Att skapa en ny resurs är därför en del av [att konfigurera Application Insights för att övervaka ett nytt program][start]. När du har skapat din nya resurs kan du hämta dess instrument nyckel och använda den för att konfigurera Application Insights SDK. Instrumentation-nyckeln länkar din telemetri till resursen.

## <a name="sign-in-to-microsoft-azure"></a>Logga in på Microsoft Azure

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

Logga in på [Azure Portal](https://portal.azure.com)och skapa en Application Insights resurs:

![Klicka på tecknet "+" i det övre vänstra hörnet. Välj Utvecklarverktyg följt av Application Insights](./media/create-new-resource/new-app-insights.png)

   | Inställningar        |  Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Unikt värde | Namn som identifierar den app som du övervakar. |
   | **Resursgrupp**     | myResourceGroup      | Namnet på den nya eller befintliga resurs gruppen som värd för App Insights-data. |
   | **Plats** | USA, östra | Välj en plats nära dig eller nära den plats där din app finns. |

> [!NOTE]
> Även om du kan använda samma resurs namn i olika resurs grupper kan det vara bra att använda ett globalt unikt namn. Detta kan vara användbart om du planerar att [utföra kors resurs frågor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) eftersom det underlättar den nödvändiga syntaxen.

Ange lämpliga värden i de obligatoriska fälten och välj sedan **Granska + skapa**.

![Ange värden i obligatoriska fält och välj sedan "granska + skapa".](./media/create-new-resource/review-create.png)

När din app har skapats öppnas ett nytt fönster. I det här fönstret visas prestanda-och användnings data om det övervakade programmet. 

## <a name="copy-the-instrumentation-key"></a>Kopiera Instrumentation-nyckeln

Instrumentation-nyckeln identifierar den resurs som du vill associera dina telemetridata med. Du kommer att behöva kopiera för att lägga till Instrumentation-nyckeln till programmets kod.

![Klicka och kopiera Instrumentation-tangenten](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Installera SDK i din app

Installera Application Insights SDK i din app. Det här steget beror på typen av program.

Använd Instrumentation-tangenten för att konfigurera [SDK: n som du installerar i ditt program][start].

SDK inkluderar standardmoduler som skickar telemetri utan att du behöver skriva någon ytterligare kod. [Använd API: et][api] för att skicka din egen telemetri för att spåra användar åtgärder eller diagnostisera problem i detalj.

## <a name="creating-a-resource-automatically"></a>Skapa en resurs automatiskt

### <a name="powershell"></a>PowerShell

Skapa en ny Application Insights resurs

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Exempel

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Resultat

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Den fullständiga PowerShell-dokumentationen för denna cmdlet och lär dig hur du hämtar Instrumentation-nyckeln finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0).

### <a name="azure-cli-preview"></a>Azure CLI (för hands version)

För att få åtkomst till för hands versionen Application Insights Azure CLI-kommandon som du först måste köra:

```azurecli
 az extension add -n application-insights
```

Om du inte kör kommandot `az extension add` visas ett fel meddelande som säger: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nu kan du köra följande för att skapa din Application Insights-resurs:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Exempel

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Resultat

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Den fullständiga Azure CLI-dokumentationen för det här kommandot och lär dig hur du hämtar Instrumentation-nyckeln finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Nästa steg
* [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)
* [Utforska mått](../../azure-monitor/app/metrics-explorer.md)
* [Skriv analysfrågor](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md