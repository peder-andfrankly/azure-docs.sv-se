---
title: Välj VM-storlekar för pooler – Azure Batch | Microsoft Docs
description: Hur du väljer bland tillgängliga VM-storlekar för datornoderna i Azure Batch pooler
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: e3d96ad7783c43dba6b69f6b11acccf790d0d6c9
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983754"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Välj en VM-storlek för Compute-noder i en Azure Batch pool

När du väljer en Node-storlek för en Azure Batch pool kan du välja bland nästan alla VM-storlekar som är tillgängliga i Azure. Azure erbjuder ett antal storlekar för virtuella Linux-och Windows-datorer för olika arbets belastningar.

Det finns några undantag och begränsningar för att välja en VM-storlek:

* Vissa VM-serier eller VM-storlekar stöds inte i batch.
* Vissa VM-storlekar är begränsade och måste vara särskilt aktiverade innan de kan allokeras.

## <a name="supported-vm-series-and-sizes"></a>VM-serien och storlekar som stöds

### <a name="pools-in-virtual-machine-configuration"></a>Pooler i konfiguration av virtuell dator

Batch-pooler i den virtuella dator konfigurationen stöder nästan alla VM-storlekar ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). I följande tabell finns mer information om storlekar och begränsningar som stöds.

Alla kampanjer och för hands versioner av virtuella datorer som inte listas är inte garanterade för support.

| Virtuell datorserie  | Storlekar som stöds | Allokeringsinställningar för batch-konto<sup>1</sup> |
|------------|---------|-----------------|
| Basic A-Series | Alla storlekar *utom* Basic_A0 (a0) | Any |
| A-serien | Alla storlekar *utom* Standard_A0 | Any |
| Av2-serien | Alla storlekar | Any |
| B-serien | Inga | Saknas |
| DC-serien | Inga | Saknas |
| Dv2, DSv2-serien | Alla storlekar | Any |
| Dv3, Dsv3-series | Alla storlekar | Any |
| Ev3, Esv3-serien | Alla storlekar | Any |
| Fsv2-serien | Alla storlekar | Any |
| H-serien | Alla storlekar | Any |
| HB-serie<sup>2</sup> | Alla storlekar | Any |
| HC-serie<sup>2</sup> | Alla storlekar | Any |
| Ls-serien | Alla storlekar | Any |
| Lsv2-serien | Inga | Saknas |
| M-serien | Standard_M64ms (endast låg prioritet), Standard_M128s (endast låg prioritet) | Any |
| Mv2-serien | Inga | Saknas |
| NC-serien | Alla storlekar | Any |
| NCv2-serie<sup>2</sup> | Alla storlekar | Any |
| NCv3-serie<sup>2</sup> | Alla storlekar | Any |
| ND-serien<sup>2</sup> | Alla storlekar | Any |
| NDv2-serien | Alla storlekar | Användarprenumerationsläge |
| NV-serien | Alla storlekar | Any |
| NVv3-serien | Inga | Saknas |
| SAP HANA | Inga | Saknas |

<sup>1</sup> vissa nyare VM-serier stöds delvis. Dessa VM-serier kan allokeras av batch-konton med **poolens fördelnings läge** inställt på **användar prenumeration**. Mer information om konfiguration av batch-konton finns i [Hantera batch-konton](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) . Se [kvoter och begränsningar](batch-quota-limit.md) för att lära dig att begära kvot för de här delvis STÖDda VM-serien för batch-konton för **användar prenumeration** .  

<sup>2</sup> de här VM-storlekarna kan allokeras i batch-pooler i konfigurationen av den virtuella datorn, men du måste begära en speciell [kvot ökning](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>Pooler i moln tjänst konfiguration

Batch-pooler i moln tjänst konfigurationen har stöd [för alla VM-storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **utom** följande:

| Virtuell datorserie  | Storlekar som inte stöds |
|------------|-------------------|
| A-serien   | Extra liten       |
| Av2-serien | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Överväganden för storlek

* **Program krav** – beakta egenskaperna och kraven för det program som ska köras på noderna. Det är lämpligt att ha i åtanke när du väljer den lämpligaste och mest kostnadseffektiva nodstorleken huruvida programmet är flertrådat och hur mycket minne det förbrukar. För [MPI arbets belastningar](batch-mpi.md) eller CUDA-program med flera instanser bör du överväga specialiserade [HPC](../virtual-machines/linux/sizes-hpc.md) [-eller GPU-aktiverade VM-](../virtual-machines/linux/sizes-gpu.md) storlekar. (Se [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-pooler](batch-pool-compute-intensive-sizes.md).)

* **Aktiviteter per nod** – det är vanligt att välja en Node-storlek förutsatt att en aktivitet körs på en nod i taget. Det kan dock vara fördelaktigt att ha flera aktiviteter (och därför flera program instanser) [som körs parallellt](batch-parallel-node-tasks.md) på datornoderna under jobb körningen. I det här fallet är det vanligt att välja en storlek för flera kärnor för att hantera den ökade efter frågan av parallell körning.

* **Belastnings nivåer för olika aktiviteter** -alla noder i en pool har samma storlek. Om du avser att köra program med olika systemkrav och/eller belastningsnivåer rekommenderar vi att du använder olika pooler.

* **Region tillgänglighet** – en VM-serie eller storlek kanske inte är tillgänglig i de regioner där du skapar dina batch-konton. Information om hur du kontrollerar att en storlek är tillgänglig finns i [produkt tillgänglighet per region](https://azure.microsoft.com/regions/services/).

* **Kvoter** - [kärnor](batch-quota-limit.md#resource-quotas) i batch-kontot kan begränsa antalet noder i en särskild storlek som du kan lägga till i en batch-pool. Information om hur du begär en kvot ökning finns i [den här artikeln](batch-quota-limit.md#increase-a-quota). 

* **Konfiguration av pool** – i allmänhet har du fler alternativ för VM-storlek när du skapar en pool i konfigurationen för den virtuella datorn, jämfört med moln tjänst konfigurationen.

## <a name="next-steps"></a>Nästa steg

* En djupgående översikt över batch finns i [utveckla storskaliga parallella beräknings lösningar med batch](batch-api-basics.md).
* Information om hur du använder beräknings intensiva VM-storlekar finns i [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-pooler](batch-pool-compute-intensive-sizes.md).
