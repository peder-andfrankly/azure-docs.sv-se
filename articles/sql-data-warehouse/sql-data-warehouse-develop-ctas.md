---
title: CREATE TABLE SOM SELECT (CTAS)
description: Förklaring och exempel på instruktionen CREATE TABLE AS SELECT (CTAS) i Azure SQL Data Warehouse för att utveckla lösningar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 4992bb00fa5397ef6a4e055e08b445d35f5ed77a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685860"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>CREATE TABLE som SELECT (CTAS) i Azure SQL Data Warehouse

I den här artikeln beskrivs instruktionen CREATE TABLE AS SELECT (CTAS) T-SQL i Azure SQL Data Warehouse för utveckling av lösningar. Artikeln innehåller också kod exempel.

## <a name="create-table-as-select"></a>CREATE TABLE SOM VÄLJ

Instruktionen [CREATE TABLE as Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) är en av de viktigaste T-SQL-funktionerna som är tillgängliga. CTAS är en parallell åtgärd som skapar en ny tabell baserat på utdata från ett SELECT-uttryck. CTAS är det enklaste och snabbaste sättet att skapa och infoga data i en tabell med ett enda kommando.

## <a name="selectinto-vs-ctas"></a>Välj... I vs. CTAS

CTAS är en mer anpassningsbar version av [Select... INTO](/sql/t-sql/queries/select-into-clause-transact-sql) -instruktion.

Följande är ett exempel på en enkel markering... IKRAFTTRÄDANDE

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Välj... I kan du inte ändra antingen distributions metoden eller index typen som en del av åtgärden. Du skapar `[dbo].[FactInternetSales_new]` med standard distributions typen ROUND_ROBIN och standard tabell strukturen för GRUPPERat COLUMNSTORE-INDEX.

Med CTAS kan du å andra sidan Ange både distributionen av tabell data och tabell struktur typ. Så här konverterar du det föregående exemplet till CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Om du bara försöker ändra indexet i din CTAS-åtgärd och käll tabellen är hash-distribuerad, behåller du samma distributions kolumn och datatyp. Detta förhindrar data flyttning mellan distributioner under drift, vilket är mer effektivt.

## <a name="use-ctas-to-copy-a-table"></a>Använda CTAS för att kopiera en tabell

Kanske är en av de vanligaste användningarna av CTAS att skapa en kopia av en tabell för att ändra DDL. Anta att du ursprungligen skapade tabellen som `ROUND_ROBIN`och nu vill ändra den till en tabell som distribuerats i en kolumn. CTAS hur du ändrar distributions kolumnen. Du kan också använda CTAS för att ändra partitionering, indexering eller kolumn typer.

