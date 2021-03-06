---
title: Självstudie – exportera mall från Azure Portal
description: Lär dig hur du använder en exporterad mall för att slutföra din mall utveckling.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6c22a8e2b479ed7d3a225bc649d89f5302d0f536
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406019"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Självstudie: Använd exporterad mall från Azure Portal

I den här själv studie serien har du skapat en mall för att distribuera ett Azure Storage-konto. I de kommande två självstudierna lägger du till en *App Service plan* och en *webbplats*. I stället för att skapa mallar från grunden får du lära dig hur du exporterar mallar från Azure Portal och hur du använder exempel mallar från [Azures snabb starts mallar](https://azure.microsoft.com/resources/templates/). Du anpassar mallarna efter din användning. Den här självstudien fokuserar på att exportera mallar och anpassa resultatet för din mall. Det tar ungefär **14 minuter** att slutföra.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om utdata](template-tutorial-add-outputs.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med Resource Manager Tools-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [verktyg för mallar](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Granska mall

I slutet av den föregående själv studie kursen hade mallen följande JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json)]

Den här mallen fungerar bra för att distribuera lagrings konton, men du kanske vill lägga till fler resurser i den. Du kan exportera en mall från en befintlig resurs för att snabbt hämta JSON för resursen.

## <a name="create-app-service-plan"></a>Skapa apptjänstplan

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs**.
1. I **Sök på Marketplace**anger **App Service plan**och väljer sedan **App Service plan**.  Välj inte **App Service plan (klassisk)**
1. Välj **Skapa**.
1. Går

    - **Prenumeration**: Välj din Azure-prenumeration.
    - **Resurs grupp**: Välj **Skapa ny** och ange sedan ett namn. Ange ett annat resurs grupps namn än det som du har använt i den här själv studie serien.
    - **Namn**: Ange ett namn för App Service-planen.
    - **Operativ system**: Välj **Linux**.
    - **Region**: Välj en Azure-plats. Välj till exempel **USA, centrala**.
    - **Pris nivå**: om du vill spara kostnader ändrar du SKU till **Basic B1** (under utveckling/testning).

    ![Exportera mall-Portal för Resource Manager-mall](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Välj **Granska och skapa**.
1. Välj **Skapa**. Det tar en stund att skapa resursen.

## <a name="export-template"></a>Exportera mall

1. Välj **Gå till resurs**.

    ![Gå till resurs](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Välj **Exportera mall**.

    ![Exportera mall för Resource Manager-mall](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   Funktionen Exportera mall tar det aktuella läget för en resurs och skapar en mall för att distribuera den. Att exportera en mall kan vara ett användbart sätt att snabbt hämta den JSON du behöver för att distribuera en resurs.

1. Kopiera **Microsoft. Web/Server grupper** -definitionen och parameter definitionen till mallen.

    ![Exportera mall för mall i Resource Manager-mall](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Normalt är den exporterade mallen mer utförlig än du kanske vill när du skapar en mall. Till exempel har SKU-objektet i den exporterade mallen fem egenskaper. Den här mallen fungerar, men du kan bara använda egenskapen **namn** . Du kan börja med den exporterade mallen och sedan ändra den så att den passar dina behov.

## <a name="revise-existing-template"></a>Ändra befintlig mall

Den exporterade mallen ger dig merparten av den JSON du behöver, men du måste anpassa den för din mall. Var särskilt uppmärksam på skillnader i parametrar och variabler mellan mallen och den exporterade mallen. Det är självklart att export processen inte känner till de parametrar och variabler som du redan har definierat i mallen.

I följande exempel visas tilläggen i mallen. Den innehåller den exporterade koden och vissa ändringar. Först ändras namnet på parametern så att det matchar din namngivnings konvention. För det andra använder den plats parametern för platsen för App Service-planen. Tredje, tar den bort **namnet** i **egenskaperna Properties** eftersom det här värdet är redundant med egenskapen **Name** på resurs nivå.

Kopiera hela filen och ersätt din mall med dess innehåll.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json?range=1-77&highlight=28-31,50-69)]

## <a name="deploy-template"></a>Distribuera mallen

Använd antingen Azure CLI eller Azure PowerShell för att distribuera en mall.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **templateFile** -variabeln till sökvägen till mallfilen, som du ser i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan kontrol lera distributionen genom att utforska resurs gruppen från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper**på den vänstra menyn.
1. Välj den resurs grupp som du har distribuerat till.
1. Resurs gruppen innehåller ett lagrings konto och en App Service plan.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du exporterar en mall från Azure Portal och hur du använder den exporterade mallen för mall utvecklingen. Du kan också använda Azures snabb starts mallar för att förenkla mallarnas utveckling.

> [!div class="nextstepaction"]
> [Använda Azure snabb starts mallar](template-tutorial-quickstart-template.md)
