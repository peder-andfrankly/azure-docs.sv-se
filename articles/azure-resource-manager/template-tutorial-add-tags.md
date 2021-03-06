---
title: Självstudie – Lägg till taggar till resurser i mallen
description: Lägg till taggar till resurser som du distribuerar i din Azure Resource Manager-mall. Med taggar kan du logiskt organisera resurser.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 078d85b3120baf5b8bea9c305fb1f97763f8df59
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406071"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>Självstudie: Lägg till taggar i din Resource Manager-mall

I den här självstudien får du lära dig hur du lägger till taggar i resurser i mallen. Med [taggar](resource-group-using-tags.md) kan du logiskt organisera dina resurser. De märkes värden som visas i kostnads rapporter. Den här självstudien tar **8 minuter** att slutföra.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om snabb starts mallar](template-tutorial-quickstart-template.md), men det är inget krav.

Du måste ha Visual Studio Code med Resource Manager Tools-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [verktyg för mallar](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Granska mall

Din tidigare mall har distribuerat ett lagrings konto, App Service plan och en webbapp.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

När du har distribuerat dessa resurser kan du behöva spåra kostnader och hitta resurser som tillhör en kategori. Du kan lägga till taggar för att lösa problemen.

## <a name="add-tags"></a>Lägga till taggar

Du kan tagga resurser för att lägga till värden som hjälper dig att identifiera användningen. Du kan till exempel lägga till taggar som visar miljön och projektet. Du kan lägga till taggar som identifierar ett kostnads ställe eller teamet som äger resursen. Lägg till värden som passar din organisation.

I följande exempel visas ändringarna i mallen. Kopiera hela filen och ersätt din mall med dess innehåll.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-template"></a>Distribuera mallen

Det är dags att distribuera mallen och titta på resultaten.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **templateFile** -variabeln till sökvägen till mallfilen, som du ser i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan kontrol lera distributionen genom att utforska resurs gruppen från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper**på den vänstra menyn.
1. Välj den resurs grupp som du har distribuerat till.
1. Välj en av resurserna, till exempel lagrings konto resursen. Nu ser du att det finns taggar.

   ![Visa Taggar](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till taggar till resurserna. I nästa själv studie kurs får du lära dig hur du använder parameter-filer för att förenkla överföring av värden till mallen.

> [!div class="nextstepaction"]
> [Använd parameter fil](template-tutorial-use-parameter-file.md)
