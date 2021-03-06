---
title: Azure VMware-lösning av CloudSimple – skapa CloudSimple-tjänst
description: Beskriver hur du skapar CloudSimple-tjänsten i Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893951"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Skapa Azure VMware-lösningen av CloudSimple-tjänsten

För att komma igång med Azure VMware-lösningen från CloudSimple skapar du Azure VMware-lösningen från CloudSimple-tjänsten i Azure Portal.

## <a name="before-you-begin"></a>Innan du börjar

Allokera ett/28 CIDR-block för gateway-undernätet. Ett Gateway-undernät krävs per CloudSimple-tjänst och är unikt för den region där det skapats. Gateway-undernätet används för gräns nätverks tjänster och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljön. De nätverk som kommunicerar med CloudSimple omfattar lokala nätverk och virtuella Azure-nätverk.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-the-service"></a>Skapa tjänsten

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-tjänster**.
    ![search CloudSimple service](media/create-cloudsimple-service-search.png)
3. Välj **CloudSimple-tjänster**.
4. Klicka på **Lägg till** för att skapa en ny tjänst.
    ![lägga till CloudSimple-tjänsten](media/create-cloudsimple-service-add.png)
5. Välj den prenumeration där du vill skapa CloudSimple-tjänsten.
6. Välj resurs grupp för tjänsten. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange namnet för att identifiera tjänsten.
8. Ange CIDR för service Gateway. Ange ett/28-undernät som inte överlappar med något av dina lokala undernät, Azure-undernät eller planerade CloudSimple-undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa CloudSimple-tjänsten](media/create-cloudsimple-service.png)
9. Klicka på **OK**

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [etablerar noder](create-nodes.md)
* Lär dig hur du [skapar ett privat moln](create-private-cloud.md)
* Lär dig hur du [konfigurerar en privat moln miljö](quickstart-create-private-cloud.md)
