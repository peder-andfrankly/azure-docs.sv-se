---
title: Skriptexempel för Azure PowerShell – Uppdatera RDP-användarnamnet och lösenordet | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Uppdatera RDP-användarnamnet och lösenordet för alla noder i Service Fabric-klustret av en viss nodtyp.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 2618d9100c81cdb35b4a3a82436e94068a9f9bf0
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035525"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Uppdatera administratörens användarnamn och lösenord för de virtuella datorerna i ett kluster

Varje [nodtyp](../service-fabric-cluster-nodetypes.md) i ett Service Fabric-kluster är en VM-skalningsuppsättning. Det här exempelskriptet uppdaterar administratörens användarnamn och lösenord för de virtuella datorerna i klustret i en viss nodtyp.  Lägg till filnamnstillägget VMAccessAgent till skalningsuppsättningen, eftersom administratörslösenordet inte är en ändringsbar egenskap för skalningsuppsättningar.  Ändringarna av användarnamn och lösenord gäller för alla noder i skalningsuppsättningen. Anpassa parametrarna efter behov.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installera Azure PowerShell, om så behövs, med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview). 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Hämtar egenskaperna för en klusternodstyp (en VM-skalningsuppsättning).   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Lägger till ett tillägg till VM-skalningsuppsättningen.|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|Uppdaterar tillståndet för VM-skalningsuppsättningen till ett lokalt VMSS-objekts tillstånd.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
