---
title: Minska Azure VMware-lösningen genom CloudSimple privat moln
description: Beskriver hur du krymper ett privat CloudSimple-moln.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108588"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Krymp ett privat CloudSimple-moln

CloudSimple ger flexibiliteten att dynamiskt minska ett privat moln.  Ett privat moln består av ett eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder. När du krymper ett privat moln tar du bort en nod från det befintliga klustret eller tar bort ett helt kluster. 

## <a name="before-you-begin"></a>Innan du börjar

Följande villkor måste vara uppfyllda för krympning av ett privat moln.  Hanterings kluster (det första klustret) som skapades när ett privat moln skapades går inte att ta bort.

* Ett vSphere-kluster måste ha tre noder.  Det går inte att krympa ett kluster med tre noder.
* Den totala förbrukade lagringen bör inte överskrida den totala kapaciteten efter krympning av klustret.
* Kontrol lera om några DRS-regler (Distributed Resource Scheduler) förhindrar vMotion av en virtuell dator.  Om det finns regler inaktiverar eller tar du bort reglerna.  DRS-regler inkluderar virtuell dator som värd tillhörighets regler.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Krympa ett privat moln

1. [Få åtkomst till CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** .

3. Klicka på det privata moln som du vill krympa

4. På sidan Sammanfattning klickar du på **Krymp**.

    ![Krymp privat moln](media/shrink-private-cloud.png)

5. Välj det kluster som du vill krympa eller ta bort. 

    ![Krymp privat moln – Välj kluster](media/shrink-private-cloud-select-cluster.png)

6. Välj **ta bort en nod** eller **ta bort hela klustret**. 

7. Verifiera kluster kapaciteten

8. Klicka på **Skicka** för att krympa det privata molnet.

Krympningen av det privata molnet startar.  Du kan övervaka förloppet i aktiviteterna.  Krympnings processen kan ta några timmar beroende på vilka data som behöver synkroniseras om på virtuellt San.

> [!NOTE]
> 1. Om du krymper ett privat moln genom att ta bort det sista eller det enda klustret i data centret tas data centret inte bort.
> 2. Om någon DRS regel överträdelse uppstår tas noden inte bort från klustret och uppgifts beskrivningen visar att borttagning av en nod bryter mot DRS-regler i klustret.    


## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer i Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)
