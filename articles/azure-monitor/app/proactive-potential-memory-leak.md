---
title: Smart identifiering-möjlig minnes läcka upptäcktes av Azure Application Insights | Microsoft Docs
description: Övervaka program med Azure Application insikter om potentiella minnes läckor.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/12/2017
ms.openlocfilehash: f07da754c6c9e0ad0541db12740c1d80f7f884fd
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818802"
---
# <a name="memory-leak-detection-preview"></a>Identifiering av minnes läckor (för hands version)

Application Insights analyserar automatiskt minnes förbrukningen för varje process i programmet och kan varna dig om potentiella minnes läckor eller ökad minnes användning.

Den här funktionen kräver ingen särskild konfiguration, förutom att [Konfigurera prestanda räknare](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) för din app. Den är aktiv när din app genererar tillräckligt med minnes prestanda räknare telemetri (till exempel privata byte).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När får jag den här typen av meddelande om Smart identifiering?
Ett typiskt meddelande kommer att följa en konsekvent ökning i minnes förbrukning under en lång tids period, i en eller flera processer och/eller en eller flera datorer, som ingår i ditt program. Machine Learning-algoritmer används för att identifiera ökad minnes användning som matchar mönstret i en minnes läcka.

## <a name="does-my-app-really-have-a-problem"></a>Har min app faktiskt ett problem?
Nej, en avisering innebär inte att din app definitivt har ett problem. Även om minnes läcka mönster ofta indikerar ett program problem, kan dessa mönster vara typiska för din specifika process eller ha en naturlig affärs justering och kan ignoreras.

## <a name="how-do-i-fix-it"></a>Hur gör jag för att åtgärda det?
Aviseringarna innehåller diagnostikinformation som ska stödjas i diagnostisk analys processen:
1. **Prioritering.** Meddelandet visar mängden minnes ökning (i GB) och tidsintervallet som minnet har ökat. Detta kan hjälpa dig att tilldela en prioritet för problemet.
2. **Utrymme.** Hur många datorer uppvisade minnes läcket? Hur många undantag utlöstes under den potentiella minnes läckan? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller minnes läcket som visar processens minnes förbrukning över tid. Du kan också använda relaterade objekt och rapporter som länkar till kompletterande information för att hjälpa dig att diagnostisera problemet ytterligare.
