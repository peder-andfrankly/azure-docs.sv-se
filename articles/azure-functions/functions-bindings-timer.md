---
title: Timer-utlösare för Azure Functions
description: Förstå hur du använder timer-utlösare i Azure Functions.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: de36f760fb637ad02446265927e7df7aa91b2abf
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928382"
---
# <a name="timer-trigger-for-azure-functions"></a>Timer-utlösare för Azure Functions 

Den här artikeln förklarar hur du arbetar med timer-utlösare i Azure Functions. Med en timer-utlösare kan du köra en funktion enligt ett schema. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Timer-utlösaren finns i [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-paketet, version 2. x. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

Timer-utlösaren finns i [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-paketet, version 3. x. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exempel

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som körs varje gången minuterna har ett värde mellan fem (t. ex. om funktionen börjar vid 18:57:00 blir nästa prestanda på 19:00:00). [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) -objektet skickas till funktionen.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en timer-utlösare bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver en logg som anger om den här funktionen ska anropas på grund av en utebliven schema förekomst. [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) -objektet skickas till funktionen.

Här är bindningsdata i den *function.json* fil:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Här är C#-skriptkoden:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en timer-utlösare bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver en logg som anger om den här funktionen ska anropas på grund av en utebliven schema förekomst. Ett [Timer-objekt](#usage) skickas till funktionen.

Här är bindningsdata i den *function.json* fil:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Här är JavaScript-kod:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I följande exempel används en timerupplösning-bindning vars konfiguration beskrivs i filen *Function. JSON* . Den faktiska [python-funktionen](functions-reference-python.md) som använder bindningen beskrivs i filen  *__init__. py* . Objektet som har skickats till funktionen är av typen [Azure. functions. TimerRequest-objekt](/python/api/azure-functions/azure.functions.timerrequest). Funktions logiken skriver till loggarna som anger om det aktuella anropet beror på en utebliven schema förekomst. 

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Här är python-koden:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Följande exempel funktion utlöses och körs var femte minut. `@TimerTrigger` anteckning i funktionen definierar schemat med samma sträng format som [cron-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Använd [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar ett CRON-uttryck eller en `TimeSpan`. Du kan bara använda `TimeSpan` om Function-appen körs på en App Service plan. I följande exempel visas ett CRON-uttryck:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

`@TimerTrigger` anteckning i funktionen definierar schemat med samma sträng format som [cron-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `TimerTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste vara inställd på "timerTrigger". Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Ej tillämpligt | Måste anges till ”in”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Ej tillämpligt | Namnet på variabeln som representerar timer-objektet i funktions koden. | 
|**schedule**|**ScheduleExpression**|Ett [cron-uttryck](#ncrontab-expressions) eller ett [TimeSpan](#timespan) -värde. En `TimeSpan` kan bara användas för en Function-app som körs i en App Service-plan. Du kan lägga till schema uttrycket i en app-inställning och ange den här egenskapen till appens inställnings namn i **%** tecken, som i det här exemplet: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Om `true`anropas funktionen när körningen startar. Till exempel startar körningen när funktions programmet aktive ras efter inaktivitet på grund av inaktivitet. När Function-appen startas om på grund av funktions ändringar och när funktions programmet skalas ut. Därför bör **runOnStartup** sällan om de skulle anges till `true`, särskilt i produktion. |
|**useMonitor**|**UseMonitor**|Ange till `true` eller `false` för att ange om schemat ska övervakas. Schema övervakningen har kvar schema förekomster för att se till att schemat upprätthålls korrekt även när Function App-instanser startas om. Om detta inte anges uttryckligen är standardvärdet `true` för scheman som har ett upprepnings intervall som är större än eller lika med 1 minut. För scheman som utlöses mer än en gång per minut är standardvärdet `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Vi rekommenderar att du ställer in **runOnStartup** till `true` i produktion. Med den här inställningen kan kod köras vid mycket oförutsägbara tidpunkter. I vissa produktions inställningar kan de här extra körningarna leda till betydligt högre kostnader för appar som hanteras av förbruknings planer. Med **runOnStartup** aktive ras till exempel utlösaren när din Function-app skalas. Se till att du är helt medveten om produktions beteendet för dina funktioner innan du aktiverar **runOnStartup** i produktion.   

## <a name="usage"></a>Användning

När en timer-utlösare anropas, skickas ett Timer-objekt till funktionen. Följande JSON är en exempel representation av objektet timer.

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

Egenskapen `IsPastDue` är `true` när det aktuella funktions anropet är senare än schemalagt. Till exempel kan en omstart av en funktion orsaka att ett anrop saknas.

## <a name="ncrontab-expressions"></a>NCRONTAB-uttryck 

Azure Functions använder [NCronTab](https://github.com/atifaziz/NCrontab) -biblioteket för att tolka NCronTab-uttryck. Ett NCRONTAB-uttryck liknar ett CRON-uttryck, förutom att det innehåller ett ytterligare sjätte fält vid början som ska användas för tids precision i sekunder:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Varje fält kan ha en av följande typer av värden:

|Typ  |Exempel  |Utlöses av  |
|---------|---------|---------|
|Ett speciellt värde |<nobr>"0 5 * * * *"</nobr>|i hh: 05:00 där HH är varje timme (en gång i timmen)|
|Alla värden (`*`)|<nobr>"0 * 5 * * *"</nobr>|5: mm: 00 varje dag, där mm är varje minut i timmen (60 gånger per dag)|
|Ett intervall (`-` operator)|<nobr>"5-7 * * * *"</nobr>|vid HH: mm: 05, hh: mm: 06 och HH: mm: 07 där hh: mm är varje minut i varje timme (3 gånger per minut)|
|En uppsättning värden (`,` operatör)|<nobr>"5,8,10 * * * * *"</nobr>|vid tt: mm: 05, hh: mm: 08 och HH: mm: 10 där hh: mm är varje minut i varje timme (tre gånger per minut)|
|Ett intervall värde (`/` operator)|<nobr>"0 */5 * * * *"</nobr>|i hh: 05:00, hh: 10:00, hh: 15:00, och så vidare till och med hh: 55:00 där HH är varje timme (12 gånger i timmen)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB-exempel

Här följer några exempel på NCRONTAB-uttryck som du kan använda för timer-utlösaren i Azure Functions.

|Exempel|Utlöses av  |
|---------|---------|
|`"0 */5 * * * *"`|var femte minut|
|`"0 0 * * * *"`|en gång överst i varje timme|
|`"0 0 */2 * * *"`|var två: e timme|
|`"0 0 9-17 * * *"`|en gång i timmen från 9 till 5 PM|
|`"0 30 9 * * *"`|vid 9:30 varje dag|
|`"0 30 9 * * 1-5"`|kl. 9:30 varje vardag|
|`"0 30 9 * Jan Mon"`|kl. 9:30 varje måndag i januari|


### <a name="ncrontab-time-zones"></a>NCRONTAB tids zoner

Talen i ett CRON-uttryck refererar till en tid och ett datum, inte ett tidsintervall. Till exempel refererar en 5 i `hour`-fältet till 5:00 AM, inte var 5: e timme.

Standard tids zonen som används med CRON-uttryck är Coordinated Universal Time (UTC). Om du vill att ditt CRON-uttryck ska baseras på en annan tidszon skapar du en app-inställning för din Function-app med namnet `WEBSITE_TIME_ZONE`. Ange värdet till namnet på den önskade tids zonen som visas i [Microsoft Time Zone-indexet](https://technet.microsoft.com/library/cc749073).

  > [!NOTE]
  > `WEBSITE_TIME_ZONE` stöds för närvarande inte i Linux-förbruknings planen.

Till exempel är *Eastern Standard Time* UTC-05:00. Använd följande NCRONTAB-uttryck som kontona för UTC-tidszonen för att utlösa timer-utlösare vid 10:00 AM EST per dag:

```
"0 0 15 * * *"
``` 

Eller skapa en app-inställning för din Function-app med namnet `WEBSITE_TIME_ZONE` och ange värdet **Eastern, normal tid**.  Använder sedan följande NCRONTAB-uttryck: 

```
"0 0 10 * * *"
``` 

När du använder `WEBSITE_TIME_ZONE`justeras tiden för tid ändringar i den angivna tids zonen, t. ex. sommar tid. 

## <a name="timespan"></a>TimeSpan

 En `TimeSpan` kan bara användas för en Function-app som körs i en App Service-plan.

Till skillnad från ett CRON-uttryck anger ett `TimeSpan` värde tidsintervall mellan varje funktions anrop. När en funktion slutförs efter att ha kört längre än det angivna intervallet anropar timern omedelbart funktionen igen.

`TimeSpan` formatet har uttryckts som en sträng `hh:mm:ss` när `hh` är mindre än 24. När de två första siffrorna är 24 eller större är formatet `dd:hh:mm`. Här följer några exempel:

|Exempel |Utlöses av  |
|---------|---------|
|"01:00:00" | varje timma        |
|"00:01:00"|varje minut         |
|"24:00:00" | var 24: e timme        |
|"1,00:00:00" | Varje dag        |

## <a name="scale-out"></a>Skalbarhet

Om en Function-app skalar ut till flera instanser, körs bara en instans av en timer-utlöst funktion över alla instanser.

## <a name="function-apps-sharing-storage"></a>Function Apps delning lagring

Om du delar lagrings konton över Function-appar som inte har distribuerats till App Service kan du uttryckligen behöva tilldela värd-ID till varje app.

| Funktions version | Inställning                                              |
| ----------------- | ---------------------------------------------------- |
| 2. x (och högre)  | `AzureFunctionsWebHost__hostid` miljö variabel |
| 1.x               | `id` i *Host. JSON*                                  |

Du kan utelämna identifiering svärdet eller manuellt ange varje funktions programmets identifierings konfiguration till ett annat värde.

Timer-utlösaren använder ett lagrings lås för att säkerställa att det bara finns en timer-instans när en Function-app skalar ut till flera instanser. Om två Functions-appar delar samma identifierings konfiguration och använder en timer-Utlösare körs bara en timer.

## <a name="retry-behavior"></a>Omprövnings beteende

Till skillnad från kön utlöses inte timer-utlösaren igen när en funktion Miss lyckas. När en funktion Miss lyckas anropas den inte igen förrän nästa gång enligt schemat.

## <a name="troubleshooting"></a>Felsöka

Information om vad som ska göras när timer-utlösaren inte fungerar som förväntat finns i [undersöka och rapportera problem med timer-utlösta funktioner som inte kan utlösas](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå till en snabb start som använder en timer-utlösare](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
