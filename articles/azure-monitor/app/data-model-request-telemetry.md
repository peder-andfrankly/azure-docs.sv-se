---
title: Data modell för telemetri om begär Anden – Azure Application insikter
description: Application Insights data modell för telemetri för begäran
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: b253a95a39f118efe82e36ac7261a4d6c62a99d6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928839"
---
# <a name="request-telemetry-application-insights-data-model"></a>Begär telemetri: Application Insights data modell

Ett objekt för telemetri (i [Application Insights](../../azure-monitor/app/app-insights-overview.md)) representerar den logiska sekvensen för körning som utlöses av en extern begäran till ditt program. Varje begär ande körning identifieras av unika `ID` och `url` som innehåller alla körnings parametrar. Du kan gruppera begär Anden efter logiska `name` och definiera `source` för den här begäran. Kod körningen kan resultera i `success` eller `fail` och har en viss `duration`. Både lyckade och misslyckade körningar kan grupperas ytterligare av `resultCode`. Start tid för den begärda Telemetrin som definierats på kuvert nivån.

Telemetri för begär ande stöder standard utöknings modellen med anpassade `properties` och `measurements`.

## <a name="name"></a>Namn

Namnet på begäran representerar kod Sök vägen som krävs för att bearbeta begäran. Lågt kardinalitet för att tillåta en bättre gruppering av begär Anden. För HTTP-begäranden representerar den HTTP-metod och URL-sökväg som `GET /values/{id}` utan det faktiska `id` svärdet.

Application Insights Web SDK skickar namn för begäran "i befintligt skick" med avseende på brev fall. Gruppering i UI är Skift läges känsligt, så `GET /Home/Index` räknas separat från `GET /home/INDEX` även om de ofta resulterar i samma styrenhet och åtgärds körning. Orsaken till detta är att webb adresser i allmänhet är [SKIFT läges känsliga](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Du kanske vill se om alla `404` hände för URL: er skrivna med versaler. Du kan läsa mer om namn samling för begäran genom ASP.NET Web SDK i [blogg inlägget](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maxlängd: 1024 tecken

## <a name="id"></a>ID

Identifierare för en begär ande anrops instans. Används för korrelation mellan begäran och andra telemetri-objekt. ID måste vara globalt unikt. Mer information finns på sidan [korrelation](../../azure-monitor/app/correlation.md) .

Maxlängd: 128 tecken

## <a name="url"></a>URL

Begär URL med alla parametrar för frågesträng.

Maxlängd: 2048 tecken

## <a name="source"></a>Källa

Källa för begäran. Exempel är Instrumentation-nyckeln för anroparen eller IP-adressen för anroparen. Mer information finns på sidan [korrelation](../../azure-monitor/app/correlation.md) .

Maxlängd: 1024 tecken

## <a name="duration"></a>Längd

Varaktighet för begäran i format: `DD.HH:MM:SS.MMMMMM`. Måste vara positivt och mindre än `1000` dagar. Det här fältet är obligatoriskt som telemetri för begäran representerar åtgärden med början och slutet.

## <a name="response-code"></a>Svarskod

Resultat av en begär ande körning. HTTP-statuskod för HTTP-begäranden. Det kan vara `HRESULT` värde eller undantags typ för andra typer av begär Anden.

Maxlängd: 1024 tecken

## <a name="success"></a>Lyckades

Indikering för lyckat eller misslyckat anrop. Det här fältet är obligatoriskt. Om du inte uttryckligen anger `false`-en begäran anses vara lyckad. Ange det här värdet om du vill `false` om åtgärden avbröts av undantag eller returnerade fel resultat kod.

Application Insights definiera en begäran som lyckad när svars koden är mindre än `400` eller lika med `401`för webb programmen. Det finns dock fall då den här standard mappningen inte matchar programmets semantik. Svars koden `404` kan indikera "inga poster", som kan vara en del av ett vanligt flöde. Det kan också indikera en bruten länk. För brutna länkar kan du till och med implementera mer avancerad logik. Du kan bara markera brutna länkar som ett avbrott när dessa länkar finns på samma plats genom att analysera URL-referenten. Eller markera dem som felaktiga vid åtkomst från företagets mobil program. På samma sätt `301` och `302` indikerar att det inte går att komma åt från klienten som inte stöder omdirigering.

Delvis accepterade innehålls `206` kan tyda på ett problem med en övergripande begäran. Application Insights slut punkten tar till exempel emot en batch med telemetri-objekt som en enskild begäran. Den returnerar `206` när vissa objekt i gruppen inte bearbetades korrekt. Öknings takten för `206` anger ett problem som behöver undersökas. Liknande logik gäller för `207` multi-status där det är sämsta separata svars koder.

Du kan läsa mer om resultat kod och status kod för begäran i [blogg inlägget](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- [Skriv telemetri för anpassad begäran](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Se [data modell](data-model.md) för Application Insights typer och data modell.
- Lär dig hur du [konfigurerar ASP.net Core](../../azure-monitor/app/asp-net.md) program med Application Insights.
- Kolla ut [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
