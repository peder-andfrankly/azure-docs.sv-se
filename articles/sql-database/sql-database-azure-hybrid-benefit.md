---
title: Azure Hybrid-förmån
description: Använd befintliga SQL Server licenser för SQL Database rabatter.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: 394c674da63bbda643246c3d61fb670d6ac8d1f7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928623"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

I den etablerade beräknings nivån för den vCore-baserade inköps modellen kan du byta ut dina befintliga licenser för rabatterade priser på SQL Database genom att använda [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Med den här Azure-förmånen kan du Spara upp till 30 procent på Azure SQL Database genom att använda dina lokala SQL Server licenser med Software Assurance. 

> [!NOTE]
> Att ändra till Azure Hybrid-förmån kräver ingen stillestånds tid.

![priset](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Välj en licens modell

Med Azure Hybrid-förmån kan du välja att bara betala för den underliggande Azure-infrastrukturen genom att använda din befintliga SQL Server-licens för själva SQL Database-motorn (bas beräknings priser) eller så kan du betala för både den underliggande infrastrukturen och SQL Server licens (licens pris ingår).

Du kan välja eller ändra licensierings modellen med hjälp av Azure Portal eller genom att använda någon av följande API: er:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ange eller uppdatera licens typen med hjälp av PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Ange eller uppdatera licens typen med hjälp av Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [AZ SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create)
- [AZ SQL mi-uppdatering](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-apitabrest"></a>[REST API](#tab/rest)

Ange eller uppdatera licens typen genom att använda REST API:

- [Databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate)
- [Databaser – uppdatera](/rest/api/sql/databases/update)
- [Hanterade instanser – skapa eller uppdatera](/rest/api/sql/managedinstances/createorupdate)
- [Hanterade instanser – uppdatera](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Nästa steg

- För att välja mellan SQL Database distributions alternativ, se [välja rätt distributions alternativ i Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- En jämförelse av SQL Database funktioner finns i [Azure SQL Database funktioner](sql-database-features.md).
