---
title: Gränser för Azure Cosmos DB Gremlin
description: Referens dokumentation för körnings begränsningar i diagram motor
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029843"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin-gränser
Den här artikeln beskriver gränserna för Azure Cosmos DB Gremlin-motorn och förklarar hur de kan påverka kund bläddringskontroll.

Cosmos DB Gremlin skapas ovanpå Cosmos DB-infrastruktur. På grund av detta gäller alla gränser som beskrivits i [Azure Cosmos DB tjänst gränser](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) fortfarande. 

## <a name="limits"></a>Begränsningar

När Gremlin-gränsen uppnås avbryts Traversal med en **x-MS-status-kod** på 429 som indikerar ett begränsnings fel. Mer information finns i [Gremlin-serverns svars rubriker](gremlin-limits.md) .

**Resurs**    | **Standardgräns** | **Förklaring**
--- | --- | ---
*Skriptets längd* | **64 KB** | Maximal längd för ett Gremlin-bläddringsskript per begäran.
*Operatorns djup* | **400** |  Totalt antal unika steg i en bläddring. @No__t-0 har till exempel ett antal operatorer 2: V () och out (), ```g.V('label').repeat(out()).times(100)``` har ett operatörs djup på 3: V (), REPEAT () och out () eftersom ```.times(100)``` är en parameter till ```.repeat()```-operator.
*Grad av parallellitet* | **32** | Maximalt antal lagringspartitioner i en enskild förfrågan till lagringsskiktet. Grafer med hundratals partitioner påverkas av den här gränsen.
*Upprepningsgräns* | **32** | Maximalt antal iterationer en ```.repeat()```-operator kan köra. Varje iteration av ```.repeat()```-steget i de flesta fall kör bredd-första genom gång, vilket innebär att alla traverser är begränsade till högst 32 hopp mellan hörnen.
*Tidsgräns för bläddring* | **30 sekunder** | Traversal avbryts när den är för tillfället. Cosmos DB Graph är en OLTP-databas där ett stort antal bläddringar kan slutföras på bara några millisekunder. Om du vill köra OLAP-frågor i Cosmos DB Graph använder du [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) med [diagram Data ramar](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) och [Cosmos DB Spark-anslutning](https://github.com/Azure/azure-cosmosdb-spark).
*Tidsgräns för vilande anslutning* | **1 timme** | Hur lång tid som Gremlin-tjänsten ska hålla inaktiva WebSocket-anslutningar öppna. TCP Keep-Alive-paket eller HTTP Keep-Alive-begäranden utökar inte anslutnings livs längd utöver den här gränsen. Cosmos DB Graph-motor anser att WebSocket-anslutningar är inaktiva om inga aktiva Gremlin-begäranden körs på den.
*Resurstoken per timme* | **100** | Antal unika resurstoken som Gremlin-klienter använder till att ansluta till Gremlin-kontot i en region. När programmet överskrider den unika kvoten för token kommer `"Exceeded allowed resource token limit of 100 that can be used concurrently"` att returneras vid nästa autentiseringsbegäran.

## <a name="next-steps"></a>Nästa steg
* [Azure Cosmos DB Gremlin-svarshuvuden](gremlin-headers.md) 
* [Azure Cosmos DB-resurspooler med Gremlin](how-to-use-resource-tokens-gremlin.md)
