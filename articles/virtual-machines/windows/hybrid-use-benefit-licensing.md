---
title: Azure Hybrid-förmånen för Windows Server
description: Lär dig hur du maximerar dina Windows Software Assurance-förmåner för att ta med lokala licenser till Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: 40697925d399962399da499e0469198a0e997f66
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038633"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid-förmånen för Windows Server
För kunder med Software Assurance kan Azure Hybrid-förmån för Windows Server använda dina lokala Windows Server-licenser och köra virtuella Windows-datorer i Azure till en lägre kostnad. Du kan använda Azure Hybrid-förmån för Windows Server för att distribuera nya virtuella datorer med Windows OS. Den här artikeln går igenom stegen för att distribuera nya virtuella datorer med Azure Hybrid-förmån för Windows Server och hur du kan uppdatera befintliga virtuella datorer som körs. Mer information om Azure Hybrid-förmån för Windows Server-licensiering och kostnads besparingar finns på [sidan för Azure Hybrid-förmån för Windows Server-licensiering](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Varje licens för dubbla processorer eller varje uppsättning av licenser för processorer med 16 kärnor berättigar innehavaren till två instanser med upp till åtta processorkärnor eller en instans med upp till 16 processorkärnor. Azure Hybrid-användningsförmånen för Standard Edition-licenser kan endast användas en gång – antingen lokalt eller i Azure. Datacenter Edition-förmånerna berättigar till samtidig användning både lokalt och i Azure.
>

> [!Important]
> Att använda Azure Hybrid-förmån för Windows Server med virtuella datorer som kör Windows Server OS stöds nu i alla regioner, inklusive virtuella datorer med ytterligare program vara som SQL Server eller Marketplace-programvara från tredje part. 
>

> [!NOTE]
> För klassiska virtuella datorer stöds endast distribution av nya virtuella datorer från lokala anpassade avbildningar. Om du vill dra nytta av de funktioner som stöds i den här artikeln måste du först migrera klassiska virtuella datorer till Resource Manager-modellen.
>

 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Sätt att använda Azure Hybrid-förmån för Windows Server
Det finns några sätt att använda virtuella Windows-datorer med Azure Hybrid-förmån:

1. Du kan distribuera virtuella datorer från någon av de tillhandahållna Windows Server-avbildningarna på Azure Marketplace
2. Du kan ladda upp en anpassad virtuell dator och distribuera med hjälp av en Resource Manager-mall eller Azure PowerShell
3. Du kan växla och konvertera den befintliga virtuella datorn mellan att köra med Azure Hybrid-förmån eller betala på begäran-kostnad för Windows Server
4. Du kan också använda Azure Hybrid-förmån för Windows Server på den virtuella datorns skal uppsättning även


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Skapa en virtuell dator med Azure Hybrid-förmån för Windows Server
Alla Windows Server OS-baserade avbildningar stöds för Azure Hybrid-förmån för Windows Server. Du kan använda Azure Platform support-avbildningar eller ladda upp dina egna anpassade Windows Server-avbildningar. 

### <a name="portal"></a>Portal
Om du vill skapa en virtuell dator med Azure Hybrid-förmån för Windows Server kan du använda växla under avsnittet "Spara pengar".

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Mall
I dina Resource Manager-mallar måste ytterligare en parameter `licenseType` anges. Du kan läsa mer om hur du [redigerar Azure Resource Manager mallar](../../resource-group-authoring-templates.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konvertera en befintlig virtuell dator med hjälp av Azure Hybrid-förmån för Windows Server
Om du har en befintlig virtuell dator som du vill konvertera för att kunna använda Azure Hybrid-förmån för Windows Server kan du uppdatera den virtuella datorns licens typ genom att följa anvisningarna nedan.

> [!NOTE]
> Att ändra licens typen på den virtuella datorn gör inte att systemet startas om eller orsakar en tjänst interuption.  Det är helt enkelt en uppdatering av en metadata-flagga.
> 

### <a name="portal"></a>Portal
Från bladet för virtuell Portal-dator kan du uppdatera den virtuella datorn så att den använder Azure Hybrid-förmån genom att välja alternativet "konfiguration" och växla mellan alternativet "Azure Hybrid Benefit"

### <a name="powershell"></a>PowerShell
- Konvertera befintliga virtuella Windows Server-datorer till Azure Hybrid-förmån för Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Konvertera virtuella Windows Server-datorer med förmåner tillbaka till "betala per användning"

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Konvertera befintliga virtuella Windows Server-datorer till Azure Hybrid-förmån för Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Så här verifierar du att din virtuella dator utnyttjar licens förmånen
När du har distribuerat den virtuella datorn via PowerShell, Resource Manager-mall eller Portal kan du kontrol lera inställningen i följande metoder.

### <a name="portal"></a>Portal
Från bladet virtuell Portal-dator kan du Visa växlings fönstret för Azure Hybrid-förmån för Windows Server genom att välja fliken konfiguration.

### <a name="powershell"></a>PowerShell
I följande exempel visas licens typen för en enskild virtuell dator
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Resultat:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Den här utmatningen kontrasterar med följande distribuerade virtuella datorer utan Azure Hybrid-förmån för Windows Server-licensiering:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> Att ändra licens typen på den virtuella datorn gör inte att systemet startas om eller orsakar en tjänst interuption. Det är endast en licens flagga för metadata.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Visa en lista med alla virtuella datorer med Azure Hybrid-förmån för Windows Server i en prenumeration
Om du vill se och räkna alla virtuella datorer som har distribuerats med Azure Hybrid-förmån för Windows Server kan du köra följande kommando från din prenumeration:

### <a name="portal"></a>Portal
På resurs bladet för den virtuella datorn eller den virtuella datorns skalnings uppsättningar kan du Visa en lista över alla VM: er och licensierings typ genom att konfigurera tabell kolumnen så att den inkluderar "Azure Hybrid-förmån". VM-inställningen kan antingen vara i läget "aktive rad", "ej aktive rad" eller "stöds inte".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Distribuera en skalnings uppsättning för virtuella datorer med Azure Hybrid-förmån för Windows Server
I dina Resource Manager-mallar för skalnings uppsättningen för virtuella datorer måste ytterligare en parameter `licenseType` anges i egenskapen VirtualMachineProfile. Du kan göra detta under skapa eller uppdatera för din skalnings uppsättning med ARM-mall, PowerShell, Azure CLI eller REST.

I följande exempel används ARM-mallen med en Windows Server 2016 Data Center-avbildning:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Du kan också lära dig mer om hur du [ändrar en skalnings uppsättning för virtuella datorer](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) för fler sätt att uppdatera din skalnings uppsättning.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [hur du sparar pengar med Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Läs mer om [vanliga frågor och svar om Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Läs mer om [Azure Hybrid-förmån för Windows Server-licensiering detaljerad vägledning](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Läs mer om [Azure Hybrid-förmån för Windows Server och Azure Site Recovery gör migreringen av program till Azure ännu mer kostnads effektiv](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Läs mer om [Windows 10 på Azure med värd för flera innehavare](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Lär dig mer om att [använda Resource Manager-mallar](../../azure-resource-manager/resource-group-overview.md)
