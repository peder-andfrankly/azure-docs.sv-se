---
title: Azure Monitor för tjänst leverantörer | Microsoft Docs
description: Azure Monitor kan hjälpa Managed Service Providers (MSP), stora företag, oberoende program varu leverantörer (ISV) och värd leverantörer att hantera och övervaka servrar i kundens lokala eller moln infrastruktur.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 08/06/2019
ms.openlocfilehash: b0f25d01421edd329b03d8f2b7e1aafaa2ba67d5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932067"
---
# <a name="azure-monitor-for-service-providers"></a>Azure Monitor för tjänst leverantörer
Log Analytics arbets ytor i Azure Monitor kan hjälpa Managed Service Providers (MSP), stora företag, oberoende program varu leverantörer (ISV) och värd tjänster att hantera och övervaka servrar i kundens lokala eller molnbaserade infrastruktur. 

Stora företag delar många likheter med tjänst leverantörer, särskilt när det finns ett centraliserat IT-team som ansvarar för att hantera den för många olika affär senheter. För enkelhetens skull använder det här dokumentet termen *Service Provider* men samma funktioner är också tillgängliga för företag och andra kunder.

För partner och tjänst leverantörer som ingår i ett [CSP-program (Cloud Solution Provider)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) Log Analytics i Azure Monitor en av de Azure-tjänster som är tillgängliga i [Azure CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Arkitekturer för tjänst leverantörer

Log Analytics-arbetsytor är en metod som administratören kan använda för att styra flödet och isoleringen av [loggdata](data-platform-logs.md) och skapa en arkitektur som tillgodoser specifika affärs behov. [Den här artikeln](design-logs-deployment.md) beskriver design-, distributions-och migrerings överväganden för en arbets yta och i artikeln [Hantera åtkomst](manage-access.md) beskrivs hur du tillämpar och hanterar behörigheter för att logga data. Tjänste leverantörer har ytterligare överväganden.

Det finns tre möjliga arkitekturer för tjänst leverantörer angående Log Analytics arbets ytor:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. distribuerade loggar lagras i arbets ytor som finns i kundens klient organisation 

I den här arkitekturen distribueras en arbets yta i kundens klient organisation som används för alla loggar för kunden. Administratörer för tjänst leverantörer beviljas åtkomst till den här arbets ytan med [Azure Active Directory gäst användare (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Administratörerna av tjänst leverantören måste växla till deras kund katalog i Azure Portal för att kunna komma åt dessa arbets ytor.

Fördelarna med den här arkitekturen är:
* Kunden kan hantera åtkomst till loggarna med hjälp av sin egen [rollbaserade åtkomst](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Varje kund kan ha olika inställningar för arbets ytan, till exempel kvarhållning och data capping.
* Isolering mellan kunder för regler och kompatibilitet.
* Avgiften för varje arbets yta samlas in i kundens prenumeration.
* Loggar kan samlas in från alla typer av resurser, inte bara för agent-baserade. Till exempel loggar i Azure granskning.

Nack delarna med den här arkitekturen är:
* Det är svårare för tjänste leverantören att hantera ett stort antal kund klienter samtidigt.
* Administratörer av tjänst leverantörer måste vara etablerade i kund katalogen.
* Tjänste leverantören kan inte analysera data över sina kunder.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. centrala loggar lagras på en arbets yta som finns i tjänste leverantörens klient organisation

I den här arkitekturen lagras loggarna inte i kundens klienter, utan bara på en central plats inom en av tjänst leverantörens prenumerationer. De agenter som är installerade på kundens virtuella datorer konfigureras för att skicka sina loggar till den här arbets ytan med hjälp av arbetsyte-ID och hemlig nyckel.

Fördelarna med den här arkitekturen är:
* Det är enkelt att hantera ett stort antal kunder och integrera dem i olika Server dels system.

* Tjänste leverantören har fullständig ägarskap över loggarna och de olika artefakterna, till exempel funktioner och sparade frågor.

* Tjänste leverantören kan utföra analyser för alla sina kunder.

Nack delarna med den här arkitekturen är:
* Den här arkitekturen kan bara användas för agentbaserade VM-data. den kommer inte att använda PaaS, SaaS och Azures infrastruktur data källor.

* Det kan vara svårt att separera data mellan kunderna när de slås samman till en enda arbets yta. Det enda bästa sättet att göra detta är att använda datorns fullständigt kvalificerade domän namn (FQDN) eller via Azures prenumerations-ID. 

* Alla data från alla kunder kommer att lagras i samma region med en enda faktura och samma inställningar för kvarhållning och konfiguration.

* Azure Fabric-och PaaS-tjänster som Azure-diagnostik och Azures gransknings loggar kräver att arbets ytan finns i samma klient organisation som resursen, och kan därför inte skicka loggarna till den centrala arbets ytan.

* Alla VM-agenter från alla kunder kommer att autentiseras till den centrala arbets ytan med samma arbetsyte-ID och nyckel. Det finns ingen metod för att blockera loggar från en specifik kund utan att avbryta andra kunder.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrid loggar lagras i arbets ytan som finns i kundens klient organisation och vissa av dem hämtas till en central plats.

Den tredje arkitektur kombinationen mellan de två alternativen. Den baseras på den första distribuerade arkitekturen där loggarna är lokala för varje kund, men använder en mekanism för att skapa en central logg databas. En del av loggarna hämtas till en central plats för rapportering och analys. Den här delen kan vara ett litet antal data typer eller en sammanfattning av aktiviteten, till exempel daglig statistik.

Det finns två alternativ för att implementera loggar på en central plats:

1. Central arbets yta: tjänste leverantören kan skapa en arbets yta i sin klient och använda ett skript som använder [fråge-API](https://dev.loganalytics.io/) [: t med data insamlings-API: et](../../azure-monitor/platform/data-collector-api.md) för att hämta data från de olika arbets ytorna till den här centrala platsen. Ett annat alternativ än ett skript är att använda [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI som en central plats: Power BI kan fungera som den centrala platsen när de olika arbets ytorna exporterar data till den med hjälp av integreringen mellan arbets ytan Log Analytics och [Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Nästa steg

* Automatisera skapandet och konfigurationen av arbets ytor med [Resource Manager-mallar](template-workspace-configuration.md)

* Automatisera skapandet av arbets ytor med [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Använd [aviseringar](../../azure-monitor/platform/alerts-overview.md) för att integrera med befintliga system

* Generera sammanfattnings rapporter med [Power BI](../../azure-monitor/platform/powerbi.md)

* Granska processen med [att konfigurera Log Analytics och Power BI för att övervaka flera CSP-kunder](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
