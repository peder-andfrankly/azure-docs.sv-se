---
title: Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure | Microsoft Docs
description: Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 8136e65636561079603986f0d6ff30bcbd68258f
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534227"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Det finns två alternativ för att konfigurera ett pacemaker-kluster i Azure. Du kan antingen använda en avgränsnings agent, som tar hand om att starta om en misslyckad nod via Azure-API: erna eller så kan du använda en SBD-enhet.

SBD-enheten kräver minst en ytterligare virtuell dator som fungerar som en iSCSI-målserver och som tillhandahåller en SBD-enhet. Dessa iSCSI-mål servrar kan dock delas med andra pacemaker-kluster. Fördelen med att använda en SBD-enhet är snabbare att redundansväxla och om du använder SBD-enheter lokalt behöver du inte göra några ändringar på hur du hanterar pacemaker-klustret. Du kan använda upp till tre SBD-enheter för ett pacemaker-kluster för att tillåta att en SBD-enhet blir otillgänglig, till exempel under operativ Systems korrigering av iSCSI Target Server. Om du vill använda mer än en SBD-enhet per pacemaker, se till att distribuera flera iSCSI-målservern och Anslut en SBD från varje iSCSI-målserver. Vi rekommenderar att du använder antingen en SBD-enhet eller tre. Pacemaker kan inte automatiskt begränsa en klusternod om du bara konfigurerar två SBD-enheter och en av dem inte är tillgänglig. Om du vill kunna begränsa när en iSCSI-målserver är nere måste du använda tre SBD-enheter och därför tre iSCSI-målservern.

Om du inte vill investera på en ytterligare virtuell dator kan du också använda Azure-stängsel-agenten. Nack delen är att en redundansväxling kan ta mellan 10 och 15 minuter om en resurs slutar fungera eller om klusternoderna inte längre kan kommunicera med varandra.

