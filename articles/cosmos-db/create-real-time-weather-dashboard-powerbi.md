---
title: Skapa en instrument panel i real tid med hjälp av Azure Cosmos DB, Azure Analysis Services och Power BI
description: Lär dig hur du skapar en Live väder instrument panel i Power BI att använda Azure Cosmos DB och Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376598"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Skapa en instrument panel i real tid med hjälp av Azure Cosmos DB och Power BI

I den här artikeln beskrivs de steg som krävs för att skapa en Live väder instrument panel i Power BI att använda Azure Cosmos DB och Azure Analysis Services. Instrument panelen för Power BI visar diagram för att visa information i real tid om temperatur-och Rainfall i en region.

## <a name="reporting-scenarios"></a>Rapporterings scenarier

Det finns flera sätt att ställa in rapporterings instrument paneler på data som lagras i Azure Cosmos DB. Beroende på föråldrade krav och storleken på data, beskriver följande tabell rapporterings konfigurationen för varje scenario:


|Scenario |Konfiguration |
|---------|---------|
|1. Genererar ad hoc-rapporter (ingen uppdatering)    |  [Power BI Azure Cosmos DB anslutning med import läge](powerbi-visualize.md)       |
|2. Generera ad hoc-rapporter med periodisk uppdatering   |  [Power BI Azure Cosmos DB anslutning med import läge (schemalagd Periodisk uppdatering)](powerbi-visualize.md)       |
|3. Rapportering på stora data mängder (< 10 GB)     |  Power BI Azure Cosmos DB anslutning med stegvis uppdatering       |
|4. Rapportera real tid för stora data mängder    |  Power BI Azure Analysis Services-anslutning med direkt fråga + Azure Analysis Services (Azure Cosmos DB koppling)       |
|5. Rapportering om real tids data med agg regeringar     |  [Power BI Spark-anslutning med direkt fråga + Azure Databricks + Cosmos DB Spark-anslutning.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Rapportering om real tids data med agg regeringar för stora data uppsättningar   |  Power BI Azure Analysis Services Connector med direkt fråga + Azure Analysis Services + Azure Databricks + Cosmos DB Spark-anslutning.       |

Scenarier 1 och 2 kan enkelt konfigureras med hjälp av Azure Cosmos DB Power BI-anslutningen. I den här artikeln beskrivs inställningarna för scenarierna 3 och 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI med stegvis uppdatering

Power BI har ett läge där stegvis uppdatering kan konfigureras. Det här läget eliminerar behovet av att skapa och hantera Azure Analysis Services partitioner. Stegvis uppdatering kan ställas in så att endast de senaste uppdateringarna i stora data mängder filtreras. Det här läget fungerar dock bara med Power BI Premium tjänst som har en begränsning på 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services

Azure Analysis Services tillhandahåller en fullständigt hanterad plattform som en tjänst som är värd för data modeller i företags klass i molnet. Enorma data uppsättningar kan läsas in från Azure Cosmos DB till Azure Analysis Services. För att undvika att fråga hela data uppsättningen hela tiden kan data uppsättningarna delas upp i Azure Analysis Services partitioner, vilka kan uppdateras oberoende av olika frekvenser.

## <a name="power-bi-incremental-refresh"></a>Power BI stegvis uppdatering

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Mata in väder data i Azure Cosmos DB

Konfigurera en pipeline för inmatning för att läsa in [väder data](https://catalog.data.gov/dataset/local-weather-archive) till Azure Cosmos dB. Du kan konfigurera ett [Azure Data Factory (ADF)-](../data-factory/connector-azure-cosmos-db.md) jobb för att regelbundet läsa in de senaste väder uppgifterna i Azure Cosmos dB med http-källan och Cosmos DB mottagare.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Anslut Power BI till Azure Cosmos DB

1. **Anslut Azure Cosmos-kontot till Power BI** – öppna Power BI Desktop och Använd Azure Cosmos DB Connector för att välja rätt databas och behållare.

   ![Azure Cosmos DB Power BI-anslutningsprogram](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Konfigurera stegvis uppdatering** – Följ stegen i [stegvis uppdatering med Power BI](/power-bi/service-premium-incremental-refresh) artikel för att konfigurera stegvis uppdatering för data uppsättningen. Lägg till parametrarna **Ranging** och **RangeEnd** enligt följande skärm bild:

   ![Konfigurera intervall parametrar](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Eftersom data uppsättningen har en datum kolumn som är i text format, ska parametrarna **rang Est Art** och **RangeEnd** omvandlas till att använda följande filter. I fönstret **avancerad redigerare** ändrar du frågan Lägg till följande text för att filtrera raderna baserat på parametrarna rang Est art och RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Beroende på vilken kolumn och datatyp som finns i käll data uppsättningen kan du ändra fälten rang Est art och RangeEnd enligt detta

   
   |Egenskap  |Datatyp  |Filter  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] > duration. TotalSeconds (rang-#datetime (1970, 1,0, 0,0)) och [_ts] < varaktighet. TotalSeconds (RangeEnd-#datetime (1970, 1, 1, 0)))       |
   |Datum (till exempel:-2019-08-19)     |   Sträng      | [Document. Date] > DateTime. ToText (rang, "åååå-MM-DD") och [Document. Date] < DateTime. ToText (RangeEnd, "åååå-MM-DD")        |
   |Datum (till exempel:-2019-08-11 12:00:00)   |  Sträng       |  [Document. Date] > DateTime. ToText (rang, "åååå-mm-dd HH: mm: SS") och [Document. Date] < DateTime. ToText (RangeEnd, "åååå-mm-dd HH: mm: SS")       |


1. **Definiera uppdaterings principen** – definiera uppdaterings principen genom att gå till fliken **stegvis uppdatering** på **snabb** menyn för tabellen. Ange att uppdaterings principen ska uppdateras **varje dag** och lagra senaste månads information.

   ![Definiera uppdaterings princip](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Ignorera varningen som säger att *M-frågan inte kan bekräftas vara viktad*. Azure Cosmos DB Connector viker filter frågor.

1. **Läs in data och generera rapporter** – genom att använda de data som du har läst in tidigare skapar du diagrammen för att rapportera om temperatur-och Rainfall.

   ![Läs in data och generera rapport](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Publicera rapporten till Power BI Premium** – eftersom den stegvisa uppdateringen är en Premium-funktion kan du bara välja en arbets yta på Premium-kapacitet i dialog rutan publicera. Den första uppdateringen kan ta längre tid att importera historiska data. Efterföljande data uppdateringar är mycket snabbare eftersom de använder stegvis uppdatering.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Mata in väder data i Azure Cosmos DB 

Konfigurera en pipeline för inmatning för att läsa in [väder data](https://catalog.data.gov/dataset/local-weather-archive) till Azure Cosmos dB. Du kan konfigurera ett Azure Data Factory (ADF)-jobb för att regelbundet läsa in de senaste väder uppgifterna i Azure Cosmos DB med HTTP-källan och Cosmos DB mottagare.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Anslut Azure Analysis Services till Azure Cosmos-konto

1. **Skapa ett nytt Azure Analysis Services-kluster** - [skapa en instans av Azure Analysis Services](../analysis-services/analysis-services-create-server.md) i samma region som Azure Cosmos-kontot och Databricks-klustret.

1. **Skapa ett nytt Analysis Services tabell projekt i Visual Studio** -  [Installera SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) och skapa ett Analysis Services tabell projekt i Visual Studio.

   ![Skapa Azure Analysis Services-projekt](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Välj den **integrerade arbets ytans** instans och Ange kompatibilitetsnivån till **SQL Server 2017/Azure Analysis Services (1400)**

   ![Azure Analysis Services tabell modells designer](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Lägg till Azure Cosmos db data källa** – navigera till **modeller**> **data källor** > **ny data källa** och Lägg till Azure Cosmos db data källan som visas på följande skärm bild:

   ![Lägg till Cosmos DB data Källa](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Anslut till Azure Cosmos DB genom att ange **konto-URI**, **databas namn**och **behållar namn**. Nu kan du se data från Azure Cosmos-behållaren som importeras till Power BI.

   ![Förhandsgranska Azure Cosmos DB data](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Skapa Analysis Servicess modellen** – öppna Frågeredigeraren och utför de åtgärder som krävs för att optimera den inlästa data uppsättningen:

   * Extrahera endast väder relaterade kolumner (temperatur och Rainfall)

   * Extrahera månads informationen från tabellen. Dessa data är användbara när du skapar partitioner enligt beskrivningen i nästa avsnitt.

   * Omvandla temperatur kolumnerna till tal

   Det resulterande M-uttrycket ser ut så här:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Ändra dessutom data typen för temperatur kolumnerna till decimal för att se till att dessa värden kan ritas i Power BI.

1. **Skapa Azure Analysis-partitioner** – skapa partitioner i Azure Analysis Services för att dela upp data uppsättningen i logiska partitioner som kan uppdateras oberoende av varandra och med olika frekvenser. I det här exemplet skapar du två partitioner som delar upp data uppsättningen i den senaste månadens data och allt annat.

   ![Skapa Analysis Services-partitioner](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Skapa följande två partitioner i Azure Analysis Services:

   * **Senaste månaden** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Historik** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Distribuera modellen till Azure Analysis Server** -högerklicka på Azure Analysis Services projektet och välj **distribuera**. Lägg till Server namnet i rutan **Egenskaper för distributions Server** .

   ![Distribuera Azure Analysis Services modell](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Konfigurera partition uppdateringar och sammanfogningar** – Azure Analysis Services tillåter oberoende bearbetning av partitioner. Eftersom vi vill att den **senaste månads** partitionen ska uppdateras kontinuerligt med de senaste data ställer du in uppdaterings intervallet på 5 minuter. Det är inte nödvändigt att uppdatera data i historisk partition. Dessutom måste du skriva kod för att konsolidera den senaste month-partitionen till den historiska partitionen och skapa en ny senaste månads partition.


## <a name="connect-power-bi-to-analysis-services"></a>Anslut Power BI till Analysis Services

1. **Anslut till Azure-Analysis Server med hjälp av Azure Analysis Services Database Connector** – Välj **Live-läge** och Anslut till Azure Analysis Services-instansen som visas på följande skärm bild:

   ![Hämta data från Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Läs in data och generera rapporter** – genom att använda de data som du har läst in tidigare skapar du diagram för att rapportera om temperatur-och Rainfall. Eftersom du skapar en Live-anslutning ska frågorna köras på data i den Azure Analysis Services modell som du har distribuerat i föregående steg. Temperatur diagram kommer att uppdateras inom fem minuter efter att de nya data har lästs in i Azure Cosmos DB.

   ![Läs in data och generera rapporter](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om Power BI finns i [Kom igång med Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Anslut Qlik Sense till Azure Cosmos DB och visualisera dina data](visualize-qlik-sense.md)