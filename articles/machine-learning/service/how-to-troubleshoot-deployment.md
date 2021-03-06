---
title: Distribuera felsökningsguide
titleSuffix: Azure Machine Learning
description: Lär dig hur du arbetar runt, löser och felsöker vanliga Docker-distributions fel med Azure Kubernetes service och Azure Container Instances med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 7d7744aab6d83b3415f32ca9e311940836c6f93c
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997249"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Felsöka Azure Machine Learning Azure Kubernetes service och Azure Container Instances distribution

Lär dig att undvika eller lösa vanliga Docker-distributions fel med Azure Container Instances (ACI) och Azure Kubernetes service (AKS) med Azure Machine Learning.

När du distribuerar en modell i Azure Machine Learning utför systemet ett antal uppgifter. Distribution aktiviteter är:

1. Registrera modellen i arbetsytan modellen registret.

2. Skapa en Docker-avbildning, inklusive:
    1. Ladda ned den registrerade modellen från registret. 
    2. Skapa en docker-fil med en Python-miljö baserat på de beroenden som du anger i miljön yaml-fil.
    3. Lägg till modellfiler och bedömningsskriptet som du anger i dockerfile.
    4. Skapa en ny Docker-avbildning med hjälp av dockerfile.
    5. Registrera Docker-avbildningen med Azure Container Registry som är associerade med arbetsytan.

    > [!IMPORTANT]
    > Beroende på din kod sker avbildnings skapandet automatiskt utan dina indata.

3. Distribuera Docker-avbildningen till Azure Container Instance (ACI)-tjänsten eller till Azure Kubernetes Service (AKS).

4. Starta en ny behållare (eller behållare) i ACI eller AKS. 

Mer information om den här processen i den [modellhantering](concept-model-management-and-deployment.md) introduktion.

## <a name="prerequisites"></a>Krav

