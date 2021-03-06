---
title: Skapa och ladda upp en Oracle Linux VHD
description: Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett Oracle Linux operativ system.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 16f3bc9e70f8fac6ab28318e1654742a2c3b76a1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035375"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Förbered en virtuell Oracle Linux-dator för Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan har installerat ett Oracle Linux operativ system på en virtuell hård disk. Det finns flera verktyg för att skapa. VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Anvisningar finns i [Installera Hyper-V-rollen och konfigurera en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Oracle Linux installations anmärkningar
* Se även [allmänna Linux-Installationsinstruktioner](create-upload-generic.md#general-linux-installation-notes) för mer information om hur du förbereder Linux för Azure.
* Oracles Red Hat-kompatibla kernel och deras UEK3 (unbrytande Enterprise-kernel) stöds både i Hyper-V och Azure. För bästa resultat bör du uppdatera till den senaste kärnan samtidigt som du förbereder din Oracle Linux virtuella hård disk.
* Oracle-UEK2 stöds inte på Hyper-V och Azure eftersom den inte omfattar de nödvändiga driv rutinerna.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell hård disk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten Convert-VHD.
* När du installerar Linux-systemet rekommenderar vi att du använder standardpartitioner snarare än LVM (vanligt vis som standard för många installationer). På så sätt undviker du LVM namn konflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för fel sökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas på data diskar om det är lämpligt.
* NUMA stöds inte för större VM-storlekar på grund av ett fel i Linux kernel-versioner nedan 2.6.37. Det här problemet påverkar främst distributioner med hjälp av den överordnade Red Hat 2.6.32-kärnan. Med manuell installation av Azure Linux-agenten (waagent) inaktive ras NUMA i GRUB-konfigurationen för Linux-kärnan. Mer information om detta finns i stegen nedan.
* Konfigurera inte en swap-partition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlings fil på den tillfälliga resurs disken.  Mer information om detta finns i stegen nedan.
* Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till 1 MB. När du konverterar från en RAW-disk till VHD måste du se till att den råa disk storleken är en multipel av 1 MB före konverteringen. Mer information finns i [installations information för Linux](create-upload-generic.md#general-linux-installation-notes) .
* Kontrol lera att `Addons`-lagringsplatsen är aktive rad. Redigera filen `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) eller `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7) och ändra rad `enabled=0` till `enabled=1` under **[ol6_addons]** eller **[ol7_addons]** i den här filen.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Du måste slutföra vissa konfigurations steg i operativ systemet för att den virtuella datorn ska kunna köras i Azure.

1. I mittenfönstret i Hyper-V Manager väljer du den virtuella datorn.
2. Klicka på **Anslut** för att öppna fönstret för den virtuella datorn.
3. Avinstallera NetworkManager genom att köra följande kommando:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Obs:** Om paketet inte redan är installerat kommer det här kommandot inte att fungera med ett fel meddelande. Detta är normalt.
4. Skapa en fil med namnet **nätverk** i katalogen `/etc/sysconfig/` som innehåller följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Skapa en fil med namnet **ifcfg-eth0** i katalogen `/etc/sysconfig/network-scripts/` som innehåller följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Ändra udev-regler för att undvika att skapa statiska regler för Ethernet-gränssnitten. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Se till att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:
   
        # chkconfig network on
8. Installera python-pyasn1 genom att köra följande kommando:
   
        # sudo yum install python-pyasn1
9. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Det gör du genom att öppna "/boot/grub/menu.lst" i en text redigerare och se till att standard kärnan innehåller följande parametrar:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Detta säkerställer också att alla konsol meddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-support med fel söknings problem. Detta inaktiverar NUMA på grund av ett fel i Oracles Red Hat-kompatibla kernel.
   
   Förutom ovanstående, rekommenderar vi att du *tar bort* följande parametrar:
   
        rhgb quiet crashkernel=auto
   
   Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten.
   
   `crashkernel` alternativet kan vara rätt konfigurerat om det behövs, men Observera att den här parametern kommer att minska mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på de mindre VM-storlekarna.
10. Se till att SSH-servern är installerad och konfigurerad för start vid start.  Detta är vanligt vis standardvärdet.
11. Installera Azure Linux-agenten genom att köra följande kommando. Den senaste versionen är 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Observera att om du installerar WALinuxAgent-paketet tas NetworkManager-och NetworkManager-GNOME-paketen bort, om de inte redan har tagits bort enligt beskrivningen i steg 2.
12. Skapa inte växlings utrymme på OS-disken.
    
    Azure Linux-agenten kan automatiskt konfigurera växlings utrymme med hjälp av den lokala resurs disk som är kopplad till den virtuella datorn efter etableringen på Azure. Observera att den lokala resurs disken är en *temporär* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg) ändrar du följande parametrar i/etc/waagent.conf på lämpligt sätt:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Klicka på **åtgärd-> stänga av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.

