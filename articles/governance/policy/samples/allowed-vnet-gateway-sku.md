---
title: Exempel – Tillåtna SKU:er för virtuell nätverksgateway
description: Den här exempelprincipdefinitionen kräver att virtuella nätverksgatewayer använder en godkänd SKU och gatewaytyp.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 91956fded705ffd05c511f2744fc0009650f3d9e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74071615"
---
# <a name="sample---allowed-virtual-network-gateway-skus"></a>Exempel – Tillåtna SKU:er för virtuell nätverksgateway

Den här principen kräver att virtuella nätverksgatewayer använder en godkänd SKU och gatewaytyp. Du anger en matris med godkända SKU:er och en matris med godkända gatewaytyper.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exempelmall

[!code-json[main](../../../../policy-templates/samples/Network/virtual-network-gateway-skus/azurepolicy.json "Allowed Virtual Network Gateway SKUs")]

Du kan distribuera den här mallen med hjälp av [Azure Portal](#deploy-with-the-portal) eller med [PowerShell](#deploy-with-powershell) eller [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Distribuera med portalen

[![distribuera princip exemplet till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fvirtual-network-gateway-skus%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "virtual-network-gateway-skus" -DisplayName "Allowed Virtual Network Gateway SKUs" -description "This policy enables you to specify a set of virtual network gateway SKUs that your organization can deploy." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -listOfAllowedSKUs <Allowed SKUs> -gatewayType <Gateway Type> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Rensa PowerShell-distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'virtual-network-gateway-skus' --display-name 'Allowed Virtual Network Gateway SKUs' --description 'This policy enables you to specify a set of virtual network gateway SKUs that your organization can deploy.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "virtual-network-gateway-skus"
```

### <a name="clean-up-azure-cli-deployment"></a>Rensa Azure CLI-distributionen

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Granska fler exempel på [Azure Policy-exempel](index.md)