---
title: Azure Service Fabric tillstånds känslig Reliable Services diagnostik | Microsoft Docs
description: Diagnostiska funktioner för tillstånds känsliga Reliable Services i Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 50e3368aa8808307fa479a290eaf10ca3f22289d
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242871"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnostisk funktionalitet för tillståndskänsliga Reliable Services
Azure Service Fabric tillstånds känslig Reliable Services StatefulServiceBase-klassen genererar [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) -händelser som kan användas för att felsöka tjänsten, ge insikter om hur körningen fungerar och hjälp med fel sökning.

## <a name="eventsource-events"></a>EventSource-händelser
EventSource namn för den tillstånds känsliga Reliable Services StatefulServiceBase-klassen är "Microsoft-ServiceFabric-Services". Händelser från den här händelse källan visas i fönstret [diagnostik-händelser](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) när tjänsten [felsöks i Visual Studio](service-fabric-debugging-your-application.md).

Exempel på verktyg och tekniker som hjälper dig att samla in och/eller Visa EventSource-händelser är [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics.md)och [Microsoft TraceEvent-biblioteket](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Events
| Händelse namn | Händelse-ID | Nivå | Händelse Beskrivning |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Information |Genereras när service RunAsync-aktiviteten startas |
| StatefulRunAsyncCancellation |2 |Information |Genereras när service RunAsync-aktiviteten avbryts |
| StatefulRunAsyncCompletion |3 |Information |Genereras när service RunAsync-uppgiften är färdig |
| StatefulRunAsyncSlowCancellation |4 |Varning |Genereras när RunAsync-aktiviteten tar för lång tid för att slutföra annullering |
| StatefulRunAsyncFailure |5 |Fel |Genereras när RunAsync-aktiviteten genererar ett undantag |

## <a name="interpret-events"></a>Tolka händelser
StatefulRunAsyncInvocation-, StatefulRunAsyncCompletion-och StatefulRunAsyncCancellation-händelser är användbara för service Writer för att förstå livs cykeln för en tjänst, samt tids inställningen för när en tjänst startar, avbryter eller slutförs. Den här informationen kan vara användbar vid fel sökning av tjänst problem eller förståelse för tjänste livs cykeln.

Service Writers bör stå nära uppmärksamhet på StatefulRunAsyncSlowCancellation-och StatefulRunAsyncFailure-händelser eftersom de indikerar problem med tjänsten.

StatefulRunAsyncFailure genereras när en tjänst RunAsync ()-aktivitet genererar ett undantag. Ett undantags fel tyder vanligt vis på ett fel eller en bugg i tjänsten. Dessutom orsakar undantaget att tjänsten Miss fungerar, så den flyttas till en annan nod. Den här åtgärden kan vara dyr och kan fördröja inkommande begär Anden medan tjänsten flyttas. Service Writers bör avgöra orsaken till undantaget och, om möjligt, minimera det.

StatefulRunAsyncSlowCancellation genereras när en begäran om att avbryta en RunAsync-aktivitet tar längre tid än fyra sekunder. När en tjänst tar för lång tid att avbryta, påverkar det att tjänsten kan startas om snabbt på en annan nod. Det här scenariot kan påverka tjänstens övergripande tillgänglighet.

## <a name="performance-counters"></a>Prestandaräknare
Reliable Services runtime definierar följande prestanda räknar kategorier:

| Kategori | Beskrivning |
| --- | --- |
| Service Fabric transaktionell replikerare |Räknare som är speciella för Azure Service Fabric-transaktionell replikerare |
| Service Fabric TStore |Räknare som är speciella för Azure Service Fabric-TStore |

Service Fabric transaktionell replikerare används av den [pålitliga tillstånds hanteraren](service-fabric-reliable-services-reliable-collections-internals.md) för att replikera transaktioner inom en angiven uppsättning [repliker](service-fabric-concepts-replica-lifecycle.md).

Service Fabric TStore är en komponent som används i [pålitliga samlingar](service-fabric-reliable-services-reliable-collections-internals.md) för att lagra och hämta nyckel/värde-par.

[Windows prestanda övervaknings](https://technet.microsoft.com/library/cc749249.aspx) programmet som är tillgängligt som standard i Windows-operativsystemet kan användas för att samla in och Visa prestanda räknar data. [Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in prestanda räknar data och ladda upp den till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Instans namn för prestanda räknare
Ett kluster som har ett stort antal pålitliga tjänster eller Reliable service-partitioner har ett stort antal instanser av prestanda räknaren för transaktionell replikering. Detta är också fallet för TStore prestanda räknare, men multipliceras också med antalet Reliable-ordlistor och tillförlitliga köer som används. Instans namn för prestanda räknaren kan hjälpa till att identifiera den angivna [partitionen](service-fabric-concepts-partitioning.md), tjänst repliken och tillstånds leverantören, om TStore, som prestanda räknar instansen är associerad med.

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric typ av transaktionell replikerare
För kategorin `Service Fabric Transactional Replicator`är räknar instans namnen i följande format:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* är sträng representationen av Service Fabric partitions-ID: t som prestanda räknar instansen är associerad med. Partitions-ID: t är ett GUID och dess sträng representation genereras via [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) med format specificeraren "D".

*ServiceFabricReplicaId* är det ID som är kopplat till en specifik replik av en tillförlitlig tjänst. Replik-ID: t ingår i prestanda räknarens instans namn för att säkerställa dess unika värde och undvika konflikter med andra prestanda räknar instanser som genereras av samma partition. Mer information om repliker och deras roll i pålitliga tjänster finns [här](service-fabric-concepts-replica-lifecycle.md).

Följande räknar instans namn är typiskt för en räknare i kategorin `Service Fabric Transactional Replicator`:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

I föregående exempel är `00d0126d-3e36-4d68-98da-cc4f7195d85e` sträng representationen av Service Fabric partitions-ID: t och `131652217797162571` är replik-ID.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore-kategori
För kategorin `Service Fabric TStore`är räknar instans namnen i följande format:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* är sträng representationen av Service Fabric partitions-ID: t som prestanda räknar instansen är associerad med. Partitions-ID: t är ett GUID och dess sträng representation genereras via [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) med format specificeraren "D".

*ServiceFabricReplicaId* är det ID som är kopplat till en specifik replik av en tillförlitlig tjänst. Replik-ID: t ingår i prestanda räknarens instans namn för att säkerställa dess unika värde och undvika konflikter med andra prestanda räknar instanser som genereras av samma partition. Mer information om repliker och deras roll i pålitliga tjänster finns [här](service-fabric-concepts-replica-lifecycle.md).

*StateProviderId* är det ID som är kopplat till en tillstånds leverantör i en tillförlitlig tjänst. ID för tillstånds leverantören ingår i instans namnet för prestanda räknaren för att skilja en TStore från en annan.

*PerformanceCounterInstanceDifferentiator* är ett DIFFERENTIERINGS-ID som är kopplat till en prestanda räknar instans i en tillstånds leverantör. Den här differentieringen ingår i namnet på prestanda räknar instansen för att säkerställa dess unika värde och undvika konflikter med andra prestanda räknar instanser som genererats av samma tillstånds leverantör.

*StateProviderName* är namnet som associeras med en tillstånds leverantör i en tillförlitlig tjänst. Namnet på tillstånds leverantören ingår i namnet på prestanda räknarens instans för att användarna enkelt ska kunna identifiera vilket tillstånd det tillhandahåller.

Följande räknar instans namn är typiskt för en räknare i kategorin `Service Fabric TStore`:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

I föregående exempel är `00d0126d-3e36-4d68-98da-cc4f7195d85e` sträng representationen av Service Fabric partitions-ID: t `131652217797162571` är replik-ID: t, `142652217797162571` är ID för tillstånds leverantören och `1337` är instansen av prestanda räknaren instans. `urn:MyReliableDictionary/dataStore` är namnet på den tillstånds leverantör som lagrar data för samlingen med namnet `urn:MyReliableDictionary`.

### <a name="transactional-replicator-performance-counters"></a>Prestanda räknare för transaktionell replikering

Reliable Services runtime genererar följande händelser under kategorin `Service Fabric Transactional Replicator`

 Räknar namn | Beskrivning |
| --- | --- |
| BEGIN TXN-åtgärder/SEK | Antalet nya Skriv transaktioner som skapas per sekund.|
| TXN-åtgärder/SEK | Antalet åtgärder för att lägga till/uppdatera/ta bort i Reliable Collections per sekund.|
| Logg tömnings byte/SEK | Antalet byte som rensas till disken av transaktionell replikering per sekund |
| Begränsade åtgärder/SEK | Antalet åtgärder som avvisats varje sekund av transaktionell replikering på grund av begränsning. |
| Genomsnittlig transaktion i MS/commit | Genomsnittlig bekräftelse latens per transaktion i millisekunder |
| Genomsnittlig svars tid för tömning (MS) | Genomsnittlig varaktighet för disk tömnings åtgärder som initieras av transaktionell replikerare i millisekunder |

### <a name="tstore-performance-counters"></a>TStore prestanda räknare

Reliable Services runtime genererar följande händelser under kategorin `Service Fabric TStore`

 Räknar namn | Beskrivning |
| --- | --- |
| Objekt antal | Antalet objekt i arkivet.|
| Diskstorlek | Den totala disk storleken, i byte, för en kontroll punkts fil för arkivet.|
| Skrivna byte för kontroll punkts fil/SEK | Antalet skrivna byte per sekund för den senaste kontroll punkts filen.|
| Kopiera disk överförings byte/SEK | Antalet Disk byte som lästs (på den primära repliken) eller skrivits (på en sekundär replik) per sekund under en lagrings kopia.|

## <a name="next-steps"></a>Nästa steg
[EventSource-providers i PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
