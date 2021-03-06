---
title: Azure Virtual WAN global-överföring av nätverks arkitektur | Microsoft Docs
description: Lär dig mer om global transit nätverks arkitektur för virtuellt WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 9b736e897278c4656df83c30388efc812ac6193d
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607367"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Global överföring av nätverks arkitektur och virtuellt WAN

Moderna företag kräver allmänt förekommande-anslutning mellan Hyper-distribuerade program, data och användare i hela molnet och lokalt. Global transit nätverks arkitektur antas av företag för att konsolidera, ansluta och kontrol lera det molnbaserade moderna, globala företags IT-utrymmet.

Den globala överförings nätverks arkitekturen baseras på en klassisk nav-och-eker-nätverksanslutning där det molnbaserade nätverkets hubb möjliggör transitiv anslutning mellan slut punkter som kan fördelas mellan olika typer av ekrar.

I den här modellen kan ekrar vara:
* Virtuellt nätverk (virtuella nätverk)
* Fysisk gren plats
* Fjärran vändare
* Internet

![hubb och eker](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Bild 1: global transiterings nav-och-eker-nätverk**

Bild 1 visar den logiska vyn för det globala överförings nätverket där geografiskt distribuerade användare, fysiska platser och virtuella nätverk är anslutna via en nätverks hubb som finns i molnet. Den här arkitekturen möjliggör logisk överföring av ett hopp mellan nätverks slut punkterna.

## <a name="globalnetworktransit"></a>Globalt överförings nätverk med virtuellt WAN

Azure Virtual WAN är en Microsoft-hanterad moln nätverks tjänst. Alla nätverks komponenter som den här tjänsten består av är värdbaserade och hanteras av Microsoft. Mer information om virtuellt WAN finns i artikeln [Översikt över virtuella WAN-nätverk](virtual-wan-about.md) .

Azure Virtual WAN möjliggör en global överförings nätverks arkitektur genom att aktivera allmänt förekommande, alla anslutningar mellan globalt distribuerade uppsättningar av moln arbets belastningar i virtuella nätverk, avdelnings platser, SaaS och PaaS program och användare.

![Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Bild 2: globalt överförings nätverk och virtuellt WAN**

I Azure Virtual WAN-arkitekturen är virtuella WAN-hubbar etablerade i Azure-regioner, som du kan välja att ansluta dina grenar, virtuella nätverk och fjärran vändare till. De fysiska gren platserna är anslutna till hubben med Premium-ExpressRoute eller plats-till-plats-VPN, virtuella nätverk är anslutna till hubben via VNet-anslutningar och fjärran vändare kan ansluta direkt till hubben med hjälp av användarens VPN (punkt-till-plats-VPN). Virtual WAN stöder även VNet-anslutning över flera regioner där ett VNet i en region kan anslutas till en virtuell WAN-hubb i en annan region.

Du kan upprätta ett virtuellt WAN-nätverk genom att skapa en enda virtuell WAN-hubb i den region som har det största antalet ekrar (grenar, virtuella nätverk, användare) och sedan ansluta de ekrar som finns i andra regioner till hubben. Det här är ett användbart alternativ när ett företags avtryck huvudsakligen finns i en region med några få fjärrekers.  
  
## <a name="hubtohub"></a>NAV-till-hubb-anslutning

Ett företags moln utrymme kan omfatta flera moln regioner och är optimal (svars tid) för att få åtkomst till molnet från en region som är närmast deras fysiska plats och användare. En av nyckel principerna för global överförings nätverks arkitektur är att aktivera anslutningar mellan olika regioner mellan alla moln-och lokala nätverks slut punkter. Det innebär att trafik från en gren som är ansluten till molnet i en region kan komma åt en annan gren eller ett VNet i en annan region med nav-till-hubb-anslutning som är aktive rad av [Azure globalt nätverk](https://azure.microsoft.com/global-infrastructure/global-network/).

![över-region](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Bild 3: virtuell WAN-anslutning mellan regioner**

När flera hubbar är aktiverade i ett enda virtuellt WAN-nätverk, kopplas hubbarna automatiskt samman via nav-till-hubb-länkar, vilket möjliggör global anslutning mellan grenar och virtuella nätverk som distribueras över flera regioner. 

Dessutom kan hubbar som är alla delar av samma virtuella WAN-nätverk associeras med olika regionala åtkomst-och säkerhets principer. Mer information finns i [säkerhet och princip kontroll](#security) längre fram i den här artikeln.

## <a name="anytoany"></a>Alla-till-alla-anslutningar

Global transit nätverks arkitektur möjliggör alla-till-alla-anslutningar via virtuella WAN-hubbar. Den här arkitekturen eliminerar eller minskar behovet av fullständigt nät eller en partiell nät anslutning mellan ekrar, som är mer komplexa för att bygga och underhålla. Dessutom är routnings kontroll i nav-och-eker vs. nät nätverk enklare att konfigurera och underhålla.

Alla anslutningar (i kontexten för en global arkitektur) gör det möjligt för företag med globalt distribuerade användare, grenar, data Center, virtuella nätverk och program att ansluta till varandra via "transitering"-hubben. Azure Virtual WAN fungerar som globalt system för överföring.

![valfri](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Bild 4: vägar för virtuella WAN-trafik**

Azure Virtual WAN stöder följande anslutnings vägar för global överföring. Bokstäverna i parenteser mappas till bild 4.

* Branch-till-VNet (a)
* Gren-till-gren (b)
  * ExpressRoute Global Reach och virtuellt WAN
* Fjärran vändare till VNet (c)
* Fjärran sluten användare-till-gren (d)
* VNet-till-VNet (e)
* Gren till hubb – hubb-till-gren (f)
* Gren till hubb – hubb-till-VNet (g)
* VNet-till-hubb – hubb-till-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Gren-till-VNet (a) och gren-till-VNet över flera regioner (g)

Branch-till-VNet är den primära sökvägen som stöds av Azure Virtual WAN. Med den här sökvägen kan du ansluta grenar till Azure IAAS Enterprise-arbetsbelastningar som har distribuerats i Azure virtuella nätverk. Grenar kan anslutas till det virtuella WAN-nätverket via ExpressRoute eller VPN för plats till plats. Trafik överföringarna till virtuella nätverk som är anslutna till de virtuella WAN-hubbarna via VNet-anslutningar. Explicit [Gateway-överföring](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) krävs inte för virtuellt WAN-nätverk eftersom det virtuella WAN-nätverket automatiskt aktiverar Gateway-överföring till gren platsen. Se artikeln om [virtuella WAN-partners](virtual-wan-configure-automation-providers.md) om hur du ansluter en SD-WAN-CPE till virtuellt WAN.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach och virtuellt WAN

ExpressRoute är ett privat och flexibelt sätt att ansluta dina lokala nätverk till Microsoft Cloud. Virtuellt WAN stöder Express Route-anslutningar. Anslutning av en förgrenings plats till ett virtuellt WAN med Express Route kräver 1) krets för att vara på en Global Reach aktive rad plats.

ExpressRoute Global Reach är en tilläggs funktion för ExpressRoute. Med Global Reach kan du länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk. Grenar som är anslutna till Azure Virtual WAN med ExpressRoute kräver att ExpressRoute-Global Reach kommunicerar med varandra.

I den här modellen kan varje gren som är ansluten till den virtuella WAN-hubben med ExpressRoute ansluta till virtuella nätverk med hjälp av gren-till-VNet-sökvägen. Gren-till-gren-trafik översätter inte navet eftersom ExpressRoute Global Reach ger en mer optimal sökväg över Azure WAN.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Gren-till-gren (b) och gren-till-gren över flera regioner (f)

Grenar kan anslutas till ett virtuellt Azure-nav med ExpressRoute-kretsar och/eller plats-till-plats-VPN-anslutningar. Du kan ansluta grenar till det virtuella WAN-hubben i den region som ligger närmast grenen.

Med det här alternativet kan företag utnyttja Azures stamnät för att ansluta grenar. Men även om den här funktionen är tillgänglig bör du väga fördelarna med att ansluta grenar över Azure Virtual WAN jämfört med ett privat WAN.  

### <a name="remote-user-to-vnet-c"></a>Fjärran vändare till VNet (c)

Du kan aktivera direkt säker fjärråtkomst till Azure med punkt-till-plats-anslutning från en fjärran sluten användar klient till ett virtuellt WAN. Företags-fjärran vändare behöver inte längre hairpin till molnet med hjälp av ett företags-VPN.

### <a name="remote-user-to-branch-d"></a>Fjärran sluten användare-till-gren (d)

Med sökvägen för fjärran sluten användare kan fjärran vändare som använder en punkt-till-plats-anslutning till Azure komma åt lokala arbets belastningar och program genom att använda molnet. Den här sökvägen ger fjärran vändare möjlighet att komma åt arbets belastningar som båda är distribuerade i Azure och lokalt. Företag kan aktivera central molnbaserad säker fjärråtkomst i Azure Virtual WAN.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet-till-VNet-överföring (e) och VNet-till-VNet över flera regioner (h)

VNet-till-VNet-överföringen gör att virtuella nätverk kan ansluta till varandra för att koppla samman flera nivåer som implementeras över flera virtuella nätverk. Alternativt kan du ansluta virtuella nätverk till varandra via VNet-peering och detta kan vara lämpligt för vissa scenarier där överföring via VWAN Hub inte är nödvändigt.

## <a name="security"></a>Säkerhet och princip kontroll

Azures virtuella WAN-hubbar sammankopplar alla nätverks slut punkter i hybrid nätverket och kan eventuellt se all trafik i överförings nätverket. Virtuella WAN-hubbar kan konverteras till säkra virtuella hubbar genom att distribuera Azure-brandväggen inuti VWAN-hubbar för att aktivera molnbaserad säkerhet, åtkomst och princip kontroll. Dirigering av Azure-brandväggar i virtuella WAN-hubbar kan utföras av Azure Firewall Manager.

[Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) innehåller funktioner för att hantera och skala säkerhet för globala överförings nätverk. Azure Firewall Manager ger möjlighet till central hantering av routning, global princip hantering, avancerade Internet säkerhets tjänster via tredje part tillsammans med Azure-brandväggen.

![skyddad virtuell hubb med Azure-brandvägg](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Bild 5: säker virtuell hubb med Azure-brandvägg**

Azure-brandväggen till det virtuella WAN-nätverket stöder följande anslutnings vägar för Global säker överföring. Bokstäverna i parenteser mappas till bild 5.

* Säker överföring mellan virtuella nätverk (e)
* VNet-till-Internet eller säkerhets tjänst från tredje part (i)
* Lokal-till-Internet eller säkerhets tjänst från tredje part (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Säker överföring mellan virtuella nätverk (e)

Skydd mot VNet-till-VNet gör att virtuella nätverk kan ansluta till varandra via Azure-brandväggen i den virtuella WAN-hubben.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-till-Internet eller säkerhets tjänst från tredje part (i)

Den skyddade överföringen från virtuella nätverk till Internet eller från tredje part gör att virtuella nätverk kan ansluta till Internet eller en säkerhets tjänst från tredje part som stöds via Azure-brandväggen i den virtuella WAN-hubben.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Lokal-till-Internet eller säkerhets tjänst från tredje part (j)
Med en säker transit från gren till Internet eller från tredje part kan grenar ansluta till Internet eller en säkerhets tjänst från tredje part som stöds via Azure-brandväggen i den virtuella WAN-hubben.

## <a name="next-steps"></a>Nästa steg

Skapa en anslutning med Virtual WAN och Distribuera Azure-brandväggen i VWAN Hub (s).

* [Plats-till-plats-anslutningar med hjälp av virtuellt WAN](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-anslutningar med hjälp av virtuellt WAN](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager för att distribuera Azure VB i VWAN](https://go.microsoft.com/fwlink/?linkid=2107683)
