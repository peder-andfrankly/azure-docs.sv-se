---
title: 'Snabb start: bygga en python-app med Azure Cosmos DB SQL API-konto'
description: Presenterar ett Python-kodexempel som du kan använda för att ansluta till och fråga Azure Cosmos DB SQL API:t
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 11/03/2019
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 82426c0093550864b421d7acc35780c4173895a8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824739"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Snabb start: bygga ett python-program med ett Azure Cosmos DB SQL API-konto

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Den här snabbstarten visar hur du skapar ett [SQL API](sql-api-introduction.md)-konto i Azure Cosmos DB, en dokumentdatabas och en container med Azure-portalen. Sedan skapar du och kör en konsolapp som är byggd med Python SDK för [SQL API](sql-api-sdk-python.md).

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och fråga dokument, nyckel/värde-, bred kolumn-och Graf-databaser. Alla dessa åtgärder drar nytta av distribution och skalning av Azure Cosmos DB.

I den här snabb starten används version 4 av [python SDK](https://pypi.org/project/azure-cosmos/#history).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python 3.6 +](https://www.python.org/downloads/), med `python` körbar fil tillgänglig i `PATH`.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Python-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Lägg till en behållare

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona vi en SQL API-app från GitHub, ange anslutningssträngen och köra appen.

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```cmd
    md "git-samples"
    ```
   Om du använder en bash-prompt bör du i stället använda följande kommando:

   ```bash
   mkdir "git-samples"
   ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen.

1. I [Azure Portal](https://portal.azure.com/), i ditt Azure Cosmos-konto, i det vänstra navigeringset väljer du **nycklar**. Använd kopieringsknapparna till höger för att kopiera **URI** och **Primärnyckel** till filen `cosmos_get_started.py` i nästa steg.

    ![Hämta en åtkomst nyckel och en URI i inställningarna för nycklar i Azure Portal](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. Öppna `cosmos_get_started.py`-filen i \git-samples\azure-Cosmos-DB-python-Getting-Started i Visual Studio Code.

3. Kopiera ditt **URI** -värde från portalen (med kopierings knappen) och gör det till värdet för **slut punkts** variabeln i ``cosmos_get_started.py``. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Kopiera sedan värdet för **primär nyckeln** från portalen och gör det till värdet för **nyckeln** i ``cosmos_get_started.py``. Du har nu uppdaterat appen med all information som behövs för kommunikation med Azure Cosmos DB. 

    `key = 'FILLME'`

5. Spara filen ``cosmos_get_started.py``.

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Lär dig mer om databas resurserna som skapats i kod eller gå vidare till [Uppdatera anslutnings strängen](#update-your-connection-string).

Följande kodavsnitt är alla hämtade från filen `cosmos_get_started.py`.

* CosmosClient initieras. Se till att uppdatera värdena "slut punkt" och "nyckel" enligt beskrivningen i avsnittet [Uppdatera anslutnings strängen](#update-your-connection-string) . 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* En ny databas skapas.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* En ny behållare skapas med 400 RU/s av [tillhandahållet data flöde](request-units.md). Vi väljer `lastName` som [partitionsnyckel](partitioning-overview.md#choose-partitionkey), som gör att vi kan göra effektiva frågor som filtrerar på den här egenskapen. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Vissa objekt läggs till i containern. Behållare är en samling objekt (JSON-dokument) som kan ha varierande schema. Hjälp metoder ```get_[name]_family_item``` returnerar representationer av en familj som lagras i Azure Cosmos DB som JSON-dokument.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Punkt läsningar (nyckel värdes uppslag) utförs med hjälp av metoden `read_item`. Vi skriver ut [avgiften](request-units.md) för varje åtgärd.
    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* En fråga utförs med SQL-frågesyntax. Eftersom vi använder partitionsnyckel för ```lastName``` i WHERE-satsen, kommer Azure Cosmos DB effektivt dirigera den här frågan till relevanta partitioner, vilket förbättrar prestandan.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Kör appen

1. Gå till Visual Studio Code och välj **Visa** > **Kommandopalett**. 

2. I kommandotolken anger du **Python: Välj tolk** och välj sedan den version av Python som ska användas.

    Sidfoten i Visual Studio Code uppdateras för att ange vilken tolk som valts. 

3. Välj **Visa** > **Integrerad terminal** så att den integrerade Visual Studio Code-terminalen öppnas.

4. I fönstret för den integrerade terminalen kontrollerar du att du befinner dig i mappen azure-cosmos-db-python-getting-started. Om du inte är det kör du följande kommando för att växla till exempelmappen. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Kör följande kommando för att installera paketet azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Om du får ett felmeddelande om nekad åtkomst när du försöker installera azure-cosmos måste du [köra VS Code som administratör](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Kör följande kommando för att köra exemplet och skapa och lagra nya dokument i Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Om du vill bekräfta att de nya objekten har skapats och sparats väljer du **Datautforskaren** > **AzureSampleFamilyDatabase** > **objekt**i Azure Portal. Visa de objekt som har skapats. Här är ett exempel på JSON-dokument för Andersen-serien:

```json
{
    "id": "Andersen-1569479288379",
    "lastName": "Andersen",
    "district": "WA5",
    "parents": [
        {
            "familyName": null,
            "firstName": "Thomas"
        },
        {
            "familyName": null,
            "firstName": "Mary Kay"
        }
    ],
    "children": null,
    "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
    },
    "registered": true,
    "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
    "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
    "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
    "_attachments": "attachments/",
    "_ts": 1569479288
}
```

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos-konto, skapar en behållare med hjälp av Datautforskaren och kör en app. Du kan nu importera ytterligare data till ditt Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB för användning med SQL API:t](import-data.md)


