---
title: Sök i Azure Cosmos DB data
titleSuffix: Azure Cognitive Search
description: Crawla en Azure Cosmos DB data källa och mata in data i ett fullständigt text sökbart index i Azure Kognitiv sökning. Indexerare automatiserar data inmatning för valda data källor som Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 616e5dc5ac6416d2efe1d9338b99c2b400fe572a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977122"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Så här indexerar du Cosmos DB data med hjälp av en indexerare i Azure Kognitiv sökning 

> [!IMPORTANT] 
> SQL API är allmänt tillgängligt.
> MongoDB API, Gremlin API och API för Cassandra support finns för närvarande i offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Du kan begära åtkomst till för hands versionerna genom att fylla i [det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). [REST API version 2019-05-06-Preview](search-api-preview.md) innehåller för hands versions funktioner. Det finns för närvarande begränsad Portal support och inget stöd för .NET SDK.

> [!WARNING]
> Endast Cosmos DB samlingar med en [indexerings princip](https://docs.microsoft.com/azure/cosmos-db/index-policy) inställd på [konsekvent](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) stöds av Azure kognitiv sökning. Indexering av samlingar med en Lazy-indexerings princip rekommenderas inte och kan leda till att data saknas. Samlingar med inaktiverade index stöds inte.

Den här artikeln visar hur du konfigurerar en Azure Cosmos DB [indexerare](search-indexer-overview.md) för att extrahera innehåll och göra den sökbar i Azure kognitiv sökning. Det här arbets flödet skapar ett Azure Kognitiv sökning-index och läser in det med befintlig text som extraherats från Azure Cosmos DB. 

Eftersom terminologin kan vara förvirrande, är det värt att notera att [Azure Cosmos DB indexering](https://docs.microsoft.com/azure/cosmos-db/index-overview) och [Azure kognitiv sökning indexering](search-what-is-an-index.md) är distinkta åtgärder, unika för varje tjänst. Innan du startar Azure Kognitiv sökning indexering måste Azure Cosmos DB-databasen redan finnas och innehålla data.

Cosmos DB indexeraren i Azure Kognitiv sökning kan crawla [Azure Cosmos DB objekt](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) som nås via olika protokoll.

+ För [SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), som är allmänt tillgängligt, kan du använda [portalen](#cosmos-indexer-portal), [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)eller [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet).

+ För [MongoDB-API (för hands version)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) och [Gremlin-API (för hands version)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)kan du använda antingen [portalen](#cosmos-indexer-portal) eller [REST API version 2019-05-06-Preview](search-api-preview.md) på ett [skapa indexerare (rest)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) -anrop för att skapa indexeraren.

+ För [API för Cassandra (för hands version)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction)kan du bara använda [REST API version 2019-05-06 – för hands version](search-api-preview.md) på ett [rest-anrop (Create Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) ).


> [!Note]
> Du kan omvandla en röst på användar rösten för [tabell-API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) om du vill se hur den stöds i Azure kognitiv sökning.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Använda portalen

> [!Note]
> Portalen stöder för närvarande SQL API och MongoDB-API (för hands version).

Den enklaste metoden för att indexera Azure Cosmos DB objekt är att använda en guide i [Azure Portal](https://portal.azure.com/). Genom att sampla data och läsa metadata i behållaren kan guiden [**Importera data**](search-import-data-portal.md) i Azure kognitiv sökning skapa ett standard index, mappa käll fält till mål index fält och läsa in indexet i en enda åtgärd. Beroende på storleken och komplexiteten hos källdata kan du ha ett fullständigt texts öknings index på några minuter.

Vi rekommenderar att du använder samma region eller plats för både Azure Kognitiv sökning och Azure Cosmos DB för kortare svars tid och för att undvika bandbredds kostnader.

### <a name="1---prepare-source-data"></a>1 – Förbered källdata

Du bör ha ett Cosmos DB-konto, en Azure Cosmos DB-databas som är mappad till SQL API, MongoDB-API (för hands version) eller Gremlin-API (för hands version) och innehåll i-databasen.

Kontrol lera att Cosmos DB-databasen innehåller data. [Guiden Importera data](search-import-data-portal.md) läser metadata och utför data sampling för att härleda ett index schema, men läser även in data från Cosmos dB. Om data saknas slutar guiden med det här felet "fel vid identifiering av index schema från data Källa: det gick inte att skapa ett prototyp index eftersom data källan" emptycollection "returnerade inga data".

### <a name="2---start-import-data-wizard"></a>2-Starta guiden Importera data

Du kan [starta guiden](search-import-data-portal.md) från kommando fältet på sidan Azure kognitiv sökning tjänst eller om du ansluter till Cosmos DB SQL API kan du klicka på **Lägg till Azure-kognitiv sökning** i avsnittet **Inställningar** i Cosmos DB kontots vänstra navigerings fönster.

   ![Importera data kommando i portalen](./media/search-import-data-portal/import-data-cmd2.png "Starta guiden Importera data")

### <a name="3---set-the-data-source"></a>3 – ange data källan

På sidan **data källa** måste källan vara **Cosmos DB**, med följande specifikationer:

+ **Namn** är namnet på objektet i data källan. När du har skapat kan du välja den för andra arbets belastningar.

+ **Cosmos DB konto** ska vara den primära eller sekundära anslutnings strängen från Cosmos DB, med en `AccountEndpoint` och en `AccountKey`. För MongoDB-samlingar lägger du till **ApiKind = MongoDB** i slutet av anslutnings strängen och avgränsar den från anslutnings strängen med ett semikolon. För Gremlin-API och API för Cassandra använder du instruktionerna för [REST API](#cosmosdb-indexer-rest).

+ **Databasen** är en befintlig databas från kontot. 

+ **Samlingen** är en behållare med dokument. Det måste finnas dokument för att importen ska lyckas. 

+ **Frågan** kan vara tom om du vill ha alla dokument, annars kan du mata in en fråga som väljer en delmängd av dokumentet. **Frågan** är bara tillgänglig för SQL-API: et.

   ![Cosmos DB definition av data Källa](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB definition av data Källa")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 – hoppa över sidan "utöka innehåll" i guiden

Att lägga till kognitiva färdigheter (eller anrikning) är inte ett import krav. Om du inte har ett speciellt behov av att [lägga till AI-berikning](cognitive-search-concept-intro.md) till din indexerings pipeline, bör du hoppa över det här steget.

Om du vill hoppa över steget klickar du på blå knappar längst ned på sidan för "Nästa" och "hoppa över".

### <a name="5---set-index-attributes"></a>5-Ange indexattribut

På sidan **index** visas en lista med fält med data typen och en serie kryss rutor för att ställa in indexattribut. Guiden kan generera en fält lista baserat på metadata och genom att sampla data källan. 

Du kan massredigera attribut genom att klicka på kryss rutan överst i en Attribute-kolumn. Välj **hämtnings** **Bart och sökbart** för varje fält som ska returneras till en klient-app och för full texts öknings bearbetning. Du märker att heltal inte är full text eller fuzzy sökbar (tal utvärderas orda Grant och ofta är användbara i filter).

Granska beskrivningen av [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) och [språk analys](https://docs.microsoft.com/rest/api/searchservice/language-support) verktyg för mer information. 

Ägna en stund åt att granska dina val. När du har kört guiden skapas fysiska data strukturer och du kan inte redigera dessa fält utan att släppa och återskapa alla objekt.

   ![Cosmos DB index definition](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB index definition")

### <a name="6---create-indexer"></a>6 – skapa indexerare

Fullständigt angivet skapar guiden tre distinkta objekt i din Sök tjänst. Ett data käll objekt och ett index objekt sparas som namngivna resurser i Azure Kognitiv sökning-tjänsten. Det sista steget skapar ett indexerare-objekt. Om du namnger indexeraren kan den finnas som en fristående resurs, som du kan schemalägga och hantera oberoende av indexet och datakällobjektet, vilket skapas i samma guide ordning.

Om du inte är bekant med indexerare är en *indexerare* en resurs i Azure kognitiv sökning som crawlar en extern data källa för sökbart innehåll. Utdata från guiden **Importera data** är en indexerare som crawlar din Cosmos db data källa, extraherar sökbart innehåll och importerar det till ett index på Azure kognitiv sökning.

Följande skärm bild visar standard indexerings konfigurationen. Du kan växla till **en gång** om du vill köra indexeraren en gång. Klicka på **Skicka** för att köra guiden och skapa alla objekt. Indexeringen börjar omedelbart.

   ![Cosmos DB indexerings definition](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB indexerings definition")

Du kan övervaka data import på Portal sidorna. Förlopps meddelanden indikerar indexerings status och hur många dokument som laddas upp. 

När indexeringen är klar kan du använda [Sök Utforskaren](search-explorer.md) för att fråga ditt index.

> [!NOTE]
> Om du inte ser de data du förväntar dig kan du behöva ange fler attribut för fler fält. Ta bort indexet och indexeraren som du nyss skapade och gå igenom guiden igen och ändra dina val för indexattribut i steg 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Använda REST-API:er

Du kan använda REST API för att indexera Azure Cosmos DB data, efter ett arbets flöde med tre delar som är gemensamt för alla indexerare i Azure Kognitiv sökning: skapa en data källa, skapa ett index, skapa en indexerare. Data extrahering från Cosmos DB sker när du skickar en begäran om att skapa indexerare. När den här begäran har avslut ATS har du ett index som kan anropas. 

> [!NOTE]
> För att indexera data från Cosmos DB Gremlin API eller Cosmos DB API för Cassandra måste du först begära åtkomst till gated-förhands visningen genom att fylla i [det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). När din begäran har bearbetats kommer du att få anvisningar om hur du använder [REST API version 2019-05-06 – för hands version](search-api-preview.md) för att skapa data källan.

Tidigare i den här artikeln beskrivs att [Azure Cosmos DB indexering](https://docs.microsoft.com/azure/cosmos-db/index-overview) och [Azure kognitiv sökning indexerings](search-what-is-an-index.md) indexering är distinkta åtgärder. För Cosmos DB indexering indexeras som standard alla dokument automatiskt utom med API för Cassandra. Om du inaktiverar automatisk indexering kan dokument endast nås via sina egna länkar eller via frågor med hjälp av dokument-ID: t. Azure Kognitiv sökning indexering kräver att Cosmos DB automatisk indexering aktive ras i samlingen som ska indexeras av Azure-Kognitiv sökning. När du registrerar dig för för hands versionen av Cosmos DB API för Cassandra indexeraren får du instruktioner om hur du konfigurerar Cosmos DB indexering.

> [!WARNING]
> Azure Cosmos DB är nästa generation av DocumentDB. Tidigare med API version **2017-11-11** kan du använda syntaxen för `documentdb`. Detta innebar att du kan ange typ av data källa som `cosmosdb` eller `documentdb`. Från och med API version **2019-05-06** har båda Azure kognitiv sökning-API: erna och portalen endast stöd för `cosmosdb`-syntaxen enligt anvisningarna i den här artikeln. Det innebär att data käll typen måste `cosmosdb` om du vill ansluta till en Cosmos DB-slutpunkt.

### <a name="1---assemble-inputs-for-the-request"></a>1 – Sammanställ indata för begäran

För varje begäran måste du ange tjänst namnet och administratörs nyckeln för Azure Kognitiv sökning (i POST huvudet) och lagrings kontots namn och nyckel för Blob Storage. Du kan använda [Postman](search-get-started-postman.md) för att skicka HTTP-förfrågningar till Azure kognitiv sökning.

Kopiera följande fyra värden till anteckningar så att du kan klistra in dem i en förfrågan:

+ Namn på Azure Kognitiv sökning-tjänst
+ Administratörs nyckel för Azure Kognitiv sökning
+ Cosmos DB anslutnings sträng

Du kan hitta dessa värden i portalen:

1. På Portal sidorna för Azure Kognitiv sökning kopierar du URL: en för Sök tjänsten från översikts sidan.

2. I det vänstra navigerings fönstret klickar du på **nycklar** och kopierar antingen den primära eller sekundära nyckeln (de är motsvarande).

3. Växla till Portal sidorna för ditt Cosmos-lagrings konto. I det vänstra navigerings fönstret, under **Inställningar**, klickar du på **nycklar**. Den här sidan innehåller en URI, två uppsättningar anslutnings strängar och två uppsättningar nycklar. Kopiera en av anslutnings strängarna till anteckningar.

### <a name="2---create-a-data-source"></a>2 – Skapa en data Källa

En **data källa** anger de data som ska indexeras, autentiseringsuppgifter och principer för att identifiera ändringar i data (till exempel ändrade eller borttagna dokument i din samling). Data källan definieras som en oberoende resurs så att den kan användas av flera indexerare.

Formulera en POST-begäran för att skapa en data Källa:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Bröd texten i begäran innehåller definitionen av data källan, som ska innehålla följande fält:

| Fält   | Beskrivning |
|---------|-------------|
| **Namn** | Krävs. Välj ett namn som ska representera ditt data käll objekt. |
|**typ**| Krävs. Måste vara `cosmosdb`. |
|**klientautentiseringsuppgifter** | Krävs. Måste vara en Cosmos DB anslutnings sträng.<br/>För SQL-samlingar är anslutnings strängar i följande format: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>För MongoDB-samlingar lägger du till **ApiKind = MongoDB** i anslutnings strängen:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Registrera dig för för [hands versionen](https://aka.ms/azure-cognitive-search/indexer-preview) av Gremlin-diagram och Cassandra-tabeller för att få åtkomst till för hands versionen och information om hur du formaterar autentiseringsuppgifterna.<br/><br/>Undvik port nummer i slut punkts-URL: en. Om du inkluderar port numret kan Azure Kognitiv sökning inte indexera Azure Cosmos DB-databasen.|
| **fönster** | Innehåller följande element: <br/>**namn**: obligatoriskt. Ange ID för den databas samling som ska indexeras.<br/>**fråga**: valfritt. Du kan ange en fråga för att förenkla ett godtyckligt JSON-dokument till ett plant schema som Azure Kognitiv sökning kan indexera.<br/>För API: et för MongoDB, Gremlin API och API för Cassandra, stöds inte frågor. |
| **dataChangeDetectionPolicy** | Rekommenderas. Se avsnittet [Indexera ändrade dokument](#DataChangeDetectionPolicy) .|
|**dataDeletionDetectionPolicy** | Valfri. Se avsnittet [Indexera borttagna dokument](#DataDeletionDetectionPolicy) .|

### <a name="using-queries-to-shape-indexed-data"></a>Använda frågor för att forma indexerade data
Du kan ange en SQL-fråga för att förenkla inkapslade egenskaper eller matriser, egenskaper för projekt-JSON och filtrera data som ska indexeras. 

> [!WARNING]
> Anpassade frågor stöds inte för **MONGODB API**, **Gremlin API**och **API för Cassandra**: `container.query` parameter måste vara null eller utelämnad. Om du behöver använda en anpassad fråga kan du berätta för oss om [användar rösten](https://feedback.azure.com/forums/263029-azure-search).

Exempel dokument:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Filter fråga:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Förenklings fråga:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projektions fråga:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Fråga för mat ris förenkling:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 – skapa ett mål Sök index 

[Skapa ett Azure kognitiv sökning-index](/rest/api/searchservice/create-index) om du inte redan har en. I följande exempel skapas ett index med ett ID och beskrivnings fält:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Se till att schemat för mål indexet är kompatibelt med schemat för käll-JSON-dokumenten eller utdata för din anpassade fråga-projektion.

> [!NOTE]
> För partitionerade samlingar är standard dokument nyckeln Azure Cosmos DB `_rid` egenskap, som Azure Kognitiv sökning byter namn automatiskt till `rid` eftersom fält namn inte får börja med ett under streck. Dessutom innehåller Azure Cosmos DB `_rid` värden ogiltiga tecken i Azure Kognitiv sökning-nycklar. Av den anledningen är `_rid` värdena base64-kodade.
> 
> För MongoDB-samlingar byter Azure Kognitiv sökning automatiskt namn på egenskapen `_id` till `id`.  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mappning mellan JSON-datatyper och Azure Kognitiv sökning data typer
| Data typen JSON | Kompatibla fält typer för mål index |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Siffror som ser ut som heltal |Edm.Int32, Edm.Int64, Edm.String |
| Siffror som ser ut som svävande punkter |Edm.Double, Edm.String |
| Sträng |Edm.String |
| Matriser med primitiva typer, till exempel ["a", "b", "c"] |Collection(Edm.String) |
| Strängar som ser ut som datum |Edm.DateTimeOffset, Edm.String |
| Subjson-objekt, till exempel {"typ": "Point", "koordinater": [Long, Lat]} |Edm.GeographyPoint |
| Andra JSON-objekt |Gäller inte |

### <a name="4---configure-and-run-the-indexer"></a>4 – Konfigurera och kör indexeraren

När indexet och data källan har skapats är du redo att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Indexeraren körs varannan timme (schema intervall anges till "PT2H"). Om du vill köra en indexerare var 30: e minut anger du intervallet till "PT30M". Det kortaste intervall som stöds är 5 minuter. Schemat är valfritt – om det utelämnas körs en indexerare bara en gång när den skapas. Du kan dock köra en indexerare på begäran när du vill.   

Mer information om API för att skapa index finns i [skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Mer information om hur du definierar indexerare scheman finns i [så här schemalägger du indexerare för Azure kognitiv sökning](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Använda .NET

Den allmänt tillgängliga .NET SDK: n har fullständig paritet med allmänt tillgängliga REST API. Vi rekommenderar att du läser avsnittet tidigare REST API för att lära dig begrepp, arbets flöden och krav. Du kan sedan se följande dokumentation om .NET API-referens för att implementera en JSON-indexerare i förvaltad kod.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexera ändrade dokument

Syftet med en princip för data ändrings identifiering är att effektivt identifiera ändrade data objekt. För närvarande är den enda princip som stöds [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) med hjälp av egenskapen `_ts` (timestamp) som tillhandahålls av Azure Cosmos DB, som anges på följande sätt:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Att använda den här principen rekommenderas starkt för att säkerställa prestanda för bästa indexerare. 

Om du använder en anpassad fråga ser du till att egenskapen `_ts` projiceras av frågan.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Stegvisa framsteg och anpassade frågor

Stegvis förlopp under indexeringen säkerställer att om körningen av Indexer avbryts vid tillfälliga haverier eller körnings tids gräns, kan indexeraren Hämta var den slutade nästa gång den körs, i stället för att behöva indexera om hela samlingen från grunden. Detta är särskilt viktigt när du indexerar stora samlingar. 

Om du vill aktivera stegvisa förloppet när du använder en anpassad fråga måste du se till att frågan beställer resultatet i kolumnen `_ts`. Detta möjliggör regelbunden kontroll som pekar på att Azure Kognitiv sökning använder för att ge stegvisa framsteg i närvaro av problem.   

I vissa fall, även om frågan innehåller en `ORDER BY [collection alias]._ts`-sats, kanske inte Azure Kognitiv sökning härleda att frågan beställs av `_ts`. Du kan berätta för Azure Kognitiv sökning att resultatet sorteras med hjälp av egenskapen `assumeOrderByHighWaterMarkColumn` konfiguration. Om du vill ange det här tipset skapar eller uppdaterar du indexeraren enligt följande: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexera borttagna dokument

När rader tas bort från samlingen vill du normalt även ta bort dessa rader från Sök indexet. Syftet med en identifierings princip för data borttagning är att effektivt identifiera borttagna data objekt. För närvarande är den enda princip som stöds `Soft Delete` principen (borttagning är markerad med en flagga för viss sortering), som anges på följande sätt:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Om du använder en anpassad fråga bör du kontrol lera att den-egenskap som `softDeleteColumnName` har refererats till av frågan.

I följande exempel skapas en data källa med en princip för mjuk borttagning:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Nästa steg

Gratulerar! Du har lärt dig hur du integrerar Azure Cosmos DB med Azure Kognitiv sökning med hjälp av en indexerare.

* Mer information om Azure Cosmos DB finns på sidan för [Azure Cosmos DB tjänsten](https://azure.microsoft.com/services/cosmos-db/).
* Mer information om Azure Kognitiv sökning finns på [sidan Sök tjänst](https://azure.microsoft.com/services/search/).
