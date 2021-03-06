---
title: 'Snabb start: bygga en webbapp med Azure API för mongo DB och Java SDK – Azure Cosmos DB'
description: Presenterar ett Java-kodexempel som du kan använda för att ansluta till och ställa frågor men hjälp av Azure Cosmos DB:s API för MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: bfded407790d01d65feec0b6e069c23739064e97
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756924"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Snabb start: skapa en konsol app med Java och MongoDB-API: et i Azure Cosmos DB

> [!div class="op_single_selector"]
> * [NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

I den här snabb starten använder du Azure Cosmos DB API för mongo DB och Java SDK för att skapa en konsol-webbapp. Med Azure Cosmos DB kan du snabbt skapa och fråga dokument, nyckel/värde-och diagram databaser, som alla har nytta av den globala distributionen och den horisontella skalnings kapaciteten i kärnan av Cosmos DB.

Den här snabbstarten visar hur du skapar ett Cosmos-konto med [Azure Cosmos DB-API för MongoDB](mongodb-introduction.md). Sedan kommer du att skapa och distribuera en konsolapp som är byggd med hjälp av [MongoDB Java-drivrutinen](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Krav

Innan du kan köra det här exemplet måste du uppfylla följande krav:
* [Installera JDK för Azure och Azure Stack JDK version 8](https://aka.ms/azure-jdks)
* Maven (Kör `apt-get install maven` om du inte har Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Lägga till en samling

Ge den nya databasen namnet **db** och den nya samlingen namnet **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Öppna sedan koden i det redigeringsprogram du föredrar. 

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

Följande kodfragment är alla hämtade från filen Program.java.

* DocumentClient initieras.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* En ny databas och samling skapas.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Några dokument infogas med `MongoCollection.insertOne`

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Några frågor körs med `MongoCollection.find`

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen.

1. Från kontot väljer du **Snabbstart**, väljer **Java**och kopierar sedan anslutnings strängen till Urklipp.

2. Öppna filen `Program.java` och ersätt argumentet för konstruktorn MongoClientURI med anslutningssträngen. Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Kör konsolappen

1. Kör `mvn package` i en terminal för att installera de npm-moduler som krävs

2. Kör `mvn exec:java -D exec.mainClass=GetStarted.Program` i en terminal för att starta Java-programmet.

Nu kan du använda [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) till att ställa frågor mot, ändra och arbeta med dessa nya data.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Cosmos-konto, skapar en samling och kör en konsolapp. Du kan nu importera ytterligare data till din Cosmos-databas.

> [!div class="nextstepaction"]
> [Importera MondoDB-data till Azure Cosmos DB](mongodb-migrate.md)
