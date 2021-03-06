---
title: Application Insights för Worker service-appar (icke-HTTP-appar) | Microsoft Docs
description: Övervaka .NET Core/. NET Framework-appar som inte är HTTP-appar med Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2019
ms.openlocfilehash: 386c171e4785fac2c7fa6da39f249e211f4c660c
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893306"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights för Worker service-program (icke-HTTP-program)

Application Insights lanserar en ny SDK, som kallas `Microsoft.ApplicationInsights.WorkerService`, som passar bäst för icke-HTTP-arbetsbelastningar som meddelanden, bakgrunds aktiviteter, konsol program osv. Dessa typer av program har inte begreppet inkommande HTTP-begäran som en traditionell ASP.NET/ASP.NET Core-webbapp, och därför stöds inte Application Insights-paket för [ASP.net](asp-net.md) -eller [ASP.net Core](asp-net-core.md) -program.

Den nya SDK: n utför inte någon telemetri-samling. I stället finns det på andra välkända Application Insights automatiska insamlare som [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) osv. Detta SDK visar tilläggs metoder för `IServiceCollection` för att aktivera och konfigurera telemetri-samlingen.

## <a name="supported-scenarios"></a>Scenarier som stöds

[Application Insights SDK för Worker-tjänsten](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) passar bäst för icke-http-program oavsett var eller hur de körs. Om ditt program körs och har nätverks anslutning till Azure, kan telemetri samlas in. Application Insights övervakning stöds överallt där .NET Core stöds. Det här paketet kan användas i den nyligen introducerade [.net Core 3,0 Worker-tjänsten](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [bakgrunds aktiviteter i ASP.net Core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), konsol program (.net Core/.NET Framework) osv.

## <a name="prerequisites"></a>Krav

En giltig Application Insights Instrumentation-nyckel. Den här nyckeln krävs för att skicka telemetri till Application Insights. Om du behöver skapa en ny Application Insights resurs för att hämta en Instrumentation-nyckel, se [skapa en Application Insights resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="using-application-insights-sdk-for-worker-services"></a>Använda Application Insights SDK för Worker Services

1. Installera [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) -paketet i programmet.
   I följande kodfragment visas de ändringar som måste läggas till i projektets `.csproj`-fil.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.8.2" />
    </ItemGroup>
```

1. Anropa `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` tilläggs metod på `IServiceCollection`, och ange Instrumentation-nyckeln. Den här metoden ska anropas i början av programmet. Den exakta platsen beror på typen av program.

1. Hämta en `ILogger` instans eller `TelemetryClient` instans från behållaren för beroende inmatning (DI) genom att anropa `serviceProvider.GetRequiredService<TelemetryClient>();` eller använda konstruktorn för konstruktorn. Det här steget utlöser konfigurationen av moduler för `TelemetryConfiguration` och automatisk insamling.

Specifika anvisningar för varje typ av program beskrivs i följande avsnitt.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3,0 Worker service-program

Det fullständiga exemplet delas [här](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Hämta och installera [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Skapa ett nytt Worker service-projekt med hjälp av Visual Studio New Project-mall eller kommando rad `dotnet new worker`
3. Installera [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) -paketet i programmet.

4. Lägg till `services.AddApplicationInsightsTelemetryWorkerService();` i `CreateHostBuilder()`-metoden i `Program.cs`-klassen, som i det här exemplet:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Ändra `Worker.cs` enligt exemplet nedan.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Konfigurera Instrumentation-tangenten.

    Även om du kan ange Instrumentation-nyckeln som ett argument för `AddApplicationInsightsTelemetryWorkerService`, rekommenderar vi att du anger Instrumentation-nyckeln i konfigurationen. Följande kod exempel visar hur du anger en Instrumentation-nyckel i `appsettings.json`. Se till att `appsettings.json` kopieras till programmets rotmapp under publiceringen.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Alternativt kan du ange Instrumentation-nyckeln i någon av följande miljövariabler.
`APPINSIGHTS_INSTRUMENTATIONKEY` eller `ApplicationInsights:InstrumentationKey`

Exempel: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
ELLER `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Normalt anger `APPINSIGHTS_INSTRUMENTATIONKEY` Instrumentation-nyckeln för program som distribueras till Web Apps som webb jobb.

> [!NOTE]
> En Instrumentation-nyckel som anges i kod WINS över miljövariabeln `APPINSIGHTS_INSTRUMENTATIONKEY`, som vinner över andra alternativ.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Core bakgrunds aktiviteter med värdbaserade tjänster

[Det här](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) dokumentet beskriver hur du skapar aktiviteter för bakgrunder i ASP.net Core 2.1/2.2-program.

Det fullständiga exemplet delas [här](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Installera Microsoft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) -paketet i programmet.
2. Lägg till `services.AddApplicationInsightsTelemetryWorkerService();` i `ConfigureServices()`-metoden, som i det här exemplet:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

Följande är koden för `TimedHostedService` där logiken för bakgrunds aktiviteter finns.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Konfigurera Instrumentation-tangenten.
   Använd samma `appsettings.json` från exemplet .NET Core 3,0 Worker service ovan.

## <a name="net-corenet-framework-console-application"></a>.NET Core/NET Framework konsol program

Som vi nämnt i början av den här artikeln kan det nya paketet användas för att aktivera Application Insights Telemetry från till och med ett vanligt konsol program. Det här paketet riktar [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)och kan därför användas för-konsol program i .net Core 2,0 eller högre, och .NET Framework 4.7.2 eller högre.

Det fullständiga exemplet delas [här](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Installera Microsoft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) -paketet i programmet.

2. Ändra Program.cs enligt exemplet nedan.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key must be specified here.
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Det här konsol programmet använder också samma standard `TelemetryConfiguration`och kan anpassas på samma sätt som exemplen i det tidigare avsnittet.

## <a name="run-your-application"></a>Köra ditt program

Kör ditt program. Exempel arbetarna från alla ovanstående steg gör ett HTTP-anrop varje sekund till bing.com och ger också några loggar med hjälp av ILogger. Dessa rader omsluts i `StartOperation` anrop av `TelemetryClient`som används för att skapa en åtgärd (i det här exemplet `RequestTelemetry` med namnet "operation"). Application Insights samlar in dessa ILogger-loggar (varning eller över som standard) och beroenden, och de korreleras till `RequestTelemetry` med överordnad-underordnad relation. Korrelationen fungerar också som en kors process/nätverks gränser. Om till exempel anropet gjordes till en annan övervakad komponent kommer den också att korreleras med den överordnade.

Den här anpassade åtgärden i `RequestTelemetry` kan ses som motsvarigheten till en inkommande webbegäran i ett typiskt webb program. Även om det inte är nödvändigt att använda en åtgärd passar den bäst med [Application Insights korrelations data modell](https://docs.microsoft.com/azure/azure-monitor/app/correlation) – med `RequestTelemetry` fungerar som överordnad åtgärd och varje telemetri som genereras inom arbetarnas iterationer behandlas som ett logiskt objekt som tillhör samma åtgärd. Den här metoden garanterar också att all telemetri som genereras (automatisk och manuell) har samma `operation_id`. När samplingen baseras på `operation_id`behåller eller släpper algoritmen all telemetri från en enda iteration.

Nedan visas en lista över all telemetri som samlats in automatiskt av Application Insights.

### <a name="live-metrics"></a>Live-mått

[Live-mått](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) kan användas för att snabbt kontrol lera om Application Insights övervakning har kon figurer ATS korrekt. Det kan ta några minuter innan telemetri börjar visas i portalen och i analysen, men Live-mått visar processor användningen för den process som körs i nära real tid. Det kan också visa andra telemetri som begär Anden, beroenden, spår osv.

### <a name="ilogger-logs"></a>ILogger-loggar

Loggar som släpps via `ILogger` av allvarlighets grad `Warning` eller större samlas in automatiskt. Följ [ILogger-dokument](ilogger.md#control-logging-level) för att anpassa vilka logg nivåer som samlas in av Application Insights.

### <a name="dependencies"></a>Beroenden

Beroende insamling är aktiverat som standard. [Den här](asp-net-dependencies.md#automatically-tracked-dependencies) artikeln förklarar de beroenden som samlas in automatiskt och som även innehåller steg för manuell spårning.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` är aktiverat som standard och samlar in en standard uppsättning räknare från .NET Core 3,0-appar. Självstudien om [EventCounter](eventcounters.md) visar en lista över standard uppsättningen med insamlade räknare. Den innehåller också anvisningar om hur du anpassar listan.

### <a name="manually-tracking-additional-telemetry"></a>Manuellt spåra ytterligare telemetri

SDK: n samlar automatiskt in telemetri enligt beskrivningen ovan, i de flesta fall behöver användaren skicka ytterligare telemetri till Application Insights-tjänsten. Det rekommenderade sättet att spåra ytterligare telemetri är genom att hämta en instans av `TelemetryClient` från beroende insprutning och sedan anropa en av de `TrackXXX()` [API](api-custom-events-metrics.md) -metoder som stöds. Ett annat vanligt användnings fall är [anpassad spårning av åtgärder](custom-operations-tracking.md). Den här metoden visas i arbets exemplen ovan.

## <a name="configure-the-application-insights-sdk"></a>Konfigurera Application Insights SDK

Standard `TelemetryConfiguration` som används av Worker service SDK liknar den automatiska konfigurationen som används i ett ASP.NET-eller ASP.NET Core-program, minus TelemetryInitializers som används för att utöka telemetri från `HttpContext`.

Du kan anpassa Application Insights SDK för Worker-tjänsten om du vill ändra standard konfigurationen. Användare av Application Insights ASP.NET Core SDK kan vara bekanta med att ändra konfigurationen genom att använda ASP.NET Core inbyggd [beroende inmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). WorkerService SDK baseras också på liknande principer. Gör nästan alla konfigurations ändringar i `ConfigureServices()` avsnittet genom att anropa lämpliga metoder på `IServiceCollection`, enligt beskrivningen nedan.

> [!NOTE]
> När du använder denna SDK kan du inte ändra konfigurationen genom att ändra `TelemetryConfiguration.Active` och ändringarna visas inte.

### <a name="using-applicationinsightsserviceoptions"></a>Använda ApplicationInsightsServiceOptions

Du kan ändra några vanliga inställningar genom att skicka `ApplicationInsightsServiceOptions` till `AddApplicationInsightsTelemetryWorkerService`, som i det här exemplet:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Observera att `ApplicationInsightsServiceOptions` i det här SDK: n finns i namn området `Microsoft.ApplicationInsights.WorkerService` i stället för att `Microsoft.ApplicationInsights.AspNetCore.Extensions` i ASP.NET Core SDK.

Inställningar som används ofta i `ApplicationInsightsServiceOptions`

|Inställning | Beskrivning | Standard
|---------------|-------|-------
|EnableQuickPulseMetricStream | Aktivera/inaktivera LiveMetrics-funktionen | sant
|EnableAdaptiveSampling | Aktivera/inaktivera adaptiv sampling | sant
|EnableHeartbeat | Funktionen Aktivera/inaktivera pulsslag, som regelbundet (15 min standard) skickar ett anpassat mått med namnet "HeartBeatState" med information om körnings miljön som .NET-version, Azure-miljö information, om tillämpligt, osv. | sant
|AddAutoCollectedMetricExtractor | Aktivera/inaktivera AutoCollectedMetrics Extractor, som är en TelemetryProcessor som skickar församlade mått om begär Anden/beroenden innan provtagning sker. | sant

Se de [konfigurerbara inställningarna i `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) för den senaste listan.

### <a name="sampling"></a>Sampling

Application Insights SDK för Worker-tjänsten stöder både fast priss ätt och anpassningsbar sampling. Adaptiv sampling är aktiverat som standard. Konfigurering av sampling för Worker-tjänsten görs på samma sätt som för [ASP.net Core program](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Lägger till TelemetryInitializers

Använd [telemetri-initierare](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) när du vill definiera egenskaper som skickas med all telemetri.

Lägg till nya `TelemetryInitializer` i `DependencyInjection` container och SDK lägger automatiskt till dem i `TelemetryConfiguration`.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Tar bort TelemetryInitializers

Telemetri-initierare finns som standard. Om du vill ta bort alla eller vissa telemetri-initierare använder du följande exempel kod *när* du har anropat `AddApplicationInsightsTelemetryWorkerService()`.

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Lägga till telemetri-processorer

Du kan lägga till anpassade telemetri-processorer till `TelemetryConfiguration` med hjälp av tilläggs metoden `AddApplicationInsightsTelemetryProcessor` på `IServiceCollection`. Du kan använda telemetri-processorer i [avancerade filtrerings scenarier](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) för att få mer direkt kontroll över vad som ingår eller exkluderas från Telemetrin som du skickar till den Application Insights tjänsten. Använd följande exempel.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurera eller ta bort standard TelemetryModules

Application Insights använder telemetri-moduler för att automatiskt samla in telemetri om vissa arbets belastningar utan att behöva spåra manuellt.

Följande moduler för automatisk insamling är aktiverade som standard. Dessa moduler är ansvariga för automatisk insamling av telemetri. Du kan inaktivera eller konfigurera dem för att ändra deras standard beteende.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Om du vill konfigurera standard `TelemetryModule`använder du tilläggs metoden `ConfigureTelemetryModule<T>` på `IServiceCollection`, som du ser i följande exempel.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Konfigurera telemetri kanal

Standard kanalen är `ServerTelemetryChannel`. Du kan åsidosätta det som visas i följande exempel.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Inaktivera telemetri dynamiskt

Om du vill inaktivera telemetri villkorligt och dynamiskt kan du lösa `TelemetryConfiguration` instans med ASP.NET Core beroende injektions behållare var som helst i koden och ange `DisableTelemetry` flagga på den.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Hur kan jag spåra telemetri som inte samlas in automatiskt?

Hämta en instans av `TelemetryClient` med hjälp av konstruktorn för konstruktorn och anropa den nödvändiga `TrackXXX()`-metoden på den. Vi rekommenderar att du inte skapar nya `TelemetryClient`-instanser. En singleton-instans av `TelemetryClient` är redan registrerad i `DependencyInjection` container, som delar `TelemetryConfiguration` med en rest av Telemetrin. Att skapa en ny `TelemetryClient` instans rekommenderas endast om den behöver en konfiguration som är separat från resten av Telemetrin.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Kan jag använda Visual Studio IDE för att publicera Application Insights till ett Worker service-projekt?

Visual Studio IDE-onboarding stöds för närvarande endast för ASP.NET/ASP.NET Core-program. Det här dokumentet kommer att uppdateras när Visual Studio levererar stöd för att registrera Worker service-program.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kan jag aktivera Application Insights övervakning med hjälp av verktyg som Statusövervakare?

Nej. [Statusövervakare](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) och [statusövervakare v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) stöder för närvarande endast ASP.NET 4. x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Fungerar alla funktioner om jag kör mitt program i Linux?

Ja. Funktions stöd för det här SDK: n är samma på alla plattformar, med följande undantag:

* Prestanda räknare stöds bara i Windows med undantag för process-CPU/minne som visas i Live-mått.
* Även om `ServerTelemetryChannel` är aktiverat som standard, om programmet körs i Linux eller MacOS, skapar kanalen inte automatiskt en lokal lagringsmapp för att hålla telemetri tillfälligt om det uppstår nätverks problem. På grund av den här begränsningen försvinner telemetri när det uppstår tillfälliga nätverks-eller Server problem. Undvik det här problemet genom att konfigurera en lokal mapp för kanalen:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Exempelprogram

[Program för .net Core-konsol](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Använd det här exemplet om du använder ett konsol program som skrivits i antingen .NET Core (2,0 eller högre) eller .NET Framework (4.7.2 eller högre)

[ASP .net Core-aktiviteter i bakgrunden med HostedServices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Använd det här exemplet om du befinner dig i Asp.Net Core 2.1/2.2 och skapar bakgrunds aktiviteter enligt den officiella vägledningen [här](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[.Net Core 3,0 Worker-tjänst](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Använd det här exemplet om du har ett .NET Core 3,0 Worker service-program enligt den officiella vägledningen [här](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>SDK för öppen källkod

[Läs och bidra till koden](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Nästa steg

* [Använd API: et](../../azure-monitor/app/api-custom-events-metrics.md) för att skicka egna händelser och mått för en detaljerad vy över appens prestanda och användning.
* [Spåra ytterligare beroenden som inte spåras automatiskt](../../azure-monitor/app/auto-collect-dependencies.md).
* [Utöka eller filtrera automatiskt insamlad telemetri](../../azure-monitor/app/api-filtering-sampling.md).
* [Beroende inmatning i ASP.net Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
