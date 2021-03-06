---
title: Skapa en grundläggande ingress-kontrollant i Azure Kubernetes service (AKS)
description: Lär dig hur du installerar och konfigurerar en grundläggande NGINX ingress-kontrollant i ett Azure Kubernetes service-kluster (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 7b1fb26adc49067c35745011414ada7b33d7e55e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913584"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Skapa en ingress-kontrollant i Azure Kubernetes service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [nginx ingress-kontrollanten][nginx-ingress] i ett Azure Kubernetes service-kluster (AKS). Två program körs sedan i AKS-klustret, som var och en är tillgänglig över den enskilda IP-adressen.

Du kan också:

- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS [-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln använder Helm för att installera NGINX ingress-kontrollant, cert Manager och en webbapp. Du måste ha Helm initierat i ditt AKS-kluster och med ett tjänst konto för till gången. Mer information om hur du konfigurerar och använder Helm finns i [installera program med Helm i Azure Kubernetes service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI-version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingångs kontroll enhet

Om du vill skapa en ingångs kontroll använder du `Helm` för att installera *nginx-ingress*. För ytterligare redundans distribueras två repliker av NGINX-ingångs styrenheterna med parametern `--set controller.replicaCount`. Se till att det finns fler än en nod i ditt AKS-kluster för att få full nytta av att köra repliker av ingångs styrenheten.

Ingångs kontroll enheten måste också schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i för hands version i AKS) behöver inte köra ingångs styrenheten. En Node-selektor anges med parametern `--set nodeSelector` för att instruera Kubernetes Scheduler att köra NGINX-ingångs styrenheten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för de ingress-resurser som heter *ingress-Basic*. Ange ett namn område för din egen miljö efter behov. Om ditt AKS-kluster inte är RBAC-aktiverat lägger du till `--set rbac.create=false` i Helm-kommandona.

> [!TIP]
> Om du vill aktivera [IP-konservering för klient källa][client-source-ip] för begär anden till behållare i klustret lägger du till `--set controller.service.externalTrafficPolicy=Local` i Helm install-kommandot. Klientens käll-IP lagras i begär ande huvudet under *X-forwarded – for*. När du använder en ingångs kontroll för att aktivera IP-konservering för klient källa fungerar inte SSL-vidarekoppling.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

När belastnings Utjämnings tjänsten för Kubernetes skapas för NGINX-ingångs styrenheten tilldelas en dynamisk offentlig IP-adress, som visas i följande exempel:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
aspiring-labradoodle-nginx-ingress-controller        LoadBalancer   10.0.61.144    40.117.74.8   80:30386/TCP,443:32276/TCP   6m2s
aspiring-labradoodle-nginx-ingress-default-backend   ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Inga ingångs regler har skapats ännu. därför visas sidan NGINX ingress Controller standard 404 om du bläddrar till den interna IP-adressen. Ingress-regler konfigureras i följande steg.

## <a name="run-demo-applications"></a>Köra demo program

För att se hur ingångs enheten fungerar kan vi köra två demo program i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt "Hello World"-program.

Innan du kan installera exempel Helm-diagrammen lägger du till Azure samples-lagringsplatsen i din Helm-miljö på följande sätt:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Skapa det första demonstrations programmet från ett Helm-diagram med följande kommando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Installera nu en andra instans av demo programmet. För den andra instansen anger du en ny rubrik så att de två programmen är visuellt åtskilda. Du anger också ett unikt tjänst namn:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Skapa en ingress-väg

Båda programmen körs nu på ditt Kubernetes-kluster. Skapa en Kubernetes ingress-resurs för att dirigera trafik till varje program. I ingress-resursen konfigureras de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras trafik till adress `http://40.117.74.8/` till tjänsten med namnet `aks-helloworld`. Trafik till adress `http://40.117.74.8/hello-world-two` dirigeras till `ingress-demo`s tjänsten.

Skapa en fil med namnet `hello-world-ingress.yaml` och kopiera i följande exempel YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Skapa den inkommande resursen med hjälp av kommandot `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testa ingångs styrenheten

Om du vill testa vägarna för ingångs styrenheten bläddrar du till de två programmen. Öppna en webbläsare till IP-adressen för din NGINX-ingångs kontroll, till exempel *http://40.117.74.8* . Det första demo programmet visas i webbläsaren, som du ser i följande exempel:

![Första app som körs bakom ingångs styrenheten](media/ingress-basic/app-one.png)

Lägg nu till */Hello-World-Two* -sökvägen till IP-adressen, t. ex. *http://40.117.74.8/hello-world-two* . Det andra demonstrations programmet med den anpassade rubriken visas:

![Den andra appen som körs bakom ingångs styrenheten](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln används Helm för att installera ingångs komponenter och exempel appar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. De här resurserna omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempel namnområdet eller enskilda resurser.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempel namn området och alla resurser

Om du vill ta bort hela exempel namnområdet använder du kommandot `kubectl delete` och anger namn på namn området. Alla resurser i namn området tas bort.

```console
kubectl delete namespace ingress-basic
```

Ta sedan bort Helm-lagrings platsen för appen AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

Alternativt är en mer detaljerad metod att ta bort de enskilda resurserna som skapats. Visar en lista över Helm-utgåvor med kommandot `helm list`. Leta efter diagram med namnet *nginx – ingress* och *AKS-HelloWorld*, som du ser i följande exempel resultat:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
aspiring-labradoodle    1           Wed Mar 27 19:55:37 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      ingress-basic
esteemed-koala          1           Wed Mar 27 19:59:18 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
wonderful-puma          1           Wed Mar 27 19:59:07 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
```

Ta bort utgåvorna med kommandot `helm delete`. I följande exempel tar vi bort NGINX ingress-distributionen och de två exempel AKS Hello World-apparna.

```
$ helm delete aspiring-labradoodle esteemed-koala wonderful-puma

release "aspiring-labradoodle" deleted
release "esteemed-koala" deleted
release "wonderful-puma" deleted
```

Ta sedan bort Helm-lagrings platsen för appen AKS Hello World:

```console
helm repo remove azure-samples
```

Ta bort ingångs vägen som riktar sig mot trafik till exempel apparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

Slutligen kan du ta bort själva namn området. Använd kommandot `kubectl delete` och ange namn på namn området:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln ingår några externa komponenter i AKS. Mer information om dessa komponenter finns i följande projekt sidor:

- [Helm CLI][helm-cli]
- [NGINX ingress-styrenhet][nginx-ingress]

Du kan också:

- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS [-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
