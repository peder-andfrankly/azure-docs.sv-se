---
title: CLI-exempel – Lägg till en enskild databas i gruppen redundans-Azure SQL Database
description: Exempel skript för Azure CLI för att skapa en Azure SQL Database enskild databas, lägga till den i en grupp för redundans och testa redundans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 8e3c525230c3de530a93bd61a9227e9a4d7ed10b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933416"
---
# <a name="use-cli-to-add-an-azure-sql-database-single-database-into-a-failover-group"></a>Använd CLI för att lägga till en Azure SQL Database enkel databas i en failover-grupp

Detta exempel på PowerShell-skript skapar en enda databas, skapar en grupp för växling vid fel, lägger till databasen i den och testar redundansväxlingen. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ-konto uppsättning](/cli/azure/account?view=azure-cli-latest#az-account-set) | Anger att en prenumeration är den aktuella aktiva prenumerationen. | 
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [AZ SQL Server-brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule) | Skapar en servers brand Väggs regler. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Skapar en databas. | 
| [AZ SQL-redundans – grupp skapa](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Skapar en grupp för redundans. | 
| [AZ SQL-redundans – grupp lista](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Visar en lista över failover-grupper på en server. |
| [AZ SQL-redundans – grupp uppsättning – primär](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Ange den primära gruppen för redundans genom att redundansväxla alla databaser från den aktuella primära servern. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../sql-database-cli-samples.md).
