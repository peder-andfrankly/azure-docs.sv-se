---
title: IBM DB2-pureScale på Azure
description: I den här artikeln visar vi en arkitektur för att köra en IBM DB2 pureScale-miljö på Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: c597bb47ba6d075523b2eb2ca4d146fa22a97a2e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083083"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2-pureScale på Azure

IBM DB2 pureScale-miljön ger ett databas kluster för Azure med hög tillgänglighet och skalbarhet på Linux-operativsystem. Den här artikeln visar en arkitektur för att köra DB2 pureScale på Azure.

## <a name="overview"></a>Översikt

Företag har länge använda RDBMS-plattformar (Relations databas hanterings system) för sina behov av Online Transaction Processing (OLTP). Dessa dagar migrerar många av de stordatorbaserade databas miljöerna till Azure som ett sätt att utöka kapaciteten, minska kostnaderna och upprätthålla en stabil drifts kostnads struktur.

Migrering är ofta det första steget när du ska göra en äldre plattform modern. Till exempel har en företags kund nyligen reagerat sin IBM DB2-miljö som körs på z/OS till IBM DB2 pureScale på Azure. Även om den inte är identisk med den ursprungliga miljön ger IBM DB2-pureScale på Linux liknande funktioner för hög tillgänglighet och skalbarhet som IBM DB2 för z/OS som körs i en parallell Sysplex-konfiguration på stordatoren.

