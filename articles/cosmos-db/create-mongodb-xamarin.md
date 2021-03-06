---
title: Skapa en Xamarin.Forms-app med .NET och Azure Cosmos DB:s API för MongoDB
description: Presenterar ett Xamarin-kodexempel som du kan använda för att ansluta till och ställa frågor till Azure Cosmos DB:s API för MongoDB
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: masoucou
ms.openlocfilehash: a0612ea06c71b2a93e6fb76f5d82516cfbad8657
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860330"
---
# <a name="quickstart-build-a-xamarinforms-app-with-net-sdk-and-azure-cosmos-dbs-api-for-mongodb"></a>Snabbstart: Skapa en Xamarin.Forms-app med .NET SDK och Azure Cosmos DB:s API för MongoDB

> [!div class="op_single_selector"]
> * [NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB.

I den här snabbstarten visas hur du kan skapa ett [Cosmos-konto som konfigurerats med Azure Cosmos DB:s API för MongoDB](mongodb-introduction.md), en dokumentdatabas och en samling med hjälp av Azure Portal. Du skapar sedan en att göra-app medXamarin.Forms med hjälp av [MongoDB .NET-drivrutinen](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Nödvändiga förutsättningar för att köra exempelappen

Om du vill köra exemplet behöver du [Visual Studio](https://www.visualstudio.com/downloads/) eller [Visual Studio för Mac](https://visualstudio.microsoft.com/vs/mac/) och ett giltigt Azure CosmosDB-konto.

Om du inte redan har Visual Studio, hämta [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) med den **mobil utveckling med .NET** arbetsbelastning som installeras med installationsprogrammet.

Om du föredrar att arbeta på en Mac laddar du ned [Visual Studio för Mac](https://visualstudio.microsoft.com/vs/mac/) och kör installationsprogrammet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Exemplet som beskrivs i den här artikeln är kompatibelt med MongoDB.Driver version 2.6.1.

## <a name="clone-the-sample-app"></a>Klona exempelappen

Ladda först ned eller klona exempelappen från GitHub. Den implementerar en att göra-app med MongoDB:s dokumentlagringsmodell.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Om du inte vill använda git kan du [ladda ned projektet som en ZIP-fil](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string).

Följande kodfragment är alla hämtade från klassen `MongoService`, som finns på följande sökväg: src/TaskList.Core/Services/MongoService.cs.

* Initiera Mongo-klienten.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Hämta en referens till databasen och samlingen. MongoDB .NET-SDK skapar automatiskt både databasen och samlingen om de inte redan finns.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Hämta alla dokument som en lista.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Fråga efter specifika dokument.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Skapa en uppgift och infoga den i samlingen.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Uppdatera en uppgift i en samling.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Ta bort en uppgift från en samling.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen.

1. Öppna ditt Azure Cosmos DB-konto i [Azure-portalen](https://portal.azure.com/), klicka på **Anslutningssträng** och därefter på **Läs- och skrivnycklar**. Du använder kopiera-knapparna på höger sida av skärmen för att kopiera den primära anslutningssträngen i nästa steg.

2. Öppna filen **APIKeys.cs** i katalogen **Helpers** katalogen i projektet **TaskList.Core**.

3. Kopiera värdet för **primär anslutningssträng** från portalen (med kopieringsknappen) och gör det till värdet för fältet **ConnectionString** i filen **APIKeys.cs**.

Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB.

## <a name="run-the-app"></a>Kör appen

### <a name="visual-studio-2019"></a>Visual Studio 2019

1. I Visual Studio högerklickar du på varje projekt i **Solution Explorer** och därefter på **Hantera NuGet-paket**.
2. Klicka på **Återställ alla NuGet-paket**.
3. Högerklicka på **TaskList.Android** och välj **Ange som startprojekt**.
4. Tryck på F5 för att starta felsökning av programmet.
5. Om du vill köra på iOS ansluts datorn först till en Mac (här är [instruktionerna](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio) för hur du gör detta).
6. Högerklicka på projektet **TaskList.iOS** och välj **Ange som startprojekt**.
7. Klicka på F5 för att starta felsökning av programmet.

### <a name="visual-studio-for-mac"></a>Visual Studio för Mac

1. Välj TaskList.iOS eller TaskList.Android i listrutan för plattformen, beroende på vilken plattform som du vill köra på.
2. Tryck på cmd + RETUR för att starta felsökningen av programmet.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB-konto och kör en Xamarin.Forms-app via API:t för MongoDB. Du kan nu importera ytterligare data till ditt Cosmos DB-konto.

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB konfigurerat med Azure Cosmos DB:s API för MongoDB](mongodb-migrate.md)
