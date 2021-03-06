---
title: Så här testar du en kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: Testa kunskapsbasen QnA Maker är en viktig del av en iterativ process för att förbättra svaren som returneras. Du kan testa kunskapsbas via ett förbättrat chatt-gränssnitt som också kan du göra ändringar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c139d3a740067e3cecaff90d3171d7b0cb3d52c7
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091760"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Testa din kunskaps bas i QnA Maker

Testa kunskapsbasen QnA Maker är en viktig del av en iterativ process för att förbättra svaren som returneras. Du kan testa kunskapsbas via ett förbättrat chatt-gränssnitt som också kan du göra ändringar.

## <a name="interactively-test-in-qna-maker-portal"></a>Testa interaktivt i QnA Maker Portal

1. Gå till kunskaps basen genom att välja namnet på sidan **Mina kunskaps baser** .
1. Välj **testa** i programmets övre panel för att komma åt test-bildspel-panelen.
1. Ange en fråga i textrutan och tryck RETUR.
1. Bäst matchade svaret från kunskapsbasartikel returneras som svaret.

### <a name="clear-test-panel"></a>Rensa test panelen

Om du vill rensa alla angivna test frågor och resultat från test konsolen väljer du **börja** i det övre vänstra hörnet på test panelen.

### <a name="close-test-panel"></a>Stäng test panelen

Välj knappen **testa** igen för att stänga test panelen. Panelen Test är öppen, kan du inte redigera innehållet i kunskapsbasen.

### <a name="inspect-score"></a>Granska resultatet

Du har granskat informationen om test resultatet i inspektions panelen.

1.  Med test-out-panelen öppen väljer du **Granska** för mer information om svaret.

    ![Granska svar](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Inspektions panelen visas. Panelen visas den översta bedömning avsikt, samt alla identifierade entiteter. På panelen visas resultatet av den valda uttryck.

### <a name="correct-the-top-scoring-answer"></a>Korrigera upp bedömning svar

Om upp bedömning svar är felaktigt, väljer du rätt svar från listan och välj **spara och träna**.

![Korrigera upp bedömning svar](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Lägga till alternativa frågor

Du kan lägga till alternativa former av en fråga till ett visst svar. Typ av alternativ som svarar på i textrutan och klicka på Ange om du vill lägga till dem. Välj **spara och träna** att lagra uppdateringarna.

![Lägga till alternativa frågor](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Lägg till ett nytt svar

Du kan lägga till ett nytt svar om någon av de befintliga svar som kunde matchas är felaktiga eller svaret inte finns i knowledge base (ingen bra matchning hittades i KB). 

Längst ned i listan med svar använder du text rutan för att ange ett nytt svar och trycker på RETUR för att lägga till den. 

Välj **spara och träna** att spara det här svaret. Ett nytt par frågor svar har nu lagts till din kunskapsbas. 

> [!NOTE]
> Alla ändringar till din kunskapsbas endast sparas när du trycker på den **spara och träna** knappen.

### <a name="test-the-published-knowledge-base"></a>Testa den publicerade kunskaps basen

Du kan testa den publicerade versionen av kunskaps basen i test fönstret. När du har publicerat KB väljer du rutan **publicerad KB** och skickar en fråga för att få resultat från den publicerade KB.

![Testa mot en publicerad KB](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Batch-test med verktyget

Använd batch-testverktyget när du vill:

* Fastställ bästa svar och Poäng för en uppsättning frågor
* verifiera förväntat svar för uppsättning av frågor

Batch-testning ingår i batch-testverktyget. Det här verktyget är tillgängligt som en [zippad körbar fil](https://aka.ms/qnamakerbatchtestingtool) för hämtning eller som [ C# käll kod](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting). 

[Referens dokumentation om verktyget](../reference-tsv-format-batch-testing.md) inkluderar:

* kommando rads exemplet för verktyget
* formatet för TSV-och utdatafiler 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Publicera en kunskapsbas](./publish-knowledge-base.md)
