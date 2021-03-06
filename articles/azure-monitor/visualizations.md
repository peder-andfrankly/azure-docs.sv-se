---
title: Visualisera data från Azure Monitor | Microsoft Docs
description: Innehåller en översikt över tillgängliga metoder för att visualisera mått-och loggdata som lagras i Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/24/2018
ms.openlocfilehash: c350cb1f8e7343f6d8e7a416aa0bba71388eaa86
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597841"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualisera data från Azure Monitor
Den här artikeln innehåller en översikt över tillgängliga metoder för att visualisera logg-och mått data som lagras i Azure Monitor.

Visualiseringar som diagram och diagram kan hjälpa dig att analysera dina övervaknings data och gå ned på problem och identifiera mönster. Beroende på vilket verktyg du använder kan du också välja att dela visualiseringar med andra användare i och utanför organisationen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-dashboards"></a>Azure-instrumentpaneler
[Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md) är den primära instrument panels tekniken för Azure. De är särskilt användbara när man tillhandahåller en enda panel med glas över din Azure-infrastruktur och tjänster så att du snabbt kan identifiera viktiga problem.

![Instrumentpanel](media/visualizations/dashboard.png)

### <a name="advantages"></a>Fördelar
- Djupgående integrering i Azure. Visualiseringar kan fästas på instrument paneler från flera Azure-sidor, inklusive Metrics Explorer, Log Analytics och Application Insights.
- Stöder både mått och loggar.
- Kombinera data från flera källor, inklusive utdata från [mått Utforskaren](platform/metrics-charts.md), [logg frågor](log-query/log-query-overview.md)och [kartor](app/app-map.md) och tillgänglighet i Application Insights.
- Alternativ för personliga eller delade instrument paneler. Integrerad med Azure [Role-baserad autentisering (RBAC)](../role-based-access-control/overview.md).
- Automatisk uppdatering. Mått uppdateringen är beroende av tidsintervallet med minst fem minuter. Loggar uppdateras varje timme med ett manuellt uppdaterings alternativ på begäran genom att klicka på ikonen "uppdatera" i en specifik visualisering, eller genom att uppdatera hela instrument panelen.
- Parametrized Mät instrument paneler med tidsstämpel och anpassade parametrar.
- Alternativ för flexibel layout.
- Hel skärms läge.


### <a name="limitations"></a>Begränsningar
- Begränsad kontroll över logg visualiseringar utan stöd för data tabeller. Det totala antalet data serier är begränsat till 10 med ytterligare data serier grupperade under en _annan_ Bucket.
- Inga anpassade parameter stöd för logg diagram.
- Logg diagram är begränsade till de senaste 30 dagarna.
- Det går bara att fästa logg diagram på delade instrument paneler.
- Ingen interaktivitet med instrument panels data.
- Begränsad kontext granskning – nedåt.

## <a name="azure-monitor-views"></a>Azure Monitor vyer
Med [vyer i Azure Monitor](platform/view-designer.md) kan du skapa anpassade visualiseringar med loggdata. De används av [övervaknings lösningar](insights/solutions.md) för att presentera de data som de samlar in.

![Visa](media/visualizations/view.png)

### <a name="advantages"></a>Fördelar
- Omfattande visualiseringar för loggdata.
- Exportera och importera vyer för att överföra dem till andra resurs grupper och prenumerationer.
- Integrerar i Azure Monitor hanterings modell med arbets ytor och övervaknings lösningar.
- [Filter](platform/view-designer-filters.md) för anpassade parametrar.
- Interaktiva, stöder flernivå granskning (Visa som visar mer information om en annan vy)

### <a name="limitations"></a>Begränsningar
- Stöder loggar men inte mått.
- Inga personliga vyer. Tillgänglig för alla användare med åtkomst till arbets ytan.
- Ingen automatisk uppdatering.
- Alternativ för begränsad layout.
- Inget stöd för att fråga över flera arbets ytor eller Application Insights program.
- Frågor är begränsade till svars storlek på 8 MB och frågekörningen på 110 sekunder.


