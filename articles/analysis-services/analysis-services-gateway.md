---
title: Lokal datagateway för Azure Analysis Services | Microsoft Docs
description: En lokal gateway krävs om din Analysis Services-server i Azure ska ansluta till lokala data källor.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35ffc7f3c97ca7ab14f94c3607560ffb6ea0b399
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146847"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Ansluta till lokala data källor med lokal datagateway

Den lokala datagatewayen ger säker data överföring mellan lokala data källor och dina Azure Analysis Services-servrar i molnet. Förutom att arbeta med flera Azure Analysis Services-servrar i samma region, fungerar den senaste versionen av gatewayen också med Azure Logic Apps, Power BI, Power Apps och Microsoft Flow. Du kan associera flera tjänster i samma prenumeration och samma region med en enda Gateway. Även om den gateway som du installerar är samma för alla dessa tjänster, Azure Analysis Services och Logic Apps har ytterligare steg.

För Azure Analysis Services hämtar installationen med gatewayen första gången en process i fyra delar:

- **Hämta och kör installationen** – det här steget installerar en gateway-tjänst på en dator i din organisation. Du loggar också in på Azure med ett konto i [klient organisationens](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure AD. Konton för Azure B2B (gäst) stöds inte.

- **Registrera din gateway** – i det här steget anger du ett namn och en återställnings nyckel för din gateway och väljer en region, registrerar din gateway med moln tjänsten Gateway. Din gateway-resurs kan registreras i valfri region, men vi rekommenderar att den finns i samma region som dina Analysis Services-servrar. 

- **Skapa en gateway-resurs i Azure** – i det här steget skapar du en gateway-resurs i din Azure-prenumeration.

- **Anslut dina servrar till din gateway-resurs** – när du har en gateway-resurs i din prenumeration kan du börja ansluta dina servrar till den. Du kan ansluta flera servrar och andra resurser, förutsatt att de finns i samma prenumeration och samma region.

## <a name="how-it-works"> </a>Så här fungerar det
Den gateway som du installerar på en dator i din organisation körs som en Windows-tjänst, **lokal datagateway**. Den här lokala tjänsten är registrerad i Gateway-moln tjänsten via Azure Service Bus. Sedan skapar du en lokal datagateway-resurs för din Azure-prenumeration. Dina Azure Analysis Services-servrar ansluts sedan till din Azure gateway-resurs. När modeller på servern måste ansluta till dina lokala data källor för frågor eller bearbetning, passerar en fråga och ett data flöde Gateway-resursen, Azure Service Bus, lokal datagateway-tjänst och data källor. 

![Så här fungerar det](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Frågor och data flöde:

1. En fråga skapas av moln tjänsten med de krypterade autentiseringsuppgifterna för den lokala data källan. Den skickas sedan till en kö för att gatewayen ska kunna bearbetas.
2. Gateway-moln tjänsten analyserar frågan och skickar begäran till [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Den lokala datagatewayen avsöker Azure Service Bus för väntande begär Anden.
4. Gatewayen hämtar frågan, dekrypterar autentiseringsuppgifterna och ansluter till data källorna med dessa autentiseringsuppgifter.
5. Gatewayen skickar frågan till data källan för körning.
6. Resultaten skickas från data källan tillbaka till gatewayen och sedan till moln tjänsten och servern.

## <a name="installing"></a>Installera

När du installerar för en Azure Analysis Services-miljö är det viktigt att du följer stegen som beskrivs i [Installera och konfigurera en lokal datagateway för Azure Analysis Services](analysis-services-gateway-install.md). Den här artikeln är unik för Azure Analysis Services. Den innehåller ytterligare steg som krävs för att konfigurera en lokal datagateway-resurs i Azure och ansluta din Azure Analysis Services-server till resursen.

## <a name="ports-and-communication-settings"></a>Portar och kommunikations inställningar

Gatewayen skapar en utgående anslutning till Azure Service Bus. Den kommunicerar via utgående portar: TCP 443 (standard), 5671, 5672, 9350 till 9354.  Gatewayen kräver inte inkommande portar.

Du kan behöva inkludera IP-adresser för ditt data område i brand väggen. Du kan ladda ned [IP-listan Microsoft Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Listan uppdateras varje vecka. IP-adresserna i listan i Azure-datacenter-IP-listan är i CIDR-format. Mer information finns i [Interplatsroutning mellan domäner](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Följande är fullständigt kvalificerade domän namn som används av gatewayen.

| Domän namn | Utgående portar | Beskrivning |
| --- | --- | --- |
| *. powerbi.com |80 |HTTP som används för att hämta installations programmet. |
| *. powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queueing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Lyssnare på Service Bus Relay över TCP (kräver 443 för hämtning av Access Control-token) |
| *. frontend.clouddatahub.net |443 |HTTPS |
| *. core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *. msftncsi.com |443 |Används för att testa Internet anslutning om gatewayen inte kan kontaktas av Power BI-tjänst. |
| *. microsoftonline-p.com |443 |Används för autentisering beroende på konfiguration. |
| dc.services.visualstudio.com  |443 |Används av AppInsights för att samla in telemetri. |

### <a name="force-https"></a>Tvinga HTTPS-kommunikation med Azure Service Bus

Du kan tvinga gatewayen att kommunicera med Azure Service Bus genom att använda HTTPS i stället för direkt TCP. Det kan dock försämra prestanda avsevärt. Du kan ändra *Microsoft. powerbi. DataMovement. pipeline. GatewayCore. dll. config* -filen genom att ändra värdet från `AutoDetect` till `Https`. Den här filen finns vanligt vis i *C:\Program Files\On-premises data Gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Nästa steg 

Följande artiklar ingår i den lokala datagatewayens allmänna innehåll som gäller för alla tjänster som gatewayen stöder:

* [Vanliga frågor och svar om lokal datagateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Använd den lokala datagateway-appen](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Administration på klient nivå](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Konfigurera proxyinställningar](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Justera kommunikations inställningar](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Konfigurera loggfiler](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Felsöka](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Övervaka och optimera Gateway-prestanda](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
