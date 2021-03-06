---
title: Exponera en WebSocket-Server för Application Gateway
description: Den här artikeln innehåller information om hur du exponerar en WebSocket-Server för att Application Gateway med ingångs styrenheten för AKS-kluster.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 01fde82e69917f59f6519524c4c8828feb84a4f9
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795974"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Exponera en WebSocket-Server för Application Gateway

Som beskrivs i dokumentationen för Application Gateway v2 – den [innehåller inbyggt stöd för WebSocket-och http/2-protokollen](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic). Observera att för både Application Gateway och Kubernetes ingress – det finns ingen användar konfigurerbar inställning för att selektivt aktivera eller inaktivera WebSocket-stöd.

I Kubernetes Deployment YAML nedan visas den minsta konfiguration som används för att distribuera en WebSocket-Server, vilket är samma som att distribuera en vanlig webb server:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Med tanke på att alla krav uppfylls och du har en Application Gateway som styrs av en Kubernetes ingress i din AKS, skulle distributionen ovan leda till att en WebSockets-Server exponeras på port 80 i Application Gateway offentliga IP och `ws.contoso.com` domänsuffix.

Följande spiral-kommando testar distributionen av WebSocket-servern:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket Health-avsökningar

Om distributionen inte uttryckligen definierar hälso avsökningar försöker Application Gateway ett HTTP-fel på WebSocket-serverns slut punkt.
Beroende på Server implementeringen ([här är en som vi älskar](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) kan WebSocket-speciella huvuden krävas (`Sec-Websocket-Version` för instans).
Eftersom Application Gateway inte lägger till WebSocket-huvuden, är Application Gateway s hälso avsöknings svar från din WebSocket-Server förmodligen `400 Bad Request`.
Som ett resultat Application Gateway Markera din poddar som ohälsosam, vilket kommer att leda till en `502 Bad Gateway` för användare av WebSocket-servern.
För att undvika detta kan du behöva lägga till en HTTP GET-hanterare för en hälso kontroll på servern (`/health` till exempel, som returnerar `200 OK`).
