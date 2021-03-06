---
title: Mappa prestanda-och justerings guide för data flöde
description: Lär dig mer om viktiga faktorer som påverkar prestanda för att mappa data flöden i Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: fb2a11850370766ab174c67dd122f33879fb432a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928539"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Prestanda-och justerings guiden för att mappa data flöden

Att mappa data flöden i Azure Data Factory tillhandahålla ett kod fritt gränssnitt för att utforma, distribuera och dirigera data transformationer i stor skala. Om du inte är bekant med att mappa data flöden kan du läsa [Översikt över kart data flödet](concepts-data-flow-overview.md).

När du utformar och testar data flöden från ADF-UX måste du växla till fel söknings läge för att köra dina data flöden i real tid utan att vänta på att ett kluster ska värmas upp. Mer information finns i [fel söknings läge](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Prestanda för övervakning av data flöde

När du skapar mappnings data flöden kan du Unit testa varje omvandling genom att klicka på fliken Data förhands granskning på konfigurations panelen. När du har verifierat din logik kan du testa ditt data flöde från slut punkt till slut punkt som en aktivitet i en pipeline. Lägg till aktiviteten kör data flöde och Använd knappen Felsök för att testa data flödets prestanda. Öppna körnings planen och prestanda profilen för ditt data flöde genom att klicka på glasögon-ikonen under "åtgärder" på fliken utmatning i pipelinen.

![Data flödes övervakare](media/data-flow/mon002.png "Data flödes övervakare 2")

 Du kan använda den här informationen för att uppskatta prestanda för ditt data flöde mot olika data källor. Mer information finns i [övervaka mappning av data flöden](concepts-data-flow-monitoring.md).

![Dataflödesövervakning](media/data-flow/mon003.png "Data flödes övervakare 3")

 För pipeliniska fel söknings körningar krävs en minut i kluster konfigurations tiden i de övergripande prestanda beräkningarna för ett varmt kluster. Om du initierar standard Azure Integration Runtime kan tiden ta cirka 5 minuter.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Ökande beräknings storlek i Azure Integration Runtime

En Integration Runtime med fler kärnor ökar antalet noder i beräknings miljöerna för Spark och ger mer processor kraft för att läsa, skriva och transformera dina data.
* Prova ett **Compute-optimerat** kluster om du vill att din bearbetnings takt ska vara högre än din ingående hastighet
* Försök med **ett minnesoptimerade** kluster om du vill cachelagra mer data i minnet.

![Ny IR](media/data-flow/ir-new.png "Ny IR")

Mer information om hur du skapar en Integration Runtime finns [i integration runtime i Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Öka storleken på ditt fel söknings kluster

Som standard använder fel söknings programmet standard Azure integration runtime som skapas automatiskt för varje data fabrik. Standardvärdet Azure IR har angetts till åtta kärnor, fyra för en driver-nod och fyra för en arbetsnoden, med hjälp av allmänna beräknings egenskaper. När du testar med större data kan du öka storleken på ditt fel söknings kluster genom att skapa en Azure IR med större konfigurationer och välja den nya Azure IR när du växlar vid fel sökning. Detta instruerar ADF att använda den här Azure IR för för hands versionen av data och pipeline-felsökning med data flöden.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimering för Azure SQL Database och Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Partitionering på källa

1. Gå till fliken **optimera** och välj **Ange partitionering**
1. Välj **källa**.
1. Under **antal partitioner**anger du det maximala antalet anslutningar till din Azure SQL-databas. Du kan prova en högre inställning för att få parallella anslutningar till databasen. Vissa fall kan dock leda till snabbare prestanda med ett begränsat antal anslutningar.
1. Välj om du vill partitionera med en speciell tabell kolumn eller en fråga.
1. Om du har valt **kolumn**väljer du kolumnen partition.
1. Om du har valt **fråga**anger du en fråga som matchar partitionerings schemats databas tabell. Med den här frågan kan käll databas motorn utnyttja partition Eli minering. Käll databas tabellerna behöver inte partitioneras. Om källan inte redan är partitionerad använder ADF fortfarande data partitionering i miljön Spark-omvandling baserat på den nyckel som du väljer i käll omvandlingen.

![Käll del](media/data-flow/sourcepart3.png "Käll del")

### <a name="source-batch-size-input-and-isolation-level"></a>Käll grupps storlek, Indatatyp och isolerings nivå

Under **käll alternativ** i käll omvandlingen kan följande inställningar påverka prestanda:

* Batch-storlek instruerar ADF att lagra data i mängder i minnet i stället för rad för rad. Batchstorleken är en valfri inställning och du kan få slut på resurser på datornoderna om de inte ändras korrekt.
* Genom att ställa in en fråga kan du filtrera rader på källan innan de anländer till data flödet för bearbetning. Detta kan göra den första data hämtningen snabbare. Om du använder en fråga kan du lägga till valfria frågeuttryck för din Azure SQL DB, till exempel READ uncommitted.
* Vid Läs behörighet får du snabbare frågeresultat om käll omvandling

![Källa](media/data-flow/source4.png "Källa")

### <a name="sink-batch-size"></a>Grupp storlek för mottagare

Om du vill undvika rad-för-rad-bearbetning av dina data flöden ställer du in **batchstorleken** på fliken Inställningar för Azure SQL DB och Azure SQL DW-mottagare. Om batchstorleken anges, bearbetar ADF databas skrivningar i batchar baserat på den storlek som angetts.

![mottagare](media/data-flow/sink4.png "Kanalmottagare")

### <a name="partitioning-on-sink"></a>Partitionering på handfat

Även om du inte har dina data partitionerade i mål tabellerna rekommenderar vi att du har dina data partitionerade i Sink-omvandlingen. Partitionerade data resulterar ofta i mycket snabbare inläsningar för att tvinga alla anslutningar att använda en enda nod/partition. Gå till fliken optimera i din mottagare och *Välj partitionering med resursallokering för* att välja det idealiska antalet partitioner som ska skrivas till din mottagare.

### <a name="disable-indexes-on-write"></a>Inaktivera index vid skrivning

I din pipeline lägger du till en [lagrad procedur aktivitet](transform-data-using-stored-procedure.md) innan data flödes aktiviteten som inaktiverar index i mål tabellerna som skrivs från din mottagare. Efter din data flödes aktivitet lägger du till en annan lagrad procedur aktivitet som aktiverar dessa index.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Öka storleken på din Azure SQL DB och DW

Schemalägg en storleks ändring för din källa och mottagar Azure SQL DB och DW innan din pipeline kör för att öka data flödet och minimera Azure-begränsningen när du når DTU-gränser. När din pipeline-körning har slutförts ändrar du storlek på databaserna till normal körnings frekvens.

### <a name="azure-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Endast Azure SQL DW] Använd mellanlagring för att läsa in data i bulk via PolyBase

Om du vill undvika rad-för-rad-infogningar i din DW kontrollerar du **Aktivera mellanlagring** i mottagar inställningarna så att ADF kan använda [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). PolyBase gör att ADF kan läsa in data i bulk.
* När du kör data flödes aktiviteten från en pipeline måste du välja en BLOB eller ADLS Gen2 lagrings plats för att mellanlagra dina data under Mass inläsning.

## <a name="optimizing-for-files"></a>Optimera för filer

Vid varje omvandling kan du ange det partitionerings schema som du vill att Data Factory ska använda på fliken optimera.
* För mindre filer kan det hända att du kan välja *en enskild partition* ibland fungerar bättre och snabbare än att be Spark att partitionera dina små filer.
* Om du inte har tillräckligt med information om dina källdata väljer du *resursallokering* partitionering och anger antalet partitioner.
* Om dina data innehåller kolumner som kan vara användbara hash-nycklar väljer du *hash-partitionering*.

Vid fel sökning i data förhands granskning och fel sökning av pipelinen gäller gräns-och samplings storlekarna för filbaserade käll data uppsättningar endast för det antal rader som returneras, inte antalet rader som läses. Detta kan påverka prestandan för dina fel söknings körningar och möjligen orsaka att flödet slutar fungera.
* Fel söknings kluster är små kluster med en nod som standard och vi rekommenderar att du använder små exempel filer för fel sökning. Gå till fel söknings inställningar och peka på en liten delmängd av dina data med en temporär fil.

    ![Fel söknings inställningar](media/data-flow/debugsettings3.png "Fel söknings inställningar")

### <a name="file-naming-options"></a>Fil namns alternativ

Det vanligaste sättet att skriva transformerade data i mappnings data flöden skriver BLOB eller ADLS File Store. I din mottagare måste du välja en data uppsättning som pekar på en behållare eller mapp, inte en namngiven fil. När data flödet för mappning använder Spark för körning, delas utdata ut över flera filer baserat på ditt partitionerings schema.

Ett gemensamt partitionerings schema är att välja _utdata till en fil_, som sammanfogar alla filer i utdatafilen till en enda fil i din mottagare. Den här åtgärden kräver att utdata minskar till en enda partition på en enskild klusternod. Du kan ta bort resurser för klusternoden om du kombinerar många stora källfiler till en enda utdatafil.

För att undvika att beräkna resurser för beräknings resurser, Behåll standardvärdet optimerat schema i data flöde och Lägg till en kopierings aktivitet i din pipeline som sammanfogar alla delfiler från mappen utdata till en ny fil. Den här tekniken separerar åtgärden för omvandling från fil sammanslagning och ger samma resultat som _att ange utdata till en enskild fil_.

### <a name="looping-through-file-lists"></a>Loopa igenom fil listor

Ett data flöde för mappning kommer att köras bättre när käll omvandlingen upprepas över flera filer i stället för slingor via för varje aktivitet. Vi rekommenderar att du använder jokertecken eller fil listor i din käll omvandling. Data flödes processen körs snabbare genom att tillåta att slingor uppstår i Spark-klustret. Mer information finns i [jokertecken i käll omvandling](data-flow-source.md#file-based-source-options).

Om du till exempel har en lista med datafiler från 2019 juli som du vill bearbeta i en mapp i Blob Storage, kan du använda ett jokertecken i din käll omvandling.

```DateFiles/*_201907*.txt```

Genom att använda jokertecken innehåller pipelinen bara en data flödes aktivitet. Detta kommer att utföra bättre än en sökning mot BLOB Store som sedan itererar över alla matchade filer med hjälp av en förvarsin aktivitet med data flödes aktiviteten Kör i.

### <a name="optimizing-for-cosmosdb"></a>Optimering för CosmosDB

Inställning av data flödes-och batch-egenskaper på CosmosDB-mottagare börjar bara gälla när data flödet körs från en pipeline-dataflöde-aktivitet. De ursprungliga samlings inställningarna kommer att hanteras av CosmosDB när data flödet har körts.

* Batchstorlek: beräkna den grova rad storleken för dina data och se till att rowSize * batchstorleken är mindre än 2 000 000. Om så är fallet ökar du batchstorleken för att få bättre data flöde
* Througput: Ange en högre data flödes inställning här så att dokument kan skrivas snabbare till CosmosDB. Kom ihåg de högre RU-kostnaderna baserat på en hög data flödes inställning.
*   Budget för Skriv data flöde: Använd ett värde som är mindre än det totala antalet ru: er per minut. Om du har ett data flöde med ett stort antal Spark-partitiongs ger en mer balans mellan dessa partitioner genom att ställa in en budget genom strömning.

## <a name="next-steps"></a>Nästa steg

Se andra data flödes artiklar relaterade till prestanda:

- [Fliken optimera data flöde](concepts-data-flow-overview.md#optimize)
- [Data flödes aktivitet](control-flow-execute-data-flow-activity.md)
- [Övervaka data flödes prestanda](concepts-data-flow-monitoring.md)
