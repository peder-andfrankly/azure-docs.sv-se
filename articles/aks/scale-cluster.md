---
title: Skala ett Azure Kubernetes service-kluster (AKS)
description: Lär dig hur du skalar antalet noder i ett Azure Kubernetes service-kluster (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 719f45aeeb5c7aa7e9b5e597ed461808c9d2b005
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472585"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Skala antalet noder i ett Azure Kubernetes service-kluster (AKS)

Om resurs behoven för dina program ändras kan du manuellt skala ett AKS-kluster för att köra ett annat antal noder. När du skalar ned är noderna noggrant [avspärradea och töms][kubernetes-drain] för att minimera störningar i program som körs. När du skalar upp väntar AKS tills noderna markeras `Ready` av Kubernetes-klustret innan poddar schemaläggs.

## <a name="scale-the-cluster-nodes"></a>Skala klusternoderna

Börja med att hämta *namnet* på Node-poolen med kommandot [AZ AKS show][az-aks-show] . I följande exempel hämtas Node-namnet för klustret med namnet *myAKSCluster* i resurs gruppen *myResourceGroup* :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Följande exempel på utdata visar att *namnet* är *nodepool1*:

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Använd kommandot [AZ AKS Scale][az-aks-scale] för att skala klusternoderna. I följande exempel skalas ett kluster med namnet *myAKSCluster* till en enda nod. Ange ditt eget *--nodepool-namn* från föregående kommando, t. ex. *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Följande exempel på utdata visar att klustret har skalats till en nod, som du ser i avsnittet *agentPoolProfiles* :

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du manuellt skalat ett AKS-kluster för att öka eller minska antalet noder. Du kan också använda automatisk skalning av [klustret][cluster-autoscaler] för att automatiskt skala klustret.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
