---
title: Språk stöd – API för textanalys
titleSuffix: Azure Cognitive Services
description: 'En lista över naturliga språk som stöds av API för textanalys. I den här artikeln förklaras vilka språk som stöds för varje åtgärd: sentiment analys, extrahering av nyckel fraser, språk identifiering och enhets igenkänning.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 529568cf4f667e72b0b4fb7ba83d0b85c4a90341
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499983"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Språk-och region stöd för API för textanalys

I den här artikeln förklaras vilka språk som stöds för varje åtgärd: sentiment analys, extrahering av nyckel fraser, språk identifiering och identifiering av namngivna entiteter.

## <a name="language-detection"></a>Språkidentifiering

API för textanalys kan identifiera en mängd olika språk, varianter, dialekter och vissa regionala/kulturella språk.  Språkidentifiering returnerar "skript" för ett språk. För frasen "Jag har en hund" kommer den att returnera `en` i stället för `en-US`. Det enda särskilda fallet är kinesiska, där språk identifierings funktionen returnerar `zh_CHS` eller `zh_CHT` om det kan fastställa skriptet med texten som anges. I situationer där det inte går att identifiera ett särskilt skript för ett kinesiskt dokument kommer det bara att returnera `zh`.

Vi publicerar inte den exakta listan över språk för den här funktionen, men den kan identifiera en mängd olika språk, varianter, dialekter och vissa regionala/kulturella språk. 

Om du har innehåll som uttrycks på ett mindre vanligt språk kan du prova Språkidentifiering för att se om det returnerar en kod. Svaret för språk som inte kan identifieras är `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Attitydanalys, Extrahering av diskussionsämne och identifiering av namngiven entitet

För sentiment-analys, extrahering av nyckel fraser och enhets igenkänning är listan över språk som stöds mer selektiv eftersom analys enheterna är förfinade för att hantera språkliga regler för ytterligare språk. I namngiven enhets igenkänning v2 är stöd för den fullständiga uppsättningen [entitetstyper](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition-v2) för närvarande begränsad till följande språk: 
* Engelska
* Kinesiska – Förenklad
* Franska
* Tyska
* Spanska

Endast `Person`, `Location` och `Organization` namngivna entiteter returneras för de andra språken.

## <a name="language-list-and-status"></a>Språk lista och status

Språk stöd är inlednings vis insamlat i för hands version, som är i allmänhet tillgänglig (GA) status, oberoende av varandra och för Textanalys tjänsten. Det är möjligt att språken förblir i för hands versionen, även om API för textanalys över gångar till allmänt tillgängliga.

> [!NOTE]
> Mer information om språk stöd för den namngivna enhets igenkänningen (NER) v3 Public Preview finns i [namngivna entitetstyper](named-entity-types.md).

| Språk              | Språkkod | Sentiment | Nyckelfraser | Igenkänning av namngiven enhet | Länkning av entitet |       Anteckningar        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arabiska                |     `ar`      |           |             |           ✔ \*           |                |                    |
| Tjeckiska                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| Kinesiska – Förenklad    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                |                    |
| Kinesiska (traditionell)   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| Danska                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Nederländska                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Engelska               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| Finska               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Franska                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Tyska                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Grekiska                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| Ungerska             |     `hu`      |           |             |           ✔ \*           |                |                    |
| Italienska               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Japanska              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Koreanska                |     `ko`      |           |      ✔      |           ✔ \*           |                |                    |
| Norska (bokmål)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Polska                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Portugisiska (Portugal) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | `pt` också accepterat |
| Portugisiska (Brasilien)   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| Ryska               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Spanska               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| Svenska               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Turkiska               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

\* språk stöd finns i för hands version

\** är även tillgängligt i [Attitydanalys v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-v3-public-preview) och/eller [med namnet Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview) offentlig förhands visning.

## <a name="see-also"></a>Se även

[Cognitive Services dokumentations sida](https://docs.microsoft.com/azure/cognitive-services/)   
[Produktsida om Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
