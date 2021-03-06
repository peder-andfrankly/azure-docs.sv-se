---
title: Diagnostikloggning
titleSuffix: Azure Cognitive Services
description: Den här guiden innehåller stegvisa instruktioner för att aktivera diagnostisk loggning för en Azure-tjänst. Dessa loggar ger omfattande, frekventa data om driften av en resurs som används för problem identifiering och fel sökning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827902"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Aktivera diagnostisk loggning för Azure Cognitive Services

Den här guiden innehåller stegvisa instruktioner för att aktivera diagnostisk loggning för en Azure-tjänst. Dessa loggar ger omfattande, frekventa data om driften av en resurs som används för problem identifiering och fel sökning. Innan du fortsätter måste du ha ett Azure-konto med en prenumeration på minst en kognitiv tjänst, till exempel [webbsökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [tal tjänster](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)eller [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Förutsättningar

Om du vill aktivera diagnostikloggning måste du lagra dina loggdata någonstans. I den här självstudien används Azure Storage och Log Analytics.

* [Azure Storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) – behåller diagnostikloggar för princip granskning, statisk analys eller säkerhets kopiering. Lagrings kontot måste inte finnas i samma prenumeration som resursen som skapar loggar så länge den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) – ett flexibelt loggs öknings-och analys verktyg som möjliggör analys av obehandlade loggar som genereras av en Azure-resurs.

> [!NOTE]
> Det finns ytterligare konfigurations alternativ. Mer information finns i [samla in och använda loggdata från dina Azure-resurser](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Aktivera insamling av diagnostikdata  

Låt oss börja med att aktivera diagnostisk loggning med hjälp av Azure Portal.

> [!NOTE]
> Om du vill aktivera den här funktionen med PowerShell eller Azure CLI använder du anvisningarna i [samla in och Använd loggdata från dina Azure-resurser](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

1. Navigera till Azure Portal. Leta sedan reda på och välj en Cognitive Services resurs. Till exempel din prenumeration på Webbsökning i Bing.   
2. Gå sedan till den vänstra navigerings menyn och leta upp **övervakning** och välj **diagnostikinställningar**. Den här skärmen innehåller alla tidigare skapade diagnostikinställningar för den här resursen.
3. Om det finns en resurs som du redan har skapat som du vill använda, kan du välja den nu. Annars väljer du **+ Lägg till diagnostisk inställning**.
4. Ange ett namn för inställningen. Välj sedan **Arkiv till ett lagrings konto** och **Skicka till Log Analytics**.
5. När du uppmanas att konfigurera väljer du det lagrings konto och den OMS-arbetsyta som du vill använda för att lagra dina diagnostikloggar. **Obs!** Om du inte har ett lagrings konto eller en OMS-arbetsyta, följer du anvisningarna för att skapa ett.
6. Välj **granskning**, **RequestResponse**och **AllMetrics**. Ange sedan kvarhållningsperioden för din diagnostiska loggdata. Om en bevarande princip är inställd på noll lagras händelser för den logg kategorin under obestämd tid.
7. Klicka på **Spara**.

Det kan ta upp till två timmar innan loggnings data är tillgängliga för att fråga och analysera. Oroa dig inte om du inte ser något direkt.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Visa och exportera diagnostikdata från Azure Storage

Azure Storage är en robust lösning för objekt lagring som är optimerad för att lagra stora mängder ostrukturerade data. I det här avsnittet får du lära dig att fråga ditt lagrings konto efter totalt antal transaktioner under 30 dagar och exportera data till Excel.

1. Leta upp den Azure Storage resurs som du skapade i det sista avsnittet från Azure Portal.
2. På den vänstra navigerings menyn letar du reda på **övervakning** och väljer **mått**.
3. Använd de tillgängliga List rutorna för att konfigurera din fråga. I det här exemplet ska vi ställa in tidsintervallet för de **senaste 30 dagarna** och måttet på **transaktionen**.
4. När frågan har slutförts visas en visualisering av transaktionen under de senaste 30 dagarna. Om du vill exportera dessa data använder du knappen **Exportera till Excel** som finns överst på sidan.

Lär dig mer om vad du kan göra med diagnostikdata i [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Visa loggar i Log Analytics

Följ dessa anvisningar för att utforska logg analys data för din resurs.

1. Leta upp och välj **Log Analytics** i den vänstra navigerings menyn från Azure Portal.
2. Leta upp och välj den resurs som du skapade när du aktiverade diagnostik.
3. Leta upp och välj **loggar**under **Allmänt**. Från den här sidan kan du köra frågor mot dina loggar.

### <a name="sample-queries"></a>Exempelfrågor

Här följer några grundläggande Kusto-frågor som du kan använda för att utforska dina loggdata.

Kör den här frågan för alla diagnostikloggar från Azure Cognitive Services under en angiven tids period:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Kör den här frågan för att se de 10 senaste loggarna:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Kör den här frågan för att gruppera åtgärder efter **resurs**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Kör den här frågan för att hitta den genomsnittliga tid det tar att utföra en åtgärd:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Kör den här frågan om du vill visa mängden åtgärder över tid som delas av OperationName med antal diskretiseras för varje tiotal.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du aktiverar loggning och även de mått och logg kategorier som stöds av de olika Azure-tjänsterna finns i både [översikten över mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) i Microsoft Azure och [Översikt över artiklar om Azure Diagnostic-loggar](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) .
* Läs de här artiklarna om du vill veta mer om event hubs:
  * [Vad är Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Kom igång med Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Läs [hämta mått och diagnostikloggar från Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Läs [förstå loggs ökningar i Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
