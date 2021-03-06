---
title: Azure-diagnostik tillägg för Windows
description: Övervaka virtuella Azure Windows-datorer med Azure-diagnostik-tillägget
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: 3fcdcd4565ed0512b3efcf9b23ca349a3bbf3a6c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033484"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Azure-diagnostik tillägg för virtuella Windows-datorer

## <a name="overview"></a>Översikt

Med tillägget Azure-diagnostik VM kan du samla in övervaknings data, till exempel prestanda räknare och händelse loggar, från din virtuella Windows-dator. Du kan i detalj ange vilka data du vill samla in och var du vill att data ska placeras, till exempel ett Azure Storage konto eller en Azure Event Hub. Du kan också använda dessa data för att skapa diagram i Azure Portal eller skapa mått aviseringar.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Azure-diagnostik-tillägget kan köras mot Windows 10-klienten, Windows Server 2008 R2, 2012, 2012 R2 och 2016.

### <a name="internet-connectivity"></a>Internetanslutning

Azure-diagnostik-tillägget kräver att den virtuella mål datorn är ansluten till Internet. 

## <a name="extension-schema"></a>Tilläggsschema

[Schemat för Azure-diagnostik-tillägg och egenskaps värden beskrivs i det här dokumentet.](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Det JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra Azure-diagnostik-tillägget under en distribution av Azure Resource Manager mallar. Se [använda övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager mallar](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera Azure-diagnostik-tillägget till en befintlig virtuell dator. Ersätt egenskaperna Protected Settings och Settings med giltig JSON från tilläggs schemat ovan. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell-distribution

`Set-AzVMDiagnosticsExtension`-kommandot kan användas för att lägga till Azure-diagnostik-tillägget till en befintlig virtuell dator. Se även [använda PowerShell för att aktivera Azure-diagnostik på en virtuell dator som kör Windows](ps-extensions-diagnostics.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[I den här artikeln finns](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) en mer omfattande fel söknings guide för Azure-diagnostik-tillägget.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Azure-diagnostik-tillägget](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Granska tilläggs schema och-versioner](../../azure-monitor/platform/diagnostics-extension-schema.md)
