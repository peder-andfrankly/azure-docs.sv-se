---
title: Självstudie om Kubernetes i Azure – Uppgradera ett kluster
description: I den här självstudien om Azure Kubernetes Service (AKS) lär du dig hur du uppgraderar ett befintligt AKS-kluster till den senaste tillgängliga Kubernetes-versionen.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 9fe02c9b563259abb51a1a768c7facdf1bf601f7
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898828"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Självstudier: Uppgradera Kubernetes i Azure Kubernetes Service (AKS)

Som en del av programmets och klustrets livscykel kanske du vill uppgradera till den senaste versionen av Kubernetes och använda nya funktioner. Ett kluster med Azure Kubernetes Service (AKS) kan uppgraderas med hjälp av Azure CLI.

I del sju av sju i den här självstudien uppgraderas ett Kubernetes-kluster. Lär dig att:

> [!div class="checklist"]
> * Identifiera aktuella och tillgängliga Kubernetes-versioner
> * Uppgradera Kubernetes-noderna
> * Verifiera uppgraderingen

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades en app i en containeravbildning. Den här avbildningen laddades upp till Azure Container Registry, och du skapade ett AKS-kluster. Programmet distribuerades sedan till AKS-klustret. Om du inte har gjort dessa steg och vill följa med, börjar du med [självstudie 1 – Skapa][aks-tutorial-prepare-app]behållar avbildningar.

I den här självstudien måste du köra Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Hämta tillgängliga klusterversioner

Innan du uppgraderar ett kluster använder du kommandot [az aks get-upgrades][] för att kontrollera vilka Kubernetes-versioner som är tillgängliga för uppgradering:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

I följande exempel är den aktuella versionen *1.13.10*och de tillgängliga versionerna visas under kolumnen *uppgraderingar* .

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.13.10          1.13.10            1.14.5, 1.14.6
```

## <a name="upgrade-a-cluster"></a>Uppgradera ett kluster

I syfte att minimera störningar av program som körs avspärras och töms noderna noggrant. I den här processen utförs följande steg:

1. Kubernetes-schemaläggaren förhindrar att ytterligare poddar schemaläggs på en nod som ska uppgraderas.
1. Poddar som körs på noden schemaläggs på andra noder i klustret.
1. Det skapas en nod som kör de senaste Kubernetes-komponenterna.
1. När den nya noden är redo och ansluten till klustret börjar Kubernetes-schemaläggaren att köra poddar på den.
1. Den gamla noden tas bort och nästa nod i klustret börjar avspärrnings- och tömningsprocessen.

Använd kommandot [az aks upgrade][] för att uppgradera AKS-klustret. I följande exempel uppgraderas klustret till Kubernetes version *1.14.6*.

> [!NOTE]
> Du kan endast uppgradera en lägre version i taget. Du kan till exempel uppgradera från *1.12. x* till *1.13. x*, men inte uppgradera från *1.12. x* till *1.14. x* direkt. Uppgradera från *1.12. x* till *1.14. x*genom att först uppgradera från *1.12.* x till *1.13. x*och sedan utföra en annan uppgradering från *1.13. x* till *1.14. x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.14.6
```

I följande komprimerade exempel utdata visas *kubernetesVersion* Now Reports *1.14.6*:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.14.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Verifiera en uppgradering

Bekräfta att uppgraderingen lyckades genom att köra kommandot [az aks show][] på följande sätt:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Följande exempel på utdata visar AKS-klustret kör *KubernetesVersion 1.14.6*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.14.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Ta bort klustret

Eftersom den här självstudien är den sista delen i serien kan du ta bort AKS-klustret. Då Kubernetes-noderna körs på virtuella Azure-datorer fortsätter de att debiteras även om du inte använder klustret. Använd kommandot [AZ Group Delete][az-group-delete] för att ta bort resurs gruppen, behållar tjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Anvisningar om hur du tar bort tjänstens huvud namn finns i [AKS Service Principal överväganden och borttagning][sp-delete].

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du uppgraderat Kubernetes i ett AKS-kluster. Du har lärt dig att:

> [!div class="checklist"]
> * Identifiera aktuella och tillgängliga Kubernetes-versioner
> * Uppgradera Kubernetes-noderna
> * Verifiera uppgraderingen

Följ den här länken om du vill veta mer om AKS.

> [!div class="nextstepaction"]
> [Översikt över AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
