---
title: Data modell för Azure Application Insights-telemetri | Microsoft Docs
description: Översikt över Application Insights data modell
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.subservice: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: b14ce5cc83dcbbeef2379d21027d4bca337fd9fd
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376023"
---
# <a name="application-insights-telemetry-data-model"></a>Data modell för Application Insights telemetri

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) skickar telemetri från ditt webb program till Azure Portal, så att du kan analysera programmets prestanda och användning. Telemetri modellen är standardiserad så att det går att skapa plattforms-och språk oberoende övervakning. 

Data som samlas in av Application Insights modellera Detta typiska mönster för program körning:

![Application Insights program modell](./media/data-model/application-insights-data-model.png)

Följande typer av telemetri används för att övervaka körningen av appen. Följande tre typer samlas normalt in automatiskt av Application Insights SDK från webb program ramverket:

* [**Request**](data-model-request-telemetry.md) -Generated för att logga en begäran som tagits emot av din app. Application Insights Web SDK genererar till exempel automatiskt ett telemetri objekt för varje HTTP-begäran som din webbapp tar emot. 

    En **åtgärd** är körnings trådar som bearbetar en begäran. Du kan också [skriva kod](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) för att övervaka andra typer av åtgärder, till exempel en "väckning" i ett webb jobb eller en funktion som regelbundet bearbetar data.  Varje åtgärd har ett ID. Detta ID som kan användas för att [gruppera](../../azure-monitor/app/correlation.md) all telemetri som genereras medan din app bearbetar begäran. Varje åtgärd slutförs eller Miss lyckas och har en tids period.
* [**Undantag**](data-model-exception-telemetry.md) – representerar vanligt vis ett undantag som orsakar en åtgärd.
* [**Beroende**](data-model-dependency-telemetry.md) – representerar ett anrop från din app till en extern tjänst eller lagrings plats, till exempel en REST API eller SQL. I ASP.NET definieras beroende anrop till SQL av `System.Data`. Anrop till HTTP-slutpunkter definieras av `System.Net`. 

Application Insights tillhandahåller tre ytterligare data typer för anpassad telemetri:

* [Trace](data-model-trace-telemetry.md) – används antingen direkt eller via en adapter för att implementera diagnostikloggning med ett instrumentande ramverk som är bekant för dig, till exempel `Log4Net` eller `System.Diagnostics`.
* [Event](data-model-event-telemetry.md) – används vanligt vis för att samla in användar interaktion med tjänsten för att analysera användnings mönster.
* [Metric](data-model-metric-telemetry.md) – används för att rapportera periodiska skalära mått.

Varje telemetri-objekt kan definiera [Sammanhangs information](data-model-context.md) , t. ex. program version eller sessions-ID för användare. Context är en uppsättning strikt skrivna fält som avblockerar vissa scenarier. När program versionen är korrekt initierad kan Application Insights Identifiera nya mönster i program beteende som är korrelerade med omdistribution. Sessions-ID kan användas för att beräkna avbrottet eller problemets inverkan på användarna. Beräkning av distinkt antal sessions-ID-värden för vissa misslyckade beroenden, fel spårning eller kritiskt undantag ger en god förståelse för en effekt.

Application Insights telemetri-modellen definierar ett sätt att [korrelera](../../azure-monitor/app/correlation.md) telemetri till den funktion som det är en del av. En begäran kan till exempel göra ett SQL Database samtal och registrerad diagnostikinformation. Du kan ställa in korrelations kontexten för de telemetridata som binder tillbaka till den begär ande Telemetrin.

## <a name="schema-improvements"></a>Schema förbättringar

Application Insights data modell är ett enkelt och enkelt och kraftfullt sätt att modellera din programtelemetri. Vi strävar efter att hålla modellen enkla och smidiga för att stödja viktiga scenarier och att utöka schemat för avancerad användning.

Om du vill rapportera data modell-eller schema problem och förslag använder du GitHub [ApplicationInsights-Home-](https://github.com/Microsoft/ApplicationInsights-Home/issues) lagringsplats.

## <a name="next-steps"></a>Nästa steg

- [Skriv anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md)
- Lär dig hur du [utökar och filtrerar telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- Använd [sampling](../../azure-monitor/app/sampling.md) för att minimera mängden telemetri som baseras på data modellen.
- Kolla ut [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
