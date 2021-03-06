---
title: Beroende spårning i Azure Application Insights | Microsoft Docs
description: Övervaka beroende anrop från din lokala eller Microsoft Azure webb program med Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/25/2019
ms.openlocfilehash: 7b23da81143a4ae66d9f25cd953c4a3952f27455
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678373"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Beroende spårning i Azure Application insikter 

Ett *beroende* är en extern komponent som anropas av din app. Det är vanligt vis en tjänst som kallas att använda HTTP, en databas eller ett fil system. [Application Insights](../../azure-monitor/app/app-insights-overview.md) mäter varaktigheten för beroende anrop, oavsett om den fungerar eller inte, tillsammans med ytterligare information som namn på beroendet och så vidare. Du kan undersöka vissa beroende anrop och korrelera dem med begär Anden och undantag.

## <a name="automatically-tracked-dependencies"></a>Automatiskt spårade beroenden

Application Insights SDK: er för .NET-och .NET Core-fartyg med `DependencyTrackingTelemetryModule` som är en telemetri-modul som automatiskt samlar in beroenden. Den här beroende samlingen aktive ras automatiskt för [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) och [ASP.net Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) program, när de konfigureras enligt de länkade officiella dokumenten.  `DependencyTrackingTelemetryModule` skickas som [det här](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet-paketet och tas automatiskt när du använder något av NuGet-paketen `Microsoft.ApplicationInsights.Web` eller `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` spårar för närvarande automatiskt följande beroenden:

|Beroenden |Information|
|---------------|-------|
|Http/https | Lokala eller fjärr-http/https-anrop |
|WCF-anrop| Spåras endast automatiskt om http-baserade bindningar används.|
|SQL | Anrop som görs med `SqlClient`. Se [det här](#advanced-sql-tracking-to-get-full-sql-query) för att fånga SQL-fråga.  |
|[Azure Storage (BLOB, tabell, kö)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Anrop som görs med Azure Storage-klienten. |
|[Klient-SDK för EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 och senare. |
|[Service Bus-klient-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 och senare. |
|Azure Cosmos DB | Spåras endast automatiskt om HTTP/HTTPS används. TCP-läget samlas inte in av Application Insights. |

Om du saknar ett beroende eller om du använder ett annat SDK ser du till att det finns i listan över [automatiskt insamlade beroenden](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Om beroendet inte automatiskt samlas in kan du fortfarande spåra det manuellt med ett [spår beroende anrop](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Konfigurera automatisk beroende spårning i konsol program

Om du vill spåra beroenden automatiskt från appar i .NET/.NET Core-konsolen, installerar du NuGet-paketet `Microsoft.ApplicationInsights.DependencyCollector` och initierar `DependencyTrackingTelemetryModule` enligt följande:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Hur fungerar automatisk beroende övervakning?

Beroenden samlas in automatiskt med hjälp av någon av följande metoder:

* Använd byte kod instrumentering kring Select-metoder. (InstrumentationEngine antingen från StatusMonitor eller Azure Web App-tillägg)
* EventSource-återanrop
* DiagnosticSource-återanrop (i de senaste .NET/.NET Core SDK: erna)

## <a name="manually-tracking-dependencies"></a>Spåra beroenden manuellt

Här följer några exempel på beroenden som inte samlas in automatiskt, och därför kräver manuell spårning.

* Azure Cosmos DB spåras automatiskt om [http/https](../../cosmos-db/performance-tips.md#networking) används. TCP-läget samlas inte in av Application Insights.
* Redis

För dessa beroenden som inte samlas in automatiskt av SDK kan du spåra dem manuellt med [TrackDependency-API: et](api-custom-events-metrics.md#trackdependency) som används av de vanliga automatiska insamlings modulerna.

Om du t. ex. skapar din kod med en sammansättning som du inte har skrivit själv, kan du få tid för alla anrop till den, för att ta reda på vilket bidrag det gör till dina svars tider. Om du vill att dessa data ska visas i beroende diagram i Application Insights skickar du dem med `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Alternativt ger `TelemetryClient` utöknings metoder `StartOperation` och `StopOperation` som kan användas för att manuellt spåra beroenden, som du ser [här](custom-operations-tracking.md#outgoing-dependencies-tracking)

Om du vill inaktivera standard modulen för beroende spårning tar du bort referensen till DependencyTrackingTelemetryModule i [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) för ASP.NET program. För ASP.NET Core program följer du instruktionerna [här](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Spåra AJAX-anrop från webb sidor

För webb sidor samlar Application Insights JavaScript SDK automatiskt in AJAX-anrop som beroenden.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Avancerad SQL-spårning för att få en fullständig SQL-fråga

För SQL-anrop samlas och lagras alltid namnet på servern och databasen som namn på den insamlade `DependencyTelemetry`. Det finns ytterligare ett fält med namnet "data" som kan innehålla den fullständiga SQL-frågetexten.

För ASP.NET Core-program finns det inga ytterligare steg som krävs för att få en fullständig SQL-fråga.

För ASP.NET-program samlas fullständig SQL-fråga in med hjälp av kod Instrumentation för byte, som kräver Instrumentation-motorn. Ytterligare plattforms oberoende steg, enligt beskrivningen nedan, krävs.

| Plattform | Steg som krävs för att få en fullständig SQL-fråga |
| --- | --- |
| Azure-webbapp |På kontroll panelen i din webbapp [öppnar du bladet Application Insights](../../azure-monitor/app/azure-web-apps.md) och aktiverar SQL-kommandon under .net |
| IIS-server (virtuell Azure-dator, lokal och så vidare) | Använd Statusövervakare PowerShell-modulen för att [Installera Instrumentation-motorn](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) och starta om IIS. |
| Azure Cloud Service | Lägg till [Start aktivitet för att installera StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Din app ska kunna integreras med ApplicationInsights SDK i bygg tid genom att installera NuGet-paket för [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) eller [ASP.net Core program](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Stöds inte

I ovanstående fall är det korrekta sättet att verifiera instrument motorn korrekt installerat genom att verifiera att SDK-versionen av den insamlade `DependencyTelemetry` är "rddp". "rdddsd" eller "rddf" anger att beroenden samlas in via DiagnosticSource eller EventSource-återanrop, och därför kommer full SQL-frågan inte att fångas.

## <a name="where-to-find-dependency-data"></a>Var du hittar beroende data

* [Program kartan](app-map.md) visualiserar beroenden mellan appen och intilliggande komponenter.
* [Transaktions-diagnostik](transaction-diagnostics.md) visar enhetliga, korrelerade Server data.
* [Fliken webbläsare](javascript.md) visar AJAX-anrop från användarnas webbläsare.
* Klicka dig igenom från långsamma eller misslyckade förfrågningar för att kontrol lera deras beroende anrop.
* [Analytics](#logs-analytics) kan användas för att fråga beroende data.

## <a name="diagnosis"></a>Diagnostisera långsamma förfrågningar

Varje begär ande händelse är associerad med beroende anrop, undantag och andra händelser som spåras medan din app bearbetar begäran. Så om vissa begär Anden blir allvarliga kan du ta reda på om det beror på långsamma svar från ett beroende.

### <a name="tracing-from-requests-to-dependencies"></a>Spåra från begär anden till beroenden

Öppna fliken **prestanda** och gå till fliken **beroenden** överst bredvid åtgärder.

Klicka på ett **beroende namn** under övergripande. När du har valt ett beroende diagram över den beroende fördelningen av varaktigheten visas till höger.

![Klicka på fliken beroende längst upp på fliken prestanda och sedan ett beroende namn i diagrammet](./media/asp-net-dependencies/2-perf-dependencies.png)

Klicka på knappen blå **exempel** längst ned till höger och sedan på ett exempel för att se transaktions information från slut punkt till slut punkt.

![Klicka på ett exempel för att se transaktions information från slut punkt till slut punkt](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profilera din Live-webbplats

Ingen idé när tiden går? [Application Insights profiler](../../azure-monitor/app/profiler.md) spårar HTTP-anrop till din Live-plats och visar de funktioner i koden som tog den längsta tiden.

## <a name="failed-requests"></a>Misslyckade förfrågningar

Misslyckade förfrågningar kan också associeras med misslyckade anrop till beroenden.

Vi kan gå till fliken **problem** till vänster och sedan klicka på fliken **beroenden** överst.

![Klicka på diagram över misslyckade begär Anden](./media/asp-net-dependencies/4-fail.png)

Här kan du se antalet misslyckade beroenden. Om du vill ha mer information om en misslyckad förekomst försöker du klicka på ett beroende namn i den nedre tabellen. Du kan klicka på knappen blå **beroenden** längst ned till höger för att hämta transaktions information från slut punkt till slut punkt.

## <a name="logs-analytics"></a>Loggar (analys)

Du kan spåra beroenden i [Kusto-frågespråket](/azure/kusto/query/). Här följer några exempel.

* Hitta eventuella misslyckade beroende anrop:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Sök efter AJAX-anrop:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Hitta beroende anrop som är associerade med begär Anden:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Hitta AJAX-anrop som är kopplade till sid visningar:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Hur Miss lyckas automatiskt beroende insamlaren anrop till beroenden?*

* Misslyckade beroende anrop har fältet lyckades inställt på false. `DependencyTrackingTelemetryModule` rapporterar inte `ExceptionTelemetry`. Den fullständiga data modellen för beroende beskrivs [här](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>SDK för öppen källkod
Precis som varje Application Insights SDK är beroende samlings modul också öppen källkod. Läs och bidra till koden eller rapportera problem på [den officiella GitHub-lagrings platsen](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Nästa steg

* [Undantag](../../azure-monitor/app/asp-net-exceptions.md)
* [Sid data för användare &](../../azure-monitor/app/javascript.md)
* [Tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
