---
title: Smart identifiering-nedbrytning i allvarlighets grad för spårning i Azure Application Insights | Microsoft Docs
description: Övervaka program spår med Azure Application insikter om ovanliga mönster i trace telemetri.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/27/2017
ms.openlocfilehash: 83c1296beabaaae78289a653c6b30f6665f725c2
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820540"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Nedbrytning i allvarlighets grad för spårning (för hands version)

Spår används ofta i program, eftersom de hjälper dig att berätta vad som händer i bakgrunden. När något går fel ger spåren en viktig insyn i sekvensen av händelser som leder till det oönskade läget. Även om spårning vanligt vis inte är strukturerade, finns det en sak som kan vara konkret från dem – deras allvarlighets grad. I ett programs stabila tillstånd förväntar vi sig att förhållandet mellan "bra" spår (*information* och *utförlig*) och "dåliga" spår (*Varning*, *fel*och *kritisk*) är stabila. Antagandet är att "dåliga" spår kan ske regelbundet till en viss omfattning på grund av ett antal orsaker (tillfälliga nätverks problem för instans). Men när ett verkligt problem börjar växa, är det vanligt vis som en ökning av den relativa procent andelen "dåliga" spår och "bra" spår. Application Insights Smart identifiering analyserar automatiskt spåren som loggats av ditt program och kan varna dig om ovanliga mönster på hur din trace-telemetri är allvarlighets grad.

Den här funktionen kräver ingen särskild konfiguration, förutom att konfigurera spårnings loggning för din app (se så här konfigurerar du en spårnings logg lyssnare för [.net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) eller [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Den är aktiv när din app genererar tillräckligt med telemetri för undantag.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När får jag den här typen av meddelande om Smart identifiering?
Du kan få den här typen av meddelande om förhållandet mellan "bra" spår (spår som loggats med en nivå av *information* eller *utförlig*) och "dåliga" spårar (spår som loggats med en *varnings*nivå, *fel*eller *allvarligt*) minskar i en en angiven dag jämfört med en bas linje som beräknats under de senaste sju dagarna.

## <a name="does-my-app-definitely-have-a-problem"></a>Har min app definitivt ett problem?
Nej, en avisering innebär inte att din app definitivt har ett problem. Även om en försämring i förhållandet mellan "bra" och "dåliga" spår kan tyda på ett program problem, kan den här förändringen av förhållandet vara oskadlig. Ökningen kan till exempel bero på ett nytt flöde i programmet som avger fler "dåliga" spårningar än befintliga flöden).

## <a name="how-do-i-fix-it"></a>Hur gör jag för att åtgärda det?
Aviseringarna innehåller diagnostikinformation som ska stödjas i diagnostikprogrammet:
1. **Prioritering.** Meddelandet visar hur många åtgärder som påverkas. Detta kan hjälpa dig att tilldela en prioritet för problemet.
2. **Utrymme.** Påverkar problemet all trafik eller bara en åtgärd? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Du kan använda relaterade objekt och rapporter som länkar till kompletterande information för att hjälpa dig att diagnostisera problemet.


