---
title: Översikt över Azure Service Bus-meddelanden | Microsoft Docs
description: Beskrivning av Service Bus-meddelanden
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: 12d4bada4f84098f1559ea7b59fbbd35e0801347
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561599"
---
# <a name="what-is-azure-service-bus"></a>Vad är Azure Service Bus?

Microsoft Azure Service Bus är en helt hanterad integrerad meddelandekoordinator för företag. Service Bus kan frikoppla program och tjänster. Service Bus erbjuder en tillförlitlig och säker plattform för asynkron överföring av data och tillstånd.

Data överförs mellan olika program och tjänster med *meddelanden*. Ett meddelande är i binärformat och kan innehålla JSON, XML eller bara text. Mer information finns i [integrerings tjänster](https://azure.com/integration).

Några vanliga scenarier för meddelanden är:

* *Meddelandetjänster*. Överför affärs data, till exempel försäljnings-eller inköps order, journaler eller lager förflyttningar.
* Frikopplade *program*. Förbättra tillförlitligheten och skalbarheten för program och tjänster. Klienten och tjänsten behöver inte vara online samtidigt.
* *Ämnen och prenumerationer*. Aktivera 1:*n* relationer mellan utgivare och prenumeranter.
* *Message-sessioner*. Implementera arbets flöden som kräver meddelande ordning eller meddelande avstängning.

## <a name="namespaces"></a>Namnområden

Ett namn område är en behållare för alla meddelande komponenter. Flera köer och ämnen kan finnas i ett enda namn område och namn områden fungerar ofta som program behållare.

## <a name="queues"></a>Köer

Meddelanden skickas till och tas emot från *köer*. Köer lagrar meddelanden tills det mottagande programmet är tillgängligt för att ta emot och bearbeta dem.

![Kö](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Meddelanden i köer ordnas och tidsstämplas vid ankomsten. När de har godkänts lagras meddelanden på ett säkert sätt i redundant lagring. Meddelanden levereras i *pull* -läge och levererar bara meddelanden när de begärs.

## <a name="topics"></a>Ämnen

Du kan också använda *ämnen* för att skicka och ta emot meddelanden. Medan en kö oftast används för kommunikation från punkt till punkt är ämnen användbara i scenarier med publicering/prenumeration.

![Ämne](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Ämnen kan ha flera oberoende prenumerationer. En prenumerant på ett ämne får en kopia av varje meddelande. Prenumerationer kallas entiteter. Prenumerationerna är kvar, men kan förfalla eller autota bort.

Du kanske inte vill att enskilda prenumerationer ska ta emot alla meddelanden som skickas till ett ämne. I så fall kan du använda *regler* och *filter* för att definiera villkor som utlöser valfria *åtgärder*. Du kan filtrera angivna meddelanden och ange eller ändra meddelande egenskaper. Mer information finns i [avsnittet filter och åtgärder](topic-filters.md).

## <a name="advanced-features"></a>Avancerade funktioner

Service Bus innehåller avancerade funktioner som gör att du kan lösa mer komplexa meddelande problem. I följande avsnitt beskrivs flera av de här funktionerna.

### <a name="message-sessions"></a>Meddelandesessioner

Om du vill skapa en första in-och utgångs-(FIFO) garanti i Service Bus använder du sessioner. Message-sessioner möjliggör gemensam och ordnad hantering av icke-bundna sekvenser av relaterade meddelanden. Mer information finns i [meddelande sessioner: först in, först ut (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Vidarebefordrar

Funktionen vidarebefordran länkar en kö eller en prenumeration till en annan kö eller ämne. De måste ingå i samma namnrymd. Med automatisk vidarebefordran tar Service Bus automatiskt bort meddelanden från en kö eller prenumeration och placerar dem i en annan kö eller ett annat ämne. Mer information finns i [länkning Service Bus entiteter med vidarebefordran](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Kö för obeställbara meddelanden

Service Bus stöder en kö för obeställbara meddelanden (DLQ). En DLQ innehåller meddelanden som inte kan levereras till någon mottagare. Den innehåller meddelanden som inte kan bearbetas. Med Service Bus kan du ta bort meddelanden från DLQ och granska dem. Mer information finns i [Översikt över Service Bus köer för obeställbara meddelanden](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Schemalagd leverans

Du kan skicka meddelanden till en kö eller ett ämne för fördröjd bearbetning. Du kan schemalägga att ett jobb ska bli tillgängligt för bearbetning av ett system vid en viss tidpunkt. Mer information finns i [schemalagda meddelanden](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Skjut upp meddelanden

En kö-eller prenumerations klient kan skjuta upp hämtningen av ett meddelande till ett senare tillfälle. Detta kan bero på särskilda omständigheter i programmet. Meddelandet finns kvar i kön eller prenumerationen, men det har tagits bort. Mer information finns i [meddelande avstängning](message-deferral.md).

### <a name="batching"></a>Batchbearbetning

Med batching på klient sidan kan en kö-eller ämnes klient fördröja att skicka ett meddelande under en viss tids period. Om klienten skickar ytterligare meddelanden under den här tiden överförs dessa meddelanden i en enskild batch. Mer information finns i [batching av klient sidan](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transaktioner

En transaktion grupper två eller flera åtgärder tillsammans i ett *körnings område*. Service Bus stöder gruppering av åtgärder mot en enskild meddelande enhet inom omfånget för en enskild transaktion. En Message-entitet kan vara en kö, ett ämne eller en prenumeration. Mer information finns i [Översikt över Service Bus transaktions bearbetning](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Filtrering och åtgärder

Prenumeranter kan definiera vilka meddelanden som de vill ta emot från ett ämne. Dessa meddelanden anges i form av en eller flera namngivna prenumerations regler. För varje matchande regel villkor skapar prenumerationen en kopia av meddelandet, som kan vara olika kommenterade för varje matchande regel. Mer information finns i [avsnittet filter och åtgärder](topic-filters.md).

### <a name="autodelete-on-idle"></a>Ta bort vid inaktivitet

Vid borttagning vid inaktivitet kan du ange ett inaktivt intervall efter vilket en kö tas bort automatiskt. Minimilängden är 5 minuter. Mer information finns i [egenskapen QueueDescription. AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Dubblettidentifiering

Ett fel kan orsaka att klienten tvekar om resultatet av en skicka-åtgärd. Dubblettidentifiering gör att avsändaren kan skicka samma meddelande igen. Ett annat alternativ är för kön eller ämnet att ignorera dubbletter av kopior. Mer information finns i [dubblettidentifiering](duplicate-detection.md).

### <a name="security-protocols"></a>Säkerhets protokoll
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus stöder säkerhetsprotokoll som [signaturer för delad åtkomst](service-bus-sas.md) (SAS), [rollbaserad åtkomstkontroll](authenticate-application.md) (RBAC) och [hanterade identiteter för Azure-resurser](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Geohaveriberedskap

När Azure-regioner eller data Center upplever drift stopp gör geo-katastrof återställning att data bearbetningen fortsätter att fungera i en annan region eller data Center. Mer information finns i [Azure Service Bus geo-Disaster Recovery](service-bus-geo-dr.md).

### <a name="security"></a>Säkerhet

Service Bus stöder standardprotokoll [AMQP 1.0](service-bus-amqp-overview.md) och [HTTP-REST](/rest/api/servicebus/).

## <a name="client-libraries"></a>Klientbibliotek

Service Bus stöder klient bibliotek för [.net](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master)och [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integrering

Service Bus är helt integrerad med följande Azure-tjänster:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med Service Bus-meddelanden, kan du läsa följande artiklar:

* Information om hur du jämför Azure Messaging-tjänster finns i [jämförelse av tjänster](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Försök att använda snabb starterna för [.net](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)eller [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Information om hur du hanterar Service Bus-resurser finns i [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Mer information om standard-och Premium nivåerna och deras priser finns i [Service Bus priser](https://azure.microsoft.com/pricing/details/service-bus/).
* Läs mer om prestanda och svars tider för Premium-nivån i [Premium-meddelanden](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
