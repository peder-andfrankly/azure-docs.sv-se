---
title: Konfigurera NFSv 4.1 standard domän för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du konfigurerar NFS-klienten för användning av NFSv 4.1 med Azure NetApp Files.
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
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: f226cdd6406372b5693d46b6d04900f2f0cda4e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906291"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Konfigurera NFSv 4.1 standard domän för Azure NetApp Files

NFSv4 introducerar konceptet för en domän för autentisering. Azure NetApp Files stöder för närvarande endast rot användar mappning från tjänsten till NFS-klienten. Om du vill använda NFSv 4.1-funktionen med Azure NetApp Files måste du uppdatera NFS-klienten.

## <a name="default-behavior-of-usergroup-mapping"></a>Standard beteende för mappning av användare/grupper

Standardvärdet för rot mappning för `nobody` användaren eftersom NFSv4-domänen är inställd på `localdomain`. När du monterar en Azure NetApp Files NFSv 4.1-volym som rot visas fil behörigheter på följande sätt:  

![Standard beteende för mappning av användare/grupper för NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Som exemplet ovan visar bör användaren för `file1` vara `root`, men den mappas till `nobody` som standard.  Den här artikeln visar hur du ställer in `file1` användare till `root`.  

## <a name="steps"></a>Steg 

1. Redigera `/etc/idmapd.conf`-filen på NFS-klienten.   
    Ta bort kommentaren till rad `#Domain` (det vill säga ta bort `#` från raden) och ändra värdet `localdomain` till `defaultv4iddomain.com`. 

    Inledande konfiguration: 
    
    ![Inledande konfiguration för NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Uppdaterad konfiguration:
    
    ![Uppdaterad konfiguration för NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Demontera alla för tillfället monterade NFS-volymer.
3. Uppdatera `/etc/idmapd.conf`-filen.
4. Starta om `rpcbind`-tjänsten på värden (`service rpcbind restart`) eller starta bara om värden.
5. Montera NFS-volymerna efter behov.   

    Se [montera eller demontera en volym för virtuella Windows-eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

I följande exempel visas den resulterande ändringen av användare/grupp: 

![Resulterande konfiguration för NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Som exemplet visar har användaren/gruppen nu ändrats från `nobody` till `root`.

## <a name="behavior-of-other-non-root-users-and-groups"></a>Beteende för andra (icke-rot) användare och grupper

Azure NetApp Files stöder lokala användare (användare som har skapats lokalt på en värd) som har behörigheter kopplade till filer eller mappar i NFSv 4.1-volymer. Tjänsten har dock inte stöd för att mappa användare/grupper över flera noder. Därför mappas inte användare som skapats på en värd som standard till användare som har skapats på en annan värd. 

I följande exempel har `Host1` tre befintliga test användar konton (`testuser01`, `testuser02`, `testuser03`): 

![Resulterande konfiguration för NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

Observera i `Host2`att test användar kontona inte har skapats, men att samma volym är monterad på båda värdarna:

![Resulterande konfiguration för NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Nästa steg 

[Montera eller demontera en volym för virtuella Windows-eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

