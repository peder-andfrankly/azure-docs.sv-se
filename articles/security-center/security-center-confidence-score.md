---
title: Förtroende poäng i Azure Security Center | Microsoft Docs
description: Security Center genererar förtroende poäng för att hjälpa ditt team att avgöra om ett hot är giltigt och hur du prioritering och prioriterar aviseringar.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 5b7e53291e10db17f848553b17dae2f84bdb08b7
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558965"
---
# <a name="alert-confidence-score"></a>Förtroendepoäng för avisering 

Azure Security Center ger dig insyn i de resurser som du kör i Azure, och aviserar dig när potentiella problem upptäcks. Mängden aviseringar kan vara en utmaning för en säkerhets åtgärds grupp för en enskild adress, och det blir nödvändigt att prioritera vilka aviseringar som ska undersökas. Att undersöka aviseringar kan vara komplexa och tids krävande och därför ignoreras vissa varningar.

Förtroende poängen i Security Center kan hjälpa ditt team att prioritering och prioritera aviseringar. Security Center använder automatiskt bransch bästa praxis, intelligenta algoritmer och processer som används av analytiker för att avgöra om ett hot är giltigt och ger dig meningsfulla insikter i form av en förtroende poäng.

## <a name="how-the-confidence-score-is-triggered"></a>Så här utlöses förtroende poängen

Aviseringar genereras när misstänkta processer identifieras på dina virtuella datorer. Security Center granskar och analyserar de här aviseringarna på virtuella Windows-datorer som körs i Azure. Den utför automatiserade kontroller och korrelationer med avancerade algoritmer i flera entiteter och data källor i hela organisationen, och alla dina Azure-resurser och ger en säkerhets Poäng som är ett mått på hur tillförlitlig Security Center att aviseringen är äkta och måste undersökas.

## <a name="understanding-the-confidence-score"></a>Förstå förtroende poängen

Förtroende poäng intervallet är mellan 1 och 100 och representerar det förtroende Security Center har som aviseringen måste undersökas. Ju högre poäng som är, desto mer tryggare Security Center är att aviseringen anger äkta skadlig aktivitet. Förtroende poängen innehåller en lista över de vanligaste orsakerna till varför aviseringen fick sina förtroende poäng. Förtroende poängen gör det enklare för säkerhetsanalytiker att prioritera sina svar på aviseringar och åtgärda de mest tryckta angreppen först, vilket i slut ändan minskar den tid det tar att svara på angrepp och överträdelser.

Så här visar du förtroende poängen:
- Öppna bladet säkerhets aviseringar på Security Center-portalen.
-  Aviseringarna och incidenterna organiseras från högsta till lägsta, vilket innebär att den mer säkra Security Center är att en avisering representerar ett hot, desto närmare är den överst på sidan. 


 ![Förtroendepoäng][1]

Så här visar du de data som bidragit till Security Center förtroende i en avisering:
- I bladet säkerhets avisering, under **förtroende**, se de observationer som har bidragit till förtroende poängen och få insikter relaterade till aviseringen. Detta ger dig mer insikt i de aktiviteter som orsakade aviseringen.

  ![Misstänkt förtroende Poäng][2]

Använd Security Center säkerhets Poäng för att prioritera aviserings prioritering i din miljö. Förtroende poängen sparar tid och ansträngning genom att automatiskt undersöka aviseringar, tillämpa bransch bästa praxis och intelligenta algoritmer och agera som en virtuell analytiker för att avgöra vilka hot som är verkliga och var du behöver fokusera på din uppmärksamhet.


## <a name="next-steps"></a>Nästa steg
Den här artikeln förklaras hur du använder säkerhets poängen för att prioritera aviserings undersökningen. I följande avsnitt kan du lära dig mer om Security Center:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
