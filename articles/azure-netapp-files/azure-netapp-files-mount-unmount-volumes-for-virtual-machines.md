---
title: Montera eller demontera en Azure NetApp Files volym för virtuella Windows-eller Linux-datorer | Microsoft Docs
description: Beskriver hur du monterar eller demonterar en volym för virtuella datorer eller virtuella Linux-datorer.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: b-juche
ms.openlocfilehash: 7f1e9500a9268e0fba054f7065e858cd801aca7b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894104"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montera eller demontera en volym för virtuella Windows- eller Linux-datorer 

Du kan montera eller demontera en volym för virtuella Windows-eller Linux-datorer om det behövs.  Monterings anvisningarna för virtuella Linux-datorer finns på Azure NetApp Files.  

1. Klicka på bladet **volymer** och välj sedan den volym som du vill montera. 
2. Klicka på **monterings instruktioner** från den valda volymen och följ sedan anvisningarna för att montera volymen. 

    ![Monterings instruktioner NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Monterings instruktioner SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Om du använder NFSv 4.1 använder du följande kommando för att montera fil systemet: `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Om du vill att en NFS-volym ska monteras automatiskt när en virtuell Azure-dator startas eller startas om, lägger du till en post i `/etc/fstab`-filen på värden. 

    Exempel: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` är IP-adressen för den Azure NetApp Files volym som finns på volym egenskaper-bladet.
    * `$FILEPATH` är Azure NetApp Files volymens export Sök väg.
    * `$MOUNTPOINT` är katalogen som skapats på Linux-värden som används för att montera NFS-exporten.

4. Om du vill montera volymen till Windows med NFS:

    a. Montera volymen på en virtuell UNIX-eller Linux-dator först.  
    b. Kör ett `chmod 777`-eller `chmod 775` kommando mot volymen.  
    c. Montera volymen via NFS-klienten i Windows.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera NFSv 4.1 standard domän för Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Vanliga frågor och svar om NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