---
## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Ändringar i Oracle Linux 7**

Att förbereda en virtuell dator med Oracle Linux 7 för Azure liknar Oracle Linux 6, men det finns flera viktiga skillnader att notera:

* Både Red Hat-kompatibla kernel och Oracles UEK3 stöds i Azure.  UEK3-kärnan rekommenderas.
* NetworkManager-paketet är inte längre i konflikt med Azure Linux-agenten. Det här paketet installeras som standard och vi rekommenderar att det inte tas bort.
* GRUB2 används nu som standard Start programmet, så proceduren för att redigera kernel-parametrar har ändrats (se nedan).
* XFS är nu standard fil systemet. Fil systemet ext4 kan fortfarande användas om du vill.

**Konfigurations steg**

1. Välj den virtuella datorn i Hyper-V Manager.
2. Klicka på **Anslut** för att öppna ett konsol fönster för den virtuella datorn.
3. Skapa en fil med namnet **nätverk** i katalogen `/etc/sysconfig/` som innehåller följande text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Skapa en fil med namnet **ifcfg-eth0** i katalogen `/etc/sysconfig/network-scripts/` som innehåller följande text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Ändra udev-regler för att undvika att skapa statiska regler för Ethernet-gränssnitten. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Se till att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:
   
        # sudo chkconfig network on
7. Installera python-pyasn1-paketet genom att köra följande kommando:
   
        # sudo yum install python-pyasn1
8. Kör följande kommando för att rensa aktuella yum-metadata och installera eventuella uppdateringar:
   
        # sudo yum clean all
        # sudo yum -y update
9. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Det gör du genom att öppna "/etc/default/grub" i en text redigerare och redigera `GRUB_CMDLINE_LINUX`-parametern, till exempel:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Detta säkerställer också att alla konsol meddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-support med fel söknings problem. Den stänger också av de nya OEL 7-namngivnings konventionerna för nätverkskort. Förutom ovanstående, rekommenderar vi att du *tar bort* följande parametrar:
   
       rhgb quiet crashkernel=auto
   
   Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten.
   
   `crashkernel` alternativet kan vara rätt konfigurerat om det behövs, men Observera att den här parametern kommer att minska mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på de mindre VM-storlekarna.
10. När du är klar med att redigera "/etc/default/grub" per ovan kör du följande kommando för att återskapa grub-konfigurationen:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Se till att SSH-servern är installerad och konfigurerad för start vid start.  Detta är vanligt vis standardvärdet.
12. Installera Azure Linux-agenten genom att köra följande kommando:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Skapa inte växlings utrymme på OS-disken.
    
    Azure Linux-agenten kan automatiskt konfigurera växlings utrymme med hjälp av den lokala resurs disk som är kopplad till den virtuella datorn efter etableringen på Azure. Observera att den lokala resurs disken är en *temporär* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg) ändrar du följande parametrar i/etc/waagent.conf på lämpligt sätt:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Klicka på **åtgärd-> stänga av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din Oracle Linux. VHD för att skapa nya virtuella datorer i Azure. Om det är första gången du laddar upp VHD-filen till Azure, se [skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).

