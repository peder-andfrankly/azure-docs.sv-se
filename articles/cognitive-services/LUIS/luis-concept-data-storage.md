---
title: Data lagring – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS lagrar krypterade i ett Azure datalager som motsvarar den region som anges av nyckeln.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639234"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Datalagring och tas bort i Cognitive Services för Språkförståelse (LUIS)
LUIS lagrar krypterade i ett Azure datalager som motsvarar den region som anges av nyckeln. Dessa data lagras i 30 dagar. 

## <a name="export-and-delete-app"></a>Exportera och ta bort appen
Användarna har full kontroll över [exportera](luis-how-to-start-new-app.md#export-app) och [tar bort](luis-how-to-start-new-app.md#delete-app) appen. 

## <a name="utterances"></a>Yttranden

Yttranden kan lagras på två olika platser. 

* Under **redigerings processen**skapas och lagras yttranden i avsikten. Yttranden i avsikter krävs för en lyckad LUIS-app. När appen har publicerats och tar emot frågor på slut punkten, bestämmer slut punktens begär `log=false`ande QueryString, om slut punktens uttryck är lagrad. Om slut punkten lagras blir den en del av den aktiva inlärnings-yttranden som finns i avsnittet **build** i portalen i avsnittet **Granska slut punkts yttranden** . 
* När du **granskar slut punkts yttranden**och lägger till en uttryck till ett avsikts sätt, lagras uttryck inte längre som en del av slut punkten yttranden som ska granskas. Den läggs till i appens avsikter. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Ta bort exempel yttranden från ett avsikts syfte

Ta bort exempel yttranden som används för träning [LUIS](luis-reference-regions.md). Om du tar bort en exempel-uttryck från LUIS-appen tas bort från LUIS-webbtjänsten och är inte tillgänglig för export.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Ta bort yttranden i granskning från aktiv inlärning

Du kan ta bort yttranden från listan över användare yttranden som LUIS föreslår i den  **[granskningssidan endpoint yttranden](luis-how-to-review-endpoint-utterances.md)** . Tar bort yttranden i den här listan förhindrar du att de ska visas som förslag, men ta bort inte dem från loggar.

Om du inte vill ha aktiva inlärnings yttranden kan du [inaktivera aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Om du inaktiverar aktiv inlärning inaktive ras även loggning.

### <a name="disable-logging-utterances"></a>Inaktivera loggning yttranden
Om du [inaktiverar aktiv inlärning](luis-how-to-review-endpoint-utterances.md#disable-active-learning) inaktive ras loggning.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Ta bort ett konto
Om du tar bort ett konto raderas alla appar, och deras exempel yttranden och loggar. Dessa data hålls kvar i 60 dagar innan kontot och data tas bort permanent.

Tar bort kontot är tillgänglig från den **inställningar** sidan. Välj namnet på ditt konto i det övre högra navigeringsfältet för att komma till den **inställningar** sidan.

## <a name="data-inactivity-as-an-expired-subscription"></a>Data inaktivitet som en prenumeration som har upphört att gälla
För datakvarhållning och borttagning, kan en inaktiv LUIS-app på _Microsofts gottfinnande_ behandlas som en prenumeration som har upphört att gälla. En app betraktas som inaktiv om den uppfyller följande kriterier för de senaste 90 dagarna: 

* Har haft **inga** anrop som görs till den.
* Har inte ändrats.
* Inte har en aktuell nyckel tilldelat till den.
* Inte har haft en användarinloggning till den.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om att exportera och ta bort en app](luis-how-to-start-new-app.md)
