---
title: Vanliga frågor och svar om API för textanalys
titleSuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om begrepp, kod och scenarier som rör API för textanalys för Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837129"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Vanliga frågor och svar om Textanalys kognitiva tjänster

 Få svar på vanliga frågor om begrepp, kod och scenarier som är relaterade till API för textanalys för Microsoft Cognitive Services på Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Kan Textanalys identifiera sarkasm?

Analys är för positiva och negativa sentiment i stället för stämnings identifiering.

Det finns alltid en viss grad av precision i sentiment-analysen, men modellen är mest användbar när det inte finns någon dold betydelse eller under text för innehållet. Järn, sarkasm, humor och liknande nyanserade innehåll förlitar sig på kulturell kontext och normer för att förmedla avsikten. Den här typen av innehåll är bland de mest utmanande att analysera. Normalt är den största skillnaden mellan en bestämd Poäng som produceras av analysatorn och en undersökande bedömning av en mänsklig för innehåll med nyanserade betydelse.

## <a name="can-i-add-my-own-training-data-or-models"></a>Kan jag lägga till mina egna tränings data eller modeller?

Nej, modellerna förtränas. De enda åtgärder som är tillgängliga för överförda data är poäng, extrahering av nyckel fraser och språk identifiering. Vi är inte värd för anpassade modeller. Om du vill skapa och vara värd för anpassade maskin inlärnings modeller bör du tänka på [maskin inlärnings funktionerna i Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Kan jag begära fler språk?

Sentiment-analys och extrahering av nyckel fraser är tillgängliga för ett [Select-antal språk](text-analytics-supported-languages.md). Bearbetning av naturligt språk är komplex och kräver avsevärd testning innan nya funktioner kan släppas. Därför undviker vi att förpresentera support så att ingen tar ett beroende på funktioner som behöver mer tid till vuxen. 

För att hjälpa oss att prioritera vilka språk som ska användas på nästa, rösta på vissa språk i [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Varför returnerar extrahering av nyckel fraser några ord, men inte andra?

Extrahering av nyckel fraser eliminerar icke-grundläggande ord och fristående adjektiv. Kombinationer av adjektiv-substantiv, till exempel "Spectacular views" eller "osäker väder" returneras tillsammans.

I allmänhet består utdata av Substantiv och objekt i meningen. Utdata visas i storleksordning, med den första frasen som är den viktigaste. Prioriteten mäts med antalet gånger som ett visst begrepp anges eller relationen mellan elementet och andra element i texten.

## <a name="why-does-output-vary-given-identical-inputs"></a>Varför varierar utmatningen, med identiska indata?

Förbättringar av modeller och algoritmer meddelas om ändringen är större eller i tyst fart på tjänsten om uppdateringen är mindre. Med tiden kan du se att samma text inmatnings resultat har en annan sentiment poäng eller en viktig fras. Detta är en normal och avsiktlig konsekvens av användningen av hanterade maskin inlärnings resurser i molnet.

## <a name="next-steps"></a>Nästa steg

Är din fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller rösta på den på vår [UserVoice-webbplats](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Se även

 [StackOverflow: API för textanalys](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Cognitive Services](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
