---
title: Vanliga användnings fall och scenarier för Azure Cosmos DB
description: 'Lär dig mer om de fem främsta användnings fallen för Azure Cosmos DB: användare som har genererat innehåll, händelse loggning, katalog data, data för användar inställningar och Sakernas Internet (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: de2bc551547706fb820813e57996e77bf49148d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888934"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Vanliga Azure Cosmos DB användnings fall
Den här artikeln innehåller en översikt över flera vanliga användnings fall för Azure Cosmos DB.  Rekommendationerna i den här artikeln fungerar som en utgångs punkt när du utvecklar ditt program med Cosmos DB.   

När du har läst den här artikeln kan du svara på följande frågor: 

* Vad är vanliga användnings fall för Azure Cosmos DB?
* Vilka är fördelarna med att använda Azure Cosmos DB för butiks program?
* Vilka är fördelarna med att använda Azure Cosmos DB som data lager för Sakernas Internet-system (IoT)?
* Vilka är fördelarna med att använda Azure Cosmos DB för webb-och mobil program?

## <a name="introduction"></a>Introduktion
[Azure Cosmos DB](../cosmos-db/introduction.md) är Microsofts globalt distribuerade databas tjänst. Tjänsten är utformad för att göra det möjligt för kunder att elastiskt (och självständigt) skala data flöde och lagrings utrymme i valfritt antal geografiska regioner. Azure Cosmos DB är den första globalt distribuerade databas tjänsten på marknaden idag för att erbjuda omfattande [service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) som omfattar data flöde, svars tid, tillgänglighet och konsekvens. 

