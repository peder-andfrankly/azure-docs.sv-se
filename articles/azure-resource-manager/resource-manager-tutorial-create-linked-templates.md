---
title: Skapa länkade mallar
description: Lär dig att skapa länkade Azure Resource Manager-mallar för att skapa virtuella datorer
author: mumian
ms.date: 12/03/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e8964335d8c436cc590c36c3ea01fac02ed2280a
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815266"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Självstudie: Skapa länkade Azure Resource Manager-mallar

Lär dig att skapa länkade Azure Resource Manager-mallar. Med hjälp av länkade mallar kan du få en mall att anropa en annan. Det är perfekt för modularisering av mallar. I den här självstudien använder du samma mall som används i [Självstudier: skapa Azure Resource Manager mallar med beroende resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md)som skapar en virtuell dator, ett virtuellt nätverk och en annan beroende resurs, inklusive ett lagrings konto. Du separerar skapandet av lagringskontoresursen till en länkad mall.

Att anropa en länkad mall är som att göra ett funktions anrop.  Du lär dig också hur du skickar parameter värden till den länkade mallen och hur du hämtar "retur värden" från den länkade mallen.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Skapa den länkade mallen
> * Ladda upp den länkade mallen
> * Länka till den länkade mallen
> * Konfigurera beroende
> * Distribuera mallen
> * Ytterligare metoder

