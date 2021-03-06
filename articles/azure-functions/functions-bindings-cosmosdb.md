---
title: Azure Cosmos DB-bindningar för Functions 1.x
description: Förstå hur du använder Azure Cosmos DB-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: bae027fc5a3b6ce7b4246c403841fa529b8884cb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925946"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Azure Cosmos DB-bindningar för Azure Functions 1.x

> [!div class="op_single_selector" title1="Välj den version av Azure Functions runtime som du använder: "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

Den här artikeln förklarar hur du arbetar med [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) bindningar i Azure Functions. Azure Functions stöder utlösa, indata och utdata-bindningar för Azure Cosmos DB.

> [!NOTE]
> Den här artikeln är för Azure Functions 1.x. Information om hur du använder dessa bindningar i functions 2. x och högre finns [Azure Cosmos DB bindningar för Azure Functions 2. x](functions-bindings-cosmosdb-v2.md).
>
>Den här bindningen hette ursprungligen DocumentDB. I funktioner version 1.x, endast utlösaren har bytt namn Cosmos DB. den indatabindning och utdatabindning NuGet-paketet kan du behålla DocumentDB-namn.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Azure Cosmos DB-bindningar stöds endast för användning med SQL-API:n. För alla andra Azure Cosmos DB-API: er ska du komma åt databasen från din funktion med hjälp av den statiska klienten för ditt API, inklusive [Azure Cosmos DB s API för MongoDB](../cosmos-db/mongodb-introduction.md), [API för Cassandra](../cosmos-db/cassandra-introduction.md), [Gremlin API](../cosmos-db/graph-introduction.md)och [tabell-API](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Azure Cosmos DB-bindningar för Functions version 1.x finns i den [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) NuGet-paketet, version 1.x. Källkoden för bindningarna finns i den [azure-webjobs-sdk-tilläggen](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) GitHub-lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Utlösare

Azure Cosmos DB-utlösaren använder den [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) att lyssna efter infogningar och uppdateringar på flera partitioner. Ändringsflöde publicerar infogningar och uppdateringar, inte borttagningar.

## <a name="trigger---example"></a>Utlösare - exempel

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som anropas när det finns infogas eller uppdateras i den angivna databasen och samlingen.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en Cosmos DB-utlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster har ändrats.

Här är bindningsdata i den *function.json* fil:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är C#-skriptkoden:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Cosmos DB-utlösare bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver loggmeddelanden när Cosmos DB-poster har ändrats.

Här är bindningsdata i den *function.json* fil:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är JavaScript-kod:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Utlösare - attribut

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) attribut.

Attributets konstruktorn tar databasens namn och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [utlösare - konfigurationen](#trigger---configuration). Här är en `CosmosDBTrigger` attributet exemplet i signaturen för metoden:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Ett komplett exempel finns i [utlösare – C#-exempel](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

---

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `CosmosDBTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** || Måste anges till `cosmosDBTrigger`. |
|**riktning** || Måste anges till `in`. Den här parametern anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** || Variabelnamnet som används i Funktionskoden som representerar en lista över dokument med ändringar. |
|**connectionStringSetting**|**connectionStringSetting** | Namnet på en appinställning som innehåller anslutningssträngen som används för att ansluta till Azure Cosmos DB-kontot som övervakas. |
|**databaseName**|**databaseName**  | Namnet på Azure Cosmos DB-databasen med den samling som övervakas. |
|**collectionName** |**CollectionName** | Namnet på samlingen som övervakas. |
|**leaseConnectionStringSetting** | **leaseConnectionStringSetting** | (Valfritt) Namnet på en appinställning som innehåller anslutningssträngen till den tjänst som innehar lånet samlingen. När inte har angetts i `connectionStringSetting` värde som ska användas. Den här parametern anges automatiskt när bindningen skapas i portalen. Anslutningssträngen för lånsamlingen måste ha skrivbehörighet.|
|**leaseDatabaseName** |**leaseDatabaseName** | (Valfritt) Namnet på den databas som innehåller den samling som används för att lagra lån. När inte har värdet för den `databaseName` inställningen används. Den här parametern anges automatiskt när bindningen skapas i portalen. |
|**leaseCollectionName** | **leaseCollectionName** | (Valfritt) Namnet på den samling som används för att lagra lån. När inte har värdet `leases` används. |
|**createLeaseCollectionIfNotExists** | **createLeaseCollectionIfNotExists** | (Valfritt) När värdet `true`, lånsamlingen skapas automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| (Valfritt) Definierar mängd Begäransenheter för att tilldela när lånsamlingen skapas. Den här inställningen är endast används när `createLeaseCollectionIfNotExists` är inställd på `true`. Den här parametern anges automatiskt när bindningen skapas med hjälp av portalen.
|**leaseCollectionPrefix**| **leaseCollectionPrefix**| (Valfritt) När värdet den lägger till ett prefix till lån som skapats i lånsamling för den här funktionen så effektivt att två separata Azure-Functions för att dela samma lånsamling med hjälp av olika prefix.
|**feedPollDelay**| **feedPollDelay**| (Valfritt) När mängd, den definierar, i millisekunder, fördröjning mellan att avsöka en partition för nya ändringar på flödet, är när alla aktuella ändringar tömda. Standardvärdet är 5 000 (5 sekunder).
|**leaseAcquireInterval**| **leaseAcquireInterval**| (Valfritt) När värdet definierar den, i millisekunder, intervallet sätta igång en uppgift att beräkna om partitioner fördelas jämnt mellan kända ha instanser. Standardvärdet är 13000 (13 sekunder).
|**leaseExpirationInterval**| **leaseExpirationInterval**| (Valfritt) När värdet definierar den, i millisekunder, intervallet som fattas lånet om ett lån som representerar en partition. Om lånet inte förnyas inom intervallet, det gör att det upphör att gälla och ägarskap för partitionen flyttas till en annan instans. Standardvärdet är 60000 (60 sekunder).
|**leaseRenewInterval**| **leaseRenewInterval**| (Valfritt) När värdet definierar den, i millisekunder, förnyelseintervallet för alla lån för partitioner som för tillfället hålls av en instans. Standardvärdet är 17000 (17 sekunder).
|**checkpointFrequency**| **checkpointFrequency**| (Valfritt) När värdet definierar den, i millisekunder, hur många lån kontrollpunkter. Standardvärdet är alltid efter varje funktions anrop.
|**maxItemsPerInvocation**| **maxItemsPerInvocation**| (Valfritt) När värdet anpassar det den maximala mängden objekt tas emot per funktionsanrop.
|**startFromBeginning**| **StartFromBeginning**| Valfritt När det är inställt, instrueras utlösaren att börja läsa ändringar från början av samlingens historik i stället för den aktuella tiden. Detta fungerar bara första gången utlösaren startar, precis som vid efterföljande körningar, så är kontroll punkterna redan lagrade. Om du anger detta till `true` när det redan har skapats lån har ingen påverkan.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning

Utlösaren kräver en andra samling som används för att lagra _lån_ över partitionerna. Både den samling som övervakas och den samling som innehåller lån måste vara tillgänglig för utlösaren ska fungera.

>[!IMPORTANT]
> Om flera funktioner har konfigurerats för att använda en Cosmos DB-utlösare för samma samling, var och en av funktionerna bör använda en dedikerad lånsamling eller ange en annan `LeaseCollectionPrefix` för varje funktion. Annars kan aktiveras endast en av funktionerna. Information om prefixet som finns i den [konfigurationsavsnittet](#trigger---configuration).

Utlösaren anger inte om ett dokument har uppdateras eller infogas, den bara innehåller själva dokumentet. Om du vill hantera uppdateringar och infogningar på olika sätt, kan du göra det genom att implementera värdefält för infogning eller uppdaterar.

## <a name="input"></a>Indata

Azure Cosmos DB-indatabindning använder SQL-API för att hämta en eller flera Azure Cosmos DB-dokument och skickar dem till Indataparametern för funktionen. Dokument-ID eller frågeparametrar kan fastställas baserat på utlösare som anropar funktionen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Det här avsnittet innehåller följande exempel:

* [Köutlösare, leta upp ID från JSON](#queue-trigger-look-up-id-from-json-c)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c)
* [HTTP-utlösare, leta upp ID från dirigera data](#http-trigger-look-up-id-from-route-data-c)
* [HTTP-utlösare, leta upp ID från dirigera data med SQL-fråga](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP utlösa får flera docs, med hjälp av SQL-fråga](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP utlösa får flera docs, med hjälp av DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Exemplen finns en enkel `ToDoItem` typ:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köa utlösare, slå upp ID från JSON

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av ett kömeddelande som innehåller ett JSON-objekt. Kö-utlösare Parsar JSON till ett objekt med namnet `ToDoItemLookup`, som innehåller det ID du söker. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder dirigera data för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Hoppa över inkommande exempel](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP-utlösare, slå upp ID från routa data med SqlQuery

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder dirigera data för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Hoppa över inkommande exempel](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista över dokument. Funktionen utlöses av en HTTP-begäran. Frågan har angetts i den `SqlQuery` attributet egenskapen.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Hoppa över inkommande exempel](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>HTTP utlösa får flera docs, med hjälp av DocumentClient (C#)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som hämtar en lista över dokument. Funktionen utlöses av en HTTP-begäran. Koden använder en `DocumentClient` instans som tillhandahålls av Azure Cosmos DB-bindning för att läsa en lista över dokument. Den `DocumentClient` instans kan också användas för skrivåtgärder.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Det här avsnittet innehåller följande exempel:

* [Köutlösare, leta upp ID från sträng](#queue-trigger-look-up-id-from-string-c-script)
* [Kö utlösaren, hämta flera, med hjälp av SQL-fråga](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP-utlösare, leta upp ID från dirigera data](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP utlösa får flera docs, med hjälp av SQL-fråga](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP utlösa får flera docs, med hjälp av DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

HTTP-utlösaren exempel referera till en enkel `ToDoItem` typ:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Köa utlösare, leta upp ID från sträng

I följande exempel visas en Cosmos DB-indatabindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser ett enskilt dokument och uppdaterar dokumentets textvärde.

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
        inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köa utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en Azure Cosmos DB-indatabindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga som använder en kö-utlösare för att anpassa Frågeparametrar.

Kö-utlösare innehåller en parameter `departmentId`. Ett kömeddelande för `{ "departmentId" : "Finance" }` returneras alla poster för ekonomiavdelningen.

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

Här är den *function.json* fil:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Här är C#-skriptkoden:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder dirigera data för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

Här är den *function.json* fil:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Här är C#-skriptkoden:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP-utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar en lista över dokument. Funktionen utlöses av en HTTP-begäran. Frågan har angetts i den `SqlQuery` attributet egenskapen.

Här är den *function.json* fil:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Här är C#-skriptkoden:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP-utlösare, hämta flera dokument med DocumentClient

I följande exempel visas en [C#-skriptfunktion](functions-reference-csharp.md) som hämtar en lista över dokument. Funktionen utlöses av en HTTP-begäran. Koden använder en `DocumentClient` instans som tillhandahålls av Azure Cosmos DB-bindning för att läsa en lista över dokument. Den `DocumentClient` instans kan också användas för skrivåtgärder.

Här är den *function.json* fil:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Här är C#-skriptkoden:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Det här avsnittet innehåller följande exempel:

* [Köutlösare, leta upp ID från JSON](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP-utlösare, leta upp ID från frågesträng](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP-utlösare, leta upp ID från dirigera data](#http-trigger-look-up-id-from-route-data-javascript)
* [Kö utlösaren, hämta flera, med hjälp av SQL-fråga](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)


<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Köa utlösare, slå upp ID från JSON

I följande exempel visas en Cosmos DB-indatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser ett enskilt dokument och uppdaterar dokumentets textvärde.

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP-utlösare, leta upp ID från frågesträng

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

Här är den *function.json* fil:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Här är JavaScript-kod:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP-utlösare, slå upp ID från flödes data

I följande exempel visas en [JavaScript-funktion](functions-reference-node.md) som hämtar ett enskilt dokument. Funktionen utlöses av en HTTP-begäran som använder en frågesträng för att ange ID för att leta upp. ID: T är används för att hämta en `ToDoItem` dokument från den angivna databasen och samlingen.

Här är den *function.json* fil:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Här är JavaScript-kod:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Köa utlösare, hämta flera dokument med SqlQuery

I följande exempel visas en Azure Cosmos DB-indatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen hämtar flera dokument som anges av en SQL-fråga som använder en kö-utlösare för att anpassa Frågeparametrar.

Kö-utlösare innehåller en parameter `departmentId`. Ett kömeddelande för `{ "departmentId" : "Finance" }` returneras alla poster för ekonomiavdelningen.

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

Den [configuration](#input---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

---

## <a name="input---attributes"></a>Indata - attribut

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribut.

Attributets konstruktorn tar databasens namn och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [följande konfigurationsavsnittet](#input---configuration).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

---

## <a name="input---configuration"></a>Indata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `DocumentDB` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**     || Måste anges till `documentdb`.        |
|**riktning**     || Måste anges till `in`.         |
|**Namn**     || Namnet på bindningsparametern som representerar dokumentet i funktionen.  |
|**databaseName** |**databaseName** |Den databas som innehåller dokumentet.        |
|**collectionName** |**CollectionName** | Namnet på den samling som innehåller dokumentet. |
|**ID**    | **Id** | ID för dokumentet som ska hämtas. Den här egenskapen stöder [bindning uttryck](./functions-bindings-expressions-patterns.md). Inte ange både den **id** och **SQL-fråga** egenskaper. Om du inte anger någon hämtas hela samlingen. |
|**sqlQuery**  |**SQL-fråga**  | En Azure Cosmos DB SQL-fråga som används för att hämta flera dokument. Egenskapen stöder runtime-bindningar, som i följande exempel: `SELECT * FROM c where c.departmentId = {departmentId}`. Inte ange både den **id** och **SQL-fråga** egenskaper. Om du inte anger någon hämtas hela samlingen.|
|**anslutning**     |**connectionStringSetting**|Namnet på den appinställning som innehåller din Azure Cosmos DB-anslutningssträng.        |
|**partitionKey**|**partitionKey**|Anger partitionsnyckelvärdet för sökningen. Omfatta bindande parametrar.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Indata - användning

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

När funktionen avslutas sparas alla ändringar som gjorts i indatamängds dokumentet via namngivna indataparametrar automatiskt.

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

När funktionen avslutas sparas alla ändringar som gjorts i indatamängds dokumentet via namngivna indataparametrar automatiskt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Uppdateringar görs inte automatiskt när funktionen avslutas. Använd i stället `context.bindings.<documentName>In` och `context.bindings.<documentName>Out` att göra uppdateringar. Se [exemplet på indatamängden](#input).

---

## <a name="output"></a>Resultat

I Azure Cosmos DB-utdatabindning kan skriva du ett nytt dokument till en Azure Cosmos DB-databas med hjälp av SQL-API.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Det här avsnittet innehåller följande exempel:

* Köutlösare, Skriv ett dokument
* Köa utlösare, skriv dokument med `IAsyncCollector`

Exemplen finns en enkel `ToDoItem` typ:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Köutlösare, Skriv ett dokument

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till ett dokument till en databas med hjälp av data som anges i meddelandet från Queue storage.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köutlösare, Skriv docs med IAsyncCollector

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som lägger till en samling dokument till en databas med hjälp av data i ett kömeddelande JSON.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Det här avsnittet innehåller följande exempel:

* Köutlösare, Skriv ett dokument
* Köa utlösare, skriv dokument med `IAsyncCollector`

### <a name="queue-trigger-write-one-doc"></a>Köutlösare, Skriv ett dokument

I följande exempel visar ett Azure Cosmos DB-utdatabindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en indatabindning i kö för en kö som tar emot JSON i följande format:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funktionen skapar Azure Cosmos DB-dokument i följande format för varje post:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är C#-skriptkoden:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Köutlösare, Skriv docs med IAsyncCollector

Om du vill skapa flera dokument som du kan binda till `ICollector<T>` eller `IAsyncCollector<T>` där `T` är en av typerna som stöds.

Det här exemplet refererar till en enkel `ToDoItem` typ:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Här är filen function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Här är C#-skriptkoden:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visar ett Azure Cosmos DB-utdatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en indatabindning i kö för en kö som tar emot JSON i följande format:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funktionen skapar Azure Cosmos DB-dokument i följande format för varje post:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Här är bindningsdata i den *function.json* fil:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

---

## <a name="output---attributes"></a>Utdata - attribut

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) attribut.

Attributets konstruktorn tar databasens namn och samlingens namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [utdata - konfigurationen](#output---configuration). Här är en `DocumentDB` attributet exemplet i signaturen för metoden:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Ett fullständigt exempel finns i [utdata](#output).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

---

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `DocumentDB` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ**     || Måste anges till `documentdb`.        |
|**riktning**     || Måste anges till `out`.         |
|**Namn**     || Namnet på bindningsparametern som representerar dokumentet i funktionen.  |
|**databaseName** | **databaseName**|Den databas som innehåller den samling där dokumentet skapas.     |
|**collectionName** |**CollectionName**  | Namnet på den samling där dokumentet skapas. |
|**createIfNotExists**  |**createIfNotExists**    | Ett booleskt värde som anger om samlingen skapas när den inte finns. Standardvärdet är *FALSKT* eftersom nya samlingar skapas med reserverat dataflöde, vilket har kostnad effekter. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**partitionKey** |När `CreateIfNotExists` är sant, definierar Nyckelsökväg partition för samlingen som har skapats.|
|**collectionThroughput**|**collectionThroughput**| När `CreateIfNotExists` är sant, definierar den [dataflöde](../cosmos-db/set-throughput.md) för samlingen som har skapats.|
|**anslutning**    |**connectionStringSetting** |Namnet på den appinställning som innehåller din Azure Cosmos DB-anslutningssträng.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

När du skriver till Utdataparametern i din funktion skapas ett dokument i databasen. Det här dokumentet har en automatiskt genererad GUID som dokument-ID Du kan ange ID för konfigurationsobjektdokument för utdatadokumentet genom att ange den `id` -egenskapen i JSON-objekt skickades till output-parameter.

> [!Note]
> När du anger ID för ett befintligt dokument, hämtar den skrivs över av det nya utdatadokumentet.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| CosmosDB | [Felkoder för CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om serverlös databehandling med Cosmos DB-databas](../cosmos-db/serverless-computing-database.md)
* [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