![Pacemaker på SLES-översikt](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> När du planerar och distribuerar Linux-pacemaker klustrade noder och SBD-enheter är det viktigt att den övergripande tillförlitligheten för den fullständiga kluster konfigurationen som vidarebefordrar mellan de virtuella DATORerna som är värd för SBD-enheter inte passerar några andra enheter som [NVA](https://azure.microsoft.com/solutions/network-appliances/). Annars kan problem och underhålls händelser med NVA ha en negativ inverkan på stabiliteten och tillförlitligheten hos den övergripande kluster konfigurationen. För att undvika sådana hinder definierar du inte routningsregler i NVA eller [användardefinierade routningsregler](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) som dirigerar trafik mellan klustrade noder och SBD enheter via NVA och liknande enheter vid planering och distribution av Linux pacemaker-klustrade noder och SBD-enheter. 
>

## <a name="sbd-fencing"></a>SBD staket

Följ dessa steg om du vill använda en SBD-enhet för staket.

### <a name="set-up-iscsi-target-servers"></a>Konfigurera iSCSI-mål servrar

Du måste först skapa de virtuella iSCSI-mål datorerna. iSCSI-målservern kan delas med flera pacemaker-kluster.

1. Distribuera nya SLES 12 SP1 eller högre virtuella datorer och Anslut till dem via SSH. Datorerna behöver inte vara stora. En virtuell dator storlek som Standard_E2s_v3 eller Standard_D2s_v3 räcker. Se till att använda Premium Storage OS-disken.

Kör följande kommandon på alla **virtuella iSCSI-mål datorer**.

1. Uppdatera SLES

   <pre><code>sudo zypper update
   </code></pre>

1. Ta bort paket

   Undvik ett känt problem med targetcli och SLES 12 SP3 genom att avinstallera följande paket. Du kan ignorera fel om paket som inte går att hitta

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Installera iSCSI-mål paket

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Aktivera tjänsten iSCSI Target

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Skapa iSCSI-enhet på iSCSI Target Server

Kör följande kommandon på alla **virtuella iSCSI-mål datorer** för att skapa iSCSI-diskar för de kluster som används av dina SAP-system. I följande exempel skapas SBD-enheter för flera kluster. Det visar hur du använder en iSCSI-målserver för flera kluster. SBD-enheterna placeras på OS-disken. Kontrol lera att du har tillräckligt med utrymme.

**`nfs`** används för att identifiera NFS-klustret, används **ascsnw1** för att identifiera ASCS-klustret för **NW1**, **dbnw1** används för att identifiera databas klustret för **NW1**, **NFS-0** och **NFS-1** är värd namnen för NFS-klusternoderna, **NW1-xscs-0** och NW1- **xscs-1** är värd namnen för **NW1** ASCS-klusternoderna och **NW1-dB-0** och **NW1-DB-1** är värd namnen för databasens klusternoder. Ersätt dem med värd namnen för klusternoderna och SID för ditt SAP-system.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Du kan kontrol lera om allt har kon figurer ATS korrekt med

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Konfigurera SBD-enhet

Anslut till iSCSI-enheten som skapades i det sista steget från klustret.
Kör följande kommandon på noderna i det nya klustret som du vill skapa.
Följande objekt har prefixet **[A]** -tillämpligt för alla noder, **[1]** , som endast gäller nod 1 eller **[2]** -gäller endast nod 2.

1. **[A]** Anslut till iSCSI-enheterna

   Börja med att aktivera iSCSI-och SBD-tjänsterna.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** ändra initierarens namn på den första noden

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Ändra innehållet i filen så att det matchar de ACL: er som du använde när du skapade iSCSI-enheten på iSCSI-målservern, till exempel för NFS-servern.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** ändra initierarens namn på den andra noden

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Ändra innehållet i filen så att det matchar de ACL: er som du använde när du skapade iSCSI-enheten på iSCSI-målservern

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** starta om iSCSI-tjänsten

   Starta om iSCSI-tjänsten för att tillämpa ändringen

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Anslut iSCSI-enheterna. I exemplet nedan är 10.0.0.17 IP-adressen för iSCSI Target Server och 3260 är standard porten. <b>IQN. 2006-04. NFS. local: NFS</b> är ett av de målnamn som anges när du kör det första kommandot nedan (iscsiadm-m-identifiering).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Se till att iSCSI-enheterna är tillgängliga och anteckna enhetens namn (i följande exempel/dev/SDE)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Nu kan du hämta ID: n för iSCSI-enheterna.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   Kommandot visar tre enhets-ID: n för varje SBD enhet. Vi rekommenderar att du använder det ID som börjar med SCSI-3, i exemplet ovan

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** skapa SBD-enheten

   Använd enhets-ID: t för iSCSI-enheterna för att skapa de nya SBD-enheterna på den första klusternoden.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** anpassa SBD-konfigurationen

   Öppna konfigurations filen för SBD

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Ändra egenskapen för SBD-enheten, aktivera pacemaker-integrering och ändra start läget för SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   Skapa `softdog` konfigurations filen

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Läs in modulen

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Kluster installation

Följande objekt har prefixet **[A]** -tillämpligt för alla noder, **[1]** , som endast gäller nod 1 eller **[2]** -gäller endast nod 2.

1. **[A]** uppdatera SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** installera komponent, krävs för kluster resurser

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** konfigurera operativ systemet

   I vissa fall skapar pacemaker många processer och därmed förbrukar det tillåtna antalet processer. I sådana fall kan det hända att ett pulsslag mellan klusternoderna Miss fungerar och leder till redundansväxlingen av dina resurser. Vi rekommenderar att du ökar det högsta antalet tillåtna processer genom att ange följande parameter.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Minska storleken på den ändrade cachen. Mer information finns i [låga skriv prestanda på SLES 11/12-servrar med stort RAM-minne](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** konfigurera Cloud-netconfig-Azure för ha-kluster

   Ändra konfigurations filen för nätverks gränssnittet så som visas nedan för att förhindra att Cloud Network-plugin-programmet tar bort den virtuella IP-adressen (pacemaker måste styra VIP-tilldelningen). Mer information finns i [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** aktivera SSH-åtkomst

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** aktivera SSH-åtkomst

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** aktivera SSH-åtkomst

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** installera stängsel-agenter
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Om du använder SUSE Linux Enterprise Server för SAP 15 bör du vara medveten om att du behöver aktivera ytterligare en modul och installera ytterligare komponent, vilket är förutsättning för att använda Azure-stängsel-agenten. Mer information om SUSE moduler och tillägg finns i [moduler och tillägg som beskrivs](https://www.suse.com/documentation/sles-15/singlehtml/art_modules/art_modules.html). Följ anvisningarna nedan för att installera Azure python SDK. 

   Följande instruktioner för hur du installerar Azure python SDK gäller endast för SUSE Enterprise Server för SAP **15**.  

    - Om du använder en egen prenumeration följer du dessa anvisningar  

    <pre><code>
    #Activate module PackageHub/15/x86_64
    sudo SUSEConnect -p PackageHub/15/x86_64
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

     - Följ dessa instruktioner om du använder prenumerationen betala per användning  

    <pre><code>#Activate module PackageHub/15/x86_64
    zypper ar https://download.opensuse.org/repositories/openSUSE:/Backports:/SLE-15/standard/ SLE15-PackageHub
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

1. **[A]** namn matchning för värdnamn

   Du kan antingen använda en DNS-server eller ändra/etc/hosts på alla noder. Det här exemplet visar hur du använder/etc/hosts-filen.
   Ersätt IP-adress och värdnamn i följande kommandon. Fördelen med att använda/etc/hosts är att klustret blir oberoende av DNS, vilket kan vara en enskild fel punkt.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Infoga följande rader i/etc/hosts. Ändra IP-adress och värdnamn för att matcha din miljö   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** installera kluster

   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Lägg till nod i kluster

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** ändra hacluster-lösenord till samma lösen ord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** justera corosync-inställningar.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Lägg till följande fetstilt innehåll i filen om värdena inte är där eller skiljer sig åt. Se till att ändra token till 30000 för att tillåta underhåll av minnes bebetjäning. Mer information finns i [den här artikeln för Linux][virtual-machines-linux-maintenance] eller [Windows][virtual-machines-windows-maintenance].

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Starta sedan om corosync-tjänsten

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Skapa STONITH-enhet för Azure stängsel-agenten

STONITH-enheten använder ett huvud namn för tjänsten för att auktorisera mot Microsoft Azure. Följ de här stegen för att skapa ett huvud namn för tjänsten.

1. Gå till <https://portal.azure.com>
1. Öppna bladet Azure Active Directory  
   Gå till egenskaper och skriv ner katalog-ID: t. Detta är **klient-ID: t**.
1. Klicka på Appregistreringar
1. Klicka på ny registrering
1. Ange ett namn, välj "konton endast i den här organisations katalogen" 
2. Välj program typ "Web", ange en inloggnings-URL (till exempel http:\//localhost) och klicka på Lägg till  
   Inloggnings-URL: en används inte och kan vara en giltig URL
1. Välj certifikat och hemligheter och klicka sedan på ny klient hemlighet
1. Ange en beskrivning för en ny nyckel, välj "upphör aldrig" och klicka på Lägg till
1. Skriv ned värdet. Den används som **lösen ord** för tjänstens huvud namn
1. Välj översikt. Anteckna program-ID: t. Den används som användar namn (**inloggnings-ID** i stegen nedan) för tjänstens huvud namn

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** skapa en anpassad roll för stängsel-agenten

Tjänstens huvud namn har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge tjänstens huvud namn behörighet att starta och stoppa (frigöra) alla virtuella datorer i klustret. Om du inte redan har skapat den anpassade rollen kan du skapa den med hjälp av [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-powershell#create-a-custom-role) eller [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-cli)

Använd följande innehåll för indatafilen. Du måste anpassa innehållet till dina prenumerationer, ersätta c276fc76-9cd4-44c9-99a7-4fd71546436e och e91d47c4-76f3-4271-a796-21b4ecfe3624 med ID: t för din prenumeration. Om du bara har en prenumeration tar du bort den andra posten i AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** tilldela den anpassade rollen till tjänstens huvud namn

Tilldela rollen för den anpassade rollen "Linux-stängsel" som skapades i det sista kapitlet till tjänstens huvud namn. Använd inte ägar rollen längre!

1. Gå till [https://portal.azure.com](https://portal.azure.com)
1. Öppna bladet alla resurser
1. Välj den virtuella datorn för den första klusternoden
1. Klicka på åtkomst kontroll (IAM)
1. Klicka på Lägg till roll tilldelning
1. Välj rollen "rollen Linux stängsel agent"
1. Ange namnet på det program som du skapade ovan
1. Klicka på Spara

Upprepa stegen ovan för den andra klusternoden.

### <a name="1-create-the-stonith-devices"></a>**[1]** skapa STONITH-enheterna

När du har redigerat behörigheterna för de virtuella datorerna kan du konfigurera STONITH-enheterna i klustret.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Standard konfiguration av pacemaker för SBD

1. **[1]** Aktivera användning av en STONITH-enhet och ange gräns fördröjningen

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Pacemaker-konfiguration för schemalagda Azure-händelser

Azure erbjuder [schemalagda händelser](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events). Schemalagda händelser tillhandahålls via meta-data-tjänsten och ger tid för programmet att förbereda för händelser som avstängning av virtuella datorer, omdistribution av virtuella datorer osv. Resurs agent **[Azure – händelser](https://github.com/ClusterLabs/resource-agents/pull/1161)** Övervakare för schemalagda Azure-händelser. Om händelser identifieras försöker agenten stoppa alla resurser på den virtuella datorn som påverkas och flytta dem till en annan nod i klustret. För att uppnå att ytterligare pacemaker-resurser måste konfigureras. 

1. **[A]** installera **Azure-Events-** agenten. 

<pre><code>sudo zypper install resource-agents
</code></pre>

2. **[1]** konfigurera resurserna i pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > När du har konfigurerat pacemaker-resurserna för Azure-Events-agenten kan du få varnings meddelanden som:  
     Varning: CIB-bootstrap-Options: okänt attribut hostName_ <strong>värdnamn</strong>  
     Varning: CIB-bootstrap-Options: okänt attribut ' Azure-events_globalPullState '  
     Varning: CIB-bootstrap-Options: okänt attribut hostName_ <strong>värdnamn</strong>  
   > Dessa varnings meddelanden kan ignoreras.

## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server][sles-nfs-guide]
* [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program][sles-guide]
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha]
