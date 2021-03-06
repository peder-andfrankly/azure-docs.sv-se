---
title: Övervaka prestanda för Java-webbappar på Linux – Azure | Microsoft Docs
description: Utökad program prestanda övervakning av din Java-webbplats med det insamlade plugin-programmet för Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.openlocfilehash: 47e5e9dd81174b79e149ba29eec725c8c17eb1a6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176401"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>insamlad: prestanda mått för Linux i Application Insights


Om du vill utforska prestanda mått för Linux-system i [Application Insights](../../azure-monitor/app/app-insights-overview.md)installerar du [samlad](https://collectd.org/), tillsammans med dess Application Insights-plugin-program. Den här lösningen för öppen källkod samlar in olika system-och nätverks statistik.

Vanligt vis använder du insamlad om du redan har [instrumenterat Java-webbtjänsten med Application Insights][java]. Det ger dig mer information som hjälper dig att förbättra appens prestanda eller diagnostisera problem. 

## <a name="get-your-instrumentation-key"></a>Hämta din Instrumentation-nyckel
I [Microsoft Azure-portalen](https://portal.azure.com)öppnar du [Application Insights](../../azure-monitor/app/app-insights-overview.md) resurs där du vill att data ska visas. (Eller [skapa en ny resurs](../../azure-monitor/app/create-new-resource.md ).)

Ta en kopia av Instrumentation-nyckeln, som identifierar resursen.

![Bläddra alla, öppna resursen och välj sedan i list rutan Essentials och kopiera Instrumentation-nyckeln](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Installera insamlad och plugin-programmet
På dina Linux Server-datorer:

1. Installera [samlad](https://collectd.org/) version 5.4.0 eller senare.
2. Hämta [plugin-programmet för Application Insights-insamlade skrivaren](https://github.com/microsoft/ApplicationInsights-Java/tree/master/collectd/src/main/java/com/microsoft/applicationinsights/collectd/internal). Notera versions numret.
3. Kopiera plugin-programmet JAR till `/usr/share/collectd/java`.
4. Redigera `/etc/collectd/collectd.conf`:
   * Se till att [Java-plugin-programmet](https://collectd.org/wiki/index.php/Plugin:Java) är aktiverat.
   * Uppdatera JVMArg för Java. class. Path så att du kan ta med följande JAR. Uppdatera versions numret så att det matchar det som du laddade ned:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Lägg till det här kodfragmentet med hjälp av Instrumentation-nyckeln från din resurs:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Här är en del av en exempel konfigurations fil:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Konfigurera andra [insamlade plugin](https://collectd.org/wiki/index.php/Table_of_Plugins)-program som kan samla in olika data från olika källor.

Starta om samlad i enlighet med dess [manual](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Visa data i Application Insights
Öppna [mått och Lägg till diagram][metrics]i Application Insights resurs och välj de mått som du vill visa från den anpassade kategorin.

Som standard aggregeras måtten på alla värddatorer som måtten samlats in från. Om du vill visa måtten per värd, på bladet diagram information, aktiverar du gruppering och väljer sedan att gruppera efter insamlad värd.

## <a name="to-exclude-upload-of-specific-statistics"></a>Så här undantar du överföring av detaljerad statistik
Som standard skickar Application Insights-plugin alla data som samlas in av alla aktiverade "Read"-plugin-program som har samlats in. 

Så här undantar du data från vissa plugin-program eller data Källor:

* Redigera konfigurations filen. 
* I `<Plugin ApplicationInsightsWriter>`lägger du till direktiv rader så här:

| Direktivet | Verkan |
| --- | --- |
| `Exclude disk` |Undanta alla data som samlas in av `disk`-plugin-programmet |
| `Exclude disk:read,write` |Undanta källorna med namnet `read` och `write` från `disk`-plugin-programmet. |

Avgränsa direktiv med en ny rad.

## <a name="problems"></a>Problem?
*Jag ser inte data i portalen*

* Öppna [sökning][diagnostic] för att se om RAW-händelserna har anlänt. Ibland tar de längre tid att visas i mått Utforskaren.
* Du kan behöva [Ange brand Väggs undantag för utgående data](../../azure-monitor/app/ip-addresses.md)
* Aktivera spårning i Application Insights-plugin-programmet. Lägg till den här raden i `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Öppna en Terminal och börja samla in i utförligt läge för att se eventuella problem som rapporteras:
  * `sudo collectd -f`

## <a name="known-issue"></a>Kända problem

Application Insights Skriv-plugin-programmet är inte kompatibelt med vissa läsa plugin-program. Vissa plugin-program skickar ibland "NaN" där Application Insights-plugin förväntar sig ett flytt ALS nummer.

Symptom: den insamlade loggen visar fel som inkluderar "AI:..." SyntaxError: oväntad token N ".

Lösning: exkludera data som samlas in av problem med Skriv-plugin-program. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


