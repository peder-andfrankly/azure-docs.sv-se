---
title: Importera SQL BACPAC-filer med mallar
description: Lär dig hur du använder SQL Database-tillägget för att importera SQL BACPAC-filer med Azure Resource Manager-mallar.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9f5e2e402e13076dc538a9c9d55e5b67e0d86d4f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978923"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Självstudie: Importera SQL BACPAC-filer med Azure Resource Manager-mallar

Lär dig hur du använder Azure SQL Database-tillägget för att importera en BACPAC-fil med Azure Resource Manager-mallar. Distributions artefakter är alla filer, förutom de viktigaste mallfiler som behövs för att slutföra en distribution. BACPAC-filen är en artefakt. I den här självstudien skapar du en mall för att distribuera en Azure SQL-server och en SQL-databas och importera en BACPAC-fil. Information om hur du distribuerar tillägg för virtuell Azure-dator med hjälp av Azure Resource Manager-mallar finns i [# Självstudie: Distribuera tillägg för virtuell dator med Azure Resource Manager-mallar](./resource-manager-tutorial-deploy-vm-extensions.md).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda en BACPAC-fil
> * Öppna en snabbstartsmall
> * Redigera mallen
> * Distribuera mallen
> * Verifiera distributionen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med Resource Manager Tools-tillägg. [Skapa Azure Resource Manager mallar i använda Visual Studio Code](./resource-manager-tools-vs-code.md).
* För att förbättra säkerheten bör du använda ett genererat lösenord för SQL-serverns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudie: Integrera Azure Key Vault vid distribution av Resource Manager-mall](./resource-manager-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-a-bacpac-file"></a>Förbereda en BACPAC-fil

En BACPAC-fil delas i [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Om du vill skapa en egen läser du [Exportera en Azure SQL-databas till en BACPAC-fil](../sql-database/sql-database-export.md). Om du väljer att publicera filen till din egen plats måste du uppdatera mallen senare under självstudien.

BACPAC-filen måste lagras i ett Azure Storage-konto innan den kan importeras med Resource Manager-mallen. Följande PowerShell-skript förbereder BACPAC-filen med följande steg:

* Ladda ned BACPAC-filen.
* skapa ett Azure Storage-konto
* Skapa en blobcontainer för ett lagringskonto.
* Ladda upp BACPAC-filen till containern.
* Visa lagrings konto nyckeln och blob-URL: en.

1. Välj **prova** att öppna Cloud Shell och klistra sedan in följande PowerShell-skript i Shell-fönstret.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Skriv ned lagrings konto nyckeln och BACPAC-filens URL. Du behöver dessa värden när du distribuerar mallen.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Mallen som används i den här självstudien lagras i [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

3. Välj **Öppna** för att öppna filen.

    Det finns två definierade resurser i mallen:

   * `Microsoft.Sql/servers`. Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`.  Se [mallreferensen](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Det är bra att få lite grundläggande förståelse av mallen innan den anpassas.
4. Välj **Arkiv**>**Spara som** för att spara en kopia av filen till den lokala datorn med namnet **azuredeploy.json**.

## <a name="edit-the-template"></a>Redigera mallen

1. Lägg till ytterligare två parametrar i slutet av **parameter** avsnittet för att ange lagrings konto nyckeln och BACPAC-URL: en:

    ```json
    "storageAccountKey": {
      "type":"string",
      "metadata":{
        "description": "Specifies the key of the storage account where the BACPAC file is stored."
      }
    },
    "bacpacUrl": {
      "type":"string",
      "metadata":{
        "description": "Specifies the URL of the BACPAC file."
      }
    }
    ```

    Lägg till ett kommatecken efter **adminPassword**. Om du vill formatera JSON-filen från VS Code trycker du på **[SHIFT] + [ALT] + F**.

    Se [förbereda en BACPAC-fil](#prepare-a-bacpac-file) för att hämta dessa två värden.

1. Lägg till två ytterligare resurser i mallen.

    * Om du vill tillåta att SQL Database-tillägget importerar BACPAC-filer måste du tillåta trafik från Azure-tjänster. Lägg till följande regel definition för brand väggen under SQL Server-definitionen:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        Mallen bör se ut så här:

        ![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Lägg till en resurs för SQL Database-tillägget till databasdefinitionen med följande JSON:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        Mallen bör se ut så här:

        ![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Information om resursdefinitionen finns i [tilläggsreferensen för SQL Database](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Här följer några viktiga element:

        * **dependsOn**: tilläggsresursen måste skapas efter att SQL-databasen har skapats.
        * **storageKeyType**: ange vilken typ av lagrings nyckel som ska användas. Värdet kan vara antingen `StorageAccessKey` eller `SharedAccessKey`. Använd `StorageAccessKey` i den här självstudien.
        * **storageKey**: Ange nyckeln för lagrings kontot där BACPAC-filen lagras. Om lagrings nyckel typen är SharedAccessKey måste den föregås av "?"
        * **storageUri**: Ange URL: en för den BACPAC-fil som lagras i ett lagrings konto.
        * **administratorLoginPassword**: Lösenordet för SQL-administratören. Använd ett genererat lösenord. Se [Förutsättningar](#prerequisites).

Den färdiga mallen ser ut så här:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Distribuera mallen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mer information om distributionsproceduren finns i avsnittet [Distribuera mallen](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Använd följande PowerShell-distributionsskript i stället:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Överväg att använda samma projekt namn som du använde när du för beredde BACPAC-filen, så att alla resurser lagras i samma resurs grupp.  Det är enklare att hantera resurser, till exempel att rensa resurserna. Om du använder samma projekt namn kan du antingen ta bort kommandot **New-AzResourceGroup** från skriptet eller svara y eller n när du tillfrågas om du vill uppdatera den befintliga resurs gruppen.

Använd ett genererat lösenord. Se [Förutsättningar](#prerequisites).

## <a name="verify-the-deployment"></a>Verifiera distributionen

För att få åtkomst till SQL Server från klient datorn måste du lägga till ytterligare en brand Väggs regel. Mer information finns i [skapa och hantera IP-brandväggens regler](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

Välj SQL-databasen från den nyligen distribuerade resursgruppen i portalen. Välj **Frågeredigerare (förhandsversion)** och ange administratörsautentiseringsuppgifterna. Du bör se två tabeller som har importerats till databasen:

![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat en SQL Server och en SQL-databas samt importerat en BACPAC-fil. BACPAC-filen lagras i ett Azure Storage-konto. Vem som helst med URL:en kan komma åt filen. Läs hur du skyddar BACPAC-filen (artefakten) i

> [!div class="nextstepaction"]
> [Skydda artefakterna](./resource-manager-tutorial-secure-artifacts.md)
