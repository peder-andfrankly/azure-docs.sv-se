---
title: Migrera icke-partitionerade Azure Cosmos-behållare till partitionerade behållare
description: Lär dig hur du migrerar alla befintliga icke-partitionerade behållare till partitionerade behållare.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 1afca920a8146ce5501900bcc9e36bdebcccca09
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706077"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrera icke-partitionerade behållare till partitionerade behållare

Azure Cosmos DB har stöd för att skapa behållare utan en partitionsnyckel. För närvarande kan du skapa icke-partitionerade behållare med hjälp av Azure CLI och Azure Cosmos DB SDK: er (.net, Java, NodeJs) som har en version som är mindre än eller lika med 2. x. Du kan inte skapa icke-partitionerade behållare med hjälp av Azure Portal. Sådana icke-partitionerade behållare är dock inte elastiska och har en fast lagrings kapacitet på 10 GB och data flödes gränsen på 10 000 RU/s.

Icke-partitionerade behållare är äldre och du bör migrera befintliga icke-partitionerade behållare till partitionerade behållare för att skala lagring och data flöde. Azure Cosmos DB innehåller en systemdefinierad mekanism för att migrera icke-partitionerade behållare till partitionerade behållare. I det här dokumentet beskrivs hur alla befintliga icke-partitionerade behållare automatiskt migreras till partitionerade behållare. Du kan bara dra nytta av funktionen för automatisk migrering om du använder v3-versionen av SDK: er på alla språk.

> [!NOTE]
> För närvarande kan du inte migrera Azure Cosmos DB MongoDB-och Gremlin API-konton med hjälp av stegen som beskrivs i det här dokumentet.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrera behållare med den systemdefinierade partitionsnyckel

För att stödja migreringen tillhandahåller Azure Cosmos DB en systemdefinierad partitionsnyckel med namnet `/_partitionkey` på alla behållare som inte har någon partitionsnyckel. Du kan inte ändra partitionens nyckel definition efter att behållarna har migrerats. Definitionen av en behållare som migreras till en partitionerad behållare är till exempel följande:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

När behållaren har migrerats kan du skapa dokument genom att fylla i egenskapen `_partitionKey` tillsammans med de andra egenskaperna för dokumentet. Egenskapen `_partitionKey` representerar partitionens partitionsnyckel.

Det är viktigt att välja rätt partitionsnyckel för att använda det etablerade data flödet optimalt. Mer information finns i [så här väljer du en partitionsnyckel](partitioning-overview.md) .

> [!NOTE]
> Du kan bara dra nytta av systemdefinierad partitionsnyckel om du använder den senaste/v3-versionen av SDK: er på alla språk.

I följande exempel visas en exempel kod för att skapa ett dokument med den systemdefinierade partitionsnyckel och läsa dokumentet:

**JSON-representation av dokumentet**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Det fullständiga exemplet finns i .net- [exempel][1] GitHub-lagringsplatsen.
                      
## <a name="migrate-the-documents"></a>Migrera dokumenten

Medan behållar definitionen har förbättrats med en partitionsnyckel, migreras inte dokumenten i behållaren automatiskt. Vilket innebär att egenskapen system partition Key `/_partitionKey` sökväg inte läggs till automatiskt i de befintliga dokumenten. Du måste partitionera om de befintliga dokumenten genom att läsa dokumenten som har skapats utan en partitionsnyckel och skriva tillbaka dem igen med `_partitionKey`-egenskapen i dokumenten.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Åtkomst till dokument som saknar partitionsnyckel

Program har åtkomst till befintliga dokument som saknar partitionsnyckel med hjälp av den särskilda system egenskapen som kallas "PartitionKey. None", det här är värdet för de icke-migrerade dokumenten. Du kan använda den här egenskapen i alla CRUD-och Query-åtgärder. I följande exempel visas ett exempel på hur du kan läsa ett enda dokument från NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Det fullständiga exemplet på hur du partitionerar om dokumenten finns i [.net-exempel][1] GitHub-lagringsplatsen. 

## <a name="compatibility-with-sdks"></a>Kompatibilitet med SDK: er

En äldre version av Azure Cosmos DB SDK: er, till exempel v2. x. x och v1. x. x, har inte stöd för den systemdefinierade partitionerings nyckel egenskapen. När du läser behållar definitionen från en äldre SDK innehåller den inte heller någon partitionsnyckel och dessa behållare fungerar precis som tidigare. Program som har skapats med den äldre versionen av SDK: er fortsätter att fungera med icke-partitionerade som är utan några ändringar. 

Om en migrerad behållare används av den senaste/v3-versionen av SDK och du börjar fylla i den systemdefinierade partitionsnyckel i de nya dokumenten, kan du inte komma åt (läsa, uppdatera, ta bort, fråga) sådana dokument från de äldre SDK: erna längre.

## <a name="next-steps"></a>Nästa steg

* [Partitionering i Azure Cosmos DB](partitioning-overview.md)
* [Enheter för programbegäran i Azure Cosmos DB](request-units.md)
* [Etablera dataflöde på containrar och databaser](set-throughput.md)
* [Arbeta med Azure Cosmos-konto](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration