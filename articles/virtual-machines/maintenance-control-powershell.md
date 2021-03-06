---
title: Underhålls kontroll för virtuella Azure-datorer med PowerShell
description: Lär dig hur du styr när underhåll tillämpas på dina virtuella Azure-datorer med underhålls kontroll och PowerShell.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: e7a5f9ba865ab555bde3125f40ee8675709bef40
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932715"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>För hands version: kontrol lera uppdateringar med underhålls kontroll och Azure PowerShell

Hantera plattforms uppdateringar, som inte kräver omstart, med hjälp av underhålls kontroll. Azure uppdaterar ofta sin infrastruktur för att förbättra tillförlitligheten, prestandan, säkerheten eller lansera nya funktioner. De flesta uppdateringar är transparenta för användarna. Vissa känsliga arbets belastningar, t. ex. spel, medie direkt uppspelning och ekonomiska transaktioner, kan inte tolerera ens några sekunder av en virtuell dator som fryser eller kopplar från för underhåll. Med underhålls kontrollen får du möjlighet att vänta på plattforms uppdateringar och tillämpa dem i ett rullande 35-dagars fönster. 

Med underhålls kontrollen kan du bestämma när du ska tillämpa uppdateringar på de isolerade virtuella datorerna.

Med underhålls kontroll kan du:
- Batch-uppdateringar till ett uppdaterings paket.
- Vänta upp till 35 dagar för att installera uppdateringar. 
- Automatisera plattforms uppdateringar för underhålls perioden med Azure Functions.
- Underhålls konfigurationer fungerar mellan prenumerationer och resurs grupper. 

> [!IMPORTANT]
> Underhålls kontrollen är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Begränsningar

- Virtuella datorer måste finnas på en [dedikerad värd](./linux/dedicated-hosts.md)eller skapas med en [isolerad VM-storlek](./linux/isolation.md).
- Efter 35 dagar tillämpas en uppdatering automatiskt och tillgänglighets begränsningarna kommer inte att respekteras.
- Användaren måste ha åtkomst till **resurs ägare** .


## <a name="enable-the-powershell-module"></a>Aktivera PowerShell-modulen

Kontrol lera att `PowerShellGet` är aktuell.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

PowerShell-cmdletarna AZ. Maintenance är i för hands version, så du måste installera modulen med parametern `AllowPrerelease` i Cloud Shell eller din lokala PowerShell-installation.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Om du installerar lokalt, se till att du öppnar PowerShell-prompten som administratör.

Du kan också uppmanas att bekräfta att du vill installera från ett *ej betrott lager*. Skriv `Y` eller Välj **Ja till alla** för att installera modulen.



## <a name="create-a-maintenance-configuration"></a>Skapa en underhålls konfiguration

Skapa en resurs grupp som en behållare för din konfiguration. I det här exemplet skapas en resurs grupp med namnet *myMaintenanceRG* i *öster*. Om du redan har en resurs grupp som du vill använda kan du hoppa över den här delen och ersätta resurs gruppens namn med dig i resten av exemplen.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Använd [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) för att skapa en underhålls konfiguration. I det här exemplet skapas en underhålls konfiguration med namnet *unconfig* som är begränsad till värden. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Med `-MaintenanceScope host` ser du till att underhålls konfigurationen används för att styra uppdateringar av värden.

Om du försöker skapa en konfiguration med samma namn, men på en annan plats, får du ett fel meddelande. Konfigurations namn måste vara unika för din prenumeration.

Du kan fråga efter tillgängliga underhålls konfigurationer med [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Tilldela konfigurationen

Använd [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) för att tilldela konfigurationen till den isolerade virtuella datorn eller den dedikerade Azure-värden.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Tillämpa konfigurationen på en virtuell dator med hjälp av konfigurationens ID. Ange `-ResourceType VirtualMachines` och ange namnet på den virtuella datorn för `-ResourceName`och resurs gruppen för den virtuella datorn för `-ResourceGroupName`. 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicate-host"></a>Dedikera värd

Om du vill tillämpa en konfiguration på en dedikerad värd måste du också inkludera `-ResourceType hosts``-ResourceParentName` med namnet på värd gruppen och `-ResourceParentType hostGroups`. 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Sök efter väntande uppdateringar

Använd [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) för att se om det finns väntande uppdateringar. Använd `-subscription` för att ange Azure-prenumerationen för den virtuella datorn om den skiljer sig från den som du är inloggad på. 

### <a name="isolated-vm"></a>Isolerad virtuell dator

Sök efter väntande uppdateringar för en isolerad virtuell dator. I det här exemplet formateras utdata som en tabell för läsbarhet.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```

### <a name="dedicated-host"></a>Dedikerad värd

För att söka efter väntande uppdateringar för en dedikerad värd. I det här exemplet formateras utdata som en tabell för läsbarhet. Ersätt värdena för resurserna med dina egna.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```

## <a name="apply-updates"></a>Tillämpa uppdateringar

Använd [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) för att tillämpa väntande uppdateringar.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Skapa en begäran om att tillämpa uppdateringar på en isolerad virtuell dator.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

### <a name="dedicated-host"></a>Dedikerad värd

Tillämpa uppdateringar på en dedikerad värd.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="remove-a-maintenance-configuration"></a>Ta bort en underhålls konfiguration

Använd [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) om du vill ta bort en underhålls konfiguration.

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [underhåll och uppdateringar](maintenance-and-updates.md).
