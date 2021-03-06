---
title: Snabb start – Resource Manager-mall VM-säkerhetskopiering
description: Lär dig hur du säkerhetskopierar virtuella datorer med Azure Resource Manager-mall
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc
ms.openlocfilehash: 7218b496a3cd94362d27b1883fa5055b819768c3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171935"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Säkerhetskopiera en virtuell dator i Azure med Resource Manager-mall

[Azure Backup](backup-overview.md) säkerhetskopierar lokala datorer och appar och virtuella Azure-datorer. Den här artikeln visar hur du säkerhetskopierar en virtuell Azure-dator med Resource Manager-mall och Azure PowerShell. Den här snabb starten fokuserar på processen att distribuera en Resource Manager-mall för att skapa ett återhämtnings tjänst valv. Mer information om hur du utvecklar Resource Manager-mallar finns i [Resource Manager-dokumentation](/azure/azure-resource-manager/) och [mallen referens](/azure/templates/microsoft.recoveryservices/allversions).

Alternativt kan du säkerhetskopiera en virtuell dator med [Azure PowerShell](./quick-backup-vm-powershell.md), [Azure CLI](quick-backup-vm-cli.md)eller i [Azure Portal](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Skapa en virtuell dator och Recovery Services valv

Ett [Recovery Services valv](backup-azure-recovery-services-vault-overview.md) är en logisk behållare som lagrar säkerhetskopierade data för skyddade resurser, till exempel virtuella Azure-datorer. När ett säkerhets kopierings jobb körs skapas en återställnings punkt i Recovery Services valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Med den här mallen kan du distribuera enkel virtuell Windows-dator och Recovery Services valv som kon figurer ATS med DefaultPolicy för skydd.

Om du vill distribuera mallen väljer du **prova** att öppna Azure Cloud Shell och klistrar sedan in följande PowerShell-skript i Shell-fönstret. Om du vill klistra in koden högerklickar du på Shell-fönstret och väljer **Klistra in**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Azure PowerShell används för att distribuera Resource Manager-mallen i den här snabb starten. [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md), [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)och rest- [API: et](../azure-resource-manager/resource-group-template-deploy-rest.md) kan också användas för att distribuera mallar.

## <a name="start-a-backup-job"></a>Starta ett säkerhetskopieringsjobb

Mallen skapar en virtuell dator och aktiverar den virtuella datorn igen. När du har distribuerat mallen måste du starta ett säkerhets kopierings jobb. Mer information finns i [starta ett säkerhets kopierings jobb](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Övervaka säkerhetskopieringen

Information om hur du övervakar säkerhets kopierings jobbet finns i [övervaka säkerhets kopierings jobbet](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Rensa distributionen

Om du inte längre behöver säkerhetskopiera den virtuella datorn kan du rensa den.

- Om du vill prova att återställa den virtuella datorn hoppar du över rensningen.
- Om du använde en befintlig virtuell dator kan du hoppa över den slutliga cmdleten [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) så att du lämnar resurs gruppen och den virtuella datorn på plats.

Inaktivera skydd, ta bort återställnings punkterna och valvet. Ta sedan bort resurs gruppen och tillhör ande virtuella dator resurser enligt följande:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett Recovery Services-valv, aktiverat skydd på en virtuell dator och skapat den första återställningspunkten.

- [Lär dig hur](tutorial-backup-vm-at-scale.md) du säkerhetskopierar virtuella datorer i Azure Portal.
- [Lär dig hur](tutorial-restore-disk.md) du snabbt återställer en virtuell dator
