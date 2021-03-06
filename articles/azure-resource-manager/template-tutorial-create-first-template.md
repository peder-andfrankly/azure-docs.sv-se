---
title: Självstudie – Skapa & distribuera mall
description: Skapa din första Azure Resource Manager-mall. I självstudien får du lära dig mer om mallens syntax och hur du distribuerar ett lagrings konto.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9650b8c67e3fd5c786b066c53e78b106935e11e1
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406024"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Självstudie: skapa och distribuera din första Azure Resource Manager-mall

I den här självstudien beskrivs Azure Resource Manager mallar. Det visar hur du skapar en start-mall och distribuerar den till Azure. Du lär dig mer om mallens struktur och de verktyg du behöver för att arbeta med mallar. Det tar ungefär **12 minuter** att slutföra den här självstudien, men den faktiska tiden varierar beroende på hur många verktyg du behöver installera.

Den här självstudien är den första i en serie. När du går igenom serien ändrar du stegvisa start mal len tills du har utforskat alla kärn delar av en Resource Manager-mall. Dessa element är Bygg stenarna för mycket mer komplexa mallar. Vi hoppas i slutet av serien och du är säker på att du har skapat dina egna mallar och kan automatisera dina distributioner med mallar.

Om du vill lära dig mer om fördelarna med att använda mallar och varför du bör automatisera distributionen med mallar, se [Azure Resource Manager mallar](template-deployment-overview.md).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="get-tools"></a>Hämta verktyg

Låt oss börja med att kontrol lera att du har de verktyg du behöver för att skapa och distribuera mallar.

### <a name="editor"></a>Redigeringsprogram

Mallar är JSON-filer. Om du vill skapa mallar behöver du en lämplig JSON-redigerare. Vi rekommenderar Visual Studio Code med Resource Manager Tools-tillägget. Om du behöver installera dessa verktyg kan du [skapa Azure Resource Manager mallar i använda Visual Studio Code](./resource-manager-tools-vs-code.md).

### <a name="command-line-deployment"></a>Kommando rads distribution

Du behöver också antingen Azure PowerShell eller Azure CLI för att distribuera mallen. Installations anvisningar finns i:

- [Installera Azure PowerShell](/powershell/azure/install-az-ps)
- [Installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows)
- [Installera Azure CLI på Linux](/cli/azure/install-azure-cli-linux)

När du har installerat antingen Azure PowerShell eller Azure CLI kontrollerar du att du loggar in för första gången. Mer information finns i [Logga in-PowerShell](/powershell/azure/install-az-ps#sign-in) eller [Logga in – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

OK, du är redo att börja lära dig om mallar.

## <a name="create-your-first-template"></a>Skapa din första mall

1. Öppna Visual Studio Code med tillägget Resource Manager Tools installerat.
1. I menyn **Arkiv** väljer du **ny fil** för att skapa en ny fil.
1. Välj **Spara som**på **Arkiv** -menyn.
1. Ge filen namnet **azuredeploy** och välj tillägget **JSON** -fil. Det fullständiga namnet på filen **azuredeploy. JSON**.
1. Spara filen på din arbets Station. Välj en sökväg som är lätt att komma ihåg eftersom du kommer att ange den sökvägen senare när du distribuerar mallen.
1. Kopiera och klistra in följande JSON i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Så här ser din VS Code-miljö ut:

    ![Resource Manager-mall, första mallen för Visual Studio Code](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Den här mallen distribuerar inga resurser. Vi börjar med en tom mall så att du kan bekanta dig med stegen för att distribuera en mall och minimera risken för att något går fel.

    JSON-filen innehåller följande element:

    - **$schema**: anger platsen för JSON schema filen. Schema filen beskriver de egenskaper som är tillgängliga i en mall. Schemat definierar till exempel **resurser** som en av de giltiga egenskaperna för en mall. Bry dig inte om att datumet för schemat är 2015-01-01. Den här schema versionen är uppdaterad och innehåller alla de senaste funktionerna. Schema datumet har inte ändrats på grund av att det inte har skett några ändringar sedan introduktionen.
    - **contentVersion**: anger versionen för mallen (till exempel 1.0.0.0). Du kan ange valfritt värde för det här elementet. Använd det här värdet om du vill dokumentera viktiga ändringar i mallen. När du distribuerar resurser med hjälp av mallen kan det här värdet användas för att se till att rätt mall används.
    - **resurser**: innehåller de resurser som du vill distribuera eller uppdatera. För närvarande är det tomt, men du kommer att lägga till resurser senare.

1. Spara filen.

Grattis, du har skapat din första mall.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in med dina Azure-autentiseringsuppgifter om du vill börja arbeta med Azure PowerShell/Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Skapa resursgrupp

När du distribuerar en mall anger du en resurs grupp som ska innehålla resurserna. Innan du kör distributions kommandot skapar du resurs gruppen med antingen Azure CLI eller Azure PowerShell. Välj flikarna i följande kod avsnitt om du vill välja mellan Azure PowerShell och Azure CLI. CLI-exemplen i den här artikeln är skrivna för bash-gränssnittet.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Distribuera mallen

Om du vill distribuera mallen använder du antingen Azure CLI eller Azure PowerShell. Använd den resurs grupp som du skapade. Ge distributionen ett namn så att du enkelt kan identifiera den i distributions historiken. För enkelhetens skull kan du också skapa en variabel som lagrar sökvägen till mallfilen. Den här variabeln gör det enklare för dig att köra distributions kommandon eftersom du inte behöver ange sökvägen varje gång du distribuerar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Distributions kommandot returnerar resultat. Leta efter `ProvisioningState` för att se om distributionen har slutförts.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

![Etablerings status för PowerShell-distribution](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

![Etablerings status för Azure CLI-distribution](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan kontrol lera distributionen genom att utforska resurs gruppen från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **resurs grupper**på den vänstra menyn.

1. Välj resurs grupps distributionen i den senaste proceduren. Standard namnet är **myResourceGroup**. Du får inte se någon resurs som distribuerats i resurs gruppen.

1. Observera att statusen för distributionen visas i det övre högra hörnet i översikten. Select **1 lyckades**.

   ![Visa distributions status](./media/template-tutorial-create-first-template/deployment-status.png)

1. Du ser en historik över distributionen av resurs gruppen. Välj **blanktemplate**.

   ![Välj distribution](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. En sammanfattning av distributionen visas. I det här fallet finns det inte mycket att se eftersom inga resurser har distribuerats. Senare i den här serien kan det vara bra att granska sammanfattningen i distributions historiken. Lägg märke till vänster om du vill visa indata, utdata och mallen som används under distributionen.

   ![Visa distributions Sammanfattning](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill ta bort resurs gruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

Du har skapat en enkel mall för att distribuera till Azure. I nästa självstudie lägger du till ett lagrings konto i mallen och distribuerar det till din resurs grupp.

> [!div class="nextstepaction"]
> [Lägg till resurs](template-tutorial-add-resource.md)
