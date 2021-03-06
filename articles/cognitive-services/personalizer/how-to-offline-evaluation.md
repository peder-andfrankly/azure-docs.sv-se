---
title: Så här gör du en offline-utvärdering – Personanpassare
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du använder offline-utvärdering för att mäta appens effektivitet och analysera din inlärnings slinga.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: c2aec0db2d1f9865188f2749a0eeb765a14d04ed
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953008"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analysera din inlärnings slinga med en offline-utvärdering

Lär dig hur du slutför en offline-utvärdering och förstår resultatet.

Med offline-utvärdering kan du mäta hur effektiv Personanpassare jämförs med programmets standard beteende, se vilka funktioner som bidrar mest till anpassning och identifiera nya maskin inlärnings värden automatiskt.

Läs om [offline-utvärderingar](concepts-offline-evaluation.md) för mer information.


## <a name="prerequisites"></a>Krav

* En konfigurerad personanpassa slinga
* Den personliga slingan måste ha en representativ mängd data – som en ungefärligt rekommenderar vi minst 50 000 händelser i sina loggar för meningsfulla utvärderings resultat. Om du vill kan du också ha tidigare exporterade _Learning-principfiler_ som du kan jämföra och testa i samma utvärdering.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Steg för att starta en ny offline-utvärdering

1. Leta upp din anpassnings resurs i [Azure Portal](https://azure.microsoft.com/free/).
1. I Azure Portal går du till avsnittet **utvärderingar** och väljer **Skapa utvärdering**.
    ![i Azure Portal går du till avsnittet * * utvärdering * * och väljer * * Skapa utvärdering * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Konfigurera följande värden:

    * Ett utvärderings namn
    * Start-och slutdatum – dessa datum är tidigare, vilket anger det data intervall som ska användas i utvärderingen. Dessa data måste finnas i loggarna, enligt vad som anges i [datakvarhållning](how-to-settings.md) svärdet.
    * Optimerings identifiering har angetts till **Ja**

    ![Välj inställningar för utvärdering av offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Starta utvärderingen genom att välja **OK**. 

## <a name="results"></a>Resultat

Utvärderingen kan ta lång tid att köra, beroende på mängden data som ska bearbetas, antalet inlärnings principer som ska jämföras och om en optimering har begärts.

När du är klar kan du välja utvärderingen i listan över utvärderingar. 

Jämförelser av inlärnings principer är:

* **Online princip**: den aktuella inlärnings principen som används i personanpassa
* **Bas linje**: programmets standard (som fastställs av den första åtgärden som skickas i rang anrop).
* **Slumpmässig princip**: ett tänkt ranknings beteende som alltid returnerar slumpmässiga val av åtgärder från de angivna.
* **Anpassade principer**: ytterligare inlärnings principer laddas upp när utvärderingen startades.
* **Optimerad princip**: om utvärderingen startades med alternativet för att identifiera en optimerad princip kommer den också att jämföras och du kommer att kunna ladda ned den eller göra den till en utbildnings princip som ersätter den aktuella.

![Resultat diagram över inställningar för offline-utvärdering](./media/offline-evaluation/evaluation-results.png)

Effektiviteten i [funktioner](concepts-features.md) för åtgärder och sammanhang.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur offline-utvärdering fungerar](concepts-offline-evaluation.md).
