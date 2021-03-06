---
title: Rotera certifikat i Azure Kubernetes service (AKS)
description: Lär dig hur du roterar dina certifikat i ett Azure Kubernetes service-kluster (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00d8546cb20d12c5f1a94bdcababa04a77c73133
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134416"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Rotera certifikat i Azure Kubernetes service (AKS)

I Azure Kubernetes service (AKS) används certifikat för autentisering med många av dess komponenter. Med jämna mellanrum kan du behöva rotera dessa certifikat för säkerhets-eller princip orsaker. Du kan till exempel ha en princip för att rotera alla dina certifikat var 90 dag.

Den här artikeln visar hur du roterar certifikaten i ditt AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI-version 2.0.76 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].


### <a name="install-aks-preview-cli-extension"></a>Installera AKS-Preview CLI-tillägg

Om du vill använda den här funktionen behöver du *AKS-Preview CLI-* tillägg version 0.4.21 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS-certifikat, certifikat utfärdare och tjänst konton

AKS genererar och använder följande certifikat, certifikat utfärdare och tjänst konton:

* AKS-API-servern skapar en certifikat utfärdare (CA) som kallas för kluster certifikat utfärdaren.
* API-servern har en kluster certifikat utfärdare som signerar certifikat för enkelriktad kommunikation från API-servern till kubelets.
* Varje kubelet skapar också en certifikat signerings förfrågan, som är signerad av kluster certifikat utfärdaren för kommunikation från kubelet till API-servern.
* Nyckel värdes arkivet etcd har ett certifikat som signerats av kluster certifikat utfärdaren för kommunikation från etcd till API-servern.
* Nyckel värdes arkivet etcd skapar en certifikat utfärdare som signerar certifikat för att autentisera och auktorisera datareplikering mellan etcd-replikeringar i AKS-klustret.
* API Aggregator använder kluster certifikat utfärdaren för att utfärda certifikat för kommunikation med andra API: er, t. ex. öppna Service Broker för Azure. API Aggregator kan också ha sin egen certifikat utfärdare för att utfärda dessa certifikat, men för närvarande används kluster certifikat utfärdaren.
* Varje nod använder en tjänst konto-token (SA) som är signerad av kluster certifikat utfärdaren.
* `kubectl` klienten har ett certifikat för att kommunicera med AKS-klustret.

> [!NOTE]
> AKS-kluster som skapats före mars 2019 har certifikat som upphör att gälla efter två år. Alla kluster som skapats efter mars 2019 eller ett kluster som har sina certifikat roterade har certifikat som upphör att gälla efter 30 år.

## <a name="rotate-your-cluster-certificates"></a>Rotera dina kluster certifikat

> [!WARNING]
> Att rotera dina certifikat med hjälp av `az aks rotate-certs` kan orsaka upp till 30 minuters stillestånds tid för ditt AKS-kluster.

Använd [AZ AKS get-credentials][az-aks-get-credentials] för att logga in på ditt AKS-kluster. Det här kommandot hämtar och konfigurerar också `kubectl` klient certifikatet på den lokala datorn.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Använd `az aks rotate-certs` för att rotera alla certifikat, certifikat utfärdare och SAs i klustret.

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Det kan ta upp till 30 minuter innan `az aks rotate-certs` har slutförts. Om kommandot Miss lyckas innan du slutför ska du använda `az aks show` för att kontrol lera att klustrets status är *certifikats rotation*. Om klustret är i ett felaktigt tillstånd kör du `az aks rotate-certs` för att rotera dina certifikat igen.

Kontrol lera att de gamla certifikaten inte längre är giltiga genom att köra ett `kubectl`-kommando. Eftersom du inte har uppdaterat de certifikat som används av `kubectl`visas ett fel meddelande.  Exempel:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Uppdatera certifikatet som används av `kubectl` genom att köra `az aks get-credentials`.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Kontrol lera att certifikaten har uppdaterats genom att köra ett `kubectl`-kommando som nu kommer att lyckas. Exempel:

```console
kubectl get no
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du automatiskt roterar ditt klusters certifikat, certifikat utfärdare och SAs. Du kan se [metod tips för kluster säkerhet och uppgraderingar i Azure Kubernetes service (AKS)][aks-best-practices-security-upgrades] om du vill ha mer information om rekommenderade säkerhets metoder för AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
