---
title: Azure Service Fabric CLI – sfctl sa – kluster | Microsoft Docs
description: Beskriver kommandona för fristående kluster för Service Fabric CLI-sfctl.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: ecdd288d7cb320b91ab4c69697d334f8d9459e62
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901022"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Hantera fristående Service Fabric kluster.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
| konfigurationsfil | Hämta Service Fabric fristående kluster konfiguration. |
| config-Upgrade | Börja uppgradera konfigurationen av ett Service Fabric fristående kluster. |
| uppgradering-status | Hämta uppgraderings status för kluster konfiguration för ett Service Fabric fristående kluster. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa – kluster konfiguration
Hämta Service Fabric fristående kluster konfiguration.

Kluster konfigurationen innehåller egenskaper för klustret som innehåller olika nodtyper i klustret, säkerhetskonfigurationer, fel-och uppgraderings domänens topologier osv.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Configuration-API-version [required] | API-versionen av den fristående kluster-JSON-konfigurationen. |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa – kluster konfiguration – uppgradering
Börja uppgradera konfigurationen av ett Service Fabric fristående kluster.

Verifiera de angivna parametrarna för konfigurations uppgradering och börja uppgradera kluster konfigurationen om parametrarna är giltiga.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --Cluster-config [required] | Kluster konfigurationen. |
| --program-hälso principer | JSON-kodad ord lista med par av program typ namn och maximal procent andel är inte felfri innan fel uppstår. |
| --delta-ej felfri-noder | Den högsta tillåtna procent andelen av delta hälso försämring under uppgraderingen. Tillåtna värden är heltals värden från noll till 100. |
| --hälso kontroll-försök igen | Hur lång tid det tar mellan försök att utföra hälso kontroller om programmet eller klustret inte är felfritt.  Standard\: PT0H0M0S. |
| --hälso kontroll – stabil | Hur lång tid programmet eller klustret måste vara felfritt innan uppgraderingen fortsätter till nästa uppgraderings domän.  Standard\: PT0H0M0S. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --hälso kontroll-vänta | Vänte tiden när en uppgraderings domän har slutförts innan hälso kontrollerna påbörjas.  Standard\: PT0H0M0S. |
| --timeout-t | Standard\: 60. |
| --ej felfri-program | Högsta tillåtna procent andel felaktiga program under uppgraderingen. Tillåtna värden är heltals värden från noll till 100. |
| --ej felfri-noder | Den högsta tillåtna procent andelen Felaktiga noder under uppgraderingen. Tillåtna värden är heltals värden från noll till 100. |
| --uppgradera-Domain-delta-unhealthy-Nodes | Den högsta tillåtna procent andelen uppgradering av uppgraderings domänens delta hälso försämring under uppgraderingen. Tillåtna värden är heltals värden från noll till 100. |
| --uppgradering-Domain-timeout | Hur lång tid varje uppgraderings domän måste vara slutförd innan FailureAction körs.  Standard\: PT0H0M0S. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |
| --uppgraderings-timeout | Den tid som den övergripande uppgraderingen måste slutföras innan FailureAction körs.  Standard\: PT0H0M0S. <br><br> Den tolkas först som en sträng som representerar en varaktighet på ISO 8601. Om detta Miss lyckas tolkas det som ett tal som representerar det totala antalet millisekunder. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |

### <a name="examples"></a>Exempel

Starta en uppdatering av kluster konfigurationen
``` 
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-    
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"   
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-Cluster Upgrade-status
Hämta uppgraderings status för kluster konfiguration för ett Service Fabric fristående kluster.

Hämta information om uppgraderings status för kluster konfiguration för ett Service Fabric fristående kluster.

### <a name="arguments"></a>Argument

|Argument|Beskrivning|
| --- | --- |
| --timeout-t | Tids gränsen för servern för att utföra åtgärden på några sekunder. Denna timeout anger den tids period som klienten vill vänta tills den begärda åtgärden har slutförts. Standardvärdet för den här parametern är 60 sekunder.  Standard\: 60. |

### <a name="global-arguments"></a>Globala argument

|Argument|Beskrivning|
| --- | --- |
| --Felsök | Öka loggnings utförligheten för att visa alla fel söknings loggar. |
| --hjälp-h | Visa det här hjälp meddelandet och avsluta. |
| --utdata-o | Utdataformat.  Tillåtna värden\: JSON, jsonc, Table, TSV.  Standard\: JSON. |
| --fråga | Frågesträngen JMESPath. Mer information och exempel finns i http\://jmespath.org/. |
| --utförlig | Öka loggningens utförlighet. Använd--debug för fullständiga fel söknings loggar. |


## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](/azure/service-fabric/scripts/sfctl-upgrade-application).