---
title: Integrering av Azures säkerhets produkter med Azure Security Center
description: I det här avsnittet presenteras Azure-säkerhetsprodukter som har integrerats med Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 693e7d35a0bb4c7dfbb3e033690a5e86e2c398a8
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278338"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Integrering av Azures säkerhets produkter i Azure Security Center

Azure Security Center ger dig ytterligare Microsoft-licenser för att arbeta med följande säkerhets produkter:

* [Azure-WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure-WAF<a name="azure-waf"></a>

Azure Application Gateway erbjuder en WAF (brandvägg för webbaserade program) som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem.

Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. Application Gateway-WAF baseras på kärn regel uppsättningen 3,0 eller 2.2.9 från det öppna säkerhets projektet för webb program. WAF uppdateras automatiskt för att skydda mot nya sårbarheter, utan ytterligare konfiguration som behövs. WAF-aviseringar strömmas till Security Center. Mer information om de aviseringar som genererats av WAF finns i [regel grupper och regler för webb programs brand vägg](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Azure DDoS Protection<a name="azure-ddos"></a>

DDoS-attacker (distributed denial of Service) är kända för att vara lätta att köra. De har blivit ett bra säkerhets problem, särskilt om du flyttar dina program till molnet. 

En DDoS-attack syftar till att göra slut på ett programs resurser, så att programmet blir otillgängligt för behöriga användare. DDoS-attacker kan rikta in sig på alla slut punkter som kan nås via Internet.

Azure DDoS Protection kombineras med bästa praxis för program design, vilket ger ett försvar mot DDoS-attacker. DDoS Protection tillhandahåller olika tjänst nivåer. Mer information finns i [Azure DDoS Protection översikt](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

DDoS Protection standard kan minimera följande typer av attacker:

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**En volym attack upptäcktes**|Det här angrepps målet är att överbelasta nätverks lagret med en stor mängd som verkar vara legitim trafik. Den innehåller UDP-översvämmare, förstärknings belastningar och andra översvämmade paket belastningar. DDoS Protection standard minskar risken för angrepp med flera gigabyte genom att absorbera och rensa dem, med global nätverks skalning, automatiskt.|
|**Protokoll attack identifierad**|Dessa attacker återger ett mål som inte kan nås genom att utnyttja en svaghet i skikt 3-och lager 4-protokollstacken. Det omfattar SYN översvämnings attacker, reflektions attacker och andra protokoll attacker. DDoS Protection standard minimerar dessa attacker, skiljer sig från skadlig och legitim trafik, genom att interagera med klienten och blockera skadlig trafik.|
|**Resurs (program) lager attack har identifierats**|Dessa angrepp riktar sig mot webb program paket, för att avbryta överföring av data mellan värdar. Vid angrepp ingår HTTP-protokollfel, SQL-inmatning, skript körning över flera webbplatser och andra nivå 7-attacker. Använd Azure Application Gateway-WAF, med DDoS Protection standard, för att skydda dig mot dessa attacker. Det finns även WAF-erbjudanden från tredje part som är tillgängliga i Azure Marketplace.|
