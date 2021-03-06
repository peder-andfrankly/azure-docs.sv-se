---
title: Azure Service Fabric-Händelseanalys med Azure Monitor loggar | Microsoft Docs
description: Lär dig mer om att visualisera och analysera händelser med hjälp av Azure Monitor-loggarna för övervakning och diagnostik för Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: ba4923edbc59f0e6650fda1a71e1c4f79b884cf2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393493"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Händelseanalys och visualisering med Azure Monitor-loggar
 Azure Monitor-loggar samlas in och analyserar telemetri från program och tjänster i molnet och ger analysverktyg som hjälper dig att maximera tillgänglighet och prestanda. Den här artikeln beskrivs hur du kör frågor i Azure Monitor-loggar att få insikter och felsöka vad som händer i ditt kluster. Följande vanliga frågor tas upp:

* Hur felsöker jag health-händelser?
* Hur vet jag när en nod stängs av?
* Hur vet jag om min programtjänster har startats eller stoppats?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Översikt över Log Analytics-arbetsytan

>[!NOTE] 
>När lagringskontot är aktiverat som standard när kluster skapas, måste du fortfarande ställa in Log Analytics-arbetsytan att läsa från den diagnostiska lagringen.

Azure Monitor loggar samlar in data från hanterade resurser, inklusive en Azure storage-tabell eller en agent och underhåller den i ett centrallager. Data kan sedan användas för analys, aviseringar och visualisering eller ytterligare export. Azure Monitor loggar har stöd för händelser, prestandadata eller anpassade data. Kolla in [steg för att konfigurera diagnostics-tillägg för att aggregera händelser](service-fabric-diagnostics-event-aggregation-wad.md) och [steg för att skapa en Log Analytics-arbetsyta för att läsa från händelser i storage](service-fabric-diagnostics-oms-setup.md) att kontrollera att data flödar till Azure Monitor loggar.

När data har tagits emot av Azure Monitor-loggar, Azure har flera *övervakning lösningar* som är förpaketerade lösningar eller driftsinstrumentpaneler övervaka inkommande data, anpassade till flera scenarier. Dessa inkluderar en *Service Fabric-analys* lösning och en *behållare* lösning som är de två mest relevanta som diagnostik och övervakning av när du använder Service Fabric-kluster. Den här artikeln beskriver hur du använder Service Fabric-analys-lösning som har skapats med arbetsytan.

## <a name="access-the-service-fabric-analytics-solution"></a>Lösning för Service Fabric-analys

I den [Azure-portalen](https://portal.azure.com)går du till resursgruppen där du skapade Service Fabric-analys-lösningen.

Välj resursen som **ServiceFabric\<nameOfOMSWorkspace\>** .

I `Summary`, visas paneler i form av ett diagram för var och en av de lösningar som är aktiverad, inklusive ett för Service Fabric. Klicka på den **Service Fabric** graph för att fortsätta till Service Fabric-analys-lösningen.

![Service Fabric-lösning](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Följande bild visar öppnas startsidan för Service Fabric-analys-lösning. Den här sidan ger en ögonblicksbild överblick över vad som händer i ditt kluster.

![Service Fabric-lösning](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Om du har aktiverat diagnostik när klustret har skapats kan du se händelser för 

* [Service Fabric-kluster-händelser](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors programming modellhändelser](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programming modellhändelser](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Utöver Service Fabric-händelser direkt ur lådan mer detaljerad händelser kan samlas in av [uppdatera konfigurationen av diagnostiktillägget](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Visa Service Fabric-händelser, inklusive åtgärder på noder

På sidan Service Fabric-analys klickar du på diagrammet för **Service Fabric-händelser**.

![Service Fabric-lösningen, Användningskanal](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Klicka på **lista** att visa händelser i en lista. När här visas alla systemhändelser som har samlats in. Referens så de kommer från den **WADServiceFabricSystemEventsTable** i Azure Storage-konto och på samma sätt reliable services och aktörer händelser visas bredvid kommer från dessa respektive tabeller.
    
![Fråga Användningskanal](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Du kan också klicka på förstoringsglaset till vänster och använda Kusto-frågespråk för att hitta det du letar efter. Du kan exempelvis använda följande fråga för att hitta alla åtgärder som vidtas på noderna i klustret. Händelse-ID som används nedan hittar du i den [användningskanal händelser](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Du kan fråga på flera fält, till exempel de specifika noderna (dator) systemtjänsten (Aktivitetsnamn).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Visa Service Fabric Reliable Service och aktören händelser

Service Fabric-analys-sidan, klicka på diagrammet för **Reliable Services**.

![Service Fabric Solution Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Klicka på **lista** att visa händelser i en lista. Här kan du se händelser från tillförlitliga tjänster. Du kan se olika händelser för när tjänstens runasync startades och slutfördes som vanligtvis sker på distributioner och uppgraderingar. 

![Query Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Tillförlitliga aktörer händelser kan visas på liknande sätt. Om du vill konfigurera detaljerad händelser för reliable actors, måste du ändra den `scheduledTransferKeywordFilter` i konfigurationen för diagnostiktillägg (se nedan). Information om värdena för dessa finns i den [referens för reliable actors-händelser](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Kusto-frågespråket är kraftfullt. En annan värdefull fråga som du kan köra är att ta reda på vilka noder som genererar flest händelser. Frågan i skärmbilden nedan visar operativa händelser i Service Fabric aggregeras med specifik tjänst och nod.

![Fråga händelser per nod](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill aktivera infrastrukturövervakning d.v.s. prestandaräknare, gå till [att lägga till Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md). Agenten samlar in prestandaräknare och lägger till dem i din befintliga arbetsyta.
* För lokala kluster erbjuder Azure Monitor-loggar en Gateway (HTTP framåt Proxy) som kan användas för att skicka data till Azure Monitor-loggar. Läs mer om att [ansluter datorer utan Internetåtkomst till Azure Monitor-loggar med Log Analytics-gateway](../azure-monitor/platform/gateway.md).
* Konfigurera [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik.
* Bekanta dig med den [loggsökning och frågor](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av Azure Monitor-loggar.
* Få en mer detaljerad översikt över Azure Monitor-loggar och den erbjuder, läsa [vad är Azure Monitor-loggar?](../operations-management-suite/operations-management-suite-overview.md).
