---
title: Använd Cloud-Init för att lägga till en användare till en virtuell Linux-dator på Azure
description: Använda Cloud-Init för att lägga till en användare till en virtuell Linux-dator under skapandet med Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 428f489a24c24b173cb1cef0980dd17c1d8483ce
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036784"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Använd Cloud-Init för att lägga till en användare till en virtuell Linux-dator i Azure
Den här artikeln visar hur du använder [Cloud-Init](https://cloudinit.readthedocs.io) för att lägga till en användare på en virtuell dator (VM) eller Virtual Machine Scale set (VMSS) vid etablerings tiden i Azure. Detta Cloud-Init-skript körs vid första start när resurserna har etablerats av Azure. Mer information om hur Cloud-Init fungerar internt i Azure och vilka Linux-distributioner som stöds finns i [Översikt över Cloud-Init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Lägga till en användare till en virtuell dator med Cloud-Init
En av de första uppgifterna i en ny Linux VM är att lägga till ytterligare en användare för att undvika att använda *roten*. SSH-nycklar är bästa praxis för säkerhet och användbarhet. Nycklar läggs till i filen *~/.ssh/authorized_keys* med detta Cloud-Init-skript.

Om du vill lägga till en användare till en virtuell Linux-dator skapar du en fil i det aktuella gränssnittet med namnet *cloud_init_add_user. txt* och klistrar in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_add_user.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 om du vill använda **nano** -redigeraren. Kontrol lera att hela Cloud-Init-filen har kopierats korrekt, särskilt den första raden.  Du måste ange en egen offentlig nyckel (till exempel innehållet i *~/.ssh/id_rsa. pub*) för värdet för `ssh-authorized-keys:` – det har kortats ned här för att förenkla exemplet.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> Filen #cloud-config innehåller den `- default`-parameter som ingår. Då läggs användaren till i den befintliga administratörs användaren som skapades under etableringen. Om du skapar en användare utan parametern `- default`, kommer den automatiskt genererade administratörs användare som skapats av Azure-plattformen att skrivas över. 

Innan du distribuerar den här avbildningen måste du skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group) . En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [AZ VM Create](/cli/azure/vm) och anger Cloud-Init-filen med `--custom-data cloud_init_add_user.txt` enligt följande:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH till den offentliga IP-adressen för den virtuella datorn som visas i utdata från föregående kommando. Ange din egen **publicIpAddress** enligt följande:

```bash
ssh <publicIpAddress>
```

För att bekräfta att användaren har lagts till i den virtuella datorn och de angivna grupperna kan du visa innehållet i */etc/Group* -filen på följande sätt:

```bash
cat /etc/group
```

I följande exempel på utdata visas användaren från filen *cloud_init_add_user. txt* som har lagts till i den virtuella datorn och lämplig grupp:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Nästa steg
Ytterligare Cloud-Init-exempel på konfigurations ändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Köra en paket hanterare för att uppdatera befintliga paket vid första starten](cloudinit-update-vm.md)
- [Ändra lokalt värdnamn för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
