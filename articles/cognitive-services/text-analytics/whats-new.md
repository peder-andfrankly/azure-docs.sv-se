---
title: Vad är nytt i API för textanalys
titleSuffix: Text Analytics - Azure Cognitive Services
description: Den här artikeln innehåller information om nya versioner och funktioner för Azure Cognitive Services Textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 6ef23c03f01cd8e83d4327f358eaf12209659ce8
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286445"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Vad är nytt i API:et för textanalys?

API för textanalys uppdateras regelbundet. Den här artikeln innehåller information om nya versioner och funktioner för att hålla dig uppdaterad med den senaste utvecklingen.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Namngiven enhets igenkänning v3 offentlig för hands version – oktober 2019

Nästa version av namngiven enhets igenkänning (NER) är nu tillgänglig för offentlig för hands version och ger utökad identifiering och kategorisering av entiteter som finns i text. Den tillhandahåller:

* Igenkänning av följande nya enhets typer:
    * Telefonnummer
    * IP-adress

* En [ny slut punkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) för att identifiera entitets typer för personlig information (endast engelska)
* Separata slut punkter för [entitets igenkänning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) och [entitet länkning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

Enhets länkning stöder engelska och spanska. Språk stöd för NER varierar beroende på enhets typen. 

> [!div class="nextstepaction"]
> [Läs mer om namngiven enhets igenkänning v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Attitydanalys v3 offentlig för hands version – oktober 2019

[Nästa version av Attitydanalys](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) är nu tillgänglig för offentlig för hands version och ger avsevärda förbättringar av precisionen och detaljerna i API: ns text kategorisering och poängsättning. Det ger också:

* Automatisk etikettering för olika sentiment i text.
* Sentiment analys och utdata på en dokument-och menings nivå. 

Den stöder engelska (`en`), japanska (`ja`) Kinesisk (förenklad) (`zh-Hans`), traditionell kinesiska (`zh-Hant`), franska (`fr`), italienska (`it`), spanska (`es`), nederländska (`nl`), portugisiska (`pt`) och tyska (`de`) och är tillgänglig i följande regioner: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`och `West US 2`. 

> [!div class="nextstepaction"]
> [Läs mer om Attitydanalys v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>Nästa steg

* [Vad är API för textanalys?](overview.md)  
* [Exempel på användar scenarier](text-analytics-user-scenarios.md)
* [Sentiment-analys](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Språk identifiering](how-tos/text-analytics-how-to-language-detection.md)
* [Enhets igenkänning](how-tos/text-analytics-how-to-entity-linking.md)
* [Extrahering av nyckel fraser](how-tos/text-analytics-how-to-keyword-extraction.md)
