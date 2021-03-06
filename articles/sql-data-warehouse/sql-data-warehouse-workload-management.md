---
title: Arbetsbelastningshantering
description: Vägledning för att implementera arbets belastnings hantering i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 10/30/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 2563ea4ab498c11c846cfe79f0e668f7d491c2e7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692354"
---
# <a name="what-is-workload-management"></a>Vad är arbets belastnings hantering?

Att köra blandade arbets belastningar kan medföra resurs utmaningar på upptagna system.  Lösnings arkitekter söker efter olika klassiska data lager aktiviteter (till exempel att läsa in, transformera och fråga data) för att se till att det finns tillräckligt med resurser för att kunna trycka service avtal.  

Den fysiska server isoleringen kan leda till en infrastruktur som är underutnyttjad, förbokad eller i ett tillstånd där cacheminnen ständigt definieras med maskin vara som startar och stoppar.  Ett lyckat hanterings schema för arbets belastningen hanterar resurser, garanterar mycket effektiv resursutnyttjande och maximerar avkastningen på investeringen (ROI).

Ett informations lager arbets belastning avser alla åtgärder som utförs i förhållande till ett informations lager. Djupet och bredden på dessa komponenter är beroende av data lagrets förfallo nivå.  Arbets belastningen för informations lagret omfattar: 
- Hela processen med att läsa in data till lagret 
- Utföra analys och rapportering av data lager
- Hantera data i informations lagret 
- Exportera data från data lagret

Prestanda kapaciteten för ett informations lager bestäms av [data lager enheterna](what-is-a-data-warehouse-unit-dwu-cdwu.md).
- Om du vill visa de resurser som har allokerats för alla prestanda profiler, se [minnes-och samtidiga gränser] minnes-samtidighets-limits.md).
- För att justera kapaciteten kan du [skala upp eller ned](quickstart-scale-compute-portal.md).


## <a name="workload-management-concepts"></a>Koncept för arbets belastnings hantering
Tidigare hanterade du frågeresultatet på SQL Data Warehouse via [resurs klasser](resource-classes-for-workload-management.md).  Resurs klasser som tillåts att tilldela minne till en fråga baserat på roll medlemskap.  Den primära utmaningen med resurs klasser är att, när den har kon figurer ATS, inte har någon styrning eller möjlighet att styra arbets belastningen.  

Om du till exempel beviljar ett ad hoc användar roll medlemskap till smallrc tillåts användaren att använda 100% av minnet i systemet.  Med resurs klasser finns det inget sätt att reservera och se till att resurserna är tillgängliga för kritiska arbets belastningar.

Arbets belastnings hantering på SQL Data Warehouse består av tre koncept på hög nivå: [arbets belastnings klassificering](sql-data-warehouse-workload-classification.md), [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md) och [arbets belastnings isolering](sql-data-warehouse-workload-isolation.md).  Med de här funktionerna får du mer kontroll över hur arbets belastningen använder system resurser.

Arbets belastnings klassificering är begreppet att tilldela en begäran till en arbets belastnings grupp och ange prioritets nivåer.  Tidigare genomfördes den här tilldelningen via roll medlemskap med [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Nu kan du göra det via [CLASSIFER skapa arbets belastning](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  Klassificerings funktionen ger en mer omfattande uppsättning alternativ, till exempel etikett, session och tid för klassificering av begär Anden.

Arbets belastnings prioriteten påverkar i vilken ordning en begäran får åtkomst till resurser.  På ett upptaget system har en begäran med högre prioritet först åtkomst till resurser.  Prioriteten kan också se till att åtkomsten till lås har beställts. 

Arbets belastnings isolering reserverar resurser för en arbets belastnings grupp.  Resurser som är reserverade i en arbets belastnings grupp hålls exklusivt för den arbets belastnings gruppen för att säkerställa körningen.  Med arbets belastnings grupper kan du också definiera mängden resurser som tilldelas per begäran, ungefär som resurs klasser gör.  Arbets belastnings grupper ger dig möjlighet att reservera eller lägga till en mängd resurser som en uppsättning begär Anden kan förbruka.  Slutligen är arbets belastnings grupper en mekanism för att tillämpa regler, t. ex. tids gräns för frågor, för förfrågningar.  


## <a name="next-steps"></a>Nästa steg

- Mer information om arbets belastnings klassificering finns i avsnittet om [arbets belastnings klassificering](sql-data-warehouse-workload-classification.md).  
- Mer information om arbets belastnings isolering finns i [arbets belastnings isolering](sql-data-warehouse-workload-isolation.md).  
- Mer information om arbets belastnings prioritet finns i [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md).  