---
title: Hanterad instans-tidpunkts återställning
description: Återställa en SQL-databas i en hanterad instans till en tidigare tidpunkt.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: b106b1da5d012309e8d92c8e9555ee3982602e12
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707654"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Återställa en SQL-databas i en hanterad instans till en tidigare tidpunkt

Använd tidpunkts återställning (PITR) för att skapa en databas som en kopia av en annan databas från en tidigare tidpunkt. Den här artikeln beskriver hur du utför en tidpunkts återställning av en databas i en Azure SQL Database Hanterad instans.

Återställning efter tidpunkt är användbart i återställnings scenarier, t. ex. incidenter orsakade av fel, felaktiga inlästa data eller borttagning av viktiga data. Du kan också använda det bara för testning eller granskning. Säkerhetskopierade filer sparas i 7 till 35 dagar, beroende på dina databas inställningar.

Återställning av tidpunkt kan:

- Återställ en databas från en befintlig databas.
- Återställ en databas från en borttagen databas.

För en hanterad instans kan du även använda tidpunkts återställning.

- Återställ en databas till samma hanterade instans.
- Återställ en databas till en annan hanterad instans.

> [!NOTE]
> Det går inte att återställa punkt-till-tid för en hel hanterad instans. Den här artikeln förklarar bara vad som är möjligt: återställning vid olika tidpunkter av en databas som finns på en hanterad instans.

## <a name="limitations"></a>Begränsningar

När du återställer från en hanterad instans till en annan måste båda instanserna finnas i samma prenumeration och region. Återställning mellan regioner och över prenumerationer stöds inte för närvarande.

> [!WARNING]
> Tänk på lagrings storleken för din hanterade instans. Beroende på storleken på de data som ska återställas kan du få slut på instans lagring. Om det inte finns tillräckligt med utrymme för återställda data kan du använda en annan metod.

I följande tabell visas scenarier för återställning av tidpunkter för hanterade instanser:

|           |Återställ en befintlig databas till samma hanterade instans| Återställ befintlig databas till en annan hanterad instans|Återställ utelämnad databas till samma hanterade instans|Återställ släppt databas till en annan hanterad instans|
|:----------|:----------|:----------|:----------|:----------|
|**Azure-portalen**| Ja|Nej |Nej|Nej|
|**Azure CLI**|Ja |Ja |Nej|Nej|
|**PowerShell**| Ja|Ja |Ja|Ja|

## <a name="restore-an-existing-database"></a>Återställa en befintlig databas

Återställ en befintlig databas till samma instans med hjälp av Azure Portal, PowerShell eller Azure CLI. Om du vill återställa en databas till en annan instans använder du PowerShell eller Azure CLI så att du kan ange egenskaperna för den hanterade mål instansen och resurs gruppen. Om du inte anger dessa parametrar kommer databasen att återställas till den befintliga instansen som standard. Azure Portal har för närvarande inte stöd för återställning till en annan instans.

# <a name="portaltabazure-portal"></a>[Portalen](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Gå till din hanterade instans och välj den databas som du vill återställa.
3. Välj **Återställ** på databas sidan:

    ![Återställa en databas med hjälp av Azure Portal](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. På sidan **Återställ** väljer du punkten för det datum och den tid som du vill återställa databasen till.
5. Välj **Bekräfta** för att återställa databasen. Den här åtgärden startar återställnings processen, som skapar en ny databas och fyller den med data från den ursprungliga databasen vid den angivna tidpunkten. Mer information om återställnings processen finns i [återställnings tid](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Om du inte redan har Azure PowerShell installerat, se [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

Om du vill återställa-databasen med hjälp av PowerShell anger du värdena för parametrarna i följande kommando. Kör sedan kommandot:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Om du vill återställa databasen till en annan hanterad instans anger du även namnen på mål resurs gruppen och den hanterade instansen:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Mer information finns i [restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du inte redan har installerat Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Om du vill återställa databasen med hjälp av Azure CLI anger du värdena för parametrarna i följande kommando. Kör sedan kommandot:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Om du vill återställa databasen till en annan hanterad instans anger du även namnen på mål resurs gruppen och den hanterade instansen:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

En detaljerad förklaring av tillgängliga parametrar finns i [CLI-dokumentationen för att återställa en databas i en hanterad instans](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas

Det går bara att återställa en borttagen databas med hjälp av PowerShell. Databasen kan återställas till samma instans eller till en annan instans.

Om du vill återställa en borttagen databas med hjälp av PowerShell anger du värdena för parametrarna i följande kommando. Kör sedan kommandot:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Om du vill återställa den borttagna databasen till en annan instans ändrar du namnen på resurs gruppen och den hanterade instansen. Kontrol lera också att plats parametern matchar platsen för resurs gruppen och den hanterade instansen.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-an-existing-database"></a>Skriv över en befintlig databas

Om du vill skriva över en befintlig databas måste du:

1. Ta bort den befintliga databasen som du vill skriva över.
2. Byt namn på databasen för den tidpunkt som återställs till namnet på den databas som du har släppt.

### <a name="drop-the-original-database"></a>Ta bort den ursprungliga databasen

Du kan släppa databasen med hjälp av Azure Portal, PowerShell eller Azure CLI.

Du kan också släppa databasen genom att ansluta till den hanterade instansen direkt, starta SQL Server Management Studio (SSMS) och sedan köra följande Transact-SQL-kommando (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Använd någon av följande metoder för att ansluta till databasen i den hanterade instansen:

- [SSMS/Azure Data Studio via en virtuell Azure-dator](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt-till-plats](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Offentlig slut punkt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portalen](#tab/azure-portal)

I Azure Portal väljer du databasen från den hanterade instansen och väljer sedan **ta bort**.

   ![Ta bort en databas med hjälp av Azure Portal](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande PowerShell-kommando för att släppa en befintlig databas från en hanterad instans:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande Azure CLI-kommando för att släppa en befintlig databas från en hanterad instans:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Ändra det nya databas namnet så att det matchar det ursprungliga databas namnet

Anslut direkt till den hanterade instansen och starta SQL Server Management Studio. Kör sedan följande Transact-SQL-fråga (T-SQL). Frågan kommer att ändra namnet på den återställda databasen till den för den borttagna databas som du tänker skriva över.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Använd någon av följande metoder för att ansluta till databasen i den hanterade instansen:

- [Virtuell Azure-dator](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt-till-plats](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Offentlig slut punkt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [automatiserade säkerhets kopieringar](sql-database-automated-backups.md).
