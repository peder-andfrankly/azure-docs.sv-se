---
title: Översikt över Istio
description: Få en översikt över Istio
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3db2a3d75df08e7c668b1983ad9b4493446475df
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530516"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Översikt

[Istio][istio] är ett komplett, anpassningsbart och utöknings Bart tjänst nät.

## <a name="architecture"></a>Arkitektur

Istio tillhandahåller ett data plan som består av [mottagare][envoy-proxy]-baserade sidvagn. Dessa intelligenta proxyservrar styr all nätverks trafik in i och ut ur dina nätappar och arbets belastningar.

Kontroll planet hanterar konfigurationen, principen och Telemetrin via följande [komponenter][what-is-istio]:

- **Mixer** – tillämpar principer för åtkomst kontroll och användning. Samlar in telemetri från de proxyservrar som skickas till [Prometheus][prometheus].

- **Pilot** – tillhandahåller tjänst identifierings-och Traffic Management-principer/konfiguration för proxyservrarna.

- **Citadel** – ger identitets-och säkerhets funktioner som tillåter mTLS mellan tjänster.

- **Text** – abstrakta och ger konfiguration till komponenter.

Följande arkitektur diagram visar hur de olika komponenterna i data planet och kontroll planet interagerar.


![Översikt över Istio-komponenter och arkitektur.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Urvals villkor

Det är viktigt att förstå och ta hänsyn till följande områden när du utvärderar Istio för dina arbets belastningar:

- [Design mål](#design-goals)
- [Trådlösa](#capabilities)
- [Scenarier](#scenarios)


### <a name="design-goals"></a>Design mål

Följande design mål [vägleder][design-goals] Istio-projektet:

- **Maximera genomskinlighet** – Tillåt antagande med minsta möjliga arbets mängd för att få ett verkligt värde från systemet.

- **Utökning** – måste kunna växa och anpassas med föränderliga behov.

- **Portabilitet** – kör enkelt i olika typer av miljöer – molnet lokalt.

- **Princip enhetlighet** – konsekvent i princip definitionen för olika resurser.


### <a name="capabilities"></a>Kapacitet

Istio tillhandahåller följande funktioner:

- **Nät** – gatewayer (flera kluster), virtuella datorer (nät expansion)

- **Trafik hantering** – routning, delning, tids gränser, krets brytare, återförsök, ingress, utgående

- **Princip** – åtkomst kontroll, hastighets begränsning, kvot, anpassade princip kort

- **Säkerhet** – autentisering (JWT), auktorisering, kryptering (mTLS), extern ca (HashiCorp-valv)

- **Iakttagithet** – gyllene mått, spegling, spårning, anpassade kort, Prometheus, Grafana

### <a name="scenarios"></a>Scenarier

Istio passar bra för och föreslås för följande scenarier:

- Kräv utökning och omfattande uppsättning funktioner

- Expansion av nätverk för att inkludera VM-baserade arbets belastningar

- Nätverk med flera kluster tjänster

## <a name="next-steps"></a>Nästa steg

I följande dokumentation beskrivs hur du kan installera Istio på Azure Kubernetes service (AKS):

> [!div class="nextstepaction"]
> [Installera Istio i Azure Kubernetes service (AKS)][istio-install]

Du kan också ytterligare utforska Istio-koncept och ytterligare distributions modeller:

- [Istio-begrepp][what-is-istio]
- [Istio distributions modeller][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
