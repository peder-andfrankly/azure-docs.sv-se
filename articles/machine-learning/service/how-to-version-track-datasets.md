---
title: Versions hantering för data uppsättning
titleSuffix: Azure Machine Learning service
description: Lär dig hur du bäst version av data uppsättningar och hur versioner fungerar med maskin inlärnings pipeliner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: ''
ms.openlocfilehash: 6940b6ecc231befba908271920e31d4821338baa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928946"
---
# <a name="version-and-track-datasets-in-experiments"></a>Version och spårning av data uppsättningar i experiment
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du använder version och spår Azure Machine Learning data uppsättningar för reproducerbarhet. Versions hantering av data uppsättningar är ett sätt att ange data uppsättningens tillstånd så att du kan använda en angiven version av data uppsättningen för framtida experiment.

Scenarier för typiska versioner:

* När nya data är tillgängliga för omskolning
* När du använder olika metoder för data förberedelse eller funktions teknik

## <a name="prerequisites"></a>Krav

Du behöver följande för den här självstudien:

- [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Det här SDK: t innehåller paketet [azureml-DataSets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) .
    
- En [Azure Machine Learning-arbetsyta](concept-workspace.md). Hämta ett befintligt namn genom att köra följande kod eller [skapa en ny arbets yta](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- En [Azure Machine Learning data uppsättning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrera och hämta data uppsättnings versioner

Genom att registrera en data uppsättning kan du version, åter användning och dela den över experiment och med kollegor. Du kan registrera flera data uppsättningar med samma namn och hämta en speciell version efter namn och versions nummer.

### <a name="register-a-dataset-version"></a>Registrera en data uppsättnings version

Följande kod registrerar en ny version av `titanic_ds` data uppsättningen genom att ange parametern `create_new_version` till `True`. Om det inte finns någon befintlig `titanic_ds` data uppsättning registrerad i arbets ytan skapar koden en ny data uppsättning med namnet `titanic_ds` och anger dess version till 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Hämta en data uppsättning efter namn

Som standard returnerar metoden [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) i klassen `Dataset` den senaste versionen av data uppsättningen som är registrerad i arbets ytan. 

Följande kod hämtar version 1 av `titanic_ds`-datauppsättningen.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Metod tips för versions hantering

När du skapar en data uppsättnings version skapar du *inte* en extra kopia av data med arbets ytan. Eftersom data uppsättningar är referenser till data i din lagrings tjänst har du en enda källa för sanningen, som hanteras av din lagrings tjänst.

>[!IMPORTANT]
> Om data som refereras till av data mängden skrivs över eller tas bort, återställs *inte* ändringen om du anropar en angiven version av data uppsättningen.

När du läser in data från en data uppsättning laddas alltid det aktuella data innehåll som refereras till av data uppsättningen. Om du vill se till att varje data uppsättnings version är reproducerbar, rekommenderar vi att du inte ändrar data innehåll som data uppsättnings versionen refererar till. När nya data kommer in, sparar du nya datafiler i en separat datamapp och skapar sedan en ny data uppsättnings version som innehåller data från den nya mappen.

Följande bild och exempel kod visar det rekommenderade sättet att strukturera datamapparna och skapa data uppsättnings versioner som refererar till dessa mappar:

![Mappstruktur](media/how-to-version-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>Version en data uppsättning för pipeline-utdata

Du kan använda en data uppsättning som indata och utdata för varje Machine Learning pipeline-steg. När du kör pipelines igen registreras utdata för varje pipeline-steg som en ny data uppsättnings version.

Eftersom Machine Learning pipelines fyller i utdata för varje steg i en ny mapp varje gång pipelinen återanvänds, är de versioner av data uppsättningarna som är i drift att återproduceras.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Spåra data uppsättningar i experiment

För varje Machine Learning experiment kan du enkelt spåra data uppsättningarna som används som indata genom experiment `Run`-objektet.

I följande kod används metoden [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) för att spåra vilka indata-datauppsättningar som användes när experimentet kördes:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Du kan också hitta `input_datasets` från experiment med [Azure Machine Learning Studio](https://ml.azure.com/). 

Följande bild visar var du hittar indata-datauppsättningen för ett experiment på Azure Machine Learning Studio. I det här exemplet går du till fönstret **experiment** och öppnar fliken **Egenskaper** för en speciell körning av experimentet `keras-mnist`.

![Indata-datauppsättningar](media/how-to-version-datasets/input-datasets.png)

Använd följande kod för att registrera modeller med data uppsättningar:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Efter registreringen kan du se en lista över modeller som registrerats med data uppsättningen med python eller [Azure Machine Learning Studio](https://ml.azure.com/). Följande vy är från fönstret **data uppsättningar** under **till gångar**. Välj data uppsättningen och välj sedan fliken **modeller** för en lista med de modeller som är registrerade med data uppsättningen. 

![Data uppsättnings modeller för indata](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Nästa steg

* [Träna med data uppsättningar](how-to-train-with-datasets.md)
* [Fler exempel på data uppsättnings antecknings böcker](https://aka.ms/dataset-tutorial)
