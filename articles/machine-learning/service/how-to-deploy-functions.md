---
title: Distribuera ml-modeller till Azure Functions appar (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning för att distribuera en modell till en Azure Functions-app.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: d35f0bbc057c97ed4f8b6429c0573fa6bcf3742f
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807382"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Distribuera en maskin inlärnings modell till Azure Functions (för hands version)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du distribuerar en modell från Azure Machine Learning som en Function-app i Azure Functions.

> [!IMPORTANT]
> Både Azure Machine Learning och Azure Functions är allmänt tillgängliga, men möjligheten att paketera en modell från Machine Learnings tjänsten för functions är i för hands version.

Med Azure Machine Learning kan du skapa Docker-avbildningar från tränade maskin inlärnings modeller. Azure Machine Learning har nu förhands gransknings funktioner för att bygga dessa maskin inlärnings modeller i Function-appar som kan [distribueras till Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta. Mer information finns i artikeln [skapa en arbets yta](how-to-manage-workspace.md) .
* Den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* En utbildad Machine Learning-modell som registrerats i din arbets yta. Om du inte har någon modell använder du [själv studie kursen om bild klassificering: träna modell](tutorial-train-models-with-aml.md) att träna och registrera en.

    > [!IMPORTANT]
    > Kodfragmenten i den här artikeln förutsätter att du har angett följande variabler:
    >
    > * `ws` – din Azure Machine Learning-arbetsyta.
    > * `model` – den registrerade modell som ska distribueras.
    > * `inference_config` – konfigurationens konfigurations härledning.
    >
    > Mer information om hur du ställer in dessa variabler finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Förbered för distribution

Innan du distribuerar måste du definiera vad som behövs för att köra modellen som en webb tjänst. I följande lista beskrivs de grundläggande objekt som behövs för en-distribution:

* Ett __Entry-skript__. Det här skriptet accepterar begär Anden, visar begäran med hjälp av modellen och returnerar resultatet.

    > [!IMPORTANT]
    > Start skriptet är bara för din modell. den måste förstå formatet på inkommande begär ande data, formatet på de data som förväntas av din modell och formatet på de data som returneras till klienter.
    >
    > Om begär ande data har ett format som inte kan användas av din modell kan skriptet omvandla det till ett acceptabelt format. Det kan också omvandla svaret innan det returneras till klienten.
    >
    > Som standard behandlas indatatypen som text när de paketeras för funktioner. Om du är intresse rad av att använda rå byte för indata (till exempel för BLOB-utlösare) bör du använda [AMLRequest för att acceptera rå data](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#binary-data).


* **Beroenden**, till exempel hjälp skript eller python/Conda-paket som krävs för att köra registrerings skriptet eller modellen

Dessa entiteter kapslas in i en konfiguration för en __härledning__. Konfigurations konfigurationen refererar till Start skriptet och andra beroenden.

> [!IMPORTANT]
> När du skapar en konfigurations konfiguration för användning med Azure Functions måste du använda ett [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) objekt. I följande exempel visas hur du skapar ett miljö objekt och använder det med en konfigurations konfiguration:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Mer information om miljöer finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Mer information om konfiguration av konfiguration finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> När du distribuerar till Functions behöver du inte skapa en __distributions konfiguration__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Installera SDK Preview-paketet för functions-stöd

Om du vill bygga paket för Azure Functions måste du installera SDK-paketet för för hands versionen.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Skapa avbildningen

Om du vill skapa Docker-avbildningen som distribueras till Azure Functions använder du [azureml. contrib. functions. Package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) eller funktionen Package för den utlösare som du är intresse rad av. Följande kodfragment visar hur du skapar ett nytt paket med en BLOB-utlösare från modellen och konfigurationen för konfigurations härledning:

> [!NOTE]
> Kodfragmentet förutsätter att `model` innehåller en registrerad modell och att `inference_config` innehåller konfigurationen för härlednings miljön. Mer information finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

När `show_output=True`visas utdata från Docker-build-processen. När processen har slutförts har avbildningen skapats i Azure Container Registry för din arbets yta. När avbildningen har skapats visas platsen i Azure Container Registry. Den plats som returnerades är i formatet `<acrinstance>.azurecr.io/package@sha256:<hash>`.

> [!NOTE]
> Paketering för functions stöder HTTP-utlösare, BLOB-utlösare och Service Bus-utlösare Mer information om utlösare finns i [Azure Functions-bindningar](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob?tabs=csharp#trigger---blob-name-patterns).

> [!IMPORTANT]
> Spara plats informationen som används när avbildningen distribueras.

## <a name="deploy-image-as-a-web-app"></a>Distribuera avbildning som en webbapp

1. Använd följande kommando för att hämta inloggnings uppgifterna för den Azure Container Registry som innehåller avbildningen. Ersätt `<acrinstance>` med det värde som returnerades tidigare från `package.location`: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Utdata från det här kommandot liknar följande JSON-dokument:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Spara värdet för __användar namn__ och ett av __lösen orden__.

1. Om du inte redan har en resurs grupp eller App Service-plan för att distribuera tjänsten visar följande kommandon hur du skapar båda:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    I det här exemplet används en pris nivå för _Linux Premium_ (`--sku EP1`).

    > [!IMPORTANT]
    > Avbildningar som skapats av Azure Machine Learning använda Linux, så du måste använda `--is-linux`-parametern.

1. Använd följande kommando för att skapa en Function-app. Ersätt `<app-name>` med det namn som du vill använda. Ersätt `<acrinstance>` och `<imagename>` med värdena från returnerade `package.location` tidigare:

    ```azurecli-interactive
    az storage account create --name 
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    > [!IMPORTANT]
    > Nu har Function-appen skapats. Men eftersom du inte har angett anslutnings strängen för BLOB-utlösaren eller autentiseringsuppgifterna till Azure Container Registry som innehåller avbildningen, är funktions programmet inte aktivt. I nästa steg anger du anslutnings strängen och autentiseringsinformationen för behållar registret. 

1. Skapa lagrings kontot som ska användas som utlösare och hämta dess anslutnings sträng.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name triggerStorage --query connectionString --output tsv
    ```
    Registrera den här anslutnings strängen för att tillhandahålla Function-appen. Vi kommer att använda den senare när vi ber om `<triggerConnectionString>`

1. Skapa behållarna för indata och utdata i lagrings kontot. 

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Du måste hämta taggen som är kopplad till den skapade behållaren med följande kommando:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Den senaste taggen som visas är `imagetag` nedan.

1. Använd följande kommando för att tillhandahålla funktionen appen med de autentiseringsuppgifter som krävs för att komma åt behållar registret. Ersätt `<app-name>` med det namn som du vill använda. Ersätt `<acrinstance>` och `<imagetag>` med värdena från AZ CLI-anropet i föregående steg. Ersätt `<username>` och `<password>` med ACR-inloggnings informationen som hämtades tidigare:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Det här kommandot returnerar information som liknar följande JSON-dokument:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

I det här läget börjar Function-appen läsa in avbildningen.

> [!IMPORTANT]
> Det kan ta flera minuter innan avbildningen har lästs in. Du kan övervaka förloppet med hjälp av Azure Portal.

## <a name="next-steps"></a>Nästa steg

* Lär dig att konfigurera Functions-appen i [Functions](/azure/azure-functions/functions-create-function-linux-custom-image) -dokumentationen.
* Läs mer om Blob Storage-utlösare av [Azure Blob Storage-bindningar](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Distribuera din modell till Azure App Service](how-to-deploy-app-service.md).
* [Använd en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md)
* [API-referens](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)