I den här artikeln beskrivs arkitekturen som används för den här Azure-migreringen. Kunden använde Red Hat Linux 7,4 för att testa konfigurationen. Den här versionen är tillgänglig på Azure Marketplace. Innan du väljer en Linux-distribution måste du kontrol lera de versioner som stöds för tillfället. Mer information finns i dokumentationen för [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) och [GlusterFS](https://docs.gluster.org/en/latest/).

Den här artikeln är en utgångs punkt för din plan för DB2-implementering. Dina affärs krav varierar, men samma grundläggande mönster gäller. Du kan också använda det här arkitektur mönstret för Online Analytical Processing (OLAP)-program i Azure.

Den här artikeln beskriver inte skillnaderna och möjliga migreringar för att flytta en IBM DB2 för z/OS-databas till IBM DB2-pureScale som körs på Linux. Det ger inte storleks uppskattningar och arbets belastnings analyser för att flytta från DB2 z/OS till DB2 pureScale. 

För att hjälpa dig att avgöra den bästa DB2 pureScale-arkitekturen för din miljö rekommenderar vi att du fullständigt uppskattar storlek och gör en hypotes. På käll systemet ser du till att du funderar på DB2 z/OS Parallel Sysplex med data delnings arkitektur, kopplings konfiguration och DDF (Distributed data Facility).

> [!NOTE]
> Den här artikeln beskriver en metod för DB2-migrering, men det finns andra. T. ex. kan DB2-pureScale också köras i virtualiserade lokala miljöer. IBM stöder DB2 på Microsoft Hyper-V i olika konfigurationer. Mer information finns i [arkitektur för DB2 pureScale-virtualisering](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) i IBM Knowledge Center.

## <a name="architecture"></a>Arkitektur

För att stödja hög tillgänglighet och skalbarhet i Azure kan du använda en skalbar, delad data arkitektur för DB2 pureScale. Kund migreringen använde följande exempel arkitektur.

![DB2-pureScale på virtuella Azure-datorer som visar lagring och nätverk](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2-pureScale på virtuella Azure-datorer som visar lagring och nätverk")


Diagrammet visar de logiska skikt som krävs för ett DB2 pureScale-kluster. Dessa inkluderar virtuella datorer för en klient, för hantering, för cachelagring, för databas motorn och för delad lagring. 

Förutom databas motorns noder innehåller diagrammet två noder som används för cachelagring av kluster (CFs). Minst två noder används för själva databas motorn. En DB2-server som tillhör ett pureScale-kluster kallas en medlem. 

Klustret är anslutet via iSCSI till ett GlusterFS-delat lagrings kluster med tre noder för att tillhandahålla skalbar lagring och hög tillgänglighet. DB2-pureScale installeras på virtuella Azure-datorer som kör Linux.

Den här metoden är en mall som du kan ändra för organisationens storlek och skala. Den baseras på följande:

-   Två eller flera databas medlemmar kombineras med minst två CF-noder. Noderna hanterar en global resurspool (GBP) för delade minnen och GLM-tjänster (global Lock Manager) för att kontrol lera delad åtkomst och låsa konkurrens från aktiva medlemmar. En CF-nod fungerar som primär och den andra som den sekundära, CF-noden. För att undvika en enskild felpunkt i miljön kräver ett DB2 pureScale-kluster minst fyra noder.

-   Delad lagring med hög prestanda (visas i P30 storlek i diagrammet). Var och en av Gluster FS-noderna använder den här lagringen.

-   Nätverk med höga prestanda för data medlemmar och delade lagrings enheter.

### <a name="compute-considerations"></a>Beräknings överväganden

Den här arkitekturen kör program, lagring och data nivåer på virtuella Azure-datorer. [Installations skripten för distribution](https://aka.ms/db2onazure) skapar följande:

-   Ett DB2 pureScale-kluster. Vilken typ av beräknings resurser du behöver i Azure beror på din installation. I allmänhet kan du använda två metoder:

    -   Använd ett nätverk med flera noder och högpresterande data behandling (HPC) där små till medel stora instanser får åtkomst till delad lagring. För den här HPC-typen av konfiguration ger Azure Memory-optimerade virtuella datorer i E-serien eller lagrings optimerade [virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) i L-serien nödvändig beräknings kraft.

    -   Använd färre stora instanser av virtuella datorer för data motorer. För stora instanser är de största minnesoptimerade virtuella datorer i [M-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/) idealiska för tunga minnes arbets belastningar. Du kan behöva en dedikerad instans, beroende på storleken på den logiska partitionen (LPAR) som används för att köra DB2.

-   DB2 CF använder minnesoptimerade virtuella datorer, t. ex. E-serien eller L-serien.

-   GlusterFS Storage använder standard\_-\_DS4 v2-virtuella datorer som kör Linux.

-   En GlusterFS-hopp är en standard\_DS2\_v2-virtuell dator som kör Linux.

-   Klienten är en standard\_virtuell DS3\_v2-dator som kör Windows (används för testning).

-   En vittnes Server är en\_standard\_virtuell DS3 v2-dator som kör Linux (används för DB2 pureScale).

> [!NOTE]
> Ett DB2 pureScale-kluster kräver minst två DB2-instanser. Det kräver också en cache-instans och en instans av en Lock Manager.

### <a name="storage-considerations"></a>Överväganden för lagring

Som Oracle RAC är DB2 pureScale en skalbar I/O-databas med hög prestanda. Vi rekommenderar att du använder det största [Azure Premium SSD](disks-types.md) -alternativet som passar dina behov. Mindre lagrings alternativ kan vara lämpliga för utvecklings-och test miljöer, medan produktions miljöer ofta behöver mer lagrings kapacitet. Exempel arkitekturen använder [P30](https://azure.microsoft.com/pricing/details/managed-disks/) på grund av förhållandet mellan IOPS och storlek och pris. Oavsett storlek använder Premium Storage för bästa prestanda.

DB2 pureScale använder en delad-allt-arkitektur där alla data är tillgängliga från alla klusternoder. Premium Storage måste delas mellan instanser, vare sig på begäran eller på dedikerade instanser.

Ett stort DB2 pureScale-kluster kan kräva 200 terabyte (TB) eller mer av Premium-delade lagring med IOPS på 100 000. DB2 pureScale stöder ett iSCSI-block som du kan använda på Azure. ISCSI-gränssnittet kräver ett delat lagrings kluster som du kan implementera med GlusterFS, S2D eller något annat verktyg. Den här typen av lösning skapar en virtuell storage area network-enhet (virtuellt SAN) i Azure. DB2 pureScale använder virtuellt San för att installera det klustrade fil systemet som används för att dela data mellan virtuella datorer.

Exempel arkitekturen använder GlusterFS, ett kostnads fritt, skalbart distribuerat fil system med öppen källkod som är optimerat för moln lagring.

### <a name="networking-considerations"></a>Nätverksöverväganden

IBM rekommenderar InfiniBand-nätverk för alla medlemmar i ett DB2 pureScale-kluster. DB2-pureScale använder också RDMA (Remote Direct Memory Access), där det är tillgängligt, för CFs.

Under installationen skapar du en Azure- [resurs grupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) som innehåller alla virtuella datorer. I allmänhet kan du gruppera resurser utifrån deras livstid och vem som ska hantera dem. De virtuella datorerna i den här arkitekturen kräver [accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Det är en Azure-funktion som ger en konsekvent, extremt låg nätverks fördröjning via en SR-IOV (Single-root I/O Virtualization) till en virtuell dator.

Varje virtuell Azure-dator distribueras till ett virtuellt nätverk som har undernät: Main, Gluster FS front end (gfsfe), Gluster FS server del (bfsbe), DB2 pureScale (db2be) och DB2 pureScale front end (db2fe). Installations skriptet skapar även de primära [nätverkskorten](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) på de virtuella datorerna i huvud under nätet.

Använd [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) för att begränsa nätverks trafiken inom det virtuella nätverket och för att isolera under näten.

På Azure måste DB2 pureScale använda TCP/IP som nätverks anslutning för lagring.

## <a name="next-steps"></a>Nästa steg

-   [Distribuera den här arkitekturen på Azure](deploy-ibm-db2-purescale-azure.md)
