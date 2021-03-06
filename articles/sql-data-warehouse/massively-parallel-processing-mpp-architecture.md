---
title: Azure Synapse Analytics-arkitektur (tidigare SQL DW)
description: Lär dig hur Azure Synapse Analytics (tidigare SQL DW) kombinerar massivt parallell bearbetning (MPP) med Azure Storage för att uppnå höga prestanda och skalbarhet.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: ea9629c63fcab97ba8ba83cd88592c37ae41818a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646389"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure Synapse Analytics-arkitektur (tidigare SQL DW) 

Azure Synapse är en obegränsad analys tjänst som sammanför företags data lager hantering och stor data analys. Det ger dig friheten att fråga efter data på dina villkor, med hjälp av antingen server utan på begäran eller etablerade resurser – i stor skala. Azure synapse ger de här två världar tillsammans med en enhetlig upplevelse för att mata in, förbereda, hantera och hantera data för omedelbara BI-och maskin inlärnings behov.

 Azure-Synapse har fyra komponenter:
- SQL Analytics: fullständig T-SQL-baserad analys 
    - SQL-pool (betala per DWU etablerad) – allmänt tillgänglig
    - SQL på begäran (betala per TB bearbetat) – (för hands version)
- Spark: djupt integrerad Apache Spark (förhands granskning) 
- Data integrering: hybrid data integrering (för hands version)
- Studio: enhetlig användar upplevelse.  (Förhandsversion)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>Arkitektur komponenter för SQL Analytics MPP

[SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) utnyttjar en skalbar arkitektur för att distribuera beräknings bearbetning av data över flera noder. Skalan är en abstraktion av beräknings kraften som kallas för en [informations lager enhet](what-is-a-data-warehouse-unit-dwu-cdwu.md). Compute är separat från lagring, vilket gör att du kan skala beräkning oberoende av data i systemet.

