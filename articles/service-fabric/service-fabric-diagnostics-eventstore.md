---
title: Azure Service Fabric händelse Store | Microsoft Docs
description: Lär dig mer om Azure Service Fabric EventStore
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: e7ae4c77f958bacabea50b7193817cd41ea54aa9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449772"
---
# <a name="eventstore-overview"></a>Översikt över EventStore

>[!NOTE]
>Från och med Service Fabric version 6.4. the EventStore APIs är bara tillgängliga för Windows-kluster som körs på Azure endast. Vi arbetar på att porta den här funktionen på Linux, samt våra fristående kluster.

## <a name="overview"></a>Översikt

Tjänsten EventStore är introduceras i version 6.2, ett alternativ för övervakning i Service Fabric. EventStore ger ett sätt att förstå tillståndet för dina kluster eller arbetsbelastningar vid en viss tidpunkt.
EventStore är en tillståndskänslig Service Fabric-tjänst som underhåller händelser från klustret. Händelsen exponeras via Service Fabric Explorer, REST och API: er. EventStore frågar klustret direkt för att få diagnostikdata för entiteter i ditt kluster och bör användas för att:

* Diagnostisera problem på utveckling eller testning eller som du kan använda en övervakningspipelinen
* Bekräfta att hanteringsåtgärder som du vidtar i ditt kluster bearbetas korrekt
* En ”ögonblicksbild” av hur Service Fabric interagerar med en viss enhet

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

Om du vill se en fullständig lista över händelser som är tillgängliga i EventStore Se [Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Från och med Service Fabric version 6.4. EventStore APIs och UX är allmänt tillgängliga för Azure Windows-kluster. Vi arbetar på att porta den här funktionen på Linux, samt våra fristående kluster.

Tjänsten EventStore kan frågas för händelser som är tillgängliga för varje entitet och enhetstyp i klustret. Det innebär att du kan fråga efter händelser på följande nivåer:
* Kluster: händelser som är specifika för själva (t.ex. uppgradering av klustret) klustret
* Noder: alla noden händelser på programnivå
* Nod: händelser som är specifika för en nod som identifieras av `nodeName`
* Program: alla händelser på programnivå
* Program: händelser som är specifika för ett program som identifieras av `applicationId`
* Tjänster: händelser från alla tjänster i ditt kluster
* Tjänsten: händelser från en specifik tjänst som identifierats av `serviceId`
* Partitioner: händelser från alla partitioner
* Partition: händelser från en specifik partition som identifieras av `partitionId`
* Partitionsrepliker: händelser från alla repliker / instanser på en specifik partition som identifieras av `partitionId`
* Repliken för partition: händelser från en specifik replik / instans identifieras av `replicaId` och `partitionId`

Läs mer om API Kolla in den [EventStore-API-referens](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

Tjänsten EventStore har också möjlighet att korrelera händelser i klustret. Genom att titta på händelser som har skrivits på samma gång från olika entiteter som kan påverka varandra, kan tjänsten EventStore att länka dessa händelser för att identifiera orsaker till aktiviteter i ditt kluster. Till exempel sker något av dina program blivit defekt utan framkallat ändringar i EventStore ska också titta på andra händelser som exponeras av plattformen och kan kombinera detta med en `Error` eller `Warning` händelse. Detta underlättar snabbare felsökning och analys av rotorsaker.

## <a name="enable-eventstore-on-your-cluster"></a>Aktivera EventStore på ditt kluster

### <a name="local-cluster"></a>Lokalt kluster

I [fabricSettings.json i klustret](service-fabric-cluster-fabric-settings.md), lägga till EventStoreService som en funktion för tillägg och utföra en uppgradering av klustret.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Azure-kluster av version 6.5 +
Om din Azure-kluster hämtar uppgraderats till version 6.5 eller högre, aktiverat EventStore automatiskt på ditt kluster. Om du vill avanmäla dig, måste du uppdatera klustret mallen med följande:

* Använd en API-versionen av `2019-03-01` eller senare 
* Lägga till följande kod i dina egenskaper i ditt kluster
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Azure-klusterversion 6.4

Om du använder version 6.4, kan du redigera Azure Resource Manager-mallen för att aktivera EventStore-tjänsten. Detta görs genom att utföra en [config uppgradering av kluster](service-fabric-cluster-config-upgrade-azure.md) och lägger till följande kod kan du använda PlacementConstraints för att placera repliker av EventStore-tjänsten på en specifik NodeType t.ex. en NodeType för systemtjänster . Den `upgradeDescription` avsnittet konfigurerar config uppgraderingen för att utlösa en omstart på noderna. Du kan ta bort avsnittet i en annan uppdatering.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Nästa steg
* Kom igång med EventStore-API - [med EventStore APIs i Azure Service Fabric-kluster](service-fabric-diagnostics-eventstore-query.md)
* Mer information om listan över händelser som erbjuds av EventStore - [Service Fabric-händelser](service-fabric-diagnostics-event-generation-operational.md)
* Översikt över övervakning och diagnostik i Service Fabric - [övervakning och diagnostik för Service Fabric](service-fabric-diagnostics-overview.md)
* Visa en fullständig lista över API-anrop - [EventStore REST API-referens](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Läs mer om hur du övervakar ditt kluster- [kluster och plattformen](service-fabric-diagnostics-event-generation-infra.md).
