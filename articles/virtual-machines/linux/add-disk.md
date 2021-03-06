---
title: Lägg till en datadisk till en virtuell Linux-dator med hjälp av Azure CLI
description: Lär dig att lägga till en beständig datadisk till din virtuella Linux-dator med Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.subservice: disks
ms.openlocfilehash: 5d7ec2cbbc5cc1bf8bdc87d7f82a965b3bc8c267
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037109"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Lägg till en disk till en virtuell Linux-dator
Den här artikeln visar hur du ansluter en beständig disk till den virtuella datorn så att du kan bevara dina data, även om din virtuella dator har reserveras på grund av underhåll eller storleks ändring.


## <a name="attach-a-new-disk-to-a-vm"></a>Koppla en ny disk till en virtuell dator

Om du vill lägga till en ny tom datadisk på den virtuella datorn använder du kommandot [AZ VM disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) med parametern `--new`. Om den virtuella datorn finns i en tillgänglighets zon skapas disken automatiskt i samma zon som den virtuella datorn. Mer information finns i [Översikt över Tillgänglighetszoner](../../availability-zones/az-overview.md). I följande exempel skapas en disk med namnet *myDataDisk* som är 50 GB i storlek:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk

Om du vill koppla en befintlig disk letar du reda på disk-ID och skickar ID: t till kommandot [AZ VM disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) . Följande exempel frågar efter en disk med namnet *myDataDisk* i *myResourceGroup*och kopplar den sedan till den virtuella datorn med namnet *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Ansluta till den virtuella Linux-datorn för att montera den nya disken

För att partitionera, formatera och montera den nya disken så att din virtuella Linux-dator kan använda den, SSH i den virtuella datorn. Mer information finns i [Så här använder du SSH med Linux på Azure](mac-create-ssh-keys.md). Följande exempel ansluter till en virtuell dator med den offentliga DNS-posten för *mypublicdns.westus.cloudapp.Azure.com* med användar namnet *azureuser*:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

När du är ansluten till den virtuella datorn är du redo att ansluta en disk. Börja med att leta upp disken med `dmesg` (den metod som du använder för att identifiera den nya disken kan variera). I följande exempel används dmesg för att filtrera på *SCSI-* diskar:

```bash
dmesg | grep SCSI
```

Utdata ser ut ungefär så här:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

> [!NOTE]
> Vi rekommenderar att du använder de senaste versionerna av fdisk eller delar av som är tillgängliga för din distribution.

Här är *SDC* den disk som vi vill ha. Partitionera disken med `parted`, om disk storleken är 2 tebibyte (TiB) eller större, måste du använda GPT-partitionering, om den finns under 2TiB, så kan du använda antingen MBR eller GPT-partitionering. Om du använder MBR-partitionering kan du använda `fdisk`. Gör den till en primär disk på partition 1 och godkänn de andra standardvärdena. I följande exempel startar processen `fdisk` på */dev/SDC*:

```bash
sudo fdisk /dev/sdc
```

Använd `n`-kommandot för att lägga till en ny partition. I det här exemplet väljer vi också `p` för en primär partition och accepterar resten av standardvärdena. Utdata ser ut ungefär som i följande exempel:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Skriv ut partitionstabellen genom att skriva `p` och Använd `w` för att skriva tabellen till disk och avsluta. Utdata bör se ut ungefär som i följande exempel:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
Använd kommandot nedan för att uppdatera kerneln:
```
partprobe 
```

Skriv nu ett fil system till partitionen med kommandot `mkfs`. Ange fil Systems typ och enhets namn. I följande exempel skapas ett *ext4* -filsystem på den */dev/sdc1* -partition som skapades i föregående steg:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Utdata ser ut ungefär så här:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Nu ska du skapa en katalog för att montera fil systemet med `mkdir`. I följande exempel skapas en katalog på */datadrive*:

```bash
sudo mkdir /datadrive
```

Använd `mount` för att montera fil systemet. I följande exempel monteras */dev/sdc1* -partitionen till */datadrive* -monterings punkten:

```bash
sudo mount /dev/sdc1 /datadrive
```

För att säkerställa att enheten monteras om automatiskt efter en omstart måste den läggas till i */etc/fstab* -filen. Det rekommenderas också starkt att UUID (Universal Unique IDentifier) används i */etc/fstab* för att referera till enheten i stället för bara enhets namnet (t. ex. */dev/sdc1*). Om operativ systemet upptäcker ett diskfel under starten undviker du att den felaktiga disken monteras på en specifik plats med hjälp av UUID: n. Återstående data diskar tilldelas sedan samma enhets-ID. Använd `blkid`-verktyget för att hitta UUID för den nya enheten:

```bash
sudo blkid
```

Utdata ser ut ungefär som i följande exempel:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Felaktig redigering av **/etc/fstab** -filen kan leda till ett system som inte kan startas. Om du vill veta mer om hur du redigerar den här filen i distributionens dokumentation. Vi rekommenderar också att du skapar en säkerhets kopia av/etc/fstab-filen innan du redigerar.

Öppna sedan */etc/fstab* -filen i en text redigerare enligt följande:

```bash
sudo vi /etc/fstab
```

I det här exemplet använder du UUID-värdet för den */dev/sdc1* -enhet som skapades i föregående steg och monterings punkt för */datadrive*. Lägg till följande rad i slutet av */etc/fstab* -filen:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Om du senare tar bort en datadisk utan att redigera fstab kan det hända att den virtuella datorn inte kan starta. De flesta distributioner ger antingen alternativen *nomisslyckande* och/eller *nobootwait* fstab. De här alternativen gör det möjligt för ett system att starta även om disken inte kan monteras vid start. Mer information om dessa parametrar finns i distributionens dokumentation.
>
> Alternativet *nomisslyckande* ser till att den virtuella datorn startar även om fil systemet är skadat eller om disken inte finns vid start. Utan det här alternativet kan du stöta på det sätt som beskrivs i [kan inte användas med SSH till Linux på grund av FSTAB-fel](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)
>
> Den seriella Azure VM-konsolen kan användas för konsol åtkomst till den virtuella datorn om en ändring av fstab har resulterat i ett startfel. Mer information finns i dokumentationen för den [seriella konsolen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).

### <a name="trimunmap-support-for-linux-in-azure"></a>Stöd för trimning/MAPPNING för Linux i Azure
Vissa Linux-Kernels stöder TRIMNINGs-/MAPPNINGs åtgärder för att ta bort oanvända block på disken. Den här funktionen är främst användbar i standard lagring för att informera Azure om att borttagna sidor inte längre är giltiga och kan tas bort och du kan spara pengar om du skapar stora filer och sedan tar bort dem.

Det finns två sätt att aktivera TRIMNINGs stöd i din virtuella Linux-dator. Som vanligt kan du kontakta din distribution för den rekommenderade metoden:

* Använd `discard` monterings alternativ i */etc/fstab*, till exempel:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* I vissa fall kan `discard` alternativet ha prestanda konsekvenser. Du kan också köra kommandot `fstrim` manuellt från kommando raden eller lägga till det i din CRONTAB för att köra regelbundet:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Felsökning

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Nästa steg

* För att säkerställa att din virtuella Linux-dator har kon figurer ATS korrekt kan du granska prestanda rekommendationerna [optimera dina Linux-datorer](optimization.md) .
* Utöka lagrings kapaciteten genom att lägga till ytterligare diskar och [Konfigurera RAID](configure-raid.md) för ytterligare prestanda.
