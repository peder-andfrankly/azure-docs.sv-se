---
title: Lås resurser för att förhindra ändringar
description: Förhindra att användare uppdaterar eller tar bort kritiska Azure-resurser genom att använda ett lås för alla användare och roller.
ms.topic: conceptual
ms.date: 05/14/2019
ms.openlocfilehash: 5dd5d5f58e13039842dca85ca65d6a26ce54c7e5
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150771"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Lås resurser för att förhindra oväntade ändringar

Som administratör kan du behöva låsa en prenumeration, resurs grupp eller resurs för att förhindra att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser. Du kan ange låsnivån till **CanNotDelete** eller **ReadOnly**. I portalen kallas låsen **Delete** och **Read Only** .

* **CanNotDelete** innebär att behöriga användare fortfarande kan läsa och ändra en resurs, men de kan inte ta bort resursen. 
* **ReadOnly** innebär att auktoriserade användare kan läsa en resurs, men de kan inte ta bort eller uppdatera resursen. Att använda det här låset liknar att begränsa alla behöriga användare till de behörigheter som har beviljats av rollen **läsare** . 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Hur låsen används

När du använder ett lås vid en överordnad omfattning ärver alla resurser inom den omfattningen samma lås. Även resurser som du lägger till senare ärver låset från det överordnade objektet. Det mest restriktiva låset i arv prioriteras.

Till skillnad från rollbaserad åtkomst kontroll använder du hanterings lås för att tillämpa en begränsning för alla användare och roller. Information om hur du ställer in behörigheter för användare och roller finns i [Azure Role-based Access Control](../role-based-access-control/role-assignments-portal.md).

Resource Manager-lås gäller endast för åtgärder som sker i hanterings planet, som består av åtgärder som skickas till `https://management.azure.com`. Låsen begränsar inte hur resurser utför sina egna funktioner. Resurs ändringarna är begränsade, men resurs åtgärderna är inte begränsade. Ett skrivskyddat lås på en SQL Database hindrar dig till exempel från att ta bort eller ändra databasen. Det hindrar dig inte från att skapa, uppdatera eller ta bort data i databasen. Data transaktioner är tillåtna eftersom dessa åtgärder inte skickas till `https://management.azure.com`.

Att använda **ReadOnly** kan leda till oväntade resultat eftersom vissa åtgärder som inte verkar ändra resursen verkligen kräver åtgärder som blockeras av låset. Det **skrivskyddade** låset kan tillämpas på resursen eller resurs gruppen som innehåller resursen. Några vanliga exempel på åtgärder som blockeras av ett **skrivskyddat** lås är:

* Ett **skrivskyddat** lås på ett lagrings konto förhindrar att alla användare visar nycklarna. Åtgärden lista nycklar hanteras via en POST-begäran eftersom de returnerade nycklarna är tillgängliga för Skriv åtgärder.

* Ett **skrivskyddat** lås på en app service resurs förhindrar att Visual Studio-Server Explorer visar filer för resursen, eftersom denna interaktion kräver skriv åtkomst.

* Ett **skrivskyddat** lås på en resurs grupp som innehåller en virtuell dator hindrar alla användare från att starta eller starta om den virtuella datorn. De här åtgärderna kräver en POST-begäran.

## <a name="who-can-create-or-delete-locks"></a>Vem kan skapa eller ta bort lås
Om du vill skapa eller ta bort hanterings lås måste du ha åtkomst till `Microsoft.Authorization/*` eller `Microsoft.Authorization/locks/*` åtgärder. Av de inbyggda rollerna har endast **Ägare** och **Administratör för användaråtkomst** åtkomst till dessa åtgärder.

## <a name="managed-applications-and-locks"></a>Hanterade program och lås

Vissa Azure-tjänster, till exempel Azure Databricks, använder [hanterade program](../managed-applications/overview.md) för att implementera tjänsten. I så fall skapar tjänsten två resurs grupper. En resurs grupp innehåller en översikt över tjänsten och är inte låst. Den andra resurs gruppen innehåller infrastrukturen för tjänsten och är låst.