* En **Azure-prenumeration**. Om du inte har en sådan kan du prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
* För att felsöka lokalt måste du ha en fungerande Docker-installation på det lokala systemet.

    Verifiera din Docker-installation genom att använda kommandot `docker run hello-world` från en terminal eller kommando tolk. Information om hur du installerar Docker eller felsöker Docker-fel finns i [Docker-dokumentationen](https://docs.docker.com/).

## <a name="before-you-begin"></a>Innan du börjar

Om du stöter på några problem, det första du ska göra är att bryta ned aktiviteten distribution (tidigare beskrivs) till enskilda steg för att isolera problemet.

Att dela upp distributionen i aktiviteter är användbart om du använder API för [WebService. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none--overwrite-false-) eller [webservice. deploy_from_model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none--overwrite-false-) , eftersom båda dessa funktioner utför de ovannämnda stegen som en enda åtgärd. Dessa API: er är vanligt vis praktiska, men det hjälper dig att dela upp stegen vid fel sökning genom att ersätta dem med nedanstående API-anrop.

1. Registrera modellen. Här är exempelkod:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Skapa avbildningen. Här är exempelkod:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Distribuera avbildningen som tjänst. Här är exempelkod:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

När du har uppdelade distributionsprocessen i enskilda aktiviteter kan vi titta på några av de vanligaste felen.

## <a name="image-building-fails"></a>Bild som att skapa misslyckas

Om Docker-avbildningen inte kan skapas går det inte att anropa [avbildningen. wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) eller [service. wait_for_deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) med fel meddelanden som kan ge en del LED trådar. Du kan också hitta mer information om felen från image build-loggen. Nedan är exempelkod som visar hur du identifierar build log-uri för avbildning.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

Logg-uri för avbildning är en SAS-URL som pekar på en loggfil som lagras i Azure blob storage. Helt enkelt kopiera och klistra in URI: n i ett webbläsarfönster och du kan hämta och visa loggfilen.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault åtkomst princip och Azure Resource Manager mallar

Avbildnings versionen kan också fungera på grund av ett problem med åtkomst principen på Azure Key Vault. Den här situationen kan inträffa när du använder en Azure Resource Manager-mall för att skapa arbets ytan och associerade resurser (inklusive Azure Key Vault) flera gånger. Du kan till exempel använda mallen flera gånger med samma parametrar som en del av en kontinuerlig integrering och distributions pipeline.

De flesta åtgärder för att skapa resurser via mallar är idempotenta, men Key Vault rensar åtkomst principerna varje gång mallen används. Om du rensar åtkomst principerna bryts åtkomsten till Key Vault för en befintlig arbets yta som använder den. Det här tillståndet resulterar i fel när du försöker skapa nya avbildningar. Följande är exempel på de fel som du kan ta emot:

__Portal__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

För att undvika det här problemet rekommenderar vi en av följande metoder:

* Distribuera inte mallen mer än en gång för samma parametrar. Eller ta bort de befintliga resurserna innan du använder mallen för att återskapa dem.
* Granska Key Vault åtkomst principer och Använd sedan dessa principer för att ange mallens `accessPolicies` egenskap.
* Kontrol lera om Key Vault resursen redan finns. Om det gör det, ska du inte återskapa det via mallen. Du kan till exempel lägga till en parameter som gör att du kan inaktivera skapande av den Key Vault resursen om den redan finns.

## <a name="debug-locally"></a>Felsök lokalt

Om du får problem med att distribuera en modell till ACI eller AKS kan du prova att distribuera den som en lokal webb tjänst. Med hjälp av en lokal webb tjänst blir det enklare att felsöka problem. Docker-avbildningen som innehåller modellen laddas ned och startas i det lokala systemet.

> [!WARNING]
> Distributioner av lokala webb tjänster stöds inte i produktions scenarier.

Om du vill distribuera lokalt ändrar du koden så att den använder `LocalWebservice.deploy_configuration()` för att skapa en distributions konfiguration. Använd sedan `Model.deploy()` för att distribuera tjänsten. I följande exempel distribueras en modell (som finns i variabeln `model`) som en lokal webb tjänst:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Nu kan du arbeta med tjänsten som vanligt. Till exempel visar följande kod sändning av data till tjänsten:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Uppdatera tjänsten

Under lokal testning kan du behöva uppdatera `score.py`-filen för att lägga till loggning eller försöka lösa eventuella problem som du har identifierat. Om du vill läsa in ändringarna i `score.py`-filen använder du `reload()`. Följande kod läser till exempel in skriptet för tjänsten och skickar sedan data till den. Data visas med den uppdaterade `score.py` filen:

> [!IMPORTANT]
> Metoden `reload` är bara tillgänglig för lokala distributioner. Information om hur du uppdaterar en distribution till ett annat beräknings mål finns i avsnittet uppdatering i [Distribuera modeller](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skriptet läses in på nytt från den plats som anges av `InferenceConfig`-objektet som används av tjänsten.

Om du vill ändra modellen, Conda-beroenden eller distributions konfigurationen använder du [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). I följande exempel uppdateras modellen som används av tjänsten:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Ta bort tjänsten

Om du vill ta bort tjänsten använder du [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a>Granska Docker-loggen

Du kan skriva ut detaljerad Docker-motorn loggmeddelanden från objektet. Du kan visa loggen för ACI, AKS och lokala distributioner. Följande exempel visar hur du skriver ut loggarna.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Starta tjänsten misslyckas

När avbildningen har skapats försöker systemet starta en behållare med hjälp av distributions konfigurationen. Som en del i processen för att starta upp behållaren, den `init()` funktionen i din bedömningsskriptet anropas av systemet. Om det finns undantag utan felhantering i den `init()` fungerar, visas **CrashLoopBackOff** fel i felmeddelandet.

Använd informationen i avsnittet [Granska Docker-loggen](#dockerlog) för att kontrol lera loggarna.

## <a name="function-fails-get_model_path"></a>Funktionen misslyckas: get_model_path()

I funktionen `init()` anropas ofta funktionen [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) för att hitta en modell fil eller en mapp med modell filer i behållaren. Om modell filen eller mappen inte kan hittas Miss lyckas funktionen. Det enklaste sättet att felsöka det här felet är att köra den nedan Python-kod i behållaren shell:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Det här exemplet skriver ut den lokala sökvägen (i förhållande till `/var/azureml-app`) i behållaren där bedömnings skriptet förväntar sig att hitta modell filen eller mappen. Därefter kan du kontrollera om filen eller mappen är verkligen där det förväntas vara.

Om du ställer in loggnings nivån på fel sökning kan det leda till att ytterligare information loggas, vilket kan vara användbart vid identifiering av felet.

## <a name="function-fails-runinput_data"></a>Funktionen misslyckas: run(input_data)

Om tjänsten har distribuerats, men den kraschar när du publicerar data till bedömnings-slutpunkten, du kan lägga till fel vid identifiering av instruktionen i din `run(input_data)` så att den returnerar detaljerat felmeddelande i stället. Exempel:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Obs**: returnerar felmeddelanden från den `run(input_data)` anrop görs för felsökning endast syfte. Av säkerhets skäl bör du inte returnera fel meddelanden på det här sättet i en produktions miljö.

## <a name="http-status-code-503"></a>HTTP-statuskod 503

Distributioner av Azure Kubernetes-tjänster stöder automatisk skalning, vilket gör att repliker kan läggas till för att stödja ytterligare belastning. Autoskalning är dock utformad för att hantera **gradvisa** förändringar i belastningen. Om du får stora toppar i begär Anden per sekund, kan klienterna få HTTP-statuskod 503.

Det finns två saker som kan hjälpa till att förhindra 503 status koder:

* Ändra användnings nivån där autoskalning skapar nya repliker.
    
    Som standard är den automatiska skalnings mål användningen inställd på 70%, vilket innebär att tjänsten kan hantera toppar i begär Anden per sekund (RPS) på upp till 30%. Du kan justera användnings målet genom att ange `autoscale_target_utilization` till ett lägre värde.

    > [!IMPORTANT]
    > Den här ändringen innebär inte att repliker skapas *snabbare*. I stället skapas de med ett lägre användnings tröskelvärde. I stället för att vänta tills tjänsten är 70% Använd, och om du ändrar värdet till 30%, så skapas repliker när 30% belastning sker.
    
    Om webb tjänsten redan använder de aktuella Max replikerna och du fortfarande ser 503 status koder ökar du `autoscale_max_replicas` värde för att öka det maximala antalet repliker.

* Ändra det minsta antalet repliker. Att öka de minsta replikerna ger en större pool som hanterar inkommande toppar.

    Om du vill öka det minsta antalet repliker anger `autoscale_min_replicas` till ett högre värde. Du kan beräkna de repliker som krävs genom att använda följande kod och ersätta värden med värden som är speciella för ditt projekt:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Om du får begär ande toppar som är större än de nya minsta replikerna kan hantera kan du få 503s igen. När trafik till tjänsten ökar kan du till exempel behöva öka de lägsta replikerna.

Mer information om hur du ställer in `autoscale_target_utilization`, `autoscale_max_replicas`och `autoscale_min_replicas` för finns i [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) module-referensen.


## <a name="advanced-debugging"></a>Avancerad fel sökning

I vissa fall kan du behöva interaktivt felsöka python-koden som finns i modell distributionen. Om Entry-skriptet till exempel inte fungerar och orsaken inte kan fastställas av ytterligare loggning. Genom att använda Visual Studio Code och Python Tools for Visual Studio (PTVSD) kan du koppla till koden som körs i Docker-behållaren.

> [!IMPORTANT]
> Den här fel söknings metoden fungerar inte när du använder `Model.deploy()` och `LocalWebservice.deploy_configuration` för att distribuera en modell lokalt. I stället måste du skapa en avbildning med hjälp av klassen [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) . 

Lokal distribution av webb tjänster kräver en fungerande Docker-installation på det lokala systemet. Mer information om hur du använder Docker finns i [Docker-dokumentationen](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

1. Om du vill installera Python Tools for Visual Studio (PTVSD) i din lokala VS-kod utvecklings miljö använder du följande kommando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Mer information om hur du använder PTVSD med VS Code finns i [fjärrfelsökning](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Om du vill konfigurera VS-kod för att kommunicera med Docker-avbildningen skapar du en ny fel söknings konfiguration:

    1. Från VS Code väljer du __Felsök__ -menyn och väljer sedan __Öppna konfigurationer__. En fil med namnet __Launch. JSON__ öppnas.

    1. I filen __Launch. JSON__ letar du reda på raden som innehåller `"configurations": [`och infogar följande text efter den:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Om det redan finns andra poster i avsnittet konfigurationer lägger du till ett kommatecken (,) efter den kod som du har infogat.

        Det här avsnittet bifogar Docker-behållaren med port 5678.

    1. Spara filen __Launch. JSON__ .

### <a name="create-an-image-that-includes-ptvsd"></a>Skapa en avbildning som innehåller PTVSD

1. Ändra Conda-miljön för distributionen så att den innehåller PTVSD. I följande exempel visas hur du lägger till den med hjälp av parametern `pip_packages`:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Om du vill starta PTVSD och vänta en anslutning när tjänsten startas lägger du till följande överst i `score.py`-filen:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Under fel sökning kanske du vill göra ändringar i filerna i avbildningen utan att behöva återskapa den. Om du vill installera en text redigerare (vim) i Docker-avbildningen skapar du en ny textfil med namnet `Dockerfile.steps` och använder följande som innehållet i filen:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Med en text redigerare kan du ändra filerna i Docker-avbildningen för att testa ändringar utan att skapa en ny avbildning.

1. Om du vill skapa en avbildning som använder `Dockerfile.steps`-filen använder du parametern `docker_file` när du skapar en avbildning. Följande exempel visar hur du gör detta:

    > [!NOTE]
    > Det här exemplet förutsätter att `ws` pekar på arbets ytan Azure Machine Learning och att `model` är den modell som distribueras. `myenv.yml`-filen innehåller de Conda-beroenden som skapades i steg 1.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

När avbildningen har skapats visas avbildnings platsen i registret. Platsen liknar följande text:

```text
myregistry.azurecr.io/myimage:1
```

I den här text exemplet är register namnet `myregistry` och avbildningen heter `myimage`. Avbildnings versionen är `1`.

### <a name="download-the-image"></a>Ladda ned avbildningen

1. Öppna en kommando tolk, Terminal eller annat gränssnitt och Använd följande [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) -kommando för att autentisera till den Azure-prenumeration som innehåller din Azure Machine Learning arbets yta:

    ```azurecli
    az login
    ```

1. Använd följande kommando för att autentisera till den Azure Container Registry (ACR) som innehåller din avbildning. Ersätt `myregistry` med den som returnerades när du registrerade avbildningen:

    ```azurecli
    az acr login --name myregistry
    ```

1. Använd följande kommando för att ladda ned avbildningen till din lokala Docker. Ersätt `myimagepath` med platsen som returnerades när du registrerade avbildningen:

    ```bash
    docker pull myimagepath
    ```

    Avbildningens sökväg bör likna `myregistry.azurecr.io/myimage:1`. Om `myregistry` är ditt register, `myimage` är din avbildning och `1` är avbildnings versionen.

    > [!TIP]
    > Autentiseringen från föregående steg är inte senaste för alltid. Om du väntar tillräckligt länge mellan kommandot Authentication och kommandot pull, får du ett autentiseringsfel. Om detta händer, autentisera om.

    Hur lång tid det tar att slutföra nedladdningen beror på Internet anslutningens hastighet. En nedladdnings status visas under processen. När hämtningen är klar kan du använda kommandot `docker images` för att kontrol lera att den har laddats ned.

1. Använd följande kommando för att lägga till en tagg för att göra det enklare att arbeta med avbildningen. Ersätt `myimagepath` med plats-värdet från steg 2.

    ```bash
    docker tag myimagepath debug:1
    ```

    För resten av stegen kan du referera till den lokala avbildningen som `debug:1` i stället för värdet för fullständig avbildnings Sök väg.

### <a name="debug-the-service"></a>Felsöka tjänsten

> [!TIP]
> Om du anger en tids gräns för PTVSD-anslutningen i `score.py`-filen måste du ansluta VS-kod till felsökningssessionen innan tids gränsen upphör att gälla. Starta VS Code, öppna den lokala kopian av `score.py`, ange en Bryt punkt och låt den vara klar innan du använder stegen i det här avsnittet.
>
> Mer information om fel sökning och inställning av Bryt punkter finns i [fel sökning](https://code.visualstudio.com/Docs/editor/debugging).

1. Om du vill starta en Docker-behållare med hjälp av avbildningen använder du följande kommando:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Om du vill bifoga VS Code till PTVSD i behållaren öppnar du VS Code och använder F5-tangenten eller väljer __Felsök__. När du uppmanas väljer du __Azure Machine Learning: Docker-felsöknings__ konfiguration. Du kan också välja fel söknings ikonen från sido fältet, __Azure Machine Learning: Docker-felsöknings__ post från List Rute menyn Felsök och sedan använda den gröna pilen för att koppla fel sökaren.

    ![Fel söknings ikonen, starta fel söknings knappen och konfigurations väljaren](media/how-to-troubleshoot-deployment/start-debugging.png)

Vid det här tillfället ansluter VS Code till PTVSD i Docker-behållaren och stannar vid den Bryt punkt som du har angett tidigare. Nu kan du gå igenom koden när den körs, Visa variabler osv.

Mer information om hur du använder VS Code för att felsöka python finns i [Felsöka python-koden](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Ändra behållar filerna

Om du vill göra ändringar i filerna i avbildningen kan du ansluta till den pågående behållaren och köra ett bash-gränssnitt. Därifrån kan du använda VIM för att redigera filer:

1. Om du vill ansluta till den pågående behållaren och starta ett bash-gränssnitt i behållaren använder du följande kommando:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Om du vill hitta filerna som används av tjänsten använder du följande kommando från bash-gränssnittet i behållaren om standard katalogen skiljer sig från `/var/azureml-app`:

    ```bash
    cd /var/azureml-app
    ```

    Härifrån kan du använda VIM för att redigera `score.py`-filen. Mer information om hur du använder vim finns i [använda vim-redigeraren](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Ändringar i en behållare är normalt inte sparade. Om du vill spara de ändringar du gör använder du följande kommando innan du avslutar gränssnittet som startades i ovanstående steg (det vill säga i ett annat gränssnitt):

    ```bash
    docker commit debug debug:2
    ```

    Det här kommandot skapar en ny avbildning med namnet `debug:2` som innehåller dina ändringar.

    > [!TIP]
    > Du måste stoppa den aktuella behållaren och börja använda den nya versionen innan ändringarna börjar gälla.

1. Se till att spara ändringarna som du gör i filerna i behållaren synkroniseras med de lokala filer som VS-koden använder. Annars fungerar inte fel söknings upplevelsen som förväntat.

### <a name="stop-the-container"></a>Stoppa containern

Om du vill stoppa behållaren använder du följande kommando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om distribution:

* [Hur du distribuerar och var](how-to-deploy-and-where.md)
* [Självstudie: Skapa och distribuera modeller](tutorial-train-models-with-aml.md)
