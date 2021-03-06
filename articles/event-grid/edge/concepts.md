---
title: Begrepp – Azure Event Grid IoT Edge | Microsoft Docs
description: Begrepp i Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992553"
---
# <a name="event-grid-concepts"></a>Event Grid-begrepp

I den här artikeln beskrivs huvud begreppen i Azure Event Grid.

## <a name="events"></a>Events

En händelse är den minsta mängden information som fullständigt beskriver något som hände i systemet. Varje händelse har gemensam information, t. ex. Händelsens källa, tid då händelsen ägde rum och unik identifierare. Varje händelse har också en speciell information som endast är relevant för den speciella typen av händelse. Stöd för en händelse av en storlek på upp till 1 MB är för närvarande en för hands version.

De egenskaper som ingår i en händelse finns i [Azure Event Grid händelse schema](event-schemas.md).

## <a name="publishers"></a>Utgivare

En utgivare är användaren eller organisationen som bestämmer sig för att skicka händelser till Event Grid. Du kan publicera händelser från ditt eget program.

## <a name="event-sources"></a>Händelsekällor

En händelse källa är den plats där händelsen inträffar. Varje händelse källa är relaterad till en eller flera händelse typer. Azure Storage är till exempel händelse källan för BLOB-skapade händelser. Ditt program är händelse källan för anpassade händelser som du definierar. Händelse källor ansvarar för att skicka händelser till Event Grid.

## <a name="topics"></a>Ämnen

Avsnittet Event Grid innehåller en slut punkt där källan skickar händelser. Avsnittet Event Grid skapas i utgivaren och avgör om en händelse källa behöver ett ämne eller mer än ett ämne. Ett ämne används för en samling relaterade händelser. För att svara på vissa typer av händelser, bestämmer prenumeranter vilka ämnen som ska prenumerera på.

När du designar ditt program är du flexibelt att bestämma hur många ämnen som ska skapas. Skapa ett anpassat ämne för varje kategori av relaterade händelser för stora lösningar. Överväg till exempel ett program som skickar händelser som rör ändring av användar konton och bearbetnings order. Det är osannolikt att ingen händelse hanterare vill ha båda kategorier av händelser. Skapa två anpassade ämnen och låt händelse hanterare prenumerera på den som intresserar dem. För små lösningar kanske du föredrar att skicka alla händelser till ett enda ämne. Händelse prenumeranter kan filtrera efter de händelse typer som de vill ha.

Se [REST API dokumentation](api.md) om hur du hanterar ämnen i Event Grid.

## <a name="event-subscriptions"></a>Prenumerationer på händelser

En prenumeration visar Event Grid vilka händelser på ett ämne som du är intresse rad av att ta emot. När du skapar prenumerationen anger du en slut punkt för att hantera händelsen. Du kan filtrera de händelser som skickas till slut punkten. 

Se [REST API dokumentation](api.md) om hur du hanterar prenumerationer i Event Grid.

## <a name="event-handlers"></a>Händelsehanterare

Från ett Event Grid perspektiv är en händelse hanterare den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Event Grid stöder flera typer av hanterare. Du kan använda en Azure-tjänst som stöds eller din egen webhook som hanterare. Beroende på typen av hanterare Event Grid följande olika mekanismer för att garantera att händelsen levereras. Om mål händelse hanteraren är en HTTP-webbhook görs ett nytt försök att utföra händelsen när hanteraren returnerar status kod för `200 – OK`. För Edge Hub, om händelsen levereras utan undantag, betraktas den som lyckad.

## <a name="security"></a>Säkerhet

Event Grid tillhandahåller säkerhet för att prenumerera på ämnen och publicera ämnen. Mer information finns i [Event Grid säkerhet och autentisering](security-authentication.md).

## <a name="event-delivery"></a>Händelse leverans

Om Event Grid inte kan bekräfta att en händelse har mottagits av prenumerantens slut punkt, kommer den att leverera om händelsen. Mer information finns i [Event Grid meddelande leverans och försök igen](delivery-retry.md).

## <a name="batching"></a>Batchbearbetning

När du använder ett anpassat ämne måste händelser alltid publiceras i en matris. I scenarier med lågt data flöde har matrisen bara ett värde. För användnings fall av stora volymer rekommenderar vi att du grupperar flera händelser per publicering för att uppnå högre effektivitet. Batchar kan vara upp till 1 MB. Varje händelse ska fortfarande inte vara större än 1 MB (för hands version).