Azure Cosmos DB är en globalt distribuerad databas för flera modeller som används i en mängd olika program och användnings fall. Det är ett bra val för alla program utan [Server](https://azure.com/serverless) som behöver svars tider med låg ordning i millisekunder och måste skalas snabbt och globalt. Den har stöd för flera data modeller (nyckel värde, dokument, grafer och kolumner) och många API: er för data åtkomst, inklusive [Azure Cosmos DB s API för MongoDB](mongodb-introduction.md), [SQL API](documentdb-introduction.md), [Gremlin API](graph-introduction.md)och [tabeller API](table-introduction.md) internt och på ett utöknings Bart sätt. 

Följande är några attribut för Azure Cosmos DB som gör det lämpligt för program med höga prestanda med globala ambition.

* Azure Cosmos DB internt partitioner dina data för hög tillgänglighet och skalbarhet. Azure Cosmos DB erbjuder 99,99% garantier för tillgänglighet, data flöde, låg latens och konsekvens på alla konton i en region och alla konton med flera regioner med avslappnad konsekvens och 99,999% Läs tillgänglighet för alla databas konton i flera regioner.
* Azure Cosmos DB har SSD-lagring med en låg latens-svars tid på upp till millisekunder.
* Azure Cosmos DB stöd för konsekvens nivåer som till exempel återkommande, konsekvent prefix, session och begränsad föråldrad för att ge full flexibilitet och låg kostnad till prestanda förhållandet. Ingen databas tjänst ger lika mycket flexibilitet som Azure Cosmos DB på nivå konsekvens. 
* Azure Cosmos DB har en flexibel data vänlig pris modell som mätar lagring och data flöde oberoende av varandra.
* Med Azure Cosmos DBens reserverade data flödes modell kan du tänka på antalet läsningar och skrivningar i stället för processor/minne/IOPs för den underliggande maskin varan.
* Med Azure Cosmos DBs design kan du skala till enorma begär ande volymer i storleksordningen av biljon förfrågningar per dag.

Dessa attribut är fördelaktiga i webb-, mobil-, spel-och IoT-program som behöver låg svars tid och som behöver hantera enorma mängder läsningar och skrivningar.

## <a name="iot-and-telematics"></a>IoT och telematik
Användnings områden för IoT delar ofta några mönster i hur de matar in, bearbetar och lagrar data.  För det första måste dessa system mata in burst-överföring av data från enhets sensorer för olika språk. Därefter kan dessa system bearbeta och analysera strömmande data för att få insikter i real tid. Data arkiveras sedan till kall lagring för batch Analytics. Microsoft Azure erbjuder omfattande tjänster som kan användas för användnings fall i IoT, inklusive Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight och Power BI. 

![Referens arkitektur för Azure Cosmos DB IoT](./media/use-cases/iot.png)

Burst-data kan matas in av Azure Event Hubs eftersom det erbjuder data inmatning med hög data flöde med låg latens. Data som matas in och som behöver bearbetas för insikter i real tid kan delas in i Azure Stream Analytics för analys i real tid. Data kan läsas in i Azure Cosmos DB för adhoc-frågor. När data har lästs in i Azure Cosmos DB är data klara att frågas. Dessutom kan nya data och ändringar i befintliga data läsas vid ändrings flöde. Ändra feed är en beständigt, bifogad logg som lagrar ändringar i Cosmos-behållare i sekventiell ordning. Alla data eller bara ändringar av data i Azure Cosmos DB kan användas som referens data som en del av analys i real tid. Dessutom kan data förfinas och bearbetas genom att du ansluter Azure Cosmos DB data till HDInsight för gris-, Hive-eller Map/minska-jobb.  Förfinade data läses sedan tillbaka till Azure Cosmos DB för rapportering.   

Ett exempel på en IoT-lösning som använder Azure Cosmos DB, EventHubs och Storm finns i [databasen HDInsight-Storm-exempel på GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Mer information om Azure-erbjudanden för IoT finns i [skapa Sakernas Internet](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Detalj handel och marknadsföring
Azure Cosmos DB används i stor utsträckning i Microsofts egna e-handelsplattformar som kör Windows Store och XBox Live. Den används också i detalj handels branschen för lagring av katalog data och för händelse källor i order bearbetnings pipeliner.

Scenarier för katalog data användning innebär att lagra och fråga en uppsättning attribut för entiteter som personer, platser och produkter. Några exempel på katalog data är användar konton, produkt kataloger, register över IoT-enheter och strukturbaserade system. Attribut för dessa data kan variera och kan ändras med tiden för att passa program kraven.

Överväg ett exempel på en produkt katalog för en bil delar leverantör. Varje del kan ha sina egna attribut utöver de gemensamma attribut som delas av alla delar. Dessutom kan attribut för en speciell del ändra följande år när en ny modell släpps. Azure Cosmos DB stöder flexibla scheman och hierarkiska data och lämpar sig därför väl för att lagra produkt katalog data.

![Referens arkitektur för Azure Cosmos DB Retail Catalog](./media/use-cases/product-catalog.png)

Azure Cosmos DB används ofta för händelse källor till Power Event driven arkitekturer med dess funktion för att [ändra feed](change-feed.md) . Ändrings flödet ger underordnade mikrotjänster möjlighet till tillförlitlig och stegvis läsning av infogningar och uppdateringar (till exempel order händelser) som görs till en Azure Cosmos DB. Den här funktionen kan användas för att tillhandahålla en beständig händelse lagring som en meddelande koordinator för tillstånds ändrings händelser och bearbetning av arbets flöde för enhets ordnings bearbetning mellan många mikrotjänster (som kan implementeras som [Server lös Azure Functions](https://azure.com/serverless)).

![Azure Cosmos DB ordning för pipeline för pipeliniska referenser](./media/use-cases/event-sourcing.png)

Dessutom kan data som lagras i Azure Cosmos DB integreras med HDInsight för Big data Analytics via Apache Spark-jobb. Mer information om Spark-anslutaren för Azure Cosmos DB finns i [köra ett Spark-jobb med Cosmos DB och HDInsight](spark-connector.md).

## <a name="gaming"></a>Spel
Databas nivån är en viktig komponent i spel program. Moderna spel utför grafisk bearbetning på mobil-/konsol klienter, men förlitar sig på molnet för att leverera anpassat och personligt innehåll som spel statistik, social media integration och ranknings listor med hög poäng. Spel kräver ofta en svars tid på en millisekund för läsningar och skrivningar för att ge en engagerande spel upplevelse. En spel databas måste vara snabb och kunna hantera enorma toppar i begär ande frekvenser under nya spel lanseringar och funktions uppdateringar.

Azure Cosmos DB används av spel som inblandning [: ingen man är jord](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) av [Nästa spel](https://www.nextgames.com/)och [Halo 5: vårdnadshavare](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB ger följande fördelar för spel utvecklare:

* Azure Cosmos DB tillåter att prestanda skalas upp eller ned elastiskt. Detta gör att spel kan hantera uppdatering av profiler och statistik från dussin tals till miljon tals samtidiga spelare genom att göra ett enda API-anrop.
* Azure Cosmos DB har stöd för läsningar och skrivningar i millisekunder för att undvika lags under spelspelet.
* Med hjälp av automatisk indexering i Azure Cosmos DB kan du filtrera mot flera olika egenskaper i real tid, till exempel hitta spelare utifrån deras interna spelare-ID eller deras GameCenter, Facebook, Google ID eller fråga baserat på spelarens medlemskap i en Guild. Detta är möjligt utan att skapa komplexa indexerings-eller horisontell partitionering-infrastrukturer.
* Sociala funktioner, inklusive chatt meddelanden i spelet, spelare guild-medlemskap, utmaningar som slutförs, ranknings listor och sociala grafer är enklare att implementera med ett flexibelt schema.
* Azure Cosmos DB som en hanterad plattform som en tjänst (PaaS) krävde minimalt konfigurations-och hanterings arbete för att möjliggöra snabb iteration och minska tiden till marknaden.

![Referens arkitektur för Azure Cosmos DB spel](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Webb- och mobilprogram
Azure Cosmos DB används ofta i webb program och mobilappar, och passar utmärkt för att utforma sociala interaktioner, integrera med tjänster från tredje part och för att skapa anpassade upplevelser. Cosmos DB SDK: er kan användas för att bygga rika iOS-och Android-program med hjälp av det populära [Xamarin-ramverket](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociala program
Ett vanligt användnings fall för Azure Cosmos DB är att lagra och fråga User Generated Content (UGC) för webb-, mobil-och sociala medie program. Några exempel på UGC är chatt-sessioner, tweets, blogg inlägg, klassificeringar och kommentarer. UGC i appar för sociala medier är ofta en blandning av kostnads fria formulär text, egenskaper, taggar och relationer som inte begränsas av stel struktur. Innehåll, till exempel chattar, kommentarer och inlägg kan lagras i Cosmos DB utan omvandlingar eller komplexa objektet till relationella mappning lager.  Data egenskaper kan läggas till eller ändras enkelt för att matcha krav när utvecklare itererar program koden, vilket främjar snabb utveckling.  

Program som integreras med sociala nätverk från tredje part måste reagera på att ändra scheman från dessa nätverk. Allteftersom data automatiskt indexeras som standard i Cosmos DB kan data efter frågas när som helst. De här programmen har därför flexibiliteten att hämta projektioner per respektive behov.

Många av de sociala programmen körs i global skala och kan uppvisa oförutsägbara användnings mönster. Flexibilitet vid skalning av data lagringen är viktig eftersom program lagret skalas för att matcha användnings behovet.  Du kan skala ut genom att lägga till ytterligare datapartitioner under ett Cosmos DB konto.  Dessutom kan du också skapa ytterligare Cosmos DB konton över flera regioner. Information om tillgänglighet för Cosmos DB service regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).

![Referens arkitektur för Azure Cosmos DB Web App](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personanpassning
Nuförtiden, moderna program levereras med komplexa vyer och upplevelser. Dessa är vanligt vis dynamiska, catering till användar preferenser eller stämnings-och märkes behov. Därför måste program kunna hämta anpassade inställningar effektivt för att snabbt återge GRÄNSSNITTs element och upplevelser. 

JSON, ett format som stöds av Cosmos DB, är ett effektivt format som representerar GRÄNSSNITTets layoutinformation eftersom det inte bara är lätt att tolka, men det kan också enkelt tolkas av Java Script. Cosmos DB ger justerbara konsekvens nivåer som tillåter snabba läsningar med låg latens skrivningar. Därför är det ett effektivt sätt att lagra data i gränssnittet, inklusive anpassade inställningar som JSON-dokument i Cosmos DB, för att hämta data över hela kabeln.

![Referens arkitektur för Azure Cosmos DB Web App](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Nästa steg

* Kom igång med Azure Cosmos DB genom att följa våra [snabb starter](create-sql-api-dotnet.md), som vägleder dig genom att skapa ett konto och komma igång med Cosmos dB.

* Om du vill läsa mer om kunder som använder Azure Cosmos DB kan du gå till sidan med [fallstudier för kunder](https://azure.microsoft.com/case-studies/?service=cosmos-db) .
