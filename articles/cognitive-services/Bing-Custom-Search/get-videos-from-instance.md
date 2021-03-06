---
title: Hämta videor från din anpassade vy – Anpassad sökning i Bing
titleSuffix: Azure Cognitive Services
description: Översikt på hög nivå om hur du använder Anpassad sökning i Bing för att hämta videor från din anpassade vy av webben.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 222256036a59c7df302546bbf82648c4d524d43f
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405087"
---
# <a name="get-videos-from-your-custom-view"></a>Hämta videor från din anpassade vy

Med Bing-funktionen för anpassade videor kan du utöka din anpassade Sök upplevelse med videor. På ett liknande sätt som med webbresultat har anpassad sökning stöd för videosökning i listan över webbplatser i dina instanser. Du kan hämta videor med Bing: s anpassade videor Sök-API eller via funktionen för värdbaserad gränssnitt. Att använda funktionen för värdbaserade gränssnitt är enkel att använda och rekommendera för att få din Sök upplevelse igång i kort ordning. Information om hur du konfigurerar ditt värd gränssnitt för att inkludera videor finns i [Konfigurera den värdbaserade gränssnitts upplevelsen](hosted-ui.md).

Om du vill ha mer kontroll över att Visa Sök resultaten kan du använda Bing: s anpassade videor Sök-API. Eftersom anrop till API: et liknar att anropa API för videosökning i Bing, kan du checka [videosökning i Bing](../Bing-Video-Search/search-the-web.md) för exempel som anropar API: et. Men innan du gör det kan du bekanta dig med det [anpassade videoklippet Sök-API referens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) innehåll. De huvudsakliga skillnaderna är de frågeparametrar som stöds (du måste inkludera Frågeparametern customConfig) och den slut punkt som du skickar begär anden till.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
