---
title: Översikt över Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskrivs Azure Monitor för behållare som övervakar AKS-behållare för lösningen och det värde som du får genom att övervaka hälsotillståndet för dina AKS-kluster och Container Instances i Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 8267f8148269f8b1a0717435e57614f09c229de1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841412"
---
# <a name="azure-monitor-for-containers-overview"></a>Azure Monitor för översikt över behållare

Azure Monitor för behållare är en funktion som har utformats för att övervaka prestanda för behållar arbets belastningar som distribueras till:

- Managed Kubernetes kluster som finns i Azure Kubernetes service (AKS)
- Azure Container Instances
- Självhanterade Kubernetes-kluster som finns på Azure Stack eller lokalt
- Azure Red Hat OpenShift

Övervaka dina behållare är viktigt, särskilt när du kör ett produktionskluster i skala med flera program.

Azure Monitor för behållare ger dig insyn i prestanda genom att samla in minne och processor mått från domänkontrollanter, noder och behållare som är tillgängliga i Kubernetes via mått-API. Containerloggar samlas också.  När du har aktiverat övervakning från Kubernetes-kluster samlas mått och loggar in automatiskt åt dig via en behållar version av Log Analytics agent för Linux. Måtten skrivs till mått arkivet och loggdata skrivs till logg lagret som är kopplat till din [Log Analytics](../log-query/log-query-overview.md) -arbetsyta. 

![Azure Monitor för behållare arkitektur](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Vad kostar Azure Monitor för behållare ger?

Azure Monitor för behållare ger en omfattande övervaknings upplevelse med olika funktioner i Azure Monitor så att du kan förstå prestanda och hälsa för ditt Kubernetes-kluster och behållar arbets belastningarna. Med Azure Monitor för behållare kan du:

* Identifiera AKS-behållare som körs på noden och deras genomsnittsanvändningen av processor och minne. Den här kunskapen kan hjälpa dig att identifiera flaskhalsar för resurser.
* Identifiera processor och minne användning av behållargrupper och behållare i Azure Container Instances.  
* Identifiera var behållaren finns i en styrenhet eller en pod. Med hjälp av den här kunskapen kan du visa kontrollantens eller pod's övergripande prestanda. 
* Granska arbetsbelastningar som körs på värden som inte är relaterade till de processer som standard som har stöd för poden Resursanvändning.
* Förstå beteendet för klustret under genomsnittliga och högsta belastning. Den här kunskapen kan hjälpa dig att identifiera kapacitetsbehov och avgöra den maximala belastningen kan klustret hantera. 
* Konfigurera aviseringar för att proaktivt meddela dig eller registrera den när processor-och minnes användning på noder eller behållare överskrider tröskelvärdena, eller när en hälso tillstånds ändring sker i klustret vid en infrastruktur eller noders hälso insamling.
* Integrera med [Prometheus](https://prometheus.io/docs/introduction/overview/) för att visa program-och arbets belastnings mått som samlas in från noder och Kubernetes med hjälp av [frågor](container-insights-log-search.md) för att skapa anpassade aviseringar, instrument paneler och detaljerad analys.

    >[!NOTE]
    >Stöd för Prometheus är en funktion i offentlig för hands version för tillfället.
    >

* Övervaka arbets belastningar för behållare [som distribueras till AKS-motorn](https://github.com/microsoft/OMS-docker/tree/aks-engine) lokalt och [AKS-motorn på Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Övervaka arbets belastningar [för behållare som distribueras till Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Stöd för Azure Red Hat OpenShift är en funktion i offentlig för hands version för tillfället.
    >

Titta på följande videoklipp med en mellanliggande nivå för att hjälpa dig att lära dig mer om att övervaka ditt AKS-kluster med Azure Monitor för behållare.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hur kommer jag åt den här funktionen?

Du kan komma åt Azure Monitor för behållare två sätt, från Azure Monitor eller direkt från det valda AKS-klustret. Från Azure Monitor har du ett globalt perspektiv för alla behållare som distribueras, som övervakas och som inte är så att du kan söka efter och filtrera i dina prenumerationer och resurs grupper och sedan gå vidare till Azure Monitor för behållare från valda behållaren.  Annars kan du komma åt funktionen direkt från en vald AKS-behållare från sidan AKS.  

![Översikt över metoder för att komma åt Azure Monitor för behållare](./media/container-insights-overview/azmon-containers-experience.png)

Om du är intresse rad av att övervaka och hantera dina Docker-och Windows-behållarobjekt som körs utanför AKS för att Visa konfigurations-, gransknings-och resursutnyttjande, se [lösningen för övervakning av behållare](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka ditt Kubernetes-kluster läser du [så här aktiverar du Azure Monitor för behållare](container-insights-onboard.md) för att förstå kraven och tillgängliga metoder för att aktivera övervakning. 
