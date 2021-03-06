---
title: Diagnostisera och Felsök problem när du använder Azure Functions utlösare för Cosmos DB
description: Vanliga problem, lösningar och diagnostiska steg när du använder Azure Functions utlösare för Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e3ff86770ec0337c9a4a11b30c6d88e8365bfa24
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064109"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnostisera och Felsök problem när du använder Azure Functions utlösare för Cosmos DB

Den här artikeln beskriver vanliga problem, lösningar och diagnostiska steg när du använder [Azure Functions utlösare för Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Beroenden

Azure Functions utlösare och bindningar för Cosmos DB beror på tilläggs paketen över bas Azure Functions Runtime. Behåll alltid dessa paket uppdaterade eftersom de kan innehålla korrigeringar och nya funktioner som kan åtgärda eventuella problem som kan uppstå:

* Azure Functions v2 finns i [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Azure Functions v1 finns i [Microsoft. Azure. WebJobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Den här artikeln kommer alltid att referera till Azure Functions v2 När körnings miljön anges, såvida det inte uttryckligen anges.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Använda Azure Cosmos DB SDK oberoende

De viktigaste funktionerna i tilläggs paketet är att tillhandahålla stöd för Azure Functions utlösare och bindningar för Cosmos DB. Den innehåller också [Azure Cosmos dB .NET SDK](sql-api-sdk-dotnet-core.md), vilket är användbart om du vill interagera med Azure Cosmos DB program mässigt utan att använda utlösare och bindningar.

Om du vill använda Azure Cosmos DB SDK ser du till att du inte lägger till ett annat NuGet-paket referens i projektet. I stället kan **SDK-referensen lösas via Azure Functions tilläggs paketet**. Använda Azure Cosmos DB SDK separat från utlösaren och bindningarna

Om du dessutom skapar en egen instans av [Azure Cosmos DB SDK-klienten](./sql-api-sdk-dotnet-core.md)manuellt bör du följa mönstret för att bara ha en instans av klienten [med hjälp av en metod för singleton-mönster](../azure-functions/manage-connections.md#documentclient-code-example-c). Den här processen kommer att undvika potentiella problem med socketen i dina åtgärder.

## <a name="common-scenarios-and-workarounds"></a>Vanliga scenarier och lösningar

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure Function Miss lyckas med fel meddelande samlingen finns inte

Azure Function Miss lyckas med fel meddelandet "det finns ingen käll samlings samling, namn (i databasens databas namn) eller collection2-namnet (i databasen Databas2-Name). Båda samlingarna måste finnas innan lyssnaren startar. Ange "CreateLeaseCollectionIfNotExists" till "true" om du vill skapa en låne samling automatiskt

Det innebär att antingen en eller båda av de Azure Cosmos-behållare som krävs för att utlösaren ska fungera, inte finns eller inte kan komma åt Azure-funktionen. **Själva felet anger vilken Azure Cosmos-databas och behållare som är den Utlös Ande som du behöver** utifrån din konfiguration.

1. Verifiera `ConnectionStringSetting`-attributet och att det **refererar till en inställning som finns i Azure-Funktionsapp**. Värdet för det här attributet får inte vara själva anslutnings strängen, utan namnet på konfigurations inställningen.
2. Kontrol lera att `databaseName` och `collectionName` finns i ditt Azure Cosmos-konto. Om du använder automatisk ersättning (med `%settingName%` mönster) ser du till att namnet på inställningen finns i Azure-Funktionsapp.
3. Om du inte anger en `LeaseCollectionName/leaseCollectionName`är standardvärdet "lån". Kontrol lera att behållaren finns. Du kan också ställa in `CreateLeaseCollectionIfNotExists`-attributet i utlösaren så att `true` automatiskt skapar det.
4. Verifiera ditt [Azure Cosmos-kontos brand Väggs konfiguration](how-to-configure-firewall.md) för att se att den inte blockerar Azure-funktionen.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure Function kan inte starta med "delad data flödes samling ska ha en partitionsnyckel"

Föregående versioner av Azure Cosmos DB-tillägget har inte stöd för användning av en behållare för lån som skapades i en [delad data flödes databas](./set-throughput.md#set-throughput-on-a-database). Lös problemet genom att uppdatera tillägget [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) för att hämta den senaste versionen.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure Function kan inte starta med "Lease-samlingen, om partitionerad", måste ha en partitionsnyckel som är lika med ID. "

Det här felet innebär att din aktuella Lease container är partitionerad, men sökvägen till partitionsnyckel inte `/id`. För att lösa det här problemet måste du återskapa behållaren för lån med `/id` som partitionsnyckel.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Du ser ett "värde får inte vara null. Parameter namn: o "i Azure Functions loggar när du försöker köra utlösaren

Det här problemet uppstår om du använder Azure Portal och du försöker välja knappen **Kör** på skärmen när du inspekterar en Azure-funktion som använder utlösaren. Utlösaren kräver inte att du väljer Kör för att starta. den startas automatiskt när Azure-funktionen distribueras. Om du vill kontrol lera logg strömmen i Azure-funktionen på Azure Portal, går du bara till den övervakade behållaren och infogar nya objekt. då visas automatiskt den utlösare som körs.

### <a name="my-changes-take-too-long-to-be-received"></a>Mina ändringar tar för lång tid att tas emot

Det här scenariot kan ha flera orsaker och alla bör kontrol leras:

1. Distribueras din Azure-funktion i samma region som ditt Azure Cosmos-konto? För optimal nätverksfördröjning bör både Azure-funktionen och ditt Azure Cosmos-konto vara i samma Azure-region.
2. Sker ändringarna i din Azure Cosmos-container kontinuerligt eller sporadiskt?
Om de sker sporadiskt kan det uppstå en fördröjning mellan det att ändringarna sparas och Azure-funktionen hämtar dem. Detta beror på att när utlösaren söker efter ändringar i Azure Cosmos-containern och inte hittar någon som väntar på att läsas hamnar den i viloläge en viss tid (fem sekunder som standard) innan den söker efter nya ändringar (för att undvika hög RU-förbrukning). Du kan ändra vilotiden med inställningen `FeedPollDelay/feedPollDelay` i [konfigurationen](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) för utlösaren (värdet förväntas anges i millisekunder).
3. Din Azure Cosmos-behållare kan vara [begränsad till hastighet](./request-units.md).
4. Du kan använda `PreferredLocations`-attributet i utlösaren för att ange en kommaavgränsad lista med Azure-regioner för att definiera en anpassad prioriterad anslutnings ordning.

### <a name="some-changes-are-missing-in-my-trigger"></a>Vissa ändringar saknas i min utlösare

Om du upptäcker att några av ändringarna som hände i din Azure Cosmos-behållare inte hämtas av Azure-funktionen, finns det ett första undersöknings steg som måste utföras.

När din Azure-funktion tar emot ändringarna, bearbetar den ofta dem och kan också skicka resultatet till ett annat mål. När du undersöker saknade ändringar, se till att du **mäter vilka ändringar som tas emot vid** inmatnings punkten (när Azure-funktionen startar), inte på målet.

Om vissa ändringar saknas på målet kan detta betyda att vissa fel inträffar under Azure Function-körningen när ändringarna har tagits emot.

I det här scenariot är det bästa sättet att lägga till `try/catch` block i koden och inom de slingor som kan bearbeta ändringarna, för att upptäcka eventuella problem med en viss delmängd av objekt och hantera dem efter behov (skicka dem till en annan lagrings plats för ytterligare analys eller försök igen). 

> [!NOTE]
> Azure Functions-utlösaren för Cosmos DB kommer som standard inte att försöka utföra en grupp ändringar igen om ett ohanterat undantag uppstod under kod körningen. Det innebär att det inte går att bearbeta ändringarna på grund av att det inte gick att bearbeta dem.

Om du upptäcker att vissa ändringar inte tagits emot alls av utlösaren, är det vanligaste scenariot att **en annan Azure-funktion körs**. Det kan vara en annan Azure Function som distribuerats i Azure eller en Azure-funktion som körs lokalt på en utvecklares dator som har **exakt samma konfiguration** (samma övervakade och lånade behållare) och den här Azure-funktionen stjäl en delmängd av de ändringar du förväntar dig att Azure-funktionen ska bearbeta.

Scenariot kan också verifieras om du vet hur många Azure Funktionsapp-instanser som du kör. Om du inspekterar din containers-behållare och räknar antalet låne objekt i, bör de distinkta värdena för den `Owner` egenskapen i dem vara lika med antalet instanser av Funktionsapp. Om det finns fler ägare än kända instanser av Azure-funktionsappen innebär det att det är de extra ägarna som ”stjäl” ändringarna.

Ett enkelt sätt att lösa den här situationen är att använda en `LeaseCollectionPrefix/leaseCollectionPrefix` till din funktion med ett nytt/annat värde eller, även testa med en ny container container.

### <a name="need-to-restart-and-re-process-all-the-items-in-my-container-from-the-beginning"></a>Måste starta om och bearbeta alla objekt i min behållare från början 
För att bearbeta alla objekt i en behållare på nytt från början:
1. Stoppa din Azure-funktion om den körs. 
1. Ta bort dokumenten i låne samlingen (eller ta bort och återskapa låne samlingen så att den är tom)
1. Ange attributet [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) CosmosDBTrigger i din funktion till true. 
1. Starta om Azure-funktionen. Nu kommer den att läsa och bearbeta alla ändringar från början. 

Om du anger [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) till True kommer Azure-funktionen att börja läsa ändringar från början av samlingens historik i stället för den aktuella tiden. Detta fungerar bara när det inte redan skapas lån (d.v.s. dokument i insamlingen lån). Om du anger den här egenskapen till sant, har redan skapade lån ingen påverkan. När en funktion stoppas och startas om i det här scenariot börjar den att läsa från den senaste kontroll punkten, enligt definitionen i samlingen lån. Följ stegen ovan 1-4 om du vill göra en ny process från början.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Bindning kan bara göras med IReadOnlyList\<Document > eller JArray

Det här felet uppstår om ditt Azure Functions-projekt (eller ett refererat projekt) innehåller en manuell NuGet-referens till Azure Cosmos DB SDK med en annan version än den som anges av [Azure Functions Cosmos DB-tillägget](./troubleshoot-changefeed-functions.md#dependencies).

Lös problemet genom att ta bort den manuella NuGet-referensen som lades till och låt Azure Cosmos DB SDK-referensen matcha genom Azure Functions Cosmos DB tilläggs paketet.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Ändra avsöknings intervall för Azure Function för att identifiera ändringar

Som förklarat tidigare för [mina ändringar tar](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)det för lång tid att ta emot, Azure-funktionen försätts i vilo läge under en konfigurerbar tids period (5 sekunder som standard) innan du söker efter nya ändringar (för att undvika hög ru-förbrukning). Du kan ändra vilotiden med inställningen `FeedPollDelay/feedPollDelay` i [konfigurationen](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) för utlösaren (värdet förväntas anges i millisekunder).

## <a name="next-steps"></a>Nästa steg

* [Aktivera övervakning för Azure Functions](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB fel sökning av .NET SDK](./troubleshoot-dot-net-sdk.md)
