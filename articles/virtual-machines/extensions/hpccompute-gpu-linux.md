---
title: NVIDIA GPU-drivrutins tillägg – virtuella Azure Linux-datorer
description: Microsoft Azure tillägget för att installera NVIDIA GPU-drivrutiner i virtuella datorer i N-serien som kör Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: akjosh
ms.openlocfilehash: 6ea61acfc2db3c8f1f5c9c0ac8da8f19897d441e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073733"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA GPU-drivrutins tillägg för Linux

## <a name="overview"></a>Översikt

Det här tillägget installerar NVIDIA GPU-drivrutiner på virtuella datorer i Linux N-serien. Beroende på VM-serien installerar tillägget CUDA eller GRID-drivrutiner. När du installerar NVIDIA-drivrutiner med det här tillägget accepterar du och godkänner villkoren i [licens avtalet för NVIDIA-slutanvändare](https://go.microsoft.com/fwlink/?linkid=874330). Under installationen kan den virtuella datorn startas om för att slutföra driv rutins konfigurationen.

Anvisningar om manuell installation av driv rutinerna och de aktuella versioner som stöds finns [här](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup).
Det finns också ett tillägg för att installera NVIDIA GPU-drivrutiner på [virtuella datorer med Windows N-serien](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Det här tillägget har stöd för följande OS-distributioner, beroende på driv rutins stöd för en speciell OS-version.

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

### <a name="internet-connectivity"></a>Internetanslutning

Microsoft Azure-tillägget för NVIDIA GPU-drivrutiner kräver att den virtuella mål datorn är ansluten till Internet och har åtkomst.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Egenskaper

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| publisher | Microsoft.HpcCompute | sträng |
| typ | NvidiaGpuDriverLinux | sträng |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Inställningar

Alla inställningar är valfria. Standard beteendet är att inte uppdatera kerneln om det inte krävs för installation av driv rutiner, installera den senaste driv rutinen och CUDA Toolkit (i tillämpliga fall).

| Namn | Beskrivning | Default Value | Giltiga värden | Datatyp |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Uppdatera kärnan även om det inte krävs för att installera driv rutiner | false | SANT, FALSKT | boolesk |
| driverVersion | NV: RUTNÄTs driv rutins version<br> NC/ND: CUDA Toolkit-version. De senaste driv rutinerna för de valda CUDA installeras automatiskt. | senaste | RUTNÄT: "430,30", "418,70", "410,92", "410,71", "390,75", "390,57", "390,42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | sträng |
| installCUDA | Installera CUDA Toolkit. Endast relevant för virtuella datorer i NC/ND-serien. | true | SANT, FALSKT | boolesk |


## <a name="deployment"></a>Distribution


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall 

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallarna är idealiska när du distribuerar en eller flera virtuella datorer som kräver konfiguration av efter distribution.

JSON-konfiguration för tillägg för virtuell dator kan kapslas i resursen för virtuella datorer eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resursnamn och typ. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-manager-template-child-resource.md). 

I följande exempel förutsätts att tillägget är kapslat i den virtuella dator resursen. När kapsla tillägget resursen JSON placeras i den `"resources": []` objekt av den virtuella datorn.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

Följande exempel speglar ovanstående Azure Resource Manager-och PowerShell-exempel och lägger även till anpassade inställningar som exempel på driv rutins installation som inte är standard. Mer specifikt uppdaterar OS-kerneln och installerar en specifik versions driv rutin för CUDA Toolkit.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
  }'
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Information om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och genom att använda Azure PowerShell och Azure CLI. Kör följande kommando för att se distributions status för tillägg för en specifik virtuell dator.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tillägget utförande-utdatan loggas till följande fil:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Avslutnings koder

| Slutkod | Betydelse | Möjlig åtgärd |
| :---: | --- | --- |
| 0 | Åtgärden lyckades |
| 1 | Felaktig användning av tillägg | Kontrol lera loggen för körnings utdata |
| 10 | Linux-integrerings tjänster för Hyper-V och Azure är inte tillgängliga eller installerade | Kontrol lera utdata från lspci |
| 11 | NVIDIA-GPU hittades inte på den här virtuella dator storleken | Använd en [VM-storlek och ett operativ system som stöds](../linux/n-series-driver-setup.md) |
| 12 | Bild erbjudandet stöds inte |
| 13 | VM-storleken stöds inte | Använd en virtuell dator i N-serien för att distribuera |
| 14 | Åtgärden lyckades inte | Kontrol lera loggen för körnings utdata |


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/community/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [tillägg för virtuella datorer och funktioner för Linux](features-linux.md).

Mer information om virtuella datorer i N-serien finns i [GPU-optimerade storlekar för virtuella datorer](../linux/sizes-gpu.md).
