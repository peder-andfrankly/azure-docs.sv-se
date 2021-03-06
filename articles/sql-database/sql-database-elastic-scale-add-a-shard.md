---
title: Lägga till en Shard med Elastic Database-verktyg
description: 'Använda API: er för elastisk skalning för att lägga till nya Shards i en Shard-uppsättning.'
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 4043fd374a314735173a1f07f46c8394592b81e2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823719"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Lägga till en Shard med hjälp av Elastic Database verktyg

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Lägga till en Shard för ett nytt intervall eller en ny nyckel

Program behöver ofta lägga till nya Shards för att hantera data som förväntas från nya nycklar eller nyckel intervall, för en Shard-karta som redan finns. Ett program shardade efter klient-ID kan till exempel behöva etablera en ny Shard för en ny klient, annars kan data shardade kräva en ny Shard som etableras innan varje ny månad börjar.

Om det nya intervallet med nyckel värden inte redan ingår i en befintlig mappning är det enkelt att lägga till den nya Shard och koppla den nya nyckeln eller intervallet till Shard.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exempel: lägga till en Shard och dess intervall till en befintlig Shard-karta

Det här exemplet använder TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.net](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -metoder och skapar en instans av ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) lektion. I exemplet nedan har en databas med namnet **sample_shard_2** och alla nödvändiga schema objekt i den skapats för att innehålla intervallet [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

För .NET-versionen kan du också använda PowerShell som ett alternativ till att skapa en ny Shard Map Manager. Ett exempel finns [här](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Lägga till en Shard för en tom del av ett befintligt intervall

I vissa fall kanske du redan har mappat ett intervall till en Shard och delvis fyllt det med data, men nu vill du att kommande data ska dirigeras till en annan Shard. Exempel: du Shard per dags intervall och redan har tilldelat 50 dagar till en Shard, men på dag 24 vill du att framtida data ska landa i en annan Shard. [Verktyget för delning och sammanslagning](sql-database-elastic-scale-overview-split-and-merge.md) av elastiska databaser kan utföra den här åtgärden, men om data flytten inte är nödvändig (till exempel data för intervallet med dagar [25, 50), det 50 vill säga den 25: e som är den 25 API: er för Shard Map Management direkt.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exempel: dela ett intervall och tilldela den tomma delen till en nyligen tillagd Shard

En databas med namnet "sample_shard_2" och alla nödvändiga schema objekt i den har skapats.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Viktigt**: Använd bara den här tekniken om du är säker på att intervallet för den uppdaterade mappningen är tomt.  Föregående metoder kontrollerar inte data för det område som flyttas, så det är bäst att inkludera kontroller i koden.  Om det finns rader i det område som flyttas matchar den faktiska data distributionen inte den uppdaterade Shard-kartan. Använd [verktyget Dela-sammanslagning](sql-database-elastic-scale-overview-split-and-merge.md) för att utföra åtgärden i stället i dessa fall.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
