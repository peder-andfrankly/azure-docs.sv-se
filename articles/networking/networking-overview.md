---
title: Azure-nätverk | Microsoft Docs
description: Lär dig om nätverks tjänster i Azure och deras funktioner.
services: networking
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2019
ms.author: kumud
ms.openlocfilehash: 857b38693ca85d6ab397cbe850f0cd530fefc88c
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598383"
---
# <a name="azure-networking"></a>Azure-nätverk

Nätverks tjänsterna i Azure tillhandahåller en mängd nätverksfunktioner som kan användas tillsammans eller separat. Klicka på någon av följande viktiga funktioner för att lära dig mer om dem:
- [**Anslutnings tjänster**](#connect): Anslut Azure-resurser och lokala resurser med valfri eller en kombination av dessa nätverks tjänster i Azure-Virtual Network (VNet), virtuellt WAN, ExpressRoute, VPN Gateway, Azure DNS eller Azure skydds.
- [**Program skydds tjänster**](#protect) Skydda dina program med någon eller en kombination av dessa nätverks tjänster i Azure-DDoS skydd, brand vägg, nätverks säkerhets grupper, brand vägg för webbaserade program eller Virtual Network slut punkter.
- [**Program leverans tjänster**](#deliver) Leverera program i Azure-nätverket med valfri eller en kombination av dessa nätverks tjänster i Azure-Content Delivery Network (CDN), Azure-tjänsten för front dörr, Traffic Manager, Application Gateway eller Load Balancer.
- [**Nätverks övervakning**](#monitor) – övervaka dina nätverks resurser med valfri eller en kombination av dessa nätverks tjänster i Azure-Network Watcher, ExpressRoute-övervakare, Azure Monitor eller VNet Terminal Access Point (Knacka).

## <a name="connect"></a>Anslutnings tjänster
 
I det här avsnittet beskrivs tjänster som ger anslutning mellan Azure-resurser, anslutning från ett lokalt nätverk till Azure-resurser och filial till gren anslutning i Azure-virtuellt nätverk, ExpressRoute, VPN Gateway, virtuellt WAN, DNS och Azure Skydds.

|Tjänsten|Varför ska jag använda?|Scenarier|
|---|---|---|
|[Virtuellt nätverk](#vnet)|Gör det möjligt för Azure-resurser att kommunicera på ett säkert sätt med varandra, Internet och lokala nätverk.| <p>[Filtrera nätverkstrafik](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Dirigera nätverkstrafik](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Begränsa nätverksåtkomst till resurser](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Ansluta virtuella nätverk](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Utökar dina lokala nätverk till Microsoft-molnet via en privat anslutning som under lättas av en anslutnings leverantör.|<p>[Skapa och ändra en ExpressRoute-krets](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Skapa och ändra peering för en ExpressRoute-krets](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Länka ett VNet till en ExpressRoute-krets](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Konfigurera och hantera väg filter för ExpressRoute-kretsar](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Skickar krypterad trafik mellan ett virtuellt Azure-nätverk och en lokal plats via det offentliga Internet.|<p>[Plats-till-plats-anslutningar](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[VNet-till-VNet-anslutningar](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Punkt-till-plats-anslutningar](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtuellt WAN](#virtualwan)|Optimerar och automatiserar gren anslutningen till och via Azure. Azure-regioner fungerar som hubbar som du kan välja att ansluta dina grenar till.|<p>[Plats-till-plats-anslutningar](../virtual-wan/virtual-wan-site-to-site-portal.md), [ExpressRoute-anslutningar](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Är värd för DNS-domäner som tillhandahåller namn matchning med hjälp av Microsoft Azure-infrastrukturen.|<p>[Använda Azure DNS som värd för din domän](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Skapa DNS-poster för en webbapp](../dns/dns-web-sites-custom-domain.md)</p> <p>[Skapa en aliasresurspost för Traffic Manager](../dns/tutorial-alias-tm.md)</p> <p>[Skapa en aliasresurspost för offentlig IP-adress](../dns/tutorial-alias-pip.md)</p> <p>[Skapa en aliasresurspost för zon resurs posten](../dns/tutorial-alias-rr.md)</p>|
|[Azure-skydds (för hands version)](#bastion)|Konfigurera säker och sömlös RDP/SSH-anslutning till dina virtuella datorer direkt i Azure-portalen via SSL. När du ansluter via Azure skydds behöver inte dina virtuella datorer en offentlig IP-adress|<p>[Skapa en Azure skydds-värd](../bastion/bastion-create-host-portal.md)</p><p>[Ansluta med SSH till en virtuell Linux-dator](../bastion/bastion-connect-vm-ssh.md)</p><p>[Ansluta via RDP till en virtuell Windows-dator](../bastion/bastion-connect-vm-rdp.md)</p>|
||||


### <a name="vnet"></a>Virtuellt nätverk

Azure Virtual Network (VNet) är det grundläggande Bygg blocket för ditt privata nätverk i Azure. Du kan använda en virtuella nätverk för att:
- **Kommunicera mellan Azure-resurser**: Du kan distribuera virtuella datorer och flera andra typer av Azure-resurser till ett virtuellt nätverk, som Azure App Service-miljöer, AKS (Azure Kubernetes Service) och Azure Virtual Machine Scale Sets. En fullständig lista över Azure-resurser som du kan distribuera till ett virtuellt nätverk finns i [Tjänstintegration för virtuella nätverk](../virtual-network/virtual-network-for-azure-services.md).
- **Kommunicera mellan varandra**: Du kan ansluta virtuella nätverk till varandra, så att resurser i de virtuella nätverken kan kommunicera med varandra, med hjälp av peerkoppling av virtuella nätverk. De virtuella nätverken du ansluter kan finnas i samma, eller olika, Azure-regioner. Mer information finns i peering för [virtuella nätverk](../virtual-network/virtual-network-peering-overview.md).
- **Kommunicera med Internet**: Alla resurser i ett VNet kan som standard kommunicera utgående till Internet. Du kan kommunicera inkommande trafik till en resurs genom att tilldela en offentlig IP-adress eller en offentlig lastbalanserare. Du kan också använda [offentliga IP-adresser](../virtual-network/virtual-network-public-ip-address.md) eller offentliga [Load Balancer](../load-balancer/load-balancer-overview.md) för att hantera dina utgående anslutningar.
- **Kommunicera med lokala nätverk**: Du kan ansluta dina lokala datorer och nätverk till ett virtuellt nätverk med hjälp av [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-introduction.md).

Mer information finns i [Vad är Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a>ExpressRoute-anslutning
Med ExpressRoute kan du utöka dina lokala nätverk till Microsoft-molnet via en privat anslutning som under lättas av en anslutnings leverantör. Den här anslutningen är privat. Trafiken går inte via Internet. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.  Mer information finns i [Vad är ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpngateway"></a>VPN Gateway
VPN Gateway hjälper dig att skapa krypterade anslutningar mellan olika platser till ditt virtuella nätverk från lokala platser eller skapa krypterade anslutningar mellan virtuella nätverk. Det finns olika konfigurationer som är tillgängliga för VPN Gateway anslutningar, till exempel plats-till-plats, punkt-till-plats eller VNet till VNet.
Följande diagram illustrerar flera plats-till-plats-VPN-anslutningar till samma virtuella nätverk.

![Azure VPN Gateway-anslutningar från plats till plats](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Mer information om olika typer av VPN-anslutningar finns [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtualwan"></a>Virtuellt WAN
Azure Virtual WAN är en nätverks tjänst som tillhandahåller optimerad och automatiserad gren anslutning till och via Azure. Azure-regioner fungerar som hubbar som du kan välja att ansluta dina grenar till. Du kan använda Azures stamnät för att även ansluta grenar och dra nytta av anslutningar från förgrening till VNet. Azure Virtual WAN ger tillsammans många Azure-tjänster för moln anslutning, till exempel VPN för plats till plats, ExpressRoute, punkt-till-plats-VPN för användare i ett enda drift gränssnitt. Anslutning till Azure-virtuella nätverk upprättas med hjälp av virtuella nätverks anslutningar. Mer information finns i [Vad är Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md).

![Virtual WAN-diagram](./media/networking-overview/virtualwan1.png)

### <a name="dns"></a>Azure DNS
Azure DNS är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med hjälp av samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. Mer information finns i [Vad är Azure DNS?](../dns/dns-overview.md).

### <a name="bastion"></a>Azure-skydds (för hands version)
Azure skydds-tjänsten är en ny helt plattforms oberoende PaaS-tjänst som du etablerar i det virtuella nätverket. Den ger säker och sömlös RDP/SSH-anslutning till dina virtuella datorer direkt i Azure Portal via SSL. När du ansluter via Azure Bastion behöver dina virtuella datorer inte någon offentlig IP-adress. Mer information finns i [Vad är Azure skydds?](../bastion/bastion-overview.md).

![Azure skydds-arkitektur](./media/networking-overview/architecture.png)


## <a name="protect"></a>Program skydds tjänster

I det här avsnittet beskrivs nätverks tjänster i Azure som hjälper dig att skydda dina nätverks resurser – DDoS Protection, brand vägg för webbaserade program, Azure-brandvägg, nätverks säkerhets grupper och tjänst slut punkter.

|Tjänsten|Varför ska jag använda?|Scenario|
|---|---|---|
|[DDoS skydd](#ddosprotection) |Hög tillgänglighet för dina program med skydd mot överskott IP-trafikkostnader|[Hantera Azure DDoS Protection](../virtual-network/manage-ddos-protection.md)|
|[Brand vägg för webbaserade program](#waf)|<p>[Azure WAF med Application Gateway](../application-gateway/waf-overview.md) ger regional skydd till entiteter i offentligt och privat adress utrymme</p><p>[Med Azure-WAF med front dörren](../frontdoor/waf-overview.md) får du skydd på nätverks kanten till offentliga slut punkter.</p>|<p>[Konfigurera skydds regler för bot](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Konfigurera anpassad svarskod](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Konfigurera regler för IP-begränsning](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Konfigurera regel för hastighets begränsning](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure-brandvägg](#firewall)|Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillstånds känslig brand vägg som en tjänst med inbyggd hög tillgänglighet och obegränsad moln skalbarhet.|<p>[Distribuera en Azure-brandvägg i ett VNet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[– Distribuera en Azure-brandvägg i ett hybrid nätverk](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrera inkommande trafik med Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Nätverkssäkerhetsgrupper](#nsg)|Fullständig detaljerad, distribuerad end Node-kontroll på VM/undernät för alla nätverks trafik flöden|[Filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/tutorial-filter-network-traffic.md)|
|[Tjänstslutpunkter för virtuellt nätverk](#serviceendpoints)|Ger dig möjlighet att begränsa nätverks åtkomsten till vissa Azure-tjänsteresurser till ett virtuellt nätverks under nät|[Begränsa nätverksåtkomst till PaaS-resurser](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
|||
### <a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) tillhandahåller motåtgärder mot de mest sofistikerade DDoS-hoten. Tjänsten tillhandahåller förbättrade funktioner för DDoS-minskning för ditt program och resurser som distribueras i dina virtuella nätverk. Dessutom kan kunder som använder Azure DDoS Protection ha till gång till DDoS Rapid Response-support för att engagera DDoS-experter under ett aktivt angrepp.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="waf"></a>Brand vägg för webbaserade program

Azure brand vägg för webbaserade program (WAF) ger skydd för dina webb program från vanliga webbutnyttjande och sårbarheter som SQL-inmatning och Cross Site Scripting. Azure WAF tillhandahåller direkt skydd från OWASP de 10 viktigaste säkerhets problemen via hanterade regler. Dessutom kan kunderna också konfigurera anpassade regler, som är kund hanterade regler för att ge ytterligare skydd baserat på käll-IP-intervall och begära attribut, till exempel huvuden, cookies, formulär data fält eller frågesträngs parametrar.

Kunderna kan välja att distribuera [Azure-WAF med Application Gateway](../application-gateway/waf-overview.md) som tillhandahåller regionalt skydd till entiteter i offentligt och privat adress utrymme. Kunder kan också välja att distribuera [Azure-WAF med en front dörr](../frontdoor/waf-overview.md) som ger skydd på nätverks sidan till offentliga slut punkter.

![Brandvägg för webbaserade program](./media/networking-overview/waf-overview.png)


### <a name="firewall"></a>Azure-brandvägg
Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Med hjälp av Azure Firewall kan du centralt skapa, tillämpa och logga program-och nätverks anslutnings principer över prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för din virtuella nätverksresurser som tillåter att externa brandväggar identifierar trafik som kommer från ditt virtuella nätverk. 

Mer information om Azure-brandväggen finns i [dokumentationen för Azure-brandväggen](../firewall/overview.md).

![Översikt över brandväggar](./media/networking-overview/firewall-threat.png)

### <a name="nsg"></a>Nätverks säkerhets grupper
Du kan filtrera nätverks trafik till och från Azure-resurser i ett virtuellt Azure-nätverk med en nätverks säkerhets grupp. Mer information finns i [säkerhets översikt](../virtual-network/security-overview.md).

### <a name="serviceendpoints"></a>Tjänst slut punkter
Med tjänstslutpunkter för Virtual Network (VNet) får du ett utökat privat adressutrymme för det virtuella nätverket och identiteten för ditt VNet till Azure-tjänsterna, via en direktanslutning. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Trafik från ditt VNet till Azure-tjänsten förblir alltid på Microsoft Azure-stamnätverket. Mer information finns i [tjänst slut punkter för virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md).

![Tjänstslutpunkter för virtuellt nätverk](./media/networking-overview/vnet-service-endpoints-overview.png)

## <a name="deliver"></a>Program leverans tjänster

I det här avsnittet beskrivs nätverks tjänster i Azure som hjälper dig att leverera program Content Delivery Network (CDN), Azure-tjänsten för front dörr, Traffic Manager, Application Gateway och Load Balancer.

|Tjänsten|Varför ska jag använda?|Scenario|
|---|---|---|
|[Content Delivery Network](#cdn)|Levererar innehåll med hög bandbredd till användare. CDN lagrar cachelagrat innehåll på gräns servrar i lager platser som ligger nära slutanvändare, för att minimera svars tiden|<p>[Lägg till CDN i en webbapp](../cdn/cdn-add-to-web-app.md)</p> <p>[-Åtkomst till Storage-blobbar med en Azure CDN anpassad domän över HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Lägga till en anpassad domän till din Azure CDN-slutpunkt](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Konfigurera HTTPS på en anpassad Azure CDN-domän](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure-tjänsten för front dörr](#frontdoor)|Med kan du definiera, hantera och övervaka den globala routningen för din webb trafik genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet.|<p>[Lägga till en anpassad domän i din Azure-tjänst för front dörr](../frontdoor/front-door-custom-domain.md)</p> <p>[Konfigurera HTTPS på en anpassad domän i front dörren](../frontdoor/front-door-custom-domain-https.md)</p><p>[Konfigurera geo-filtrering av brand Väggs princip för webb program](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|Distribuerar trafik baserat på DNS till tjänster i globala Azure-regioner och ger hög tillgänglighet och svars tid|<p> [Dirigera trafik för låg latens](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Dirigera trafik till en slutpunkt för prioritet](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Styra trafiken med viktade slutpunkter](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Dirigera trafik baserat på slut punktens geografiska plats](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Dirigera trafik utifrån användarens undernät](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Lastbalanserare](#loadbalancer)|Tillhandahåller regional belastnings utjämning genom att dirigera trafik mellan tillgänglighets zoner och till din virtuella nätverk. Tillhandahåller intern belastnings utjämning genom att dirigera trafik mellan och mellan dina resurser för att bygga ditt regionala program.|<p> [Belastningsutjämna Internettrafik till virtuella datorer](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Belastnings Utjämnings trafik mellan virtuella datorer i ett virtuellt nätverk](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Port vidarebefordra trafik till en angiven port på vissa virtuella datorer](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Konfigurera belastnings utjämning och utgående regler](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar.|<p>[Direkt webb trafik med Azure Application Gateway](../application-gateway/quick-create-portal.md)</p><p>[Konfigurera en programgateway med SSL-avslutning](../application-gateway/create-ssl-portal.md)</p><p>[Skapa en programgateway med webbadressbaserad omdirigering](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="cdn"></a>Content Delivery Network
Med CDN får utvecklare en global lösning för snabb innehållsleverans med hög bandbredd till användarna eftersom innehållet cachelagras på fysiska noder på strategiska platser runt om i världen. Mer information om Azure CDN finns i [Azure Content Delivery Network](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="frontdoor"></a>Azure-tjänsten för front dörr
Med Azure Front Door Service kan du definiera, hantera och övervaka global routning av din webbtrafik, genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet. Med Front Door kan du transformera dina globala (för flera regioner) konsument- och företagsprogram till robusta, högpresterande och anpassade moderna program, API:er och innehåll som når en global publik med Azure. Mer information finns i [Azures front dörr](../frontdoor/front-door-overview.md).


### <a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager är en lastbalanserare för DNS-baserad trafik som hjälper dig att distribuera trafiken optimalt till tjänster i globala Azure-regioner, med hög tillgänglighet och korta svarstider. Traffic Manager tillhandahåller en mängd metoder för trafik cirkulation för att distribuera trafik som prioritet, viktat, prestanda, geografiskt, flera värden eller undernät. Mer information om metoder för trafikroutning finns i [Traffic Manager metoder för routning](../traffic-manager/traffic-manager-routing-methods.md).

Följande diagram visar den slut punkts prioritetbaserade routningen med Traffic Manager:

![Traffic-routningsmetod för Azure Traffic Manager](./media/networking-overview/priority.png)

Mer information om Traffic Manager finns i [Vad är Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

### <a name="loadbalancer"></a>Load Balancer
Azure Load Balancer ger hög prestanda, låg latens Layer 4-belastnings utjämning för alla UDP-och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slut punkter. Du kan definiera regler för att mappa inkommande anslutningar till backend-platser genom att använda TCP-och HTTP-hälsohälso-/Bing-alternativ för att hantera tjänstens tillgänglighet. Läs mer om Load Balancer i artikeln [Load Balancer översikt](../load-balancer/load-balancer-overview.md) .

Följande bild visar ett Internet-riktat program på flera nivåer som använder både externa och interna belastningsutjämnare:

![Azure Load Balancer exempel](./media/networking-overview/IC744147.png)


### <a name="applicationgateway"></a>Application Gateway
Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. Det är en program leverans kontroll (ADC) som en tjänst som erbjuder olika Layer 7 belastnings Utjämnings funktioner för dina program. Mer information finns i [Vad är Azure Application Gateway?](../application-gateway/overview.md).

Följande diagram visar URL Path-baserad routning med Application Gateway.

![Application Gateway exempel](./media/networking-overview/figure1-720.png)

## <a name="monitor"></a>Tjänster för nätverks övervakning
I det här avsnittet beskrivs nätverks tjänster i Azure som hjälper dig att övervaka nätverks resurser – Network Watcher, ExpressRoute-övervakare, Azure Monitor och Virtual Network trycker.

|Tjänsten|Varför ska jag använda?|Scenario|
|---|---|---|
|[Network Watcher](#networkwatcher)|Hjälper dig att övervaka och felsöka anslutnings problem, hjälpa till att diagnostisera VPN-, NSG-och Dirigerings problem, avbilda paket på den virtuella datorn, automatiserade diagnos verktyg med hjälp av Azure Functions och Logic Apps|<p>[Diagnostisera problem med trafik filter för virtuella datorer](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnostisera problem med VM-routning](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Övervaka kommunikation mellan virtuella datorer](../network-watcher/connection-monitor.md)</p><p>[Diagnostisera kommunikationsproblem mellan nätverk](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Logga nätverkstrafik till och från en virtuell dator](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[Övervakaren ExpressRoute](#expressroutemonitor)|Ger real tids övervakning av nätverks prestanda, tillgänglighet och användning, hjälper till med automatisk identifiering av nätverkstopologi, ger snabbare fel isolering, identifierar tillfälliga nätverks problem, hjälper till att analysera historiska nätverks prestanda egenskaper, stöder flera prenumerationer|<p>[Konfigurera övervakare av nätverksprestanda för ExpressRoute](../expressroute/how-to-npm.md)</p><p>[ExpressRoute övervakning, mått och aviseringar](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Hjälper dig att förstå hur dina program presterar och proaktivt identifierar problem som påverkar dem och de resurser som de är beroende av.|<p>[Traffic Manager mått och aviseringar](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Azure Monitor-diagnostik för Standard Load Balancer](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Övervaka Azure Firewall-loggar och-mått](../firewall/tutorial-diagnostics.md)</p><p>[Övervakning och loggning i Azures brandvägg för webbaserade program](../frontdoor/waf-front-door-monitor.md)</p>|
|[Virtual Network trycka på](#vnettap)|Tillhandahåller kontinuerlig strömning av virtuella datorers nätverks trafik till paket insamlaren, aktiverar lösningar för hantering av nätverks-och program prestanda och verktyg för säkerhets analys|[Skapa ett VNet-tryck på resurs](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="networkwatcher"></a>Network Watcher
I Azure Network Watcher finns verktyg för att övervaka, diagnostisera, visa mått samt aktivera eller inaktivera loggar för resurser i ett virtuellt Azure-nätverk. Mer information finns i [Vad är Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroutemonitor"></a>Övervakaren ExpressRoute
Information om hur du visar ExpressRoute krets mått, diagnostikloggar och aviseringar finns i [ExpressRoute övervakning, mått och aviseringar](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azuremonitor"></a>Azure Monitor
Azure Monitor maximerar tillgänglighet och prestanda för dina program genom att leverera en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer. Det hjälper dig att förstå hur dina program fungerar och identifierar proaktivt problem som påverkar dem och de resurser som de förlitar sig på. Mer information finns i [Azure Monitor översikt](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="vnettap"></a>Virtual Network trycka på
Med hjälp av Azure Virtual Network-TRYCKNINGen (Terminal Access Point) kan du kontinuerligt strömma din virtuella dators nätverks trafik till en insamlare eller ett analys verktyg för nätverks paket. Insamlings-eller analys verktyget tillhandahålls av en [virtuell nätverks utrustnings](https://azure.microsoft.com/solutions/network-appliances/) partner. 

Följande bild visar hur det virtuella nätverket trycker på Works. 

![Hur det virtuella nätverket trycker på Works](./media/networking-overview/virtual-network-tap-architecture.png)

Mer information finns i [Vad är Virtual Network tryck](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Nästa steg

- Skapa ditt första VNet och Anslut några virtuella datorer till det genom att slutföra stegen i artikeln [skapa ditt första virtuella nätverk](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
- Anslut datorn till ett VNet genom att slutföra stegen i [artikeln Konfigurera en punkt-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Belastningsutjämna Internet trafik till offentliga servrar genom att slutföra stegen i artikeln [skapa en Internetbaserad belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
 
 
   