Mer information finns i [använda länkade och kapslade mallar när du distribuerar Azure-resurser](./resource-group-linked-templates.md).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med Resource Manager Tools-tillägg. [Skapa Azure Resource Manager mallar i använda Visual Studio Code](./resource-manager-tools-vs-code.md).
* För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid distribution av Resource Manager-mall](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure-snabbstartsmallar är en lagringsplats för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator). Det här är samma mall som används i [Självstudier: skapa Azure Resource Manager mallar med beroende resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Du sparar två kopior av samma mall som ska användas som:

* **Huvudmallen**: Skapa alla resurser förutom lagringskontot.
* **Den länkade mallen**: Skapa lagringskontot.

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
1. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen.
1. Det finns sex resurser som definieras av mallen:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/networkSecurityGroups`](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     Det är praktiskt att du får grundläggande förståelse för mallens schema innan du anpassar mallen.
1. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.
1. Välj **Fil**>**Spara som** för att skapa en annan kopia av filen med namnet **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Skapa den länkade mallen

Den länkade mallen skapar ett lagringskonto. Den länkade mallen kan användas som en fristående mall för att skapa ett lagrings konto. I den här självstudien tar den länkade mallen två parametrar och skickar tillbaka ett värde till huvud mal len. Värdet "Return" är definierat i `outputs`-elementet.

1. Öppna **linkedTemplate. JSON** i Visual Studio Code om filen inte är öppen.
1. Gör följande ändringar:

    * Ta bort alla parametrar förutom **platsen**.
    * Lägg till en parameter med namnet **storageAccountName**.

      ```json
      "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
      },
      ```

      Lagrings kontots namn och plats skickas från huvud mal len till den länkade mallen som parametrar.

    * Ta bort elementet **variabler** och alla definitioner för variabeln.
    * Ta bort alla andra resurser än lagrings kontot. Du tar bort totalt fyra resurser.
    * Uppdatera värdet för elementet **namn** för lagringskontoresursen till:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Uppdatera elementet **outputs**, så det ser ut så här:

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```

       **storageUri** krävs av VM-resursdefinitionen i huvudmallen.  Du skickar tillbaka värdet till huvudmallen som ett utdatavärde.

        När du är klar ska mallen se ut så här:

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
              }
            },
            "location": {
              "type": "string",
              "defaultValue": "[resourceGroup().location]",
              "metadata": {
                "description": "Location for all resources."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-11-01",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```

1. Spara ändringarna.

## <a name="upload-the-linked-template"></a>Ladda upp den länkade mallen

Huvudmallen och den länkade mallen måste vara tillgängliga från där du kör distributionen. I den här självstudien använder du distributions metoden för Cloud Shell som du använde i [Självstudier: skapa Azure Resource Manager mallar med beroende resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Huvudmallen (azuredeploy.json) laddas upp till gränssnittet. Den länkade mallen (linkedTemplate.json) måste delas på en säker plats. Följande PowerShell-skript skapar ett Azure Storage-konto, laddar upp mallen till lagringskontot och genererar en SAS-token för att ge begränsad åtkomst till mallfilen. För att förenkla självstudien laddar skriptet ned en slutförd länkad mall från en GitHub-lagringsplats. Om du vill använda den länkade mallen du har skapat kan du använda [Cloud shell](https://shell.azure.com) för att ladda upp din länkade mall och sedan ändra skriptet om du vill använda en egen länkad mall.

> [!NOTE]
> Skriptet begränsar SAS-token så att det kan användas inom åtta timmar. Om du behöver mer tid för att slutföra den här självstudien ökar du förfallotiden.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

Write-Host "You need the following values later in the tutorial:"
Write-Host "Resource Group Name: $resourceGroupName"
Write-Host "Linked template URI with SAS token: $templateURI"
Write-Host "Press [ENTER] to continue ..."
```

1. Välj den gröna knappen **Try it** (Prova) för att öppna fönstret Azure Cloud Shell.
2. Välj **Kopiera** för att kopiera PowerShell-skriptet.
3. Högerklicka var som helst i gränssnittsfönstret (den marinblå delen) och välj sedan **Klistra in**.
4. Anteckna de två värdena (resursgruppens namn och länkad mall-URI) längst ned i gränssnittsfönstret. Du behöver dem senare i självstudien.
5. Välj **Avsluta fokusläge** för att stänga gränssnittsfönstret.

I praktiken genererar du en SAS-token när du distribuerar huvudmallen och ger förfallodatumet för SAS-token ett mindre fönster så att de blir säkrare. Mer information finns i avsnittet om att [ange SAS-token under distribution](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Anropa den länkade mallen

Huvudmallen heter azuredeploy.json.

1. Öppna **azuredeploy. JSON** i Visual Studio Code om den inte är öppen.
1. Ersätt lagrings kontots resurs definition med följande JSON-kodfragment:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":""
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Ta hänsyn till följande information:

    * En `Microsoft.Resources/deployments`-resurs i huvudmallen används för att länka till en annan mall.
    * `deployments`-resursen har namnet `linkedTemplate`. Det här namnet används för [ konfigurering av beroende](#configure-dependency).
    * Du kan bara använda läget för [stegvis](./deployment-modes.md) distribution när du anropar länkade mallar.
    * `templateLink/uri` innehåller den länkade mallens URI. Uppdatera värdet till den URI som du får när du laddar upp den länkade mallen (knappen med en SAS-token).
    * Använd `parameters` för att skicka värden från huvudmallen till den länkade mallen.
1. Kontrollera att du har uppdaterat värdet för elementet `uri` till värdet du fick när du laddade upp den länkade mallen (knappen med en SAS-token). I praktiken vill du ange URI:n med en parameter.
1. Spara den redigerade mallen

## <a name="configure-dependency"></a>Konfigurera beroende

Återkalla från [självstudie: skapa Azure Resource Manager mallar med beroende resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md), den virtuella dator resursen är beroende av lagrings kontot:

![Beroendediagram för Azure Resource Manager-mallar](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Eftersom lagringskontot är definierat i den länkade mallen nu, måste du uppdatera följande två element i resursen `Microsoft.Compute/virtualMachines`.

* Konfigurera om elementet `dependsOn`. Lagringskontodefinitionen flyttas till den länkade mallen.
* Konfigurera om elementet `properties/diagnosticsProfile/bootDiagnostics/storageUri`. I [Skapa den länkade mallen](#create-the-linked-template) lade du till ett utdatavärde:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```

    Det här värdet krävs av huvudmallen.

1. Öppna azuredeploy.json i Visual Studio Code om den inte är öppen.
2. Expandera den virtuella datorns resursdefinition och uppdatera **dependsOn** enligt följande skärmbild:

    ![Azure Resource Manager-länkade mallar konfigurerar beroende](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* är namnet på distributionsresursen.
3. Uppdatera **properties/diagnosticsProfile/bootDiagnostics/storageUri** som visas på föregående skärmbild.
4. Spara den redigerade mallen.

## <a name="deploy-the-template"></a>Distribuera mallen

Mer information om distributionsproceduren finns i avsnittet [Distribuera mallen](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Använd samma resursgruppnamn som lagringskontot för att lagra den länkade mallen. Det gör det enklare att rensa resurser i nästa avsnitt. För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Se [Förutsättningar](#prerequisites).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="additional-practice"></a>Ytterligare övning

För att förbättra projektet gör du följande ytterligare ändringar i det färdiga projektet:

1. Ändra huvudmallen (azuredeploy.json) så att det accepterar det länkade mall-URI-värdet via en parameter.
2. Generera en token i stället för att generera en SAS-token när du laddar upp den länkade mallen när du distribuerar den huvudsakliga mallen. Mer information finns i avsnittet om att [ange SAS-token under distribution](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du modulariserat en mall i en huvudmall och en länkad mall. Information om hur du använder tillägg för virtuell dator för att utföra distributionsuppgifter finns i:

> [!div class="nextstepaction"]
> [Distribuera tillägg för virtuell dator](./resource-manager-tutorial-deploy-vm-extensions.md)
