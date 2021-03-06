---
title: 'Snabb start: identifiera tal, avsikter och entiteter – tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a87ed9355a5939393fd5e20f395cc96f35e7f150
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816080"
---
I den här snabb starten kommer du att använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt identifiera tal från ljuddata som fångats från en mikrofon. Efter att ha uppfyllt några krav, tar igenkänning av tal från en mikrofon bara fyra steg:
> [!div class="checklist"]
>
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ````IntentRecognizer````-objekt med hjälp av ````SpeechConfig````-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````IntentRecognizer````-objektet.
> * Granska ````IntentRecognitionResult```` som returneras.
