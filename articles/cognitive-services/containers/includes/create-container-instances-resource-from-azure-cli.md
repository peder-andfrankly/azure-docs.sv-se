---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Azure Container instance-resurs från Azure CLI.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 2080d283c6cb7466dcb4847a81d76a4c3109217a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012300"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Skapa en Azure Container instance-resurs från Azure CLI

I YAML nedan definieras Azure Container instance-resursen. Kopiera och klistra in innehållet i en ny fil med namnet `my-aci.yaml` och ersätt de kommenterade värdena med dina egna. Se [mallformat][template-format] för giltiga yaml. Referera till behållar [databaserna och avbildningarna][repositories-and-images] för de tillgängliga avbildnings namnen och deras motsvarande lagrings plats.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Alla platser har inte samma processor-och minnes tillgänglighet. Se tabellen [plats och resurser][location-to-resource] för att visa en lista över tillgängliga resurser för behållare per plats och operativ system.

Vi förlitar dig på den yaml-fil som [`az container create`][azure-container-create] vi har skapat för kommandot. Kör `az container create` kommandot från Azure CLI och `<resource-group>` Ersätt med ditt eget. För att skydda värden inom en YAML-distribution finns dessutom [säkra värden][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Kommandots utdata är om det `Running...` är giltigt, efter att utdata har ändrats till en JSON-sträng som representerar den nyligen skapade ACI-resursen. Behållar avbildningen är mer än sannolikt inte tillgänglig för en stund, men resursen distribueras nu.

> [!TIP]
> Var uppmärksam på platserna för de allmänt tillgängliga Azure-tjänsterna för för hands versionen, eftersom YAML krävs för att de ska kunna matcha platsen.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
