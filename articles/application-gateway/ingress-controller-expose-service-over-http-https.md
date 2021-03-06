---
title: Exponera en AKS-tjänst via HTTP eller HTTPS med hjälp av Application Gateway
description: Den här artikeln innehåller information om hur du exponerar en AKS-tjänst via HTTP eller HTTPS med hjälp av Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795575"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Exponera en AKS-tjänst via HTTP eller HTTPS med hjälp av Application Gateway 

De här självstudierna illustrerar användningen av [Kubernetes ingress-resurser](https://kubernetes.io/docs/concepts/services-networking/ingress/) för att exponera en exempel Kubernetes-tjänst via [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) över http eller https.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Installerat `ingress-azure` Helm-diagram.
  - [**Bygg-distribution**](ingress-controller-install-new.md): om du börjar från grunden läser du de här installations anvisningarna, som beskriver steg för att distribuera ett AKS-kluster med Application Gateway och installerar Application Gateway-ingångs styrenheten på AKS-klustret.
  - [**Brownfield-distribution**](ingress-controller-install-existing.md): om du har ett befintligt AKS-kluster och Application Gateway kan du läsa dessa instruktioner för att installera Application Gateway-ingångs styrenheten på AKS-klustret.
- Om du vill använda HTTPS i det här programmet behöver du ett x509-certifikat och dess privata nyckel.

## <a name="deploy-guestbook-application"></a>Distribuera `guestbook` program

Gäst programmet är ett kanoniskt Kubernetes-program som består av en webb GRÄNSSNITTs-frontend, en server del och en Redis-databas. Som standard visar `guestbook` sitt program via en tjänst med namnet `frontend` på port `80`. Utan en Kubernetes ingress-resurs kan tjänsten inte nås utanför AKS-klustret. Vi använder programmet och installationen inleder resurser för att komma åt programmet via HTTP och HTTPS.

Följ anvisningarna nedan för att distribuera gäst programmet.

1. Hämta [`guestbook-all-in-one.yaml` härifrån](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Distribuera `guestbook-all-in-one.yaml` till ditt AKS-kluster genom att köra

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Nu har `guestbook` programmet distribuerats.

## <a name="expose-services-over-http"></a>Exponera tjänster över HTTP

För att kunna exponera gäst programmet, kommer vi att använda följande ingående resurs:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Den här ingången kommer att exponera `frontend`-tjänsten för `guestbook-all-in-one`-distributionen som en standard Server del av Application Gateway.

Spara ovanstående ingress-resurs som `ing-guestbook.yaml`.

1. Distribuera `ing-guestbook.yaml` genom att köra:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Kontrol lera loggen för ingångs styrenheten för distributions status.

Nu bör `guestbook` programmet vara tillgängligt. Du kan kontrol lera detta genom att besöka Application Gatewayens offentliga adress.

## <a name="expose-services-over-https"></a>Exponera tjänster över HTTPS

### <a name="without-specified-hostname"></a>Utan angivet värdnamn

Om du inte anger värd namnet är gäst tjänsten tillgänglig på alla värd namn som pekar på programgatewayen.

1. Innan du distribuerar ingress måste du skapa en Kubernetes-hemlighet som värd för certifikatet och den privata nyckeln. Du kan skapa en Kubernetes-hemlighet genom att köra

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Definiera följande ingress. I ingressen anger du namnet på hemligheten i avsnittet `secretName`.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Ersätt `<guestbook-secret-name>` i denna ingress-resurs med namnet på din hemlighet. Lagra ovanstående ingress-resurs i ett fil namn `ing-guestbook-tls.yaml`.

1. Distribuera ing-gäst-TLS. yaml genom att köra

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Kontrol lera loggen för ingångs styrenheten för distributions status.

Nu är det `guestbook` programmet tillgängligt på både HTTP och HTTPS.

### <a name="with-specified-hostname"></a>Med angivet värdnamn

Du kan också ange värd namnet för ingången för att kunna multiplex TLS-konfigurationer och-tjänster.
Genom att ange hostname, är gäst tjänsten bara tillgänglig på den angivna värden.

1. Definiera följande ingress.
    I ingressen anger du namnet på hemligheten i avsnittet `secretName` och ersätter värd namnet i avsnittet `hosts`.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Distribuera `ing-guestbook-tls-sni.yaml` genom att köra

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Kontrol lera loggen för ingångs styrenheten för distributions status.

Nu är `guestbook` programmet endast tillgängligt på både HTTP och HTTPS på den angivna värden (`<guestbook.contoso.com>` i det här exemplet).

## <a name="integrate-with-other-services"></a>Integrera med andra tjänster

Följande ingångar gör att du kan lägga till ytterligare sökvägar i den här ingången och omdirigera Sök vägarna till andra tjänster:

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
