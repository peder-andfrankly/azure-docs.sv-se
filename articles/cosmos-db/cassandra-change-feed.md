---
title: Ändra feed i Azure Cosmos DB API för Cassandra
description: Lär dig hur du använder ändrings flöden i Azure Cosmos DB API för Cassandra för att hämta de ändringar som gjorts i dina data.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694628"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Ändra feed i Azure Cosmos DB API för Cassandra

[Ändra feed](change-feed.md) -stöd i Azure Cosmos DB API för Cassandra är tillgängligt via frågesyntaxen i Cassandra-FRÅGESPRÅKET (CQL). Med dessa predikat villkor kan du fråga API: et för ändrings flöden. Program kan hämta de ändringar som gjorts i en tabell med hjälp av den primära nyckeln (kallas även partitionsnyckel) som krävs i CQL. Du kan sedan vidta ytterligare åtgärder baserat på resultaten. Ändringar av raderna i tabellen samlas i ordningen av deras ändrings tid och sorterings ordningen garanteras per partitionsnyckel.

I följande exempel visas hur du hämtar en ändrings-feed på alla rader i en API för Cassandra i tabellen med .NET. Predikatet COSMOS_CHANGEFEED_START_TIME () används direkt i CQL för att skicka frågor till objekt i ändrings flödet från en angiven start tid (i det här fallet aktuell datetime). Du kan hämta hela exemplet [här](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

I varje iteration fortsätter frågan vid de senaste punkt ändringarna lästes, med hjälp av växlings status. Vi kan se en kontinuerlig ström med nya ändringar i tabellen i utrymmet. Vi kommer att se ändringar av rader som infogas eller uppdateras. Att titta efter borttagnings åtgärder med hjälp av Change feed i API för Cassandra stöds inte för närvarande. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

Du kan lägga till primär nyckeln i frågan för att få ändringarna till en enskild rad efter primär nyckel. I följande exempel visas hur du spårar ändringar för raden där "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller när du använder ändra feed med API för Cassandra:

* Infogningar och uppdateringar stöds för närvarande. Borttagnings åtgärden stöds inte ännu. Som en lösning kan du lägga till en mjuk markör på rader som tas bort. Du kan till exempel lägga till ett fält i raden "borttaget" och ange det som "true".
* Den senaste uppdateringen är sparad som i SQL API-kärnan och mellanliggande uppdateringar till entiteten är inte tillgängliga.


## <a name="error-handling"></a>Felhantering

Följande felkoder och meddelanden stöds när du använder ändrings flöden i API för Cassandra:

* **Http-felkod 429** – när den ändrade feeden är begränsad returneras en tom sida.

## <a name="next-steps"></a>Nästa steg

* [Hantera Azure Cosmos DB API för Cassandra resurser med Azure Resource Manager mallar](manage-cassandra-with-resource-manager.md)
