---
title: Distribuera Azure-brandväggen med flera offentliga IP-adresser med hjälp av PowerShell
description: I den här artikeln får du lära dig hur du distribuerar en Azure-brandvägg med flera offentliga IP-adresser med hjälp av Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ad54b60d8f15e36636f887015d97967740123669
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195868"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Distribuera en Azure-brandvägg med flera offentliga IP-adresser med hjälp av Azure PowerShell

Den här funktionen möjliggör följande scenarier:

- **DNAt** – du kan översätta flera standard port instanser till backend-servrarna. Om du till exempel har två offentliga IP-adresser kan du översätta TCP-port 3389 (RDP) för båda IP-adresserna.
- **SNAT** -ytterligare portar är tillgängliga för utgående SNAT-anslutningar, vilket minskar risken för SNAT-port överbelastning. För tillfället väljer Azure Firewall slumpmässigt den offentliga IP-adress som ska användas för en anslutning. Om du har filtrering av underordnade i nätverket måste du tillåta alla offentliga IP-adresser som är kopplade till din brand vägg.
 
Azure-brandväggen med flera offentliga IP-adresser är tillgänglig via Azure Portal, Azure PowerShell, Azure CLI, REST och mallar. Du kan distribuera en Azure-brandvägg med upp till 100 offentliga IP-adresser.

Följande Azure PowerShell exempel visar hur du kan konfigurera, lägga till och ta bort offentliga IP-adresser för Azure-brandväggen.

> [!NOTE]
> Du kan inte ta bort den första ipConfiguration från sidan konfiguration av offentliga IP-adresser i Azure-brandväggen. Om du vill ändra IP-adressen kan du använda Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Skapa en brand vägg med två eller flera offentliga IP-adresser

I det här exemplet skapas en brand vägg som är kopplad till det virtuella nätverkets *VNet* med två offentliga IP-adresser.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Lägga till en offentlig IP-adress i en befintlig brand vägg

I det här exemplet är den offentliga IP- *azFwPublicIp1* kopplad till brand väggen.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Ta bort en offentlig IP-adress från en befintlig brand vägg

I det här exemplet är den offentliga IP- *azFwPublicIp1* frånkopplad från brand väggen.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)