![SQL Analytics-arkitektur](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Analytics använder en Node-baserad arkitektur. Programmen ansluter och utfärdar T-SQL-kommandon till en Control-nod, vilket är den enda punkten i posten för SQL Analytics. Noden kontroll kör minnes trycks skydds motorn som optimerar frågor för parallell bearbetning och skickar sedan åtgärder till Compute-noder för att utföra sitt arbete parallellt. 

Compute-noderna lagrar alla användar data i Azure Storage och kör parallella frågor. Data flytt tjänsten (DMS) är en intern tjänst på system nivå som flyttar data mellan noderna vid behov för att köra frågor parallellt och returnera korrekta resultat. 

När du använder SQL Analytics med fristående lagrings utrymme och data bearbetning kan:

* Oberoende storleks beräknings kraft oberoende av dina lagrings behov.
* Öka eller minska beräknings kraften i en SQL-pool (data lager) utan att flytta data.
* Pausa beräknings kapacitet och lämna data intakta, så du betalar bara för lagring.
* Återuppta beräkningskapacitet under driftstimmar.

### <a name="azure-storage"></a>Azure Storage

SQL Analytics utnyttjar Azure Storage för att hålla dina användar data säkra.  Eftersom dina data lagras och hanteras av Azure Storage finns det en separat avgift för lagrings användningen. Själva informationen är shardade i **distributioner** för att optimera systemets prestanda. Du kan välja vilket horisontell partitionering-mönster som ska användas för att distribuera data när du definierar tabellen. Dessa horisontell partitionering-mönster stöds:

* Hash
* Resursallokering
* Replikera

### <a name="control-node"></a>Kontrol lera nod

Noden kontroll är hjärna i arkitekturen. Det är den som är klientdelen som interagerar med alla program och anslutningar. MPP-motorn körs på noden kontroll för att optimera och koordinera parallella frågor. När du skickar en T-SQL-fråga till SQL Analytics omvandlar noden kontroll den till frågor som körs mot varje distribution parallellt.

### <a name="compute-nodes"></a>Compute-noder

Compute-noderna ger beräknings kraften. Distributioner mappar till Compute-noder för bearbetning. När du betalar för fler beräknings resurser mappar SQL Analytics om distributionerna till de tillgängliga Compute-noderna. Antalet datornoder sträcker sig från 1 till 60 och bestäms av Service nivån för SQL Analytics.

Varje Compute-nod har ett nod-ID som visas i systemvyer. Du kan se Compute Node ID genom att leta efter kolumnen node_id i systemvyer vars namn börjar med sys. PDW _nodes. En lista över dessa systemvyer finns i [MPP system views](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Data flyttnings tjänst
Data flyttnings tjänsten (DMS) är den data transport teknik som samordnar data förflyttningen mellan Compute-noderna. Vissa frågor kräver data förflyttning för att säkerställa att de parallella frågorna returnerar korrekta resultat. När data förflyttning krävs säkerställer DMS att rätt data får rätt plats. 

## <a name="distributions"></a>Distributioner

En distribution är den grundläggande enheten för lagring och bearbetning av parallella frågor som körs på distribuerade data. När SQL Analytics kör en fråga delas arbetet upp i 60 mindre frågor som körs parallellt. 

Var och en av de 60 mindre frågorna körs på en av data distributionerna. Varje Compute-nod hanterar en eller flera av 60-distributionerna. En SQL-pool med maximala beräknings resurser har en distribution per Compute-nod. En SQL-pool med minsta beräknings resurser har alla distributioner på en Compute-nod.  

## <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller
En hash-distribuerad tabell kan tillhandahålla högsta prestanda för kopplingar och agg regeringar för stora tabeller. 

För att Shard data till en hash-distribuerad tabell använder SQL Analytics en hash-funktion för att deterministiskt tilldela varje rad till en distribution. I tabell definitionen anges en av kolumnerna som distributions kolumn. Hash-funktionen använder värdena i kolumnen distribution för att tilldela varje rad till en distribution.

Följande diagram illustrerar hur en fullständig (icke-distribuerad tabell) lagras som en hash-distribuerad tabell. 

![Distribuerad tabell](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Distribuerad tabell")  

* Varje rad tillhör en distribution.  
* En deterministisk hash-algoritm tilldelar varje rad till en distribution.  
* Antalet tabell rader per distribution varierar så som de visas i olika tabell storlekar.

Det finns prestanda överväganden för valet av en distributions kolumn, till exempel distinkthet, data skevning och de typer av frågor som körs i systemet.

## <a name="round-robin-distributed-tables"></a>Fördelade resursallokering-tabeller
En Round Robin-tabell är den enklaste tabellen för att skapa och leverera snabba prestanda när den används som en mellanlagrings tabell för belastningar.

En fördelad Round-Robin distribuerar data jämnt över tabellen men utan ytterligare optimering. En distribution väljs först slumpmässigt och sedan är buffertar för rader kopplade till distributioner sekventiellt. Det går snabbt att läsa in data i en Round Robin-tabell, men det är ofta bättre att fråga prestanda med hash-distribuerade tabeller. Kopplingar i Round-Robin-tabeller kräver reshuffling-data och detta tar ytterligare tid.


## <a name="replicated-tables"></a>Replikerade tabeller
En replikerad tabell ger snabbast höga frågeresultat för små tabeller.

En tabell som replikeras cachelagrar en fullständig kopia av tabellen på varje Compute-nod. Därför tar replikerade tabeller bort behovet av att överföra data mellan datornoder före en koppling eller agg regering. Replikerade tabeller används bäst med små tabeller. Extra lagring krävs och det finns ytterligare kostnader som uppstår när du skriver data som gör stora tabeller opraktiska.  

Diagrammet nedan visar en replikerad tabell som cachelagras på den första distributionen på varje Compute-nod.  

![Replikerad tabell](media/sql-data-warehouse-distributed-data/replicated-table.png "Replikerad tabell") 

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om Azure-Synapse kan du lära dig hur du snabbt [skapar en SQL-pool][create a SQL pool] och [läser in exempel data][load sample data]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp eftersom du kan stöta på ny terminologi. Eller titta på några av de andra Azure Synapse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Skapa ett supportärende]
* [MSDN-forum]
* [Stack Overflow-forum]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Skapa ett supportärende]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL pool]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
