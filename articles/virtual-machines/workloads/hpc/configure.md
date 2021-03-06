---
title: Databehandling med höga prestanda – Azure-datorer | Microsoft Docs
description: Läs mer om databehandling med höga prestanda på Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707813"
---
# <a name="optimization-for-linux"></a>Optimering för Linux

Den här artikeln beskriver några viktiga tekniker för att optimera din OS-avbildning. Läs mer om [aktiverar InfiniBand](enable-infiniband.md) och optimera OS-avbildningar.

## <a name="update-lis"></a>Uppdatera LIS

Om du distribuerar med hjälp av en anpassad avbildning (till exempel ett äldre operativsystem som CentOS/RHEL 7.4 eller 7.5), uppdatera LIS på den virtuella datorn.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Frigöra minne

Förbättra effektiviteten genom att automatiskt frigöra minne för att undvika till fjärrminne.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Så här gör detta finns kvar när den virtuella datorn startas om:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Inaktivera brandväggen och SELinux

Inaktivera brandväggen och SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Inaktivera cpupower

Inaktivera cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [aktiverar InfiniBand](enable-infiniband.md) och optimera OS-avbildningar.

* Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) på Azure.
