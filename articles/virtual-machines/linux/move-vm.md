---
title: Flytta en virtuell Linux-dator i Azure
description: Flytta en virtuell Linux-dator till en annan Azure-prenumeration eller resurs grupp i distributions modellen för Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 8fc04b4689ea8707ac2c605e3e4242a117773151
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035634"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Flytta en virtuell Linux-dator till en annan prenumeration eller resurs grupp
Den här artikeln vägleder dig genom hur du flyttar en virtuell Linux-dator (VM) mellan resurs grupper eller prenumerationer. Det kan vara praktiskt att flytta en virtuell dator mellan prenumerationer om du har skapat en virtuell dator i en personlig prenumeration och nu vill flytta den till företagets prenumeration.

> [!IMPORTANT]
>Det går inte att flytta Azure-Managed Disks just nu. 
>
>Nya resurs-ID: n skapas som en del av flytten. När den virtuella datorn har flyttats måste du uppdatera dina verktyg och skript för att använda de nya resurs-ID: na. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Använd Azure CLI för att flytta en virtuell dator


Innan du kan flytta den virtuella datorn med hjälp av Azure CLI måste du kontrol lera att käll-och mål prenumerationerna finns inom samma klient organisation. Om du vill kontrol lera att båda prenumerationerna har samma klient-ID använder du [AZ konto show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Om klient-ID: na för käll-och mål prenumerationerna inte är desamma måste du kontakta [supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) för att flytta resurserna till en ny klient.

För att kunna flytta en virtuell dator måste du flytta den virtuella datorn och alla dess stöd resurser. Använd kommandot [AZ Resource List](/cli/azure/resource) för att visa en lista över alla resurser i en resurs grupp och deras ID. Den hjälper till att skicka kommandots utdata till en fil så att du kan kopiera och klistra in ID: n i senare kommandon.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Om du vill flytta en virtuell dator och dess resurser till en annan resurs grupp använder du [AZ resurs flytt](/cli/azure/resource). I följande exempel visas hur du flyttar en virtuell dator och de vanligaste resurserna som krävs. Använd parametern **-ID** : t och skicka en kommaavgränsad lista (utan blank steg) med ID: n för de resurser som ska flyttas.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Om du vill flytta den virtuella datorn och dess resurser till en annan prenumeration lägger du till parametern **--destination-subscriptionId** för att ange mål prenumerationen.

När du uppmanas att bekräfta att du vill flytta de angivna resurserna anger du **Y** för att bekräfta.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Nästa steg
Du kan flytta många olika typer av resurser mellan resurs grupper och prenumerationer. Mer information finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../resource-group-move-resources.md).    