Om du försöker ta bort infrastruktur resurs gruppen får du ett fel meddelande om att resurs gruppen är låst. Om du försöker ta bort låset för infrastruktur resurs gruppen får du ett fel meddelande om att låset inte kan tas bort eftersom det ägs av ett system program.

Ta i stället bort tjänsten, som också tar bort infrastruktur resurs gruppen.

För hanterade program väljer du den tjänst som du har distribuerat.

![Välj tjänst](./media/resource-group-lock-resources/select-service.png)

Observera att tjänsten innehåller en länk till en **hanterad resurs grupp**. Resurs gruppen innehåller infrastrukturen och är låst. Den kan inte tas bort direkt.

![Visa hanterad grupp](./media/resource-group-lock-resources/show-managed-group.png)

Om du vill ta bort allt för tjänsten, inklusive resurs gruppen låst infrastruktur, väljer du **ta bort** för tjänsten.

![Ta bort tjänst](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Mall

När du använder en Resource Manager-mall för att distribuera ett lås, använder du olika värden för namn och typ beroende på låsets omfång.

Använd följande format när du använder ett lås på en **resurs**:

* namn – `{resourceName}/Microsoft.Authorization/{lockName}`
* typ `{resourceProviderNamespace}/{resourceType}/providers/locks`

Använd följande format när du använder ett lås till en **resurs grupp** eller **prenumeration**:

* namn – `{lockName}`
* typ `Microsoft.Authorization/locks`

I följande exempel visas en mall som skapar en app service-plan, en webbplats och ett lås på webbplatsen. Resurs typen för låset är resurs typen för den resurs som ska låsas och **/providers/Locks**. Namnet på låset skapas genom att resurs namnet kombineras med **/Microsoft.Authorization/** och namnet på låset.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Ett exempel på att ställa in ett lås på en resurs grupp finns i [skapa en resurs grupp och låsa den](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Du låser distribuerade resurser med Azure PowerShell med kommandot [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) .

Om du vill låsa en resurs anger du namnet på resursen, resurs typ och dess resurs grupp namn.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Ange namnet på resurs gruppen om du vill låsa en resurs grupp.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Om du vill ha information om ett lås använder du [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Använd följande för att hämta alla Lås i din prenumeration:

```azurepowershell-interactive
Get-AzResourceLock
```

Om du vill hämta alla Lås för en resurs använder du:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Om du vill hämta alla Lås för en resurs grupp använder du:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Om du vill ta bort ett lås använder du:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Du låser distribuerade resurser med Azure CLI med hjälp av kommandot [AZ lock Create](/cli/azure/lock#az-lock-create) .

Om du vill låsa en resurs anger du namnet på resursen, resurs typ och dess resurs grupp namn.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Ange namnet på resurs gruppen om du vill låsa en resurs grupp.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Om du vill ha information om ett lås använder du [AZ lock List](/cli/azure/lock#az-lock-list). Använd följande för att hämta alla Lås i din prenumeration:

```azurecli
az lock list
```

Om du vill hämta alla Lås för en resurs använder du:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Om du vill hämta alla Lås för en resurs grupp använder du:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Om du vill ta bort ett lås använder du:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST-API
Du kan låsa distribuerade resurser med [REST API för hanterings lås](https://docs.microsoft.com/rest/api/resources/managementlocks). Med REST API kan du skapa och ta bort lås och hämta information om befintliga lås.

Skapa ett lås genom att köra:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Omfånget kan vara en prenumeration, en resurs grupp eller en resurs. Namnet på låset är vad du vill för att anropa låset. Använd **2016-09-01**för API-version.

I begäran inkluderar du ett JSON-objekt som anger egenskaperna för låset.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Nästa steg
* Information om hur du strukturerar dina resurser logiskt genom [att använda taggar för att organisera dina resurser](resource-group-using-tags.md)
* Du kan tillämpa begränsningar och konventioner i din prenumeration med anpassade principer. Mer information finns i [Vad är Azure Policy?](../governance/policy/overview.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).

