---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926221"
---
Installera de senaste versionerna av Azure Service Management (SM) PowerShell-moduler och ExpressRoute-modulen. Du kan inte använda Azure CloudShell-miljön för att köra SM-moduler.

1. Följ anvisningarna i artikeln [Installera Service Management Module](/powershell/azure/servicemanagement/install-azure-ps) för att installera Azure Service Management-modulen. Om du redan har installerat AZ-eller RM-modulen måste du använda-AllowClobber.
2. Importera de installerade modulerna. När du använder följande exempel justerar du sökvägen så att den återspeglar plats och version för de installerade PowerShell-modulerna.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Logga in på ditt Azure-konto genom att öppna PowerShell-konsolen med utökade rättigheter och Anslut till ditt konto. Använd följande exempel för att få hjälp med att ansluta med Service Management-modulen:

   ```powershell
   Add-AzureAccount
   ```