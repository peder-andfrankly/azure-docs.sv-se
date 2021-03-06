---
title: Distributions kvoten överskreds
description: Beskriver hur du löser problemet med fler än 800 distributioner i resurs grupps historiken.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 7f389827513562a3add67f022fec360081754b02
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147204"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Lös fel när antalet distributioner överskrider 800

Varje resurs grupp är begränsad till 800 distributioner i distributions historiken. I den här artikeln beskrivs det fel som du får när en distribution Miss lyckas eftersom den skulle överskrida de tillåtna 800-distributionerna. Lös problemet genom att ta bort distributioner från resurs grupps historiken. Att ta bort en distribution från historiken påverkar inte några av de distribuerade resurserna.

## <a name="symptom"></a>Symptom

Under distributionen får du ett fel meddelande om att den aktuella distributionen kommer att överskrida kvoten på 800-distributioner.

## <a name="solution"></a>Lösning

### <a name="azure-cli"></a>Azure CLI

Använd kommandot [AZ Group Deployment Delete](/cli/azure/group/deployment#az-group-deployment-delete) för att ta bort distributioner från historiken.

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

Om du vill ta bort alla distributioner som är äldre än fem dagar använder du:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az group deployment list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az group deployment delete --resource-group exampleGroup --name $deployment
done
```

Du kan hämta det aktuella antalet i distributions historiken med följande kommando:

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Använd kommandot [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) för att ta bort distributioner från historiken.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Om du vill ta bort alla distributioner som är äldre än fem dagar använder du:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Du kan hämta det aktuella antalet i distributions historiken med följande kommando:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Lösningar från tredje part

Följande externa lösningar riktar sig till vissa scenarier:

* [Azure Logic Apps-och PowerShell-lösningar](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps-tillägg](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
