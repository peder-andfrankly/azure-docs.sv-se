---
title: 'Äldre Azure Virtual Network VPN gateway SKU: er | Microsoft Docs'
description: 'Så här arbetar du med den gamla virtuella Nätverksgatewayen SKU: er; Basic, standard och HighPerformance.'
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 5c745258929d495c1e568a156690f569de9f0e36
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533901"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Arbeta med virtuella nätverksgateway SKU: er (äldre SKU: er)

Den här artikeln innehåller information om äldre (gamla) virtuella nätverksgateway SKU: er. De äldre SKU: erna fungerar fortfarande i båda distributions modellerna för VPN-gatewayer som redan har skapats. Klassiska VPN-gatewayer fortsätter att använda de äldre SKU: erna, både för befintliga gatewayer och för nya gateways. När du skapar nya VPN-gatewayer för Resource Manager använder du de nya gateway-SKU: erna. Information om de nya SKU: erna finns i [about VPN gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Du kan visa tidigare Gateway-priser i avsnittet **Virtual Network gateways** , som finns på [pris sidan för ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="agg"></a>Beräknat aggregerat data flöde per SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Konfigurationer som stöds av SKU och VPN-typ

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Ändra storlek på en gateway

Du kan ändra storlek på din gateway till en gateway-SKU inom samma SKU-serie. Om du till exempel har en standard-SKU kan du ändra storlek till en HighPerformance-SKU. Du kan dock inte ändra storlek på din VPN-gateway mellan de gamla SKU: erna och de nya SKU-familjerna. Du kan till exempel inte gå från en standard-SKU till en VpnGw2-SKU eller en grundläggande SKU till VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Om du vill ändra storlek på en gateway för distributions modellen i Resource Manager med hjälp av PowerShell, använder du följande kommando:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Du kan också ändra storlek på en gateway i Azure Portal.

### <a name="classicresize"></a>Form

Om du vill ändra storlek på en gateway för den klassiska distributions modellen måste du använda PowerShell-cmdletarna för Service Management. Ange följande kommando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change"></a>Ändra till nya gateway-SKU: er

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om nya gateway SKU: er finns i [Gateway SKU: er](vpn-gateway-about-vpngateways.md#gwsku).

Mer information om konfigurations inställningar finns i [om konfigurations inställningar för VPN gateway](vpn-gateway-about-vpn-gateway-settings.md).
