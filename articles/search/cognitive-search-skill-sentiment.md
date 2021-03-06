---
title: Sentiment kognitiva kunskaper
titleSuffix: Azure Cognitive Search
description: Extrahera en positiv negativ sentiment Poäng från text i en AI-pipeline i Azure Kognitiv sökning.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3aab703b9c5ffcb5f3280060417ce32fcec2fc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791903"
---
# <a name="sentiment-cognitive-skill"></a>Sentiment kognitiva kunskaper

**Sentiment** -kompetensen utvärderar ostrukturerad text längs ett positivt och negativt värde, och för varje post returneras ett numeriskt resultat mellan 0 och 1. Poäng nära 1 anger positiv känsla och poäng nära 0 anger negativ känsla. Den här kunskapen använder Machine Learning-modeller som tillhandahålls av [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) i Cognitive Services.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet för dokument sprickor i Azure Kognitiv sökning. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser för Azure kognitiv sökning](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. text. SentimentSkill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken för en post ska vara 5000 tecken som mäts av [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Om du behöver dela upp dina data innan du skickar dem till sentiment analys, använder du [text delnings kunskapen](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är Skift läges känsliga.

| Parameternamn |                      |
|----------------|----------------------|
| defaultLanguageCode | valfritt Språk koden som ska användas för dokument som inte uttryckligen anger språk. <br/> Se en [fullständig lista över språk som stöds](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Kompetens inmatningar 

| Inmatat namn | Beskrivning |
|--------------------|-------------|
| text | Den text som ska analyseras.|
| languageCode  |  Valfritt En sträng som anger språket för posterna. Om den här parametern inte anges är standardvärdet "en". <br/>Se en [fullständig lista över språk som stöds](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Kunskaps utmatningar

| Namn på utdata | Beskrivning |
|--------------------|-------------|
| resultat | Ett värde mellan 0 och 1 som representerar sentiment för den analyserade texten. Värden som är nära 0 har negativa sentiment, nära 0,5 har neutral sentiment och värden nära 1 har positiva sentiment.|


##  <a name="sample-definition"></a>Exempel definition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
        }
    ]
}
```

##  <a name="sample-input"></a>Exempel på inmatade

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Exempel på utdata

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Anteckningar
Om värdet är tomt returneras ingen sentiment Poäng för dessa poster.

## <a name="error-cases"></a>Fel fall
Om ett språk inte stöds genereras ett fel och inget sentiment Poäng returneras.

## <a name="see-also"></a>Se också

+ [Inbyggda kunskaper](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
