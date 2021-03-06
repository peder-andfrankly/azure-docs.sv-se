---
title: Avbilda en avbildning av en virtuell Linux-dator i Azure med hjälp av Azure CLI
description: Avbilda en avbildning av en virtuell Azure-dator som ska användas för Mass distributioner med hjälp av Azure CLI.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: ed7d45fb9148bd441a3798c48be8b25e1da2b8c1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036910"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Så här skapar du en avbildning av en virtuell dator eller virtuell hård disk

<!-- generalize, image - extended version of the tutorial-->

För att skapa flera kopior av en virtuell dator (VM) för användning i Azure, avbilda du en avbildning av den virtuella datorn eller OS-VHD: n. Om du vill skapa en avbildning för distribution måste du ta bort personlig konto information. I följande steg avetablerar du en befintlig virtuell dator, frigör den och skapar en avbildning. Du kan använda den här avbildningen för att skapa virtuella datorer i alla resurs grupper i din prenumeration.

Om du vill skapa en kopia av din befintliga virtuella Linux-dator för säkerhets kopiering eller fel sökning eller ladda upp en specialiserad Linux-VHD från en lokal virtuell dator, se [Ladda upp och skapa en virtuell Linux-dator från anpassad disk avbildning](upload-vhd.md).  

Du kan använda tjänsten **Azure VM Image Builder (offentlig för hands version)** för att skapa en anpassad avbildning, inget behov av att lära sig några verktyg eller skapa pipelines för att skapa en avbildning av Image Builder. Mer information finns i [komma igång med Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

Du behöver följande objekt innan du skapar en avbildning:

* En virtuell Azure-dator som skapats i Resource Manager-distributions modellen som använder hanterade diskar. Om du ännu inte har skapat en virtuell Linux-dator kan du använda [portalen](quick-create-portal.md), [Azure CLI](quick-create-cli.md)-eller [Resource Manager-mallarna](create-ssh-secured-vm-from-template.md). Konfigurera den virtuella datorn efter behov. Du kan till exempel [lägga till data diskar](add-disk.md), tillämpa uppdateringar och installera program. 

* Den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) är installerad och är inloggad på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Föredrar du en själv studie kurs i stället?

För en förenklad version av den här artikeln och för testning, utvärdering eller inlärning av virtuella datorer i Azure, se [skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md).  Annars fortsätter du att läsa hit för att hämta hela bilden.


## <a name="step-1-deprovision-the-vm"></a>Steg 1: avetablera den virtuella datorn
Först avetablerar du den virtuella datorn med hjälp av Azure VM-agenten för att ta bort datorspecifika filer och data. Använd kommandot `waagent` med parametern `-deprovision+user` på den virtuella käll Linux-datorn. Mer information finns i [Användarguide för Azure Linux Agent](../extensions/agent-linux.md).

1. Anslut till din virtuella Linux-dator med en SSH-klient.
2. I SSH-fönstret anger du följande kommando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Kör bara det här kommandot på en virtuell dator som du ska avbilda som en avbildning. Det här kommandot garanterar inte att avbildningen är klar med all känslig information eller är lämplig för omdistribution. Parametern `+user` tar också bort det senast etablerade användar kontot. Använd endast `-deprovision`om du vill behålla autentiseringsuppgifterna för användar kontot på den virtuella datorn.
 
3. Fortsätt genom att ange **y** . Du kan lägga till parametern `-force` för att undvika det här bekräftelse steget.
4. När kommandot har slutförts anger du **Avsluta** för att stänga SSH-klienten.  Den virtuella datorn kommer fortfarande att köras nu.

## <a name="step-2-create-vm-image"></a>Steg 2: skapa en VM-avbildning
Använd Azure CLI för att markera den virtuella datorn som generaliserad och avbilda avbildningen. Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn är *myResourceGroup*, *myVnet*och *myVM*.

1. Frigör den virtuella dator som du avetablerat med [AZ VM-frigörning](/cli/azure/vm). I följande exempel avallokeras den virtuella datorn med namnet *myVM* i resurs gruppen med namnet *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Vänta tills den virtuella datorn är helt avallokerad innan du går vidare. Det kan ta några minuter att slutföra.  Den virtuella datorn stängs av under avallokeringen.

2. Markera den virtuella datorn som generaliserad med [AZ VM generalize](/cli/azure/vm). I följande exempel märks den virtuella datorn med namnet *myVM* i resurs gruppen med namnet *myResourceGroup* som generaliserad.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    En virtuell dator som har generaliserats kan inte längre startas om.

3. Skapa en avbildning av den virtuella dator resursen med [AZ image Create](/cli/azure/image#az-image-create). I följande exempel skapas en avbildning med namnet *image* i resurs gruppen med namnet *myResourceGroup* med hjälp av den virtuella dator resursen med namnet *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Avbildningen skapas i samma resurs grupp som den virtuella käll datorn. Du kan skapa virtuella datorer i alla resurs grupper i din prenumeration från den här avbildningen. Från ett hanterings perspektiv kanske du vill skapa en resurs grupp för dina VM-resurser och avbildningar.
   >
   > Om du vill lagra avbildningen i zoner – flexibel lagring måste du skapa den i en region som har stöd för [tillgänglighets zoner](../../availability-zones/az-overview.md) och inkludera parametern `--zone-resilient true`.
   
Det här kommandot returnerar JSON som beskriver avbildningen av den virtuella datorn. Spara utdata för senare referens.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Steg 3: skapa en virtuell dator från avbildningen
Skapa en virtuell dator med hjälp av avbildningen som du skapade med [AZ VM Create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVMDeployed* från avbildningen med namnet *image*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Skapa den virtuella datorn i en annan resurs grupp 

Du kan skapa virtuella datorer från en avbildning i alla resurs grupper i din prenumeration. Om du vill skapa en virtuell dator i en annan resurs grupp än avbildningen anger du det fullständiga resurs-ID: t för avbildningen. Använd [AZ avbildnings lista](/cli/azure/image#az-image-list) om du vill visa en lista över avbildningar. De utdata som genereras påminner om de i följande exempel.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

I följande exempel används [AZ VM Create](/cli/azure/vm#az-vm-create) för att skapa en virtuell dator i en annan resurs grupp än käll avbildningen genom att ange resurs-ID för avbildningen.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Steg 4: verifiera distributionen

SSH till den virtuella datorn som du skapade för att verifiera distributionen och börja använda den nya virtuella datorn. För att ansluta via SSH hittar du IP-adressen eller FQDN för den virtuella datorn med [AZ VM show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Nästa steg
Du kan skapa flera virtuella datorer från din virtuella käll avbildning. Så här gör du ändringar i din avbildning: 

- Skapa en virtuell dator från avbildningen.
- Gör uppdateringar eller konfigurations ändringar.
- Följ stegen igen för att avetablera, frigöra, generalisera och skapa en avbildning.
- Använd den här nya avbildningen för framtida distributioner. Du kan ta bort den ursprungliga avbildningen.

Mer information om hur du hanterar virtuella datorer med CLI finns i [Azure CLI](/cli/azure).
