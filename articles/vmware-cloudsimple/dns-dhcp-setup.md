---
title: Azure VMware-lösning av CloudSimple – konfigurera arbets belastning DNS och DHCP för privat moln
description: Beskriver hur du konfigurerar DNS och DHCP för program och arbets belastningar som körs i din CloudSimple privata moln miljö
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ce2394805bfc302179da807c798445c30ce9daff
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619552"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Konfigurera DNS-och DHCP-program och arbets belastningar i ditt CloudSimple privata moln

Program och arbets belastningar som körs i en privat moln miljö kräver namn matchning och DHCP-tjänster för sökning och tilldelning av IP-adresser.  En korrekt DHCP-och DNS-infrastruktur krävs för att tillhandahålla dessa tjänster.  Du kan konfigurera en virtuell dator för att tillhandahålla dessa tjänster i din privata moln miljö.  

## <a name="prerequisites"></a>Förutsättningar

* En distribuerad port grupp med VLAN konfigurerat
* Dirigera installationen till lokala eller Internetbaserade DNS-servrar
* Mall för virtuella datorer eller ISO för att skapa en virtuell dator

## <a name="linux-based-dns-server-setup"></a>Installation av Linux-baserad DNS-Server

Linux erbjuder olika paket för att konfigurera DNS-servrar.  Här är en [exempel konfiguration från DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7) med instruktioner för att skapa en Bind DNS-server med öppen källkod.

## <a name="windows-based-setup"></a>Windows-baserad installation

Dessa Microsoft-avsnitt beskriver hur du konfigurerar en Windows-Server som en DNS-server och som en DHCP-server.

* [Windows Server som DNS-Server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server som DHCP-server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
