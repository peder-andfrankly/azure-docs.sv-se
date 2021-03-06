---
title: Anslut Azure ExpressRoute med Oracle Cloud Infrastructure | Microsoft Docs
description: Anslut Azure-ExpressRoute med OCI-FastConnect (Oracle Cloud Infrastructure) för att aktivera Oracle-programlösningar mellan moln
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2019
ms.author: rogirdh
ms.openlocfilehash: 63543c0ac34536b736bd4b8cdbd47fdd98e9f9be
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802213"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Konfigurera en direkt anslutning mellan Azure och Oracle Cloud Infrastructure  

För att skapa en [integrerad miljö för flera moln](oracle-oci-overview.md) (för hands version), erbjuder Microsoft och Oracle direkt samtrafik mellan Azure och Oracle Cloud Infrastructure (OCI) via [ExpressRoute](../../../expressroute/expressroute-introduction.md) och [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Med ExpressRoute och FastConnect-samtrafik kan kunderna uppleva låg latens, högt data flöde, privat direkt anslutning mellan de två molnen.

> [!IMPORTANT]
> Anslutningen mellan Microsoft Azure och OCI är i förhands gransknings fasen. För att kunna upprätta en anslutning med låg latens mellan Azure och OCI måste din Azure-prenumeration först aktive ras för den här funktionen. Du måste registrera dig i förhands granskningen genom att fylla i det här korta [enkät formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu). Du får ett e-postmeddelande tillbaka när din prenumeration har registrerats. Du kan inte använda funktionen förrän du får ett bekräftelse meddelande. Du kan också kontakta din Microsoft-representant för att vara aktive rad för den här för hands versionen. Åtkomst till för hands versions funktionen är beroende av att Microsoft är tillgänglig och begränsad av Microsoft. Slut för ande av undersökningen garanterar inte åtkomst. Den här för hands versionen tillhandahålls utan service nivå avtal och bör inte användas för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Se [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för Microsoft Azure för hands versionerna av mer information. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

Följande bild visar en översikt över sammanlänkningen:

![Nätverks anslutning mellan moln](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Krav

* Om du vill upprätta en anslutning mellan Azure och OCI måste du ha en aktiv Azure-prenumeration och ett aktivt OCI-innehav.

* Anslutning är bara möjlig när en peering-plats i Azure ExpressRoute är i närheten av eller på samma peering-plats som OCI-FastConnect. Se [begränsningar för för hands versionen](oracle-oci-overview.md#preview-limitations).

* Din Azure-prenumeration måste vara aktive rad för den här för hands versions funktionen.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurera direkt anslutning mellan ExpressRoute och FastConnect

1. Skapa en standard ExpressRoute-krets på din Azure-prenumeration under en resurs grupp. 
    * När du skapar ExpressRoute väljer du **Oracle Cloud FastConnect** som tjänst leverantör. Information om hur du skapar en ExpressRoute-krets finns i [steg-för-steg-guiden](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * En Azure ExpressRoute-krets tillhandahåller detaljerade bandbredds alternativ, medan FastConnect stöder 1, 2, 5 eller 10 Gbit/s. Därför rekommenderar vi att du väljer någon av dessa matchande bandbredds alternativ under ExpressRoute.

    ![Skapa ExpressRoute-krets](media/configure-azure-oci-networking/exr-create-new.png)
1. Anteckna din ExpressRoute **-tjänst nyckel**. Du måste ange nyckeln när du konfigurerar FastConnect-kretsen.

    ![ExpressRoute-tjänst nyckel](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Du debiteras för ExpressRoute-avgifter så snart ExpressRoute-kretsen har tillhandahållits (även om **providerns status** **inte är etablerad**).

1. Dela ut två privata IP-adressutrymmet som inte överlappar med ditt virtuella Azure-nätverk eller det virtuella OCI-nätverkets IP-adressutrymme. Vi kommer att referera till det första IP-adressutrymmet som primärt adress utrymme och det andra IP-adressutrymmet som sekundärt adress utrymme. Anteckna de adresser som du behöver när du konfigurerar FastConnect-kretsen.
1. Skapa en gateway för dynamisk routning (DRG). Du behöver det när du skapar din FastConnect-krets. Mer information finns i dokumentationen för [Dynamic routing Gateway](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) .
1. Skapa en FastConnect-krets under din Oracle-klient. Mer information finns i Oracle- [dokumentationen](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * Under FastConnect-konfiguration väljer du **Microsoft Azure: ExpressRoute** som Provider.
    * Välj den gateway för dynamisk routning som du etablerade i föregående steg.
    * Välj den bandbredd som ska tillhandahållas. För bästa prestanda måste bandbredden matcha den bandbredd som väljs när du skapar ExpressRoute-kretsen.
    * I **Provider service Key**klistrar du in ExpressRoute-tjänst nyckeln.
    * Använd det första/30 privata IP-adressutrymmet hämtas i ett föregående steg för den **primära BGP-IP-adressen** och det andra/30 privata IP-adressutrymmet för den **sekundära BGP-IP** -adressen.
        * Tilldela den första användbara adressen för de två intervallen för Oracle BGP IP-adressen (primär och sekundär) och den andra adressen till kundens BGP IP-adress (från ett FastConnect-perspektiv). Den första användbara IP-adressen är den andra IP-adressen i adress utrymmet/30 (den första IP-adressen är reserverad av Microsoft).
    * Klicka på **Skapa**.
1. Slutför länkningen av FastConnect till det virtuella moln nätverket under din Oracle-klient via dynamisk routning-Gateway med routningstabellen.
1. Gå till Azure och se till att **leverantörs statusen** för din ExpressRoute-krets har ändrats till **etablerad** och att en peering av typen **Azure Private** har etablerats. Detta är ett krav för följande steg.

    ![Status för ExpressRoute-Provider](media/configure-azure-oci-networking/exr-provider-status.png)
1. Klicka på **Azures privata** peering. Du ser att peering-informationen har kon figurer ATS automatiskt baserat på den information som du angav när du konfigurerade FastConnect-kretsen.

    ![Inställningar för privat peering](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Anslut virtuellt nätverk till ExpressRoute

1. Skapa ett virtuellt nätverk och en virtuell nätverksgateway om du inte redan har gjort det. Mer information finns i [steg-för-steg-guiden](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Konfigurera anslutningen mellan den virtuella Nätverksgatewayen och din ExpressRoute-krets genom att köra [terraform-skriptet](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) eller genom att köra PowerShell-kommandot för att [Konfigurera ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

När du har slutfört nätverks konfigurationen kan du kontrol lera giltigheten för konfigurationen genom att klicka på **Hämta ARP-poster** och **Hämta** routningstabell under bladet ExpressRoute Private peering i Azure Portal.

## <a name="automation"></a>Automation

Microsoft har skapat terraform-skript för att aktivera automatisk distribution av nätverks Interconnect. Terraform-skripten måste autentiseras med Azure före körningen, eftersom de kräver tillräckliga behörigheter för Azure-prenumerationen. Autentisering kan utföras med hjälp av ett [Azure Active Directory tjänstens huvud namn](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) eller med hjälp av Azure CLI. Mer information finns i terraform- [dokumentationen](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Terraform-skript och relaterad dokumentation för att distribuera mellan Connect finns i den här GitHub- [lagringsplatsen](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Övervakning

Genom att installera agenter på båda molnen kan du använda Azure [övervakare av nätverksprestanda (NPM)](../../../expressroute/how-to-npm.md) för att övervaka prestanda för slut punkt till slut punkt. NPM hjälper dig att enkelt identifiera nätverks problem och hjälper dem att eliminera dem.

## <a name="delete-the-interconnect-link"></a>Ta bort Interconnect-länken

Om du vill ta bort kopplingen måste följande steg följas i den angivna ordningen. Om du inte gör det leder det till "ExpressRoute-krets".

1. Ta bort ExpressRoute-anslutningen. Ta bort anslutningen genom att klicka på ikonen **ta bort** på sidan för din anslutning. Mer information finns i ExpressRoute- [dokumentationen](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Ta bort Oracle-FastConnect från moln konsolen i Oracle.
1. När du har tagit bort Oracle FastConnect-kretsen kan du ta bort Azure ExpressRoute-kretsen.

I det här läget är borttagnings-och avetablerings processen slutförd.

## <a name="next-steps"></a>Nästa steg

* Mer information om mellan moln anslutningen mellan OCI och Azure finns i [Oracle-dokumentationen](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Använd [terraform-skript](https://aka.ms/azureociinterconnecttf) för att distribuera infrastruktur för riktade Oracle-program över Azure och konfigurera nätverks sammanlänkning. 
