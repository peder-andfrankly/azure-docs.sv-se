---
title: 'Snabb start: Använd Azure Portal för att skapa Service Bus ämnen och prenumerationer'
description: 'Snabb start: i den här snabb starten får du lära dig hur du skapar ett Service Bus ämne och prenumerationer på det avsnittet med hjälp av Azure Portal.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6788bf43ef97daf5ef2f823a502c223d718abaac
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718875"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Snabb start: Använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på ämnet
I den här snabb starten använder du Azure Portal för att skapa ett Service Bus ämne och sedan skapa prenumerationer på det avsnittet. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Vad är Service Bus-ämnen och -prenumerationer?
Service Bus-ämnen och -prenumerationer stöder en *publicera/prenumerera*-modell för meddelandekommunikation. När du använder ämnen och prenumerationer så kommunicerar inte komponenterna i ett distribuerat program direkt med varandra. Istället så utbyter de meddelanden via ett ämne, som agerar mellanhand.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

I motsats till Service Bus köer, där varje meddelande bearbetas av en enskild konsument, innehåller ämnen och prenumerationer en en-till-många-form av kommunikation med ett mönster för publicering/prenumeration. Det är möjligt att registrera flera prenumerationer för ett ämne. När ett meddelande skickas till ett ämne så görs det tillgängligt för varje prenumeration för oberoende hantering/bearbetning. En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Du kan välja att registrera filter regler för ett ämne per prenumeration, vilket gör att du kan filtrera eller begränsa vilka meddelanden till ett ämne som tas emot av vilka ämnes prenumerationer.

Med hjälp av Service Bus ämnen och prenumerationer kan du skala för att bearbeta ett stort antal meddelanden över ett stort antal användare och program.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Information om hur du skickar meddelanden till ett ämne och tar emot dessa meddelanden via en prenumeration finns i följande artikel: Välj programmeringsspråket i innehålls förteckningen. 

> [!div class="nextstepaction"]
> [Publicera och prenumerera på meddelanden](service-bus-dotnet-how-to-use-topics-subscriptions.md)
