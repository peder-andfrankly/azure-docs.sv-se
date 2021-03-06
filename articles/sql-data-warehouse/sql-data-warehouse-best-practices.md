---
title: Metod tips för SQL Analytics i Azure Synapse Analytics (tidigare SQL DW)
description: Rekommendationer och metod tips för att utveckla lösningar för SQL Analytics i Azure Synapse Analytics (tidigare SQL DW).
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6a9d7c4ed020f447e9512c7bc2edd0caecf3400d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646176"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Metod tips för SQL Analytics i Azure Synapse Analytics (tidigare SQL DW)

Den här artikeln är en samling av bästa metoder som hjälper dig att uppnå optimala prestanda från [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) -distributionen.  Syftet med den här artikeln är att ge dig grundläggande vägledning och fokusera på viktiga områden i fokus.  I varje avsnitt beskrivs ett begrepp och du kan sedan peka på mer detaljerade artiklar som beskriver konceptet i större djup. Sekvensen med ämnen är i prioritetsordning. 

## <a name="reduce-cost-with-pause-and-scale"></a>Minska kostnaderna genom att pausa och skala

Läs mer om att minska kostnaderna genom att pausa och skala i [Hantera beräkning](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Underhålla statistik
Azure SQL Data Warehouse kan konfigureras för att automatiskt identifiera och skapa statistik för kolumner.  De fråge planer som skapats av optimeringen är bara lika lämpliga som tillgängliga statistik.  Vi rekommenderar att du aktiverar AUTO_CREATE_STATISTICS för dina databaser och håller statistiken uppdaterad dagligen eller efter varje belastning för att säkerställa att statistik för kolumner som används i dina frågor alltid är uppdaterad. 

Om du tycker att det tar för lång tid att uppdatera all statistik, kanske du vill försöka bli mer selektiv om vilka kolumner som behöver frekventa statistik uppdateringar. Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag. **Du får störst nytta genom att ha uppdaterad statistik för kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.**

Se även [Manage table statistics][Manage table statistics] (Hantera tabellstatistik), [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Använda DMV:er för att övervaka och optimera frågor
SQL Analytics har flera DMV: er som kan användas för att övervaka frågekörningen.  Artikeln om övervakning nedan innehåller stegvisa anvisningar för hur du analyserar informationen från en fråga som körs.  För att snabbt hitta frågor i dessa DMV:er kan det vara bra att använda alternativet LABEL med dina frågor.

Se även [Monitor your workload using DMVs][Monitor your workload using DMVs] (Övervaka arbetsbelastningen med datahanteringsvyer), [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="tune-query-performance-with-new-product-enhancements"></a>Justera prestanda för frågor med nya produkt förbättringar
- [Prestandajustering med materialiserade vyer](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Prestandajustering med grupperade kolumnlagringsindex](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Prestandajustering med cachelagring av resultatuppsättningar](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>Gruppera INSERT-satser i batchar
En engångsinläsning till en liten tabell med en INSERT-instruktion eller en regelbunden inläsning av en sökning kan prestera utmärkt för dina behov med en instruktion som `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Men om du behöver läsa in flera tusen eller flera miljoner rader under en dag kanske du märker att singleton-infogningar med INSERT inte hänger med.  I så fall utvecklar du i stället dina processer så att de skriver till en fil och så att en annan process regelbundet körs och läser in filen.

Se även [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Använda PolyBase för att snabbt läsa in och exportera data

 SQL Analytics stöder inläsning och export av data via flera verktyg, inklusive Azure Data Factory, PolyBase och BCP.  För små datamängder där prestanda inte är viktigt räcker alla verktygen för dina behov.  Om du däremot läser in eller exporterar stora mängder data eller om snabba prestanda krävs är PolyBase det bästa valet.  
 
 PolyBase är utformat för att dra nytta av arkitekturen för minnes trycks-(massiv parallell bearbetning) och kommer att läsa in och exportera data storlekarna snabbare än andra verktyg.  PolyBase-inläsningar kan utföras med hjälp av CTAS eller INSERT INTO.  **CTAS minimerar transaktionsloggningen och är det snabbaste sättet att läsa in data.** 
 
  Azure Data Factory stöder även PolyBase-belastningar och kan uppnå liknande prestanda som CTAS.  PolyBase stöder olika filformat, inklusive Gzip-filer.  **För att maximera data flödet när du använder gzip-textfiler kan du dela upp filer i 60 eller fler filer för att maximera din belastnings parallellt.**  För snabbare totalt genomflöde bör du överväga att använda samtidig inläsning av data.

Se även [läsa in data][Load data], [Guide för att använda PolyBase][Guide for using PolyBase], [inläsning av mönster och strategier för SQL-pool][Azure SQL Data Warehouse loading patterns and strategies], [läsa in data med Azure Data Factory][Load Data with Azure Data Factory], [Flytta data med Azure Data Factory][Move data with Azure Data Factory], [Skapa externt fil format][CREATE EXTERNAL FILE FORMAT], [skapa tabell som Select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Läsa in och sedan fråga externa tabeller
Även om Polybase, även kallat externa tabeller, kan vara det snabbaste sättet att läsa in data, är det inte optimalt för frågor. PolyBase-tabeller stöder för närvarande endast Azure Blob-filer och Azure Data Lake lagring. Dessa filer har inte några beräkningsresurser som backar upp dem.  

Därför kan SQL Analytics inte avlasta detta arbete och måste därför läsa hela filen genom att läsa in den till tempdb för att kunna läsa data.  Om du har flera frågor som ska köras mot dessa data är det därför bättre att läsa in dem en gång och låta frågorna använda den lokala tabellen.

Se även [Guide for using PolyBase][Guide for using PolyBase] (Guide för att använda PolyBase)

## <a name="hash-distribute-large-tables"></a>Hash-distribuera stora tabeller
Tabeller distribueras som standard med resursallokering (Round Robin).  Detta gör det enkelt för användarna att börja skapa tabeller utan att de behöver bestämma hur tabellerna ska distribueras.  Resursallokeringstabeller kan prestera bra för vissa arbetsbelastningar, men i de flesta fall blir prestanda bättre om du väljer en distributionskolumn.  Det vanligaste exemplet på när en tabell som distribueras med en kolumn presterar avsevärt mycket bättre än en resursallokeringstabell är när två stora faktatabeller kopplas.  

Om du till exempel har en ordertabell, som distribueras efter order_id, och en transaktionstabell, som också distribueras efter order_id, och du kopplar ordertabellen till transaktionstabellen baserat på order_id, så blir den här frågan en direktfråga, vilket innebär att vi eliminerar dataflyttningsåtgärder.  Färre steg innebär en snabbare fråga.  Mindre dataflyttning gör också att frågor körs snabbare.  

När du läser in en distribuerad tabell ser du till att inkommande data inte sorteras baserat på distributionsnyckeln eftersom det gör att inläsningarna tar längre tid.  Se länkarna nedan för mer information om hur du väljer en distributions kolumn som kan förbättra prestanda samt hur du definierar en distribuerad tabell i WITH-satsen i CREATE TABLE-instruktionen.

Se även [Table overview][Table overview] (Tabellöversikt), [Table distribution][Table distribution] (Tabelldistribution), [Selecting table distribution][Selecting table distribution] (Välja tabelldistribution), [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Överpartitionera inte
När du partitionerar data kan det vara effektivt att underhålla dina data genom att byta partitionering eller optimera genomsökningar med hjälp av partition Eli minering, så att för många partitioner kan sakta ned dina frågor.  Ofta är en partitionerings strategi för hög granularitet som kan fungera bra på SQL Server kanske inte fungerar bra i SQL Analytics.  

För många partitioner kan också minska effektiviteten i grupperade columnstore-index om varje partition har färre än 1 miljoner rader.  Tänk på att när du skapar en tabell med 100 partitioner i bakgrunden kan SQL Analytics-partitioner dina data till 60-databaser, så om du skapar en tabell med partitioner, resulterar detta faktiskt i 6000-partitioner.  

Alla arbetsbelastningar är olika så det bästa rådet är att experimentera med partitioneringen för att se vad som fungerar bäst med din arbetsbelastning.  Överväg att använda lägre granularitet än vad som har fungerat i SQL Server.  Överväg exempelvis att använda veckovisa eller månadsvisa partitioneringar i stället för dagliga.

Se även [Table partitioning][Table partitioning] (Tabellpartitionering)

## <a name="minimize-transaction-sizes"></a>Minimera transaktionsstorlekar
INSERT-, UPDATE- och DELETE-instruktioner körs i en transaktion och när de misslyckas måste de återställas.  Du kan minimera risken för en lång återställning genom att minska transaktionsstorlekarna om det går.  Du kan göra det genom att dela upp INSERT-, UPDATE- och DELETE-instruktioner i flera delar.  

Om du till exempel har en INFOGNING som du förväntar dig att ta 1 timme, om möjligt, kan du dela upp den i fyra delar som varje körs på 15 minuter.  Dra nytta av minimal loggning, t.ex. med CTAS, TRUNCATE, DROP TABLE eller INSERT, för att tömma tabeller och minska risken för återställning.  

Ett annat sätt att eliminera återställningar är att använda åtgärder med endast metadata, t.ex. med partitionsväxling, för datahantering.  Till exempel kan du, i stället för att köra en DELETE-instruktion för att ta bort alla rader i en tabell där order_date var i oktober 2001, partitionera dina data månadsvis och sedan byta ut partitionen med data mot en tom partition från en annan tabell (se ALTER TABLE-exemplen).  

För opartitionerade tabeller bör du överväga att använda en CTAS för att skriva de data som du vill behålla i en tabell i stället för att använda ta bort.  Om en CTAS tar samma tids period, är det en mycket säkrare åtgärd att köra eftersom den har minimal transaktions loggning och kan avbrytas snabbt om det behövs.

Se även [Understanding transactions][Understanding transactions] (Förstå transaktioner), [Optimizing transactions][Optimizing transactions] (Optimera transaktioner), [Table partitioning][Table partitioning] (Tabellpartitionering), [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create Table As Select (CTAS)][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>Minska resultat storlekarna för frågan  
Det här steget hjälper dig att undvika problem på klient sidan som orsakas av resultat av stor fråga.  Du kan redigera frågan om du vill minska antalet returnerade rader. Med vissa verktyg för att skapa frågor kan du lägga till syntaxen "Top N" i varje fråga.  Du kan också CETAS från frågeresultatet till en temporär tabell och sedan använda PolyBase-export för bearbetning av äldre versioner.

## <a name="use-the-smallest-possible-column-size"></a>Använda minsta möjliga kolumnstorlek
När du definierar DDL: en med den minsta data typen som kommer att ge stöd för dina data förbättras frågans prestanda.  Detta är särskilt viktigt för CHAR- och VARCHAR-kolumner.  Om det längsta värdet i en kolumn är 25 tecken definierar du kolumnen som VARCHAR(25).  Undvik att definiera alla teckenkolumner med en stor standardlängd.  Definiera också kolumner som VARCHAR när det är allt som krävs i stället för att använda NVARCHAR.

Se även [Table overview][Table overview] (Tabellöversikt), [Table data types][Table data types] (Datatyper för tabeller), [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Använda tillfälliga heap-tabeller för tillfälliga data
När du är tillfälligt inleder data kan det hända att du använder en heap-tabell för att göra den övergripande processen snabbare.  Om du bara läser in data för att mellanlagra dem innan du kör fler transformationer går det mycket snabbare om du läser in tabellen till en heap-tabell än om du läser in data till en grupperad columnstore-tabell.  

Om du läser in data till en temporär tabell går inläsningen dessutom mycket fortare än om du läser in en tabell till permanent lagring.  Temporära tabeller börjar med "#" och är bara tillgängliga för den session som skapade den, så de kan bara fungera i begränsade scenarier.   Heap-tabeller definieras i WITH-satsen i en CREATE TABLE-instruktion.  Om du använder en temporär tabell måste du också komma ihåg att skapa statistik för den temporära tabellen.

Se även [Temporary tables][Temporary tables] (Temporära tabeller), [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Optimera grupperade columnstore-tabeller
Grupperade columnstore-index är ett av de mest effektiva sätten att lagra data i SQL Analytics.  Som standard skapas tabeller i SQL Analytics som grupperade ColumnStore-tabeller.  Det är viktigt att segmentkvaliteten är bra för att uppnå bästa prestanda med frågor mot columnstore-tabeller.  

När rader skrivs till columnstore-tabeller när minnet är hårt belastat, kan columnstore-segmentens kvalitet påverkas.  Segmentkvaliteten kan mätas utifrån antalet rader i en komprimerad radgrupp.  Stegvisa anvisningar för hur du identifierar och förbättrar segmentkvaliteten för grupperade columnstore-tabeller finns i avsnittet [Causes of poor columnstore index quality][Causes of poor columnstore index quality] (Orsaker till låg columnstore-indexkvalitet) i artikeln [Table indexes][Table indexes] (Tabellindex).  

Eftersom ett columnstore-segment av hög kvalitet är viktigt är det en bra idé att använda användar-ID: n som finns i resurs klassen medel eller stor för att läsa in data. Genom att använda lägre [data lager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) kan du tilldela en större resurs klass till din inläsnings användare.

Eftersom columnstore-tabeller vanligt vis inte skickar data till ett komprimerat columnstore-segment förrän det finns fler än 1 000 000 rader per tabell och varje SQL Analytics-tabell är partitionerad i 60-tabeller, som en tumregel, kommer columnstore-tabeller inte att gynna en fråga om inte tabellen innehåller fler än 60 000 000 rader.  För tabeller med färre än 60 miljoner rader kanske det inte tjänar något till att ha ett columnstore-index.  Men det skadar inte heller.  

Om du partitionerar data bör du dessutom tänka på att varje partition måste innehålla 1 miljon rader för att kunna dra nytta av ett grupperat columnstore-index.  Om en tabell har 100 partitioner måste den innehålla minst 6 miljarder rader för att ha nytta av ett arkiv med grupperade kolumner (60 distributioner * 100 partitioner * 1 miljoner rader).  

Om din tabell inte innehåller 6 miljarder rader i det här exemplet minskar du antalet partitioner eller överväger att använda en heap-tabell i stället.  Det kan också vara värt att experimentera och se om du kan förbättra prestanda med en heap-tabell med sekundära index i stället för en columnstore-tabell.

När du kör frågor mot en columnstore-tabell körs frågorna snabbare om du bara väljer de kolumner som du behöver.  

Se även [Table indexes][Table indexes] (Tabellindex), [Columnstore indexes guide][Columnstore indexes guide] (Guide för columnstore-index), [Rebuilding columnstore indexes][Rebuilding columnstore indexes] (Återskapa columnstore-index)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Använda en större resursklass för att förbättra frågeprestanda
SQL Analytics använder resurs grupper som ett sätt att allokera minne till frågor.  Från rutan är alla användare tilldelade till den lilla resurs klassen, vilket ger 100 MB minne per distribution.  Eftersom det alltid är 60 distributioner och varje distribution tilldelas minst 100 MB, är den totala minnesallokeringen i systemet 6 000 MB, eller strax under 6 GB.  

Vissa frågor, t.ex. stora kopplingar eller inläsningar till grupperade columnstore-tabeller, kan dra nytta av större minnesallokeringar.  Vissa frågor, t.ex. rena genomsökningar, har ingen nytta av detta.  På sidan vänd minskar användningen av större resurs klasser samtidighet, så du bör ta hänsyn till den här effekten innan du flyttar alla dina användare till en stor resurs klass.

Se även [Resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Använda den mindre resursklassen för bättre prestanda vid samtidig körning
Om du märker att användar frågor verkar ha lång fördröjning kan det vara så att användarna körs i större resurs klasser och använder många samtidiga platser som gör att andra frågor hamnar i kö.  Du kan se om användarnas frågor köas genom att köra `SELECT * FROM sys.dm_pdw_waits` för att se om några rader returneras.

Se även [resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md), [sys. DM _pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Andra resurser
Information om vanliga problem och lösningar finns i vår [felsökningsartikel][Troubleshooting].

Om du inte hittar det du letar efter i den här artikeln kan du prova att använda "Sök efter dokument" på vänster sida av den här sidan för att söka i alla Azure Synapse-dokument.  [Azure Synapse-forumet][Azure SQL Data Warehouse MSDN Forum] är en plats där du kan ställa frågor till andra användare och till produkt gruppen för Azure Synapse. 

Vi övervakar aktivt detta forum för att kontrollera att dina frågor besvaras antingen av en annan användare eller av någon av oss.  Om du föredrar att ställa dina frågor på Stack Overflow har vi också ett [Azure Synapse Stack Overflow-forum][Azure SQL Data Warehouse Stack Overflow Forum].

Använd slutligen feedback-sidan för [Azure-Synapse][Azure SQL Data Warehouse Feedback] för att skapa funktions begär Anden.  Genom att skicka in dina egna önskemål eller rösta fram andras förfrågningar hjälper du oss att prioritera funktioner.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
