---
title: Översikt över aviseringar och meddelande övervakning i Azure
description: Översikt över aviseringar i Azure. Aviseringar, klassiska aviseringar och aviserings gränssnittet.
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 01/28/2018
ms.openlocfilehash: b655181f41aeda71364edd061b7c81db23e59990
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951147"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Översikt över aviseringar i Microsoft Azure 

I den här artikeln beskrivs vilka aviseringar som är, deras fördelar och hur du kommer igång med dem.  




## <a name="what-are-alerts-in-microsoft-azure"></a>Vad är aviseringar i Microsoft Azure?
Aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. 

Den här artikeln beskriver den enhetliga aviserings upplevelsen i Azure Monitor, som innehåller aviseringar som tidigare hanterades av Log Analytics och Application Insights. [Föregående aviserings upplevelse](alerts-classic.overview.md) och aviserings typer kallas för *klassiska aviseringar*. Du kan visa den här äldre upplevelsen och äldre aviserings typ genom att välja **Visa klassiska aviseringar** överst på aviserings sidan. 

## <a name="overview"></a>Översikt

Diagrammet nedan visar flödet av aviseringar. 

![Diagram över varnings flöde](media/alerts-overview/Azure-Monitor-Alerts.svg)

Varnings regler separeras från aviseringar och åtgärder som vidtas när en avisering utlöses. Varnings regeln samlar in mål och kriterier för aviseringar. Varnings regeln kan vara i ett aktiverat eller inaktiverat tillstånd. Aviseringar utlöses bara när de är aktiverade. 

Följande är nyckelattribut för en varnings regel:

**Mål resurs**: definierar omfattning och signaler som är tillgängliga för aviseringar. Ett mål kan vara vilken Azure-resurs som helst. Exempel mål: en virtuell dator, ett lagrings konto, en skalnings uppsättning för virtuella datorer, en Log Analytics-arbetsyta eller en Application Insights-resurs. För vissa resurser (t. ex. virtuella datorer) kan du ange flera resurser som mål för varnings regeln.

**Signal**: genereras av mål resursen. Signaler kan vara av följande typer: mått, aktivitets logg, Application Insights och logg.

**Kriterier**: en kombination av signal och logik som tillämpas på en mål resurs. Exempel: 
   - Procent processor > 70%
   - Server svars tid > 4 MS 
   - Antal resultat för en logg fråga > 100

**Aviserings namn**: ett namn för aviserings regeln som kon figurer ATS av användaren.

**Aviserings Beskrivning**: en beskrivning av den aviserings regel som kon figurer ATS av användaren.

**Allvarlighets**grad: aviseringens allvarlighets grad efter att de kriterier som angetts i varnings regeln har uppfyllts. Allvarlighets graden kan vara mellan 0 och 4.
   - Allvarlighets grad 0 = kritisk
   - Allvarlighets grad 1 = fel
   - Allvarlighets grad 2 = varning
   - Allvarlighets grad 3 = information
   - Allvarlighets grad 4 = utförlig 

