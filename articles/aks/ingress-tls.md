---
title: Skapa en HTTPS-ingång med Azure Kubernetes service-kluster (AKS)
description: Lär dig hur du installerar och konfigurerar en NGINX ingångs hanterare som använder kryptera för att skapa TLS-certifikat i ett Azure Kubernetes service-kluster (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 845ce631209f341612b65b8d6a97e45e6b025a1f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880654"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Skapa en HTTPS ingress-styrenhet på Azure Kubernetes service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar [nginx ingress][nginx-ingress] -kontrollanten i ett Azure Kubernetes service-kluster (AKS). [Cert Manager-][cert-manager] projektet används för att automatiskt generera och konfigurera att [kryptera][lets-encrypt] certifikat. Slutligen körs två program i AKS-klustret, som var och en är tillgänglig över en enskild IP-adress.

Du kan också:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS-certifikat med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Den här artikeln använder Helm för att installera NGINX ingress-kontrollant, cert Manager och en webbapp. Du måste ha Helm initierat i ditt AKS-kluster och med ett tjänst konto för till gången. Kontrol lera att du använder den senaste versionen av Helm. Anvisningar om hur du uppgraderar finns i [installations dokument för Helm][helm-install]. Mer information om hur du konfigurerar och använder Helm finns i [installera program med Helm i Azure Kubernetes service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI-version 2.0.64 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingångs kontroll enhet

Om du vill skapa en ingångs `Helm` kontroll, använder du för att installera *nginx-ingress*. För ytterligare redundans distribueras två repliker av nginx-ingångs styrenheterna med `--set controller.replicaCount` parametern. Se till att det finns fler än en nod i ditt AKS-kluster för att få full nytta av att köra repliker av ingångs styrenheten.

Ingångs kontroll enheten måste också schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i för hands version i AKS) behöver inte köra ingångs styrenheten. En Node-selektor anges med `--set nodeSelector` parametern för att instruera Kubernetes Scheduler att köra nginx-ingångs styrenheten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för de ingress-resurser som heter *ingress-Basic*. Ange ett namn område för din egen miljö efter behov. Om ditt AKS-kluster inte är RBAC-aktiverat `--set rbac.create=false` lägger du till dem i Helm-kommandona.

> [!TIP]
> Om du vill aktivera [IP-konservering för klient källa][client-source-ip] för förfrågningar till behållare i klustret, lägger `--set controller.service.externalTrafficPolicy=Local` du till det i Helm install-kommandot. Klientens käll-IP lagras i begär ande huvudet under *X-forwarded – for*. När du använder en ingångs kontroll för att aktivera IP-konservering för klient källa fungerar inte SSL-vidarekoppling.

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

Under installationen skapas en offentlig Azure-IP-adress för ingångs styrenheten. Den här offentliga IP-adressen är statisk för den ingångs kontrollens livs längd. Om du tar bort ingångs kontrollen går den offentliga IP-adresstilldelning förlorad. Om du sedan skapar ytterligare en ingångs kontroll, tilldelas en ny offentlig IP-adress. Om du vill behålla användningen av den offentliga IP-adressen kan du i stället skapa en ingångs [kontroll enhet med en statisk offentlig IP-adress][aks-ingress-static-tls].

Använd `kubectl get service` kommandot för att hämta den offentliga IP-adressen. Det tar några minuter innan IP-adressen tilldelas till tjänsten.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Inga ingress-regler har skapats ännu. Om du bläddrar till den offentliga IP-adressen visas sidan NGINX ingångs styrenhetens standard 404.

## <a name="configure-a-dns-name"></a>Konfigurera ett DNS-namn

För att HTTPS-certifikaten ska fungera korrekt konfigurerar du ett fullständigt domän namn för IP-adressen för ingångs enheten. Uppdatera följande skript med IP-adressen för din ingångs kontroll och ett unikt namn som du vill använda för FQDN:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Ingångs styrenheten är nu tillgänglig via FQDN.

## <a name="install-cert-manager"></a>Installera cert-Manager

NGINX ingångs styrenheten stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS. Den här artikeln visar hur du använder [cert Manager][cert-manager], som [][lets-encrypt] ger automatiska funktioner för att skapa och hantera certifikat.

> [!NOTE]
> I `staging` den här artikeln används miljön för att kryptera. I produktions distributioner använder `letsencrypt-prod` du och `https://acme-v02.api.letsencrypt.org/directory` i resurs definitionerna och när du installerar Helm-diagrammet.

Om du vill installera certifikat hanterarens kontrollant i ett RBAC-aktiverat kluster, använder du `helm install` följande kommando:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.8.0 \
  jetstack/cert-manager
```

Mer information om cert Manager-konfiguration finns i [cert-Manager-projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Skapa en certifikat utfärdare för kluster utfärdare

Innan certifikaten kan utfärdas kräver certifikat hanteraren en utfärdare [][cert-manager-issuer] eller en [ClusterIssuer][cert-manager-cluster-issuer] -resurs. Dessa Kubernetes-resurser är identiska i funktioner, `Issuer` men fungerar i ett enda namn område `ClusterIssuer` och fungerar i alla namn områden. Mer information finns i dokumentationen om [cert Manager-utfärdaren][cert-manager-issuer] .

Skapa en kluster utfärdare, till exempel `cluster-issuer.yaml`, med hjälp av följande exempel manifest. Uppdatera e-postadressen med en giltig adress från din organisation:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Använd `kubectl apply -f cluster-issuer.yaml` kommandot för att skapa utfärdaren.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Köra demo program

En ingångs kontroll och en certifikat hanterings lösning har kon figurer ATS. Nu ska vi köra två demo program i ditt AKS-kluster. I det här exemplet används Helm för att distribuera två instanser av ett enkelt "Hello World"-program.

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

Båda programmen körs nu på ditt Kubernetes-kluster, men de har kon figurer ATS med en tjänst `ClusterIP`av typen. Därför är programmen inte tillgängliga från Internet. Skapa en Kubernetes ingress-resurs för att göra dem offentligt tillgängliga. I ingress-resursen konfigureras de regler som dirigerar trafik till ett av de två programmen.

I följande exempel dirigeras trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/` till tjänsten med namnet. `aks-helloworld` Trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` dirigeras `ingress-demo` till tjänsten. Uppdatera *värdarna* och *värden* till det DNS-namn som du skapade i föregående steg.

Skapa en fil med `hello-world-ingress.yaml` namnet och kopiera i följande exempel yaml.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
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

Skapa den inkommande resursen med hjälp `kubectl apply -f hello-world-ingress.yaml` av kommandot.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Skapa ett certifikat objekt

Därefter måste du skapa en certifikat resurs. Certifikat resursen definierar det önskade X. 509-certifikatet. Mer information finns i certifikat för certifikat [hanterare][cert-manager-certificates].

CERT Manager har troligt vis skapat ett certifikat objekt automatiskt för dig med hjälp av ingress-shim, som automatiskt distribueras med cert Manager sedan v 0.2.2. Mer information finns i dokumentationen om [ingress-shim][ingress-shim].

Kontrol lera att certifikatet har skapats genom att använda `kubectl describe certificate tls-secret --namespace ingress-basic` kommandot.

Om certifikatet har utfärdats visas utdata som liknar följande:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Om du behöver skapa ytterligare en certifikat resurs kan du göra det med följande exempel manifest. Uppdatera *dnsNames* och *domänerna* till det DNS-namn som du skapade i föregående steg. Om du använder en intern ingångs kontroll anger du det interna DNS-namnet för din tjänst.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret-staging
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Använd `kubectl apply -f certificates.yaml` kommandot för att skapa certifikat resursen.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret-staging created
```

## <a name="test-the-ingress-configuration"></a>Testa ingress-konfigurationen

Öppna en webbläsare till FQDN för din Kubernetes-ingångs kontroll, till exempel *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Som de här exemplen använder `letsencrypt-staging`är det utfärdade SSL-certifikatet inte betrott av webbläsaren. Godkänn varningen om du vill fortsätta till ditt program. Certifikat informationen visar att det *mellanliggande x1* -certifikatet för falska användare utfärdas av vi krypterar. Detta falska certifikat indikerar `cert-manager` att begäran bearbetades korrekt och tagits emot från providern:

![Låt oss kryptera mellanlagrings certifikat](media/ingress/staging-certificate.png)

När du ändrar vad som är krypterat `prod` att använda `staging`i stället för, används ett betrott certifikat som utfärdats av att kryptera, som du ser i följande exempel:

![Låt oss kryptera certifikat](media/ingress/certificate.png)

Demo programmet visas i webbläsaren:

![Exempel på program ett](media/ingress/app-one.png)

Lägg nu till sökvägen till */Hello-World-Two* i FQDN, till exempel *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* . Det andra demonstrations programmet med den anpassade rubriken visas:

![Program exempel två](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln används Helm för att installera ingångs komponenter, certifikat och exempel appar. När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. De här resurserna omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser kan du antingen ta bort hela exempel namnområdet eller enskilda resurser.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort exempel namn området och alla resurser

Om du vill ta bort hela exempel namnområdet `kubectl delete` använder du kommandot och anger namn på namn området. Alla resurser i namn området tas bort.

```console
kubectl delete namespace ingress-basic
kubectl delete namespace cert-manager
```

Ta sedan bort Helm-lagrings platsen för appen AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

Alternativt är en mer detaljerad metod att ta bort de enskilda resurserna som skapats. Ta först bort certifikat resurserna:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Visar nu Helm-versionerna med `helm list` kommandot. Leta efter diagram med namnet *nginx – ingress*, *cert Manager*och *AKS-HelloWorld*, som du ser i följande exempel utdata:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Wed Mar  6 19:37:43 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      kube-system
loitering-waterbuffalo  1           Wed Mar  6 20:25:01 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
flabby-deer             1           Wed Mar  6 20:27:54 2019    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Wed Mar  6 20:27:59 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort utgåvorna `helm delete` med kommandot. I följande exempel tar vi bort NGINX ingress Deployment, Certificate Manager och de två exempel AKS Hello World-apparna.

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Ta sedan bort Helm-lagrings platsen för appen AKS Hello World:

```console
helm repo remove azure-samples
```

Ta bort det egna namn området. `kubectl delete` Använd kommandot och ange namn på namn område:

```console
kubectl delete namespace ingress-basic
```

Slutligen tar du bort ingångs vägen som riktar sig mot trafik till exempel apparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln ingår några externa komponenter i AKS. Mer information om dessa komponenter finns i följande projekt sidor:

- [Helm CLI][helm-cli]
- [NGINX ingress-styrenhet][nginx-ingress]
- [cert-manager][cert-manager]

Du kan också:

- [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
- [Aktivera routnings tillägget för HTTP-program][aks-http-app-routing]
- [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS-certifikat med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
