---
title: Arbeta med matriser och objekt i Azure Cosmos DB
description: Lär dig SQL-syntaxen för att skapa matriser och objekt i Azure Cosmos DB. Den här artikeln innehåller också några exempel på hur du utför åtgärder på mat ris objekt
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870929"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Arbeta med matriser och objekt i Azure Cosmos DB

En viktig funktion i Azure Cosmos DB SQL API är matris-och objekt skapande.

## <a name="arrays"></a>Matriser

Du kan skapa matriser, som du ser i följande exempel:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Resultatet är:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

Du kan också använda [mat ris uttrycket](sql-query-subquery.md#array-expression) för att skapa en matris från under [frågans](sql-query-subquery.md) resultat. Den här frågan hämtar alla distinkta namn på underordnade objekt i en matris.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iteration

SQL API ger stöd för att iterera över JSON-matriser, med en ny konstruktion som lagts till via [nyckelordet](sql-query-keywords.md#in) i från-källan. Se följande exempel:

```sql
    SELECT *
    FROM Families.children
```

Resultatet är:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Nästa fråga utför iteration över `children` i `Families` container. Den utgående matrisen skiljer sig från föregående fråga. I det här exemplet delas `children`och sammanslags resultaten i en enda matris:  

```sql
    SELECT *
    FROM c IN Families.children
```

Resultatet är:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Du kan filtrera efter varje enskild post i matrisen, som du ser i följande exempel:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Resultatet är:

```json
    [{
      "givenName": "Lisa"
    }]
```

Du kan också aggregera över resultatet av en mat ris iteration. Följande fråga räknar till exempel antalet underordnade objekt bland alla familjer:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Resultatet är:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Kopplingar](sql-query-join.md)