---
title: SELECT-sats i Azure Cosmos DB
description: Läs om SQL SELECT-satsen för Azure Cosmos DB. Använd SQL som Azure Cosmos DB JSON-frågespråk.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: b90fc6f1f50ec2ea75619188cca36f78061f28df
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326795"
---
# <a name="select-clause-in-azure-cosmos-db"></a>SELECT-sats i Azure Cosmos DB

Varje fråga består av en SELECT-sats och [valfria from](sql-query-from.md) -och [WHERE](sql-query-where.md) -satser, enligt ANSI SQL-standarder. Normalt räknas källan i from-satsen och WHERE-satsen använder ett filter på källan för att hämta en delmängd av JSON-objekt. SELECT-satsen projekterar sedan de begärda JSON-värdena i SELECT-listan.

## <a name="syntax"></a>Syntax

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Argument
  
- `<select_specification>`  

  Egenskaper eller värde som ska väljas för resultat uppsättningen.  
  
- `'*'`  

  Anger att värdet ska hämtas utan att göra några ändringar. I synnerhet om det bearbetade värdet är ett objekt hämtas alla egenskaper.  
  
- `<object_property_list>`  
  
  Anger listan över egenskaper som ska hämtas. Varje returnerat värde är ett objekt med de angivna egenskaperna.  
  
- `VALUE`  

  Anger att JSON-värdet ska hämtas i stället för hela JSON-objektet. Detta, till skillnad från `<property_list>`, radbryts inte det beräknade värdet i ett objekt.  
 
- `DISTINCT`
  
  Anger att dubbletter av projekterade egenskaper ska tas bort.  

- `<scalar_expression>`  

  Uttryck som representerar det värde som ska beräknas. Mer information finns i avsnittet om [skalära uttryck](sql-query-scalar-expressions.md) .  

## <a name="remarks"></a>Kommentarer

Syntaxen för `SELECT *` är endast giltig om FROM-satsen har deklarerat exakt ett alias. `SELECT *` ger en identitets projektion, vilket kan vara användbart om ingen projektion behövs. SELECT * är bara giltig om FROM-satsen anges och endast en enda indatakälla har introducerats.  
  
Både `SELECT <select_list>` och `SELECT *` är "syntaktisk socker" och kan uttryckas i enkla SELECT-uttryck som visas nedan.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   motsvarar:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   motsvarar:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exempel

Följande exempel på URVALs fråga returnerar `address` från `Families` vars `id` matchar `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultaten är:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Accessor för citerad egenskap
Du kan komma åt egenskaper med operatorn för citerad egenskap []. Till exempel är `SELECT c.grade` och `SELECT c["grade"]` likvärdiga. Den här syntaxen är användbar för att undvika en egenskap som innehåller blank steg, specialtecken eller har samma namn som ett SQL-nyckelord eller reserverat ord.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Kapslade egenskaper

I följande exempel projekterar två kapslade egenskaper, `f.address.state` och `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultaten är:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>JSON-uttryck

Projektion stöder även JSON-uttryck, som du ser i följande exempel:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultaten är:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

I föregående exempel måste SELECT-satsen skapa ett JSON-objekt och eftersom exemplet inte innehåller någon nyckel använder satsen det implicita argumentet variabel namn `$1`. Följande fråga returnerar två implicita argument-variabler: `$1` och `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultaten är:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE-sats](sql-query-where.md)