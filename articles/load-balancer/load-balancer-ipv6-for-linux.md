---
title: Konfigurera DHCPv6 för virtuella Linux-datorer
titleSuffix: Azure Load Balancer
description: I den här artikeln lär du dig hur du konfigurerar DHCPv6 för virtuella Linux-datorer.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, azure-belastningsutjämnare, dual stack, offentlig IP-adress, inbyggd ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225321"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurera DHCPv6 för virtuella Linux-datorer


Några av Linux-avbildningar i Azure Marketplace för virtuella datorer har inte Dynamic Host Configuration Protocol version 6 (DHCPv6) konfigureras som standard. För att stödja IPv6, måste DHCPv6 konfigureras i Linux OS-distribution som du använder. Olika Linux-distributioner konfigurera DHCPv6 i en mängd olika sätt, eftersom de använder olika paket.

> [!NOTE]
> Senaste SUSE Linux- och CoreOS-avbildningar på Azure Marketplace har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar.

Det här dokumentet beskriver hur du aktiverar DHCPv6 så att din Linux-dator hämtar en IPv6-adress.

> [!WARNING]
> Genom att redigera felaktigt nätverkskonfigurationen kan förlora du åtkomst till den virtuella datorn. Vi rekommenderar att du testar din konfigurationsändringar på icke-produktionssystem. Anvisningarna i den här artikeln har testats på de senaste versionerna av Linux-avbildningar i Azure Marketplace. Mer detaljerade anvisningar finns i dokumentationen för din egen version av Linux.

## <a name="ubuntu"></a>Ubuntu

1. Redigera */etc/DHCP/dhclient6.conf* -filen och Lägg till följande rad:

        timeout 10;

2. Redigera nätverkskonfigurationen för eth0-gränssnittet med följande konfiguration:

   * Redigera */etc/network/interfaces.d/eth0.cfg* -filen på **Ubuntu 12,04 och 14,04**. 
   * Redigera */etc/network/interfaces.d/50-Cloud-init.cfg* -filen på **Ubuntu 16,04**.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Från och med Ubuntu 17,10 är standard mekanismen för nätverks konfiguration [netplan]( https://netplan.io).  Vid installation/instansiering kan netplan läsa nätverks konfiguration från YAML konfigurationsfiler på den här platsen:/{lib, etc, kör}/netplan/*. yaml.

Inkludera en *dhcp6: true* -instruktion för varje Ethernet-gränssnitt i konfigurationen.  Till exempel:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Under tidig start skriver netplan "Network renderare"-konfigurationen till/Run för att kunna lämna kontroll över enheter till den angivna Network daemonen för att se om det finns mer information om netplan i https://netplan.io/reference.
 
## <a name="debian"></a>Debian

1. Redigera */etc/DHCP/dhclient6.conf* -filen och Lägg till följande rad:

        timeout 10;

2. Redigera */etc/network/interfaces* -filen och Lägg till följande konfiguration:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS och Oracle Linux

1. Redigera filen */etc/sysconfig/Network* och Lägg till följande parameter:

        NETWORKING_IPV6=yes

2. Redigera */etc/sysconfig/Network-scripts/ifcfg-eth0* -filen och Lägg till följande två parametrar:

        IPV6INIT=yes
        DHCPV6C=yes

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 och openSUSE 13

Senaste SUSE Linux Enterprise Server (SLES) och openSUSE-avbildningar i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar. Om du har en virtuell dator som är baserad på en äldre eller anpassade SUSE-avbildning kan du göra följande:

1. Installera `dhcp-client` paketet om det behövs:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Redigera filen */etc/sysconfig/Network/ifcfg-eth0* och Lägg till följande parameter:

        DHCLIENT6_MODE='managed'

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 och openSUSE steg

Senaste SLES och openSUSE-avbildningar i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar. Om du har en virtuell dator som är baserad på en äldre eller anpassade SUSE-avbildning kan du göra följande:

1. Redigera */etc/sysconfig/Network/ifcfg-eth0* -filen och ersätt `#BOOTPROTO='dhcp4'`-parametern med följande värde:

        BOOTPROTO='dhcp'

2. Lägg till följande parameter i */etc/sysconfig/Network/ifcfg-eth0* -filen:

        DHCLIENT6_MODE='managed'

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Senaste CoreOS-avbildningar i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar. Om du har en virtuell dator baserat på en äldre eller anpassade CoreOS-avbildning kan du göra följande:

1. Redigera */etc/systemd/network/10_dhcp. Network* -fil:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Förnya IPv6-adress:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