**Åtgärd**: en speciell åtgärd som vidtas när aviseringen utlöses. Mer information finns i [Åtgärds grupper](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Vad du kan Avisera om

Du kan varna för mått och loggar enligt beskrivningen i [övervaka data källor](../../azure-monitor/platform/data-sources-reference.md). Dessa inkludera, men är inte begränsade till:
- Måttvärden
- Loggsökningsfrågor
- Aktivitetslogghändelser
- Hälsotillståndet för den underliggande Azure-plattformen
- Test för webbplatsens tillgänglighet

Tidigare Azure Monitor Mät värden, Application Insights, Log Analytics och Service Health hade separata aviserings funktioner. Med tiden kan Azure förbättra och kombinera både användar gränssnittet och olika metoder för aviseringar. Denna konsolidering pågår fortfarande. Därför finns det fortfarande vissa aviserings funktioner som ännu inte finns i det nya varnings systemet.  

| **Övervaka källa** | **Signal typ**  | **Beskrivning** | 
|-------------|----------------|-------------|
| Service Health | Aktivitetslogg  | Stöds ej. Se [skapa aktivitets logg aviseringar för tjänst meddelanden](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Test av webb tillgänglighet | Stöds ej. Se [webb test aviseringar](../../azure-monitor/app/monitor-web-app-availability.md). Tillgängligt för alla webbplatser som är instrumenterade att skicka data till Application Insights. Få ett meddelande när tillgänglighet eller svars tider för en webbplats är under förväntningarna. |

## <a name="manage-alerts"></a>Hantera aviseringar
Du kan ange status för en avisering för att ange var den finns i lösnings processen. När de kriterier som anges i varnings regeln uppfylls skapas eller utlöses en avisering, och den har statusen *ny*. Du kan ändra statusen när du bekräftar en avisering och när du stänger den. Alla status ändringar lagras i historiken för aviseringen.

Följande aviserings tillstånd stöds.

| Status | Beskrivning |
|:---|:---|
| Nytt | Problemet har precis identifierats och har ännu inte granskats. |
| Godkänt | En administratör har granskat aviseringen och börjat arbeta med den. |
| Stängd | Problemet har åtgärd ATS. När en avisering har stängts kan du öppna den igen genom att ändra den till ett annat tillstånd. |

*Aviserings tillståndet* är annorlunda och oberoende av *övervaknings villkoret*. Aviserings tillstånd anges av användaren. Övervaknings villkoret anges av systemet. När en varning utlöses anges övervaknings villkoret för aviseringen till *utlöst*. När det underliggande villkoret som orsakade aviseringen om att brand figurer rensas, anges övervaknings villkoret som *löst*. Aviserings statusen har inte ändrats förrän användaren ändrar det. Lär dig [hur du ändrar status för dina aviseringar och smarta grupper](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Smarta grupper 

Smarta grupper är agg regeringar för aviseringar baserade på Machine Learning-algoritmer som kan hjälpa till att minska aviserings bruset och hjälp vid fel sökning. [Lär dig mer om smarta grupper](https://aka.ms/smart-groups) och [hur du hanterar dina smarta grupper](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Aviserings upplevelse 
Sidan standard varningar innehåller en sammanfattning av de aviseringar som skapas inom ett visst tidsintervall. Den visar den totala aviseringen för varje allvarlighets grad, med kolumner som anger det totala antalet aviseringar i varje tillstånd för varje allvarlighets grad. Välj någon av allvarlighets graderna för att öppna sidan [alla aviseringar](#all-alerts-page) som filtrerats efter allvarlighets grad.

Du kan också [program mässigt räkna upp de aviserings instanser som genereras i dina prenumerationer med hjälp av REST-API: er](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Du kan endast få åtkomst till aviseringar som genererats under de senaste 30 dagarna.

Den visar eller spårar inte klassiska aviseringar. Du kan ändra prenumerationer eller filter parametrar för att uppdatera sidan. 

![Skärm bild av sidan aviseringar](media/alerts-overview/alerts-page.png)

Du kan filtrera den här vyn genom att välja värden i de nedrullningsbara menyerna längst upp på sidan.

| Kolumn | Beskrivning |
|:---|:---|
| Prenumeration | Välj de Azure-prenumerationer som du vill visa aviseringar för. Alternativt kan du välja att välja alla dina prenumerationer. Endast aviseringar som du har åtkomst till i de valda prenumerationerna ingår i vyn. |
| Resursgrupp | Välj en enskild resurs grupp. Endast aviseringar med mål i den valda resurs gruppen ingår i vyn. |
| Tidsintervall | Endast aviseringar som har utlösts inom det valda tidsintervallet ingår i vyn. De värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Välj följande värden överst på aviserings sidan för att öppna en annan sida:

| Värde | Beskrivning |
|:---|:---|
| Totalt antal aviseringar | Det totala antalet aviseringar som matchar de valda kriterierna. Välj det här värdet för att öppna vyn alla aviseringar utan filter. |
| Smarta grupper | Det totala antalet smarta grupper som har skapats från de aviseringar som matchar de valda kriterierna. Välj det här värdet för att öppna listan Smart grupper i vyn alla aviseringar.
| Totalt antal aviseringsregler | Det totala antalet aviserings regler i den valda prenumerationen och resurs gruppen. Välj det här värdet för att öppna regel vyn filtrerad på den valda prenumerationen och resurs gruppen.


## <a name="manage-alert-rules"></a>Hantera aviseringsregler
Om du vill visa sidan **regler** väljer du **Hantera aviserings regler**. Sidan regler är en plats där du kan hantera alla aviserings regler i dina Azure-prenumerationer. Den visar alla varnings regler och kan sorteras baserat på mål resurser, resurs grupper, regel namn eller status. Du kan också redigera, aktivera eller inaktivera aviserings regler från den här sidan.  

 ![Skärm bild av sidan regler](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Skapa en varningsregel
Du kan skapa aviseringar på ett konsekvent sätt, oavsett övervaknings tjänst eller signal typ. Alla utlösta aviseringar och relaterad information finns på en enda sida.
 
Så här skapar du en ny aviserings regel:
1. Välj _målet_ för aviseringen.
1. Välj _signalen_ från tillgängliga signaler för målet.
1. Ange vilken _logik_ som ska användas för data från signalen.
 
Den här förenklade redigerings processen kräver inte längre att du vet vilken övervaknings källa eller vilka signaler som stöds innan du väljer en Azure-resurs. Listan över tillgängliga signaler filtreras automatiskt baserat på den mål resurs du väljer. Baserat på det målet vägleds du också genom att definiera logiken i varnings regeln automatiskt.  

Du kan lära dig mer om hur du skapar aviserings regler i [skapa, Visa och hantera aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Aviseringar är tillgängliga i flera Azure Monitoring Services. Information om hur och när du ska använda var och en av dessa tjänster finns i [övervaka Azure-program och-resurser](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Sidan alla aviseringar 
Om du vill se sidan **alla aviseringar** väljer du **Totalt antal aviseringar**. Här kan du Visa en lista över aviseringar som har skapats inom den valda tiden. Du kan visa en lista över enskilda aviseringar eller en lista över de smarta grupper som innehåller aviseringarna. Välj banderollen överst på sidan för att växla mellan vyer.

![Skärm bild av sidan alla aviseringar](media/alerts-overview/all-alerts-page.png)

Du kan filtrera vyn genom att välja följande värden i list menyerna längst upp på sidan:

| Kolumn | Beskrivning |
|:---|:---|
| Prenumeration | Välj de Azure-prenumerationer som du vill visa aviseringar för. Alternativt kan du välja att välja alla dina prenumerationer. Endast aviseringar som du har åtkomst till i de valda prenumerationerna ingår i vyn. |
| Resursgrupp | Välj en enskild resurs grupp. Endast aviseringar med mål i den valda resurs gruppen ingår i vyn. |
| Resurstyp | Välj en eller flera resurs typer. Endast aviseringar med mål av den valda typen tas med i vyn. Den här kolumnen är bara tillgänglig efter att en resurs grupp har angetts. |
| Resurs | Välj en resurs. Endast aviseringar med den resursen som mål ingår i vyn. Den här kolumnen är bara tillgänglig efter att en resurs typ har angetts. |
| Allvarsgrad | Välj en allvarlighets grad för aviseringar eller Välj **alla** om du vill inkludera aviseringar för alla allvarlighets grader. |
| Övervakningsvillkor | Välj ett övervaknings villkor eller Välj **alla** om du vill inkludera aviseringar för alla villkor. |
| Aviseringstillstånd | Välj ett aviserings tillstånd eller Välj **alla** om du vill inkludera aviseringar för alla tillstånd. |
| Övervaka tjänst | Välj en tjänst eller Välj **alla** om du vill inkludera alla tjänster. Endast aviseringar som skapats av regler som använder tjänsten som mål ingår. |
| Tidsintervall | Endast aviseringar som har utlösts inom det valda tidsintervallet ingår i vyn. De värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Välj **kolumner** överst på sidan för att välja vilka kolumner som ska visas. 

## <a name="alert-details-page"></a>Sidan aviserings information
När du väljer en avisering innehåller den här sidan information om aviseringen och du kan ändra dess tillstånd.

![Skärm bild av sidan med aviserings information](media/alerts-overview/alert-detail2.png)

Sidan aviserings information innehåller följande avsnitt:

| Section | Beskrivning |
|:---|:---|
| Sammanfattning | Visar egenskaper och annan viktig information om aviseringen. |
| Historik | Visar en lista över varje åtgärd som vidtas av aviseringen och eventuella ändringar som gjorts i aviseringen. För närvarande begränsad till tillstånds ändringar. |
| Diagnostik | Information om den smarta grupp som aviseringen ingår i. Antalet *aviseringar* avser antalet aviseringar som ingår i den smarta gruppen. Innehåller andra aviseringar i samma smarta grupp som skapades under de senaste 30 dagarna, oavsett tids filtret på sidan aviserings lista. Välj en avisering om du vill visa information om den. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Rollbaserad åtkomst kontroll (RBAC) för dina aviserings instanser

Användningen och hanteringen av varnings instanser kräver att användaren har de inbyggda RBAC-rollerna för övervakning av [deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) eller [övervaknings läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader). De här rollerna stöds på alla Azure Resource Managers omfång, från prenumerations nivån till detaljerade tilldelningar på en resurs nivå. Om en användare till exempel bara har övervaknings deltagar åtkomst för den virtuella datorn `ContosoVM1`, kan den användaren använda och hantera enbart aviseringar som genereras på `ContosoVM1`.

## <a name="manage-your-alert-instances-programmatically"></a>Hantera aviserings instanser program mässigt

Du kanske vill fråga program mässigt efter aviseringar som har genererats mot din prenumeration. Detta kan vara att skapa anpassade vyer utanför Azure Portal eller analysera aviseringar för att identifiera mönster och trender.

Du kan fråga efter aviseringar som har genererats med dina prenumerationer antingen med hjälp av [Aviseringshantering REST API](https://aka.ms/alert-management-api) eller med hjälp av [Azure Resource Graph-REST API för aviseringar](https://docs.microsoft.com/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)).

I [Azure Resource Graph REST API för aviseringar](https://docs.microsoft.com/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)) kan du fråga efter aviserings instanser i stor skala. Detta rekommenderas när du måste hantera aviseringar som genererats över flera prenumerationer. 

Följande exempel förfrågan till API: et returnerar antalet aviseringar inom en prenumeration:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()",
  "options": {
            "dataset":"alerts"
  }
}
```
Du kan fråga aviseringarna om de [viktigaste](alerts-common-schema-definitions.md#essentials) fälten.

Använd [Aviseringshantering REST API](https://aka.ms/alert-management-api) för att få mer information om vissa aviseringar, inklusive deras [aviserings kontext](alerts-common-schema-definitions.md#alert-context) fält.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om smarta grupper](https://aka.ms/smart-groups)
- [Lär dig mer om åtgärds grupper](../../azure-monitor/platform/action-groups.md)
- [Hantera dina varnings instanser i Azure](https://aka.ms/managing-alert-instances)
- [Hantera smarta grupper](https://aka.ms/managing-smart-groups)
- [Läs mer om priser för Azure-aviseringar](https://azure.microsoft.com/pricing/details/monitor/)






