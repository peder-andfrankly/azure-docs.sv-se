---
title: Distribuera VM-tillägg med mall
description: Lär dig hur du distribuerar tillägg för virtuell dator med Azure Resource Manager-mallar
author: mumian
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c18e5959a1ec52d9f10f27f456a466669e7b99c9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325364"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Självstudie: Distribuera tillägg för virtuell dator med Azure Resource Manager-mallar

Lär dig hur du använder [Azure-tillägg för virtuell dator](../virtual-machines/extensions/features-windows.md) för att utföra konfigurations- och automatiseringsuppgifter efter distribution på virtuella Azure-datorer. Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. I den här självstudien distribuerar du ett anpassat skripttillägg från en Azure Resource Manager-mall för att köra ett PowerShell-skript på en virtuell Windows-dator.  Skriptet installerar webbserver på den virtuella datorn.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda ett PowerShell-skript
> * Öppna en snabbstartsmall
> * Redigera mallen
> * Distribuera mallen
> * Verifiera distributionen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med Resource Manager Tools-tillägg. [Skapa Azure Resource Manager mallar i använda Visual Studio Code](./resource-manager-tools-vs-code.md).
* För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid distribution av Resource Manager-mall](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-a-powershell-script"></a>Förbereda ett PowerShell-skript

Ett PowerShell-skript med följande innehåll delas från [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Om du väljer att publicera filen till en egen plats måste du uppdatera elementet `fileUri` i mallen senare i självstudien.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure-snabbstartsmallar är lagringsplatser för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator).

1. I Visual Studio Code väljer du **Arkiv** > **Öppna fil**.
1. I rutan **Filnamn** klistrar du in följande webbadress: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. Välj **Öppna** för att öppna filen.
    Mallen definierar fem resurser:

   * **Microsoft.Storage/storageAccounts**. Se [mallreferensen](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft.Network/publicIPAddresses**. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft.Network/virtualNetworks**. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft.Network/networkInterfaces**. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft.Compute/virtualMachines**. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Det är bra om du har grundläggande förståelse av mallen innan du anpassar den.

1. Spara en kopia av filen lokalt med namnet *azuredeploy.json* genom att välja **Arkiv** > **Spara som**.

## <a name="edit-the-template"></a>Redigera mallen

Lägg till en resurs för tillägg för virtuell dator i den befintliga mallen med följande innehåll:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Mer information om den här resursdefinitionen finns i [tilläggsreferensen](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Här följer några viktiga element:

* **name** (namn): eftersom tilläggsresursen är en underordnad resurs för det virtuella datorobjektet måste namnet ha den virtuella datorns namnprefix. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md).
* **dependsOn**: skapa tilläggs resursen när du har skapat den virtuella datorn.
* **fileUris**: de platser där skriptfilerna lagras. Om du väljer att inte använda den angivna platsen måste du uppdatera värdena.
* **commandToExecute**: det här kommandot anropar skriptet.

## <a name="deploy-the-template"></a>Distribuera mallen

Information om distributions proceduren finns i avsnittet "distribuera mallen" i [Självstudier: skapa Azure Resource Manager mallar med beroende resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Du bör använda ett genererat lösenord för den virtuella datorns administratörskonto. Läs mer i avsnittet om [förutsättningar](#prerequisites) i den här artikeln.

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Välj den virtuella datorn i Azure Portal.
1. I översikten för den virtuella datorn kopierar du IP-adressen genom att välja **Klicka för att kopiera**och klistra in den i en webbläsare-flik. Välkomst sidan för standard Internet Information Services (IIS) öppnas:

![Välkomstsida för Internet Information Services](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver Azure-resurserna du har distribuerat rensar du bort dem genom att ta bort resursgruppen.

1. Välj **Resursgrupp** i den vänstra rutan i Azure Portal.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
    Sex resurser visas i resursgruppen.
4. Välj **Ta bort resursgrupp** på menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerade du en virtuell dator och ett tillägg för virtuell dator. Tillägget installerade IIS-webbservern på den virtuella datorn. Du kan läsa om hur du använder Azure SQL Database-tillägget till att importera en BACPAC-fil här:

> [!div class="nextstepaction"]
> [Distribuera SQL-tillägg](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)
