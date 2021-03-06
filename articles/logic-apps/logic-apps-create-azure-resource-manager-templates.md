---
title: Skapa Logic app-mallar för distribution
description: Lär dig hur du skapar Azure Resource Manager mallar för att automatisera distribution i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: d9b2dc7432ee1b847c8c7900a3e91daa71b5a771
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793202"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Skapa Azure Resource Manager mallar för att automatisera distributionen av Azure Logic Apps

I den här artikeln beskrivs hur du kan skapa en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-overview.md) för din Logi Kap par, som hjälper dig att automatisera skapandet och distributionen av din Logic app. En översikt över strukturen och syntaxen för en mall som innehåller din arbets flödes definition och andra resurser som krävs för distribution finns i [Översikt: Automatisera distribution av logi Kap par med Azure Resource Manager mallar](logic-apps-azure-resource-manager-templates-overview.md).

Azure Logic Apps innehåller en [fördefinierad Logic app Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) som du kan återanvända, inte bara för att skapa Logi Kap par, utan även för att definiera de resurser och parametrar som ska användas för distributionen. Du kan använda den här mallen för egna affärs scenarier eller anpassa mallen efter dina behov.

> [!IMPORTANT]
> Se till att anslutningarna i din mall använder samma Azure-resurs grupp och plats som din Logic app.

Mer information om Azure Resource Manager mallar finns i följande avsnitt:

* [Azure Resource Manager mallens struktur och syntax](../azure-resource-manager/resource-group-authoring-templates.md)
* [Redigera Azure Resource Manager mallar](../azure-resource-manager/resource-group-authoring-templates.md)
* [Utveckla Azure Resource Manager-mallar för molnkonsekvens](../azure-resource-manager/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Skapa mallar med Visual Studio

För det enklaste sättet att skapa giltiga parametriserade app-mallar som är tillgängliga för distribution, använder du Visual Studio (kostnads fri Community Edition eller senare) och Azure Logic Apps verktyg för Visual Studio. Du kan sedan antingen [skapa din Logi Kap par i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) eller [söka efter och ladda ned en befintlig Logic-app från Azure Portal till Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Genom att hämta din Logic app får du en mall som innehåller definitionerna för din Logic app och andra resurser, till exempel anslutningar. Mallen är också *parameterizes*, eller definierar parametrar för, de värden som används för att distribuera din Logic app och andra resurser. Du kan ange värden för dessa parametrar i en separat parameter fil. På så sätt kan du enkelt ändra värdena utifrån dina distributions behov. Mer information finns i följande avsnitt:

* [Skapa Logic Apps med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Hantera Logic Apps i Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Skapa mallar med Azure PowerShell

Du kan skapa Resource Manager-mallar med hjälp av Azure PowerShell med [LogicAppTemplate-modulen](https://github.com/jeffhollan/LogicAppTemplateCreator). Den här modulen för öppen källkod utvärderar först din Logic app och eventuella anslutningar som används av Logic Apps. Modulen genererar sedan mal Lav resurser med de nödvändiga parametrarna för distribution.

Anta till exempel att du har en Logic-app som tar emot ett meddelande från en Azure Service Bus kö och laddar upp data till en Azure SQL-databas. Modulen bevarar all Orchestration-logik och parameterizes SQL-och Service Bus anslutnings strängar så att du kan ange och ändra dessa värden utifrån dina distributions behov.

De här exemplen visar hur du skapar och distribuerar Logi Kap par med hjälp av Azure Resource Manager mallar, Azure-pipelines i Azure DevOps och Azure PowerShell:

* [Exempel: Anslut till Azure Service Bus köer från Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till Azure Storage konton från Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Konfigurera en Function-app-åtgärd för Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till ett integrations konto från Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Installera PowerShell-moduler

1. Om du inte redan har gjort det installerar du [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

1. För det enklaste sättet att installera LogicAppTemplate-modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/LogicAppTemplate)kör du följande kommando:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Kör följande kommando för att uppdatera till den senaste versionen:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Alternativt kan du installera manuellt genom att följa stegen i GitHub för [Logic app Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Installera Azure Resource Manager-klient

För att LogicAppTemplate-modulen ska fungera med valfri Azure-klient och prenumerations-åtkomsttoken, installerar du [Azure Resource Manager client-verktyget](https://github.com/projectkudu/ARMClient), som är ett enkelt kommando rads verktyg som anropar Azure Resource Manager-API: et.

När du kör kommandot `Get-LogicAppTemplate` med det här verktyget får kommandot först en åtkomsttoken via verktyget ARMClient, pipe-token till PowerShell-skriptet och skapar mallen som en JSON-fil. Mer information om verktyget finns i den här [artikeln om klient verktyget Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Skapa mall med PowerShell

Om du vill skapa din mall efter att ha installerat LogicAppTemplate-modulen och [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)kör du följande PowerShell-kommando:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Om du vill följa rekommendationen för rörledningar i en token från [Azure Resource Manager klient verktyget](https://github.com/projectkudu/ARMClient), kör du det här kommandot i stället där `$SubscriptionId` är ditt Azure-prenumerations-ID:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Efter extraheringen kan du skapa en parameter fil från mallen genom att köra det här kommandot:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Kör det här kommandot för extraktion med Azure Key Vault referenser (endast statisk):

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parametrar | Krävs | Beskrivning |
|------------|----------|-------------|
| TemplateFile | Ja | Fil Sök vägen till mallfilen |
| KeyVault | Nej | En uppräkning som beskriver hur du hanterar möjliga Key Vault-värden. Standardvärdet är `None`. |
||||

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera Logic app-mallar](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
