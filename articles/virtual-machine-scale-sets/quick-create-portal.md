---
title: Snabbstart – Skapa en VM-skalningsuppsättning i Azure Portal | Microsoft Docs
description: Lär dig hur du snabbt skapar en VM-skalningsuppsättning i Azure Portal
services: virtual-machine-scale-sets
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: quickstart
ms.custom: H1Hack27Feb2017
ms.date: 10/23/2019
ms.author: cynthn
ms.openlocfilehash: 1bbbef6d8037b819c557e1c7fc3fff6248507986
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466382"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Snabbstart: Skapa en VM-skalningsuppsättning med Azure Portal

Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt eller definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. En Azure-lastbalanserare distribuerar sedan trafiken till de virtuella datorinstanserna i skalningsuppsättningen. I den här snabbstarten skapar du en VM-skalningsuppsättning i Azure Portal.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

Azure [Load Balancer](../load-balancer/load-balancer-overview.md) distribuerar inkommande trafik mellan felfria virtuella dator instanser. 

Först skapar du en Basic-lastbalanserare med hjälp av portalen. Det namn och den offentliga IP-adress som du skapar konfigureras automatiskt som lastbalanserarens klientdel.

1. I rutan Sök skriver du **Load Balancer**. Välj **belastnings utjämning**under **Marketplace** i Sök resultaten.
1. På fliken **grundläggande** på sidan **skapa belastnings utjämning** anger eller väljer du följande information:

    | Inställning                 | Värde   |
    | ---| ---|
    | Prenumeration  | Välj din prenumeration.    |    
    | Resursgrupp | Välj **Skapa ny** och skriv *myVMSSResourceGroup* i text rutan.|
    | Namn           | *myLoadBalancer*         |
    | Region         | Välj **USA, östra**.       |
    | Typ          | Välj **Offentlig**.       |
    | SKU           | Välj **standard**.       |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress  | *MyPip*   |
    | Tilldelning| Statisk |

1. När du är färdig väljer du **Granska + skapa** 
1. När verifieringen har godkänts väljer du **skapa**. 

![Skapa en lastbalanserare](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Skapa VM-skalningsuppsättningar
Du kan distribuera en skalningsuppsättning som anges med en Windows Server-avbildning eller en Linux-avbildningen som RHEL, CentOS, Ubuntu eller SLES.

1. Skriv **skalnings uppsättning** i sökrutan. I resultaten, under **Marketplace**, väljer du **skalnings uppsättningar för virtuella datorer**. Sidan **skapa en skalnings uppsättning för virtuell dator** öppnas. 
1. På fliken **Grundläggande inställningar** går du till **Projektinformation**, kontrollerar att korrekt prenumeration har valts och väljer sedan **Skapa ny** för resursgruppen. Skriv *myVMSSResourceGroup* som namn och välj sedan **OK** . 
1. Skriv *myScaleSet* som namn på din skalnings uppsättning.
1. I **region**väljer du en region som ligger nära ditt område.
1. Lämna standardvärdet för **virtuella ScaleSet-datorer** för **Orchestrator**.
1. Välj en Marketplace-avbildning för **avbildningen**. I det här exemplet har vi valt *Ubuntu Server 18,04 LTS*.
1. Ange önskat användarnamn och välj den autentiseringstyp du föredrar.
   - Ett **lösenord** måste innehålla minst 12 tecken och måste uppfylla tre av följande fyra komplexitetskrav: en gemen, en versal, en siffra och ett specialtecken. Mer information finns i [kraven om användarnamn och lösenord](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Om du väljer en Linux OS-diskavbildning kan du istället välja **offentlig SSH-nyckel**. Du behöver bara ange din offentliga nyckel, t.ex. *~/.ssh/id_rsa.pub*. Du kan använda Azure Cloud Shell från portalen för att [skapa och använda SSH-nycklar](../virtual-machines/linux/mac-create-ssh-keys.md).
   
    ![Skapa en VM-skalningsuppsättning](./media/virtual-machine-scale-sets-create-portal/quick-create-scaleset.png)

1. Välj **Nästa** för att flytta de andra sidorna. 
1. Lämna standardvärdena för **instans** -och **disk** sidorna.
1. På sidan **nätverk** under **belastnings utjämning**väljer du **Ja** för att ställa in skalnings uppsättnings instanserna bakom en belastningsutjämnare. 
1. I **alternativ för belastnings utjämning**väljer du **Azure Load Balancer**.
1. I **Välj en belastningsutjämnare**väljer du *myLoadBalancer* som du skapade tidigare.
1. För **Välj en backend-pool**väljer du **Skapa ny**, Skriv *myBackendPool*och välj sedan **skapa**.
1. När du är färdig väljer du **Granska + skapa**. 
1. När verifieringen har godkänts väljer du **skapa** för att distribuera skalnings uppsättningen.


## <a name="clean-up-resources"></a>Rensa resurser
Ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser när de inte längre behövs. Det gör du genom att markera resurs gruppen för skalnings uppsättningen och sedan välja **ta bort**.


## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skapat en grundläggande skalningsuppsättning i Azure Portal. Om du vill veta mer kan du gå till självstudiekursen om hur man skapar och hanterar VM-skalningsuppsättningar för Azure.

> [!div class="nextstepaction"]
> [Skapa och hantera VM-skalningsuppsättningar för Azure](tutorial-create-and-manage-powershell.md)
