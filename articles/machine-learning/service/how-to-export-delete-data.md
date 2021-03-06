---
title: Exportera eller ta bort data från arbets ytan
titleSuffix: Azure Machine Learning
description: 'Lär dig hur du exporterar eller tar bort din arbets yta med Azure Machine Learning Studio-, CLI-, SDK-och autentiserade REST-API: er.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 34ff3f9704b9c84a7daddcfd14fb9cd3e990f794
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716509"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportera eller ta bort data för din Machine Learning service-arbetsyta 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I Azure Machine Learning kan du exportera eller ta bort data för arbets ytan med autentiserade REST API. Den här artikeln beskriver hur du gör.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Kontrol lera dina data för arbets ytan
Produkt data som lagras av Azure Machine Learning kan exporteras och tas bort via Azure Machine Learning Studio-, CLI-, SDK-och autentiserade REST-API: er. Telemetri-data kan nås via Azures sekretess Portal. 

I Azure Machine Learning består personliga data av användar information i historik dokument och telemetri poster för vissa användar interaktioner med tjänsten.

## <a name="delete-workspace-data-with-the-rest-api"></a>Ta bort arbets ytans data med REST API 

Följande API-anrop kan göras med verbet HTTP DELETE för att ta bort data. De är auktoriserade genom att ha ett `Authorization: Bearer <arm-token>`-huvud i begäran där `<arm-token>` är AAD-åtkomsttoken för `https://management.core.windows.net/` slut punkten.  

Information om hur du hämtar denna token och anropar Azure-slutpunkter finns i [Azure REST API-dokumentationen](https://docs.microsoft.com/rest/api/azure/).  

I exemplen nedan ersätter du texten i {} med de instans namn som avgör den associerade resursen.

### <a name="delete-an-entire-workspace"></a>Ta bort en hel arbets yta

Använd det här anropet för att ta bort en hel arbets yta.  
> [!WARNING]
> All information tas bort och arbets ytan kommer inte längre att kunna användas.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Ta bort modeller

Använd det här anropet för att hämta en lista över modeller och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Enskilda modeller kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Ta bort till gångar

Använd det här anropet för att hämta en lista över till gångar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Enskilda till gångar kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Ta bort bilder

Använd det här anropet för att hämta en lista över avbildningar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Enskilda avbildningar kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Ta bort tjänster

Använd det här anropet för att hämta en lista över tjänster och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Enskilda tjänster kan tas bort med:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Exportera tjänst data med REST API

Följande API-anrop kan göras med HTTP GET-verbet för att exportera data. De är auktoriserade genom att ha ett `Authorization: Bearer <arm-token>`-huvud i begäran där `<arm-token>` är AAD-åtkomsttoken för slut punkten `https://management.core.windows.net/`  

Information om hur du hämtar denna token och anropar Azure-slutpunkter finns i [Azure REST API-dokumentationen](https://docs.microsoft.com/rest/api/azure/).   

I exemplen nedan ersätter du texten i {} med de instans namn som avgör den associerade resursen.

### <a name="export-workspace-information"></a>Exportera information om arbets ytan

Använd det här anropet för att hämta en lista över alla arbets ytor:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Information om en enskild arbets yta kan hämtas av:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Exportera beräknings information

Alla beräknings mål som är kopplade till en arbets yta kan hämtas av:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Information om ett enda beräknings mål kan hämtas av:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Exportera körnings historik data
Använd det här anropet för att hämta en lista över alla experiment och deras information:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Alla körningar för ett visst experiment kan erhållas genom att:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Körnings historik objekt kan hämtas av:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Alla körnings mått för ett experiment kan erhållas genom att:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Ett enda körnings mått kan hämtas av:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Exportera artefakter

Använd det här anropet för att hämta en lista över artefakter och deras sökvägar:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Exportera meddelanden

Använd det här anropet för att hämta en lista över lagrade uppgifter:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Aviseringar för en enskild uppgift kan hämtas av:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Exportera data lager

Använd det här anropet för att hämta en lista över data lager:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Enskilda data lager kan hämtas av:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportera modeller

Använd det här anropet för att hämta en lista över modeller och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Enskilda modeller kan hämtas av:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Exportera till gångar

Använd det här anropet för att hämta en lista över till gångar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Enskilda till gångar kan erhållas av:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Exportera bilder

Använd det här anropet för att hämta en lista över avbildningar och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Enskilda avbildningar kan hämtas av:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Exportera tjänster

Använd det här anropet för att hämta en lista över tjänster och deras ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Enskilda tjänster kan hämtas av:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Exportera pipelines experiment

Enskilda experiment kan erhållas genom att:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Exportera Pipeline-diagram

Enskilda diagram kan hämtas av:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exportera pipeline-moduler

Moduler kan hämtas av:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exportera pipeline-mallar

Mallar kan hämtas av:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exportera pipeline-datakällor

Data källor kan hämtas av:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Ta bort till gångar i designern

Ta bort enskilda till gångar i designern där du skapade experimentet:

1. Gå till designern

    ![Ta bort till gångar](media/how-to-export-delete-data.md/delete-experiment.png)

1. I listan väljer du det enskilda pipeline-utkastet som ska tas bort.

1. Välj **Ta bort**.

### <a name="delete-datasets-in-the-designer"></a>Ta bort data uppsättningar i designern

Om du vill ta bort data uppsättningar i designern använder du Azure Portal eller Storage Explorer för att navigera till anslutna lagrings konton och ta bort data uppsättningar där. När data uppsättningar avregistreras i designern tas endast referens punkten i lagring bort. 

## <a name="export-data-in-the-designer"></a>Exportera data i designern

Exportera data som du har lagt till i designern där du skapade experimentet:

1. Välj **data uppsättningar**till vänster.

    ![Hämta data](media/how-to-export-delete-data.md/unregister-dataset.png)