## <a name="workbooks"></a>Arbetsböcker
[Arbets böcker](../azure-monitor/app/usage-workbooks.md) är interaktiva dokument som ger djupgående insikter om dina data, undersökningar och samarbete i teamet. Vissa exempel där arbets böcker är användbara är fel söknings guider och incident Postmortem.

![Boken](media/visualizations/workbook.png)

### <a name="advantages"></a>Fördelar
- Stöder både mått och loggar.
- Stöder parametrar som aktiverar interaktiva rapporter där du kan välja ett element i en tabell dynamiskt uppdaterar associerade diagram och visualiseringar.
- Dokument som påminner om flödet.
- Alternativ för personliga eller delade arbets böcker.
- Enkel, användarvänlig redigerings upplevelse.
- Mallar har stöd för offentlig GitHub mall Galleri.

### <a name="limitations"></a>Begränsningar
- Ingen automatisk uppdatering.
- Ingen kompakt layout, t. ex. instrument paneler, som gör arbets böcker mindre användbara som ett enda fönster i glaset. Avsett för att ge djupare insikter.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) är särskilt användbart för att skapa affärsinriktade instrument paneler och rapporter, samt rapporter som analyserar långsiktiga KPI-trender. Du kan [Importera resultatet av en logg fråga](platform/powerbi.md) till en Power BI data uppsättning så att du kan dra nytta av dess funktioner, till exempel att kombinera data från olika källor och dela rapporter på webben och mobila enheter.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Fördelar
- Omfattande visualiseringar.
- Omfattande interaktivitet, inklusive inzoomning och kors filtrering.
- Lätt att dela hela organisationen.
- Integrering med andra data från flera data källor.
- Bättre prestanda med resultat som cachelagrats i en kub.


### <a name="limitations"></a>Begränsningar
- Stöder loggar men inte mått.
- Ingen Azure-integrering. Det går inte att hantera instrument paneler och modeller via Azure Resource Manager.
- Frågeresultatet måste importeras till Power BI-modellen för att konfigurera. Begränsning av resultat storlek och uppdatering.
- Begränsad data uppdatering på åtta gånger per dag.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) är en öppen plattform som perfekt på drift instrument paneler. Det är särskilt användbart för att identifiera och isolera och sorterar drift incidenter. Du kan lägga till [Grafana Azure Monitor data källans plugin-program](platform/grafana-plugin.md) i din Azure-prenumeration för att visualisera dina Azure Metric-data.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Fördelar
- Omfattande visualiseringar.
- Omfattande eko system för data källor.
- Data interaktivitet inklusive zooma in.
- Stöder parametrar.

### <a name="limitations"></a>Begränsningar
- Ingen Azure-integrering. Det går inte att hantera instrument paneler och modeller via Azure Resource Manager.
- Kostnad för att stödja ytterligare Grafana-infrastruktur eller ytterligare kostnad för Grafana-molnet.


## <a name="build-your-own-custom-application"></a>Bygg ditt eget anpassade program
Du kan komma åt data i logg-och mät data i Azure Monitor via deras API med hjälp av en REST-klient, vilket gör att du kan bygga egna anpassade webbplatser och program.

### <a name="advantages"></a>Fördelar
- Fullständig flexibilitet i användar gränssnitt, visualisering, interaktivitet och funktioner.
- Kombinera mått och loggdata med andra data källor.

### <a name="disadvantages"></a>Nackdelar
- Betydande tekniska ansträngningar krävs.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [data som samlas in av Azure Monitor](platform/data-platform.md).
- Lär dig om [Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md).
- Lär dig mer om [vyer i Azure Monitor](platform/view-designer.md).
- Lär dig mer om [arbets böcker](../azure-monitor/app/usage-workbooks.md).
- Lär dig mer om att [Importera loggdata till Power BI](../azure-monitor/platform/powerbi.md).
- Lär dig mer om [Grafana-Azure Monitor data källans plugin-program](../azure-monitor/platform/grafana-plugin.md).

