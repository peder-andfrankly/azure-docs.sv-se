---
title: 'Snabb start: identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektor REST API ochC#'
titleSuffix: Azure Cognitive Services
description: 'Använd API: t för avvikelse detektor för att identifiera avvikelser i din data serie antingen som en batch eller vid strömmande data.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 76308e2167cbedae9572f1fb5037dfb394ce4b17
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483420"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Snabb start: identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektor REST API ochC# 

Använd den här snabb starten för att börja använda de två identifierings lägena för avvikelse detektor API: erna för att identifiera avvikelser i dina tids serie data. Det C# här programmet skickar två API-begäranden som innehåller JSON-formaterade Time Series-data och hämtar svaren.

| API-begäran                                        | Programutdata                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svaret som innehåller avvikelse status (och andra data) för varje data punkt i tids serie data och positionerna för identifierade avvikelser. |
| Identifiera avvikelse statusen för den senaste data punkten | JSON-svaret som innehåller avvikelse status (och andra data) för den senaste data punkten i tids serie data.                                        |

 Även om det här programmet är skrivet i C#, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Du hittar käll koden för den här snabb starten på [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Krav

- Alla versioner av [Visual Studio 2017 eller senare](https://visualstudio.microsoft.com/downloads/),
- En avvikelse nyckel och slut punkt
- [Json.NET](https://www.newtonsoft.com/json) framework, tillgänglig som ett NuGet-paket. Så här installerar du Newtonsoft. JSON som ett NuGet-paket i Visual Studio:
    
    1. Högerklicka på ditt projekt i **Solution Explorer**.
    2. Välj **Hantera NuGet-paket**.
    3. Sök efter *Newtonsoft. JSON* och installera paketet.

- Om du använder Linux/MacOS kan du köra det här programmet med hjälp av [mono](https://www.mono-project.com/).

- En JSON-fil som innehåller tids serie data punkter. Exempel data för den här snabb starten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Skapa en resurs för avvikelse detektor

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny konsol lösning i Visual Studio och Lägg till följande paket. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Skapa variabler för din prenumerations nyckel och din slut punkt. Nedan visas de URI: er som du kan använda för avvikelse identifiering. Dessa kommer att läggas till i tjänst slut punkten senare för att skapa API-begärandena URL: er.

    | Identifierings metod                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Batch-identifiering                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Identifiering på den senaste data punkten | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begär Anden

1. Skapa en ny async-funktion som heter `Request` som använder variablerna som skapats ovan.

2. Ange klientens säkerhets protokoll och huvud information med hjälp av ett `HttpClient`-objekt. Se till att lägga till din prenumerations nyckel i `Ocp-Apim-Subscription-Key`s huvudet. Skapa sedan ett `StringContent`-objekt för begäran.

3. Skicka begäran med `PostAsync()`och returnera sedan svaret.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Identifiera avvikelser som en batch

1. Skapa en ny funktion som kallas `detectAnomaliesBatch()`. Skapa begäran och skicka den genom att anropa funktionen `Request()` med din slut punkt, prenumerations nyckel, URL för identifiering av batch-avvikelse och tids serie data.

2. Deserialisera JSON-objektet och skriv det till-konsolen.

3. Om svaret innehåller `code` fältet, skriver du ut felkoden och fel meddelandet. 

4. Annars hittar du positionerna för avvikelser i data uppsättningen. Svarets `isAnomaly` fält innehåller en matris med booleska värden, som anger om en data punkt är en avvikelse. Konvertera detta till en sträng mat ris med svars objektets `ToObject<bool[]>()` funktion. Iterera genom matrisen och skriv ut indexet för alla `true` värden. Dessa värden motsvarar indexet för avvikande data punkter, om sådana hittades.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelse statusen för den senaste data punkten

1. Skapa en ny funktion som kallas `detectAnomaliesLatest()`. Skapa begäran och skicka den genom att anropa funktionen `Request()` med din slut punkt, prenumerations nyckel, URL för den senaste punkten avvikelse identifiering och tids serie data.

2. Deserialisera JSON-objektet och skriv det till-konsolen.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Läs in dina Time Series-data och skicka begäran

1. I appens huvud metod läser du in dina JSON Time Series-data med `File.ReadAllText()`. 

2. Anropa de funktioner för avvikelse identifiering som skapats ovan. Använd `System.Console.ReadKey()` för att se till att konsol fönstret är öppet när du har kört programmet.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan om du vill visa JSON-svaret på GitHub:
* [Exempel svar för batch-identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exempel svar på senaste Poäng identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
