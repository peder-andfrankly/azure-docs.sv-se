---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: f2ee442d0d6fecf34449ad28f058615a1274bbea
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187231"
---
De äldre SKU:erna (gamla) för VPN-gatewayer är:

* Basic
* Standard
* HighPerformance

VPN Gateway använder inte SKU för UltraPerformance-gateway. Information om UltraPerformance SKU finns i [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md)-dokumentationen.

Tänk på följande när du arbetar med de äldre SKU:erna:

* Om du vill använda en PolicyBased VPN-typ måste du använda Basic SKU:n. PolicyBased VPN (tidigare kallade statisk routning) stöds inte på andra SKU:er.
* BGP stöds inte på Basic-SKU:n.
* ExpressRoute VPN Gateway samexisterande konfigurationer stöds inte på Basic-SKU:n.
* S2S VPN Gateway-anslutningar av typen aktiv-aktiv kan enbart konfigureras på HighPerformance SKU.