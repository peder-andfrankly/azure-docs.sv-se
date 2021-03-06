---
title: Vad är Azure Sentinel? | Microsoft Docs
description: Lär dig mer om Azure Sentinel, dess viktiga funktioner och hur det fungerar.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: 964ba16fb7421c58f43d8b7d0d0d96f5cd377579
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261865"
---
# <a name="what-is-azure-sentinel"></a>Vad är Azure Sentinel?

Microsoft Azure Sentinel är en skalbar, molnbaserad, molnbaserad, **Siem (Security information Event Management)** och **Soar-lösning (Security Orchestration autoresponse)** . Azure Sentinel ger intelligent säkerhets analys och hot information i hela företaget, vilket ger en enda lösning för aviserings identifiering, Hot synlighet, proaktiv jakt och hot svar. 

Azure Sentinel är din fåglar – ögon visning i hela företaget, vilket minskar belastningen på alltmer sofistikerade attacker, ökande volymer med aviseringar och långa upplösnings ramar.

- **Samla in data i moln skala** över alla användare, enheter, program och infrastruktur, både lokalt och i flera moln. 

- **Identifiera tidigare oidentifierade hot**och minimera falska positiva identifieringar med Microsofts analys och oöverträffad Hot information. 

- **Undersök hot med artificiell intelligens och söka**efter misstänkta aktiviteter i stor skala, och utnyttja cyberhot säkerhets arbete på Microsoft. 

- **Svara på incidenter snabbt** med inbyggd samordning och automatisering av vanliga uppgifter.


![Azure Sentinel Core-funktioner](./media/overview/core-capabilities.png)

Genom att skapa ett fullständigt utbud av befintliga Azure-tjänster, har Azure Sentinel inbyggd inblandning som är beprövad som Log Analytics och Logic Apps. Azure Sentinel berikar undersökningen och identifieringen med AI och tillhandahåller Microsofts hot informations ström och gör att du kan ta med din egen Hot information. 

 
## <a name="connect-to-all-your-data"></a>Anslut till alla dina data

För att kunna använda Azure Sentinel måste du först [ansluta till dina säkerhets källor](connect-data-sources.md). Azure Sentinel levereras med ett antal anslutningar för Microsoft-lösningar, som är tillgängliga direkt och ger real tids integrering, inklusive Microsoft Threat Protection-lösningar och Microsoft 365 källor, inklusive Office 365, Azure AD, Azure ATP och Microsoft Cloud App Security med mera. Dessutom finns det inbyggda anslutnings program till det bredare säkerhets eko systemet för lösningar som inte kommer från Microsoft. Du kan också använda vanliga händelse format, syslog eller REST-API för att ansluta dina data källor med Azure Sentinel.  

![Data insamlare](./media/collect-data/collect-data-page.png)

## <a name="workbooks"></a>Arbetsböcker

När du har [anslutit data källorna](quickstart-onboard.md) till Azure Sentinel kan du övervaka data med hjälp av Azure Sentinel-integrering med Azure Monitor arbets böcker, vilket ger mångsidighet i att skapa anpassade arbets böcker. Även om arbets böcker visas på olika sätt i Azure Sentinel, kan det vara praktiskt att se hur du [skapar interaktiva rapporter med Azure Monitor arbets böcker](../azure-monitor/app/usage-workbooks.md). Med Azure Sentinel kan du skapa anpassade arbets böcker i dina data och även med inbyggda mallar för arbets böcker så att du snabbt kan få insikter om dina data så snart du ansluter en data källa.

![Instrumentpaneler](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analys

För att hjälpa dig att minska bruset och minimera antalet aviseringar som du måste granska och undersöka, använder Azure Sentinel [analyser för att korrelera aviseringar till incidenter](tutorial-detect-threats-built-in.md). **Incidenter** är grupper med relaterade aviseringar som tillsammans skapar ett åtgärds möjligt hot som du kan undersöka och lösa. Använd de inbyggda korrelations reglerna som de är, eller Använd dem som start punkt för att skapa en egen. Azure Sentinel tillhandahåller också maskin inlärnings regler som mappar ditt nätverks beteende och letar efter avvikelser i dina resurser. Dessa analyser ansluter punkterna, genom att kombinera aviseringar med låg kvalitet på olika enheter i potentiella säkerhets incidenter med hög kvalitet.

![Incidenter](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Säkerhets automatisering & dirigering

Automatisera dina vanliga uppgifter och [förenkla säkerhets dirigering med spel böcker](tutorial-respond-threats-playbook.md) som integrerar med Azure-tjänster samt dina befintliga verktyg. Azure Sentinel-lösningen bygger på Azure Logic Apps, Azure-kontrollens automatiserings-och Orchestration-lösning ger en mycket utöknings bar arkitektur som möjliggör skalbara automatiseringar som nya tekniker och hot. Om du vill skapa spel böcker med Azure Logic Apps kan du välja från ett växande galleri med inbyggda spel böcker. Detta omfattar [200 + anslutningar](https://docs.microsoft.com/azure/connectors/apis-list) för tjänster som Azure Functions. Med kopplingarna kan du använda anpassad logik i kod, ServiceNow, JIRA, Zendesk, HTTP-begäranden, Microsoft Teams, slack, Windows Defender ATP och Cloud App Security.

Om du till exempel använder ServiceNow Ticket-systemet kan du använda de verktyg som finns för att använda Azure Logic Apps för att automatisera dina arbets flöden och öppna en biljett i ServiceNow varje gång en viss händelse identifieras.

![Strategiböcker](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Undersökning

I för hands versionen av Azure Sentinel [djupgående undersöknings](tutorial-investigate-cases.md) verktyg kan du förstå omfattningen och hitta rotor saken, av ett potentiellt säkerhetshot. Du kan välja en entitet i det interaktiva diagrammet för att ställa intressanta frågor för en speciell entitet och öka detalj nivån i entiteten och dess anslutningar för att komma till den grundläggande orsaken till hotet. 

![Undersökning](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Hotjakt

Använd Azure Sentinels [kraftfulla verktyg för jakt och verktyg](hunting.md), baserat på Mitre-ramverket, som gör det möjligt för dig att proaktivt söka efter säkerhetshot i din organisations data källor innan en avisering utlöses. När du har identifierat vilken jakt fråga som ger hög värdes information i möjliga attacker, kan du också skapa anpassade identifierings regler baserat på din fråga och ställa en funktions insikt som aviseringar till dina säkerhets incident svarare. Samtidigt kan du skapa bok märken för intressanta händelser, så att du kan återgå till dem senare, dela dem med andra och gruppera dem med andra korrelerande händelser för att skapa en övertygande incident för undersökning.

![Hotjakt](./media/overview/hunting.png)

## <a name="community"></a>Community

Azure Sentinel community är en kraftfull resurs för hot identifiering och automatisering. Våra Microsoft-säkerhetsanalytiker skapar och lägger till nya arbets böcker, spel böcker, jakt frågor med mera, så att du kan använda dem i din miljö. Du kan hämta exempel innehåll från den privata gruppen GitHub- [lagringsplatsen](https://aka.ms/asicommunity) för att skapa anpassade arbets böcker, jakt frågor, antecknings böcker och spel böcker för Azure Sentinel. 

![Community](./media/overview/community.png)

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure Sentinel behöver du en prenumeration på Microsoft Azure. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
- Lär dig hur du kan [publicera dina data till Azure Sentinel](quickstart-onboard.md)och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
