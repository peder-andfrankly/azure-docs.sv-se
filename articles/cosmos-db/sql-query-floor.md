---
title: BASYTA i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen VÅNINGs SQL-system i Azure Cosmos DB att returnera det största heltal som är mindre än eller lika med det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8faf223a4871f6f109f9d133716989b1bcfdee08
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871184"
---
# <a name="floor-azure-cosmos-db"></a>BASYTA (Azure Cosmos DB)
 Returnerar det största heltalsvärdet som är mindre än eller lika med det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas positiva numeriska värden, negativa värden och nollvärden med funktionen `FLOOR`.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