Anta att du har skapat den här tabellen genom att använda standard distributions typen `ROUND_ROBIN`, inte att ange en distributions kolumn i `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Nu vill du skapa en ny kopia av den här tabellen med en `Clustered Columnstore Index`, så att du kan dra nytta av prestanda för grupperade columnstore-tabeller. Du vill även distribuera den här tabellen på `ProductKey`eftersom du förväntar dig kopplingar till den här kolumnen och vill undvika att data flyttas under kopplingar på `ProductKey`. Slutligen vill du också lägga till partitionering på `OrderDateKey`, så att du snabbt kan ta bort gamla data genom att släppa gamla partitioner. Här är CTAS-instruktionen som kopierar den gamla tabellen till en ny tabell.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Slutligen kan du byta namn på tabeller, för att växla i den nya tabellen och sedan ta bort den gamla tabellen.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Använd CTAS för att arbeta runt funktioner som inte stöds

Du kan också använda CTAS för att arbeta runt ett antal funktioner som inte stöds i listan nedan. Den här metoden kan ofta vara användbar, eftersom inte bara är din kod kompatibel, men den körs ofta snabbare på SQL Data Warehouse. Den här prestandan är resultatet av dess helt parallella design. Scenarierna är:

* ANSI-kopplingar vid uppdateringar
* ANSI-kopplingar vid borttagningar
* MERGE-instruktion

> [!TIP]
> Försök att betrakta "CTAS First". Att lösa ett problem genom att använda CTAS är vanligt vis en lämplig metod, även om du skriver mer data som ett resultat.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI Join-ersättning för uppdaterings instruktioner

Du kanske upptäcker att du har en komplex uppdatering. Uppdateringen kopplar ihop fler än två tabeller med hjälp av ANSI Join-syntax för att utföra uppdateringen eller ta bort.

Föreställ dig att du behövde uppdatera den här tabellen:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

Den ursprungliga frågan kan ha tittat på något som liknar det här exemplet:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

SQL Data Warehouse stöder inte ANSI-kopplingar i `FROM`-satsen i ett `UPDATE`-uttryck, så du kan inte använda föregående exempel utan att ändra det.

Du kan använda en kombination av en CTAS och en implicit koppling för att ersätta föregående exempel:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>ANSI Join-ersättning för Delete-instruktioner

Ibland är den bästa metoden för att ta bort data att använda CTAS, särskilt för `DELETE`-uttryck som använder ANSI Join-syntax. Detta beror på att SQL Data Warehouse inte stöder ANSI-kopplingar i `FROM`-satsen i ett `DELETE`-uttryck. I stället för att ta bort data väljer du de data som du vill behålla.

Följande är ett exempel på en konverterad `DELETE`-instruktion:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Ersätt merge-instruktioner

Du kan ersätta sammanslagnings instruktioner, minst delvis, genom att använda CTAS. Du kan kombinera `INSERT` och `UPDATE` i ett enda uttryck. Eventuella borttagna poster bör begränsas från `SELECT`-instruktionen för att utesluta resultatet.

Följande exempel gäller för ett `UPSERT`:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Explicit tillstånds data typ och null-värde för utdata

När du migrerar kod kanske du kan köra över den här typen av kodnings mönster:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Du kanske tror att du bör migrera den här koden till CTAS och att du är korrekt. Det finns dock ett dolt problem här.

Följande kod ger inte samma resultat:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Observera att kolumnen "resultat" utför vidarebefordran av data typen och värdet null i uttrycket. Att överföra data typen framåt kan leda till diskret varians i värden om du inte är försiktig.

Prova det här exemplet:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Värdet som lagras för resultat skiljer sig åt. Eftersom det beständiga värdet i resultat kolumnen används i andra uttryck blir felet ännu mer betydelsefullt.

![Skärm bild av CTAS-resultat](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Detta är viktigt för datamigreringar. Även om den andra frågan är utan tvekan mer exakt är det ett problem. Data skiljer sig från käll systemet och det leder till integritets frågor i migreringen. Detta är ett av de sällsynta fall där svaret "fel" faktiskt är rätt!

Orsaken till att vi ser en olikhet mellan de två resultaten beror på en implicit typ av data. I det första exemplet definierar tabellen kolumn definitionen. När raden infogas sker en implicit typ konvertering. I det andra exemplet finns det ingen implicit typ konvertering eftersom uttrycket definierar kolumnens datatyp.

Observera också att kolumnen i det andra exemplet har definierats som en kolumn som kan ha värdet NULL, medan det i det första exemplet inte har det. När tabellen skapades i det första exemplet har kolumn null definierats explicit. I det andra exemplet slutade det uttrycket, och som standard resulterar det i en NULL-definition.

För att lösa dessa problem måste du uttryckligen ställa in typ konvertering och null-värde i SELECT-delen av CTAS-instruktionen. Du kan inte ange dessa egenskaper i CREATE TABLE.
Följande exempel visar hur du korrigerar koden:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Observera följande:

* Du kan använda CAST eller CONVERT.
* Använd ISNULL, inte sammanslagning, för att tvinga NULL. Se följande anmärkning.
* ISNULL är den yttersta funktionen.
* Den andra delen av ISNULL är konstant, 0.

> [!NOTE]
> För att null-värdet ska kunna anges korrekt är det viktigt att använda ISNULL och inte sammanslagning. SAMMANSLAGNING är inte en deterministisk funktion, så resultatet av uttrycket kan alltid vara NULL. ISNULL är annorlunda. Den är deterministisk. När den andra delen av funktionen ISNULL är en konstant eller en litteral, kommer det resulterande värdet inte att vara NULL.

Att säkerställa integriteten för dina beräkningar är också viktigt för byte av tabell partition. Anta att du har den här tabellen definierad som en fakta tabell:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Fältet belopp är dock ett beräknat uttryck. Den är inte en del av data källan.

Om du vill skapa en partitionerad data uppsättning kan du använda följande kod:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Frågan skulle köras perfekt. Problemet uppstår när du försöker att byta partition. Tabell definitionerna matchar inte. Om du vill göra tabell definitionerna identiska ändrar du CTAS för att lägga till en `ISNULL` funktion för att bevara attributets null-attribut för kolumnen.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]   
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Du kan se att typ konsekvens och underhåll egenskaper för null i en CTAS är en metod för bästa praxis. Det hjälper till att upprätthålla integriteten i dina beräkningar och säkerställer också att partition växling är möjlig.

CTAS är en av de viktigaste satserna i SQL Data Warehouse. Se till att du förstår den noggrant. Se [CTAs-dokumentationen](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [utvecklings översikten](sql-data-warehouse-overview-develop.md).

