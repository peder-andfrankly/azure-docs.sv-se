---
title: Vad är en enskild databas
description: Lär dig mer om en databas i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: 7b084954cb53d19739a0f633661e0c76fa82dd13
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820946"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Vad är en enskild databas i Azure SQL Database

Med distributions alternativet för enskild databas skapas en databas i Azure SQL Database med en egen uppsättning resurser och hanteras via en SQL Database Server. Med en enda databas är varje databas isolerad från varandra och portabel, var och en med sin egen tjänst nivå inom den [DTU-baserade inköps modellen](sql-database-service-tiers-dtu.md) eller [vCore inköps modell](sql-database-service-tiers-vcore.md) och en garanterad beräknings storlek.

> [!IMPORTANT]
> En enkel databas är ett av tre distributions alternativ för Azure SQL Database. De andra två är [elastiska pooler](sql-database-elastic-pool.md) och en [hanterad instans](sql-database-managed-instance.md).
> [!NOTE]
> En ord lista med termer i Azure SQL Database finns i [ord lista för SQL Database termer](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dynamisk skalbarhet

Du kan bygga din första app på en liten, enkel databas till låg kostnad på Server lös beräknings nivå eller en liten beräknings storlek i den allokerade beräknings nivån. Du kan ändra [beräknings-eller tjänst nivå](sql-database-single-database-scale.md) manuellt eller program mässigt när som helst för att uppfylla lösningens behov. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

## <a name="single-databases-and-elastic-pools"></a>Enkla databaser och elastiska pooler

En enskild databas kan flyttas till eller från en [elastisk pool](sql-database-elastic-pool.md) för resurs delning. För många företag och appar räcker det att kunna skapa enkla databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. Elastiska pooler är utformade för att lösa det här problemet. Konceptet är enkelt. Du allokerar prestanda resurser till en pool i stället för en enskild databas och betalar för de sammanslagna prestanda resurserna i poolen snarare än för enkel databas prestanda.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Du använder de inbyggda verktygen för [prestanda övervakning](sql-database-performance.md) och [avisering](sql-database-insights-alerts-portal.md), kombinerat med prestanda klassificeringen. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner baserat på dina aktuella eller projekterade prestandakrav. Dessutom kan SQL-databasen [skapa mått och diagnostikloggar](sql-database-metrics-diag-logging.md) för lättare övervakning.

## <a name="availability-capabilities"></a>Kapacitet för tillgänglighet

Enskilda databaser, elastiska pooler och hanterade instanser ger alla tillgänglighets egenskaper. Mer information finns i [tillgänglighets egenskaper](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Transact-SQL-skillnader

De flesta Transact-SQL-funktioner som program använder stöds fullt ut i både Microsoft SQL Server och Azure SQL Database. Till exempel fungerar kärn SQL-komponenter, till exempel data typer, operatorer, strängar, aritmetiska, logiska och markör funktioner, på samma sätt i SQL Server och SQL Database. Det finns dock några skillnad i T-SQL i DDL (Data Definition Language) och DML-element (Data Manipulation Language) som resulterar i T-SQL-uttryck och frågor som bara delvis stöds (som vi diskuterar senare i den här artikeln).
Dessutom finns det vissa funktioner och syntax som inte stöds alls eftersom Azure SQL Database är utformad för att isolera funktioner från beroenden i huvud databasen och operativ systemet. Därför är de flesta server nivå aktiviteter olämpliga för SQL Database. T-SQL-uttryck och alternativ är inte tillgängliga om de konfigurerar alternativ på server nivå, operativ system komponenter eller ange fil system konfiguration. När sådana funktioner krävs är ett lämpligt alternativ ofta tillgängligt på något annat sätt från SQL Database eller från en annan Azure-funktion eller-tjänst.

Mer information finns i [lösa skillnader i Transact-SQL under migrering till SQL Database](sql-database-transact-sql-information.md).

## <a name="security"></a>Säkerhet

SQL Database innehåller en uppsättning inbyggda funktioner för [säkerhet och efterlevnad](sql-database-security-overview.md) som hjälper ditt program att uppfylla olika krav på säkerhet och efterlevnad.

> [!IMPORTANT]
> Azure SQL Database (alla distributions alternativ) har certifierats mot ett antal efterlevnads standarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den mest aktuella listan med SQL Database certifierings certifieringar.

## <a name="next-steps"></a>Nästa steg

- Om du snabbt vill komma igång med en enda databas börjar du med snabb starts [guiden för databas](sql-database-single-database-quickstart-guide.md).
- Information om hur du migrerar en SQL Server-databas till Azure finns i [migrera till Azure SQL Database](sql-database-single-database-migrate.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).
