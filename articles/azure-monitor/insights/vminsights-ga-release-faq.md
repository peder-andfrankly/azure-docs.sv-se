---
title: Vanliga frågor och svar om Azure Monitor for VMs (GA) | Microsoft Docs
description: Azure Monitor for VMs är en lösning i Azure som kombinerar hälso-och prestanda övervakning av operativ systemet på den virtuella Azure-datorn samt identifierar program komponenter och beroenden automatiskt med andra resurser och mappar kommunikationen mellan objekten. I den här artikeln besvaras vanliga frågor om GA-versionen.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/05/2019
ms.openlocfilehash: ec0580d3360a56fc6b779b8c0a69eb2a09b007db
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928929"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Monitor for VMs allmänt tillgängliga (GA)

Vanliga frågor och svar om allmän tillgänglighet omfattar ändringar som sker i Azure Monitor for VMs när vi förbereder för vår GA-version. 

## <a name="updates-for-azure-monitor-for-vms"></a>Uppdateringar för Azure Monitor for VMs

Vi planerar att lansera en ny version av Azure Monitor for VMs i januari 2020. Kunder som aktiverar Azure Monitors för virtuella datorer efter den här versionen får den nya versionen automatiskt, men befintliga kunder som redan använder Azure Monitor for VMs uppmanas att uppgradera. Dessa vanliga frågor och svar och vår dokumentation ger vägledning för att utföra en uppgradering i stor skala om du har stora distributioner över flera arbets ytor.

Med den här uppgraderingen lagras Azure Monitor for VMs prestanda data i samma `InsightsMetrics`-tabell som [Azure Monitor för behållare](container-insights-overview.md)och gör det enklare för dig att fråga de två data uppsättningarna. Du kan också lagra Mer Diverse data uppsättningar som vi inte kunde lagra i tabellen som användes tidigare. Och våra prestandavyer kommer att uppdateras så att den nya tabellen används.

Vi flyttar till nya data typer för våra anslutnings data uppsättningar. Den här ändringen sker i december 2019 och meddelas i en Azure Update-blogg. Data som finns lagrade i `ServiceMapComputer_CL` och `ServiceMapProcess_CL`, som är anpassade logg tabeller, flyttas till dedikerade data typer som heter `VMComputer` och `VMProcess`. Genom att flytta till dedikerade data typer får de prioritet för data inmatning och tabell schemat är standardiserat i alla kunder.

Vi inser att vi ber befintliga kunder att uppgradera orsakar avbrott i sitt arbets flöde, vilket är anledningen till att vi har valt att göra detta nu i en offentlig för hands version i stället för senare efter GA.

## <a name="what-is-changing"></a>Vad ändras?

När du har slutfört onboarding-processen för Azure Monitor for VMs aktiverar du Tjänstkarta-lösningen på den arbets yta som du har valt för att lagra dina övervaknings data och konfigurerar sedan prestanda räknare för de data som vi samlar in från dina virtuella datorer. Vi kommer att lansera en ny lösning med namnet **VMInsights**, som innehåller ytterligare funktioner för data insamling tillsammans med en ny plats för att lagra dessa data i din Log Analytics-arbetsyta.

Den nuvarande processen med att använda prestanda räknare i Log Analytics-arbetsytan skickar data till `Perf`s tabellen. Den här nya lösningen skickar data till en tabell med namnet `InsightsMetrics` som också används av Azure Monitor för behållare. Det här tabell schemat gör att vi kan lagra ytterligare mått och tjänst data uppsättningar som inte är kompatibla med tabell formatet perf.

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Vad ska jag göra med prestanda räknarna i min arbets yta om jag installerar VMInsights-lösningen?

Den aktuella metoden för att aktivera Azure Monitor for VMs använder prestanda räknare i din arbets yta. Den nya metoden lagrar dessa data i en ny tabell med namnet `InsightsMetrics`.

När vi uppdaterar vårt användar gränssnitt för att använda data i `InsightsMetrics`s tabellen, kommer vi att uppdatera vår dokumentation och kommunicera det via flera kanaler, inklusive att visa en banderoll i Azure Portal. Vid det här skedet kan du välja att inaktivera dessa [prestanda räknare](vminsights-enable-overview.md#performance-counters-enabled) i arbets ytan om du inte längre behöver använda dem. 

>[!NOTE]
>Om du har varnings regler som refererar till dessa räknare i `Perf` tabellen, måste du uppdatera dem för att referera till nya data som lagras i tabellen `InsightsMetrics`. Se vår dokumentation för exempel på logg frågor som du kan använda för att referera till den här tabellen.
>

Om du bestämmer dig för att hålla prestanda räknarna aktiverade debiteras du för de data som matas in och lagras i `Perf` tabell baserat på [Log Analytics prissättning [(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Hur påverkar den här ändringen mina varnings regler?

Om du har skapat [logg aviseringar](../platform/alerts-unified-log.md) som frågar `Perf`-tabellens mål prestanda räknare som har Aktiver ATS i arbets ytan, bör du uppdatera reglerna så att de refererar till `InsightsMetrics` tabellen i stället. Den här vägledningen gäller även för alla logg Sök regler som använder `ServiceMapComputer_CL` och `ServiceMapProcess_CL`, eftersom dessa data uppsättningar flyttas till `VMComputer` och `VMProcess` tabeller.

Vi kommer att uppdatera dessa vanliga frågor och svar och vår dokumentation för att inkludera exempel på loggs öknings varningar för de data uppsättningar som vi samlar in.

## <a name="how-will-this-affect-my-bill"></a>Hur kommer detta att påverka min faktura?

Faktureringen baseras fortfarande på inmatade data och behålls i Log Analytics arbets ytan.

Prestanda data för dator nivå som vi samlar in är desamma, är av samma storlek som de data som vi lagrade i `Perf` tabellen och kostar ungefär samma belopp.

## <a name="what-if-i-only-want-to-use-service-map"></a>Vad händer om jag bara vill använda Tjänstkarta?

Det är bra. Du kommer att se prompter i Azure Portal när du visar Azure Monitor for VMs om den kommande uppdateringen. När du har lanserat får du en fråga om att du ska uppdatera till den nya versionen. Om du bara vill använda [Maps](vminsights-maps.md) -funktionen kan du välja att inte uppgradera och fortsätta använda Maps-funktionen i Azure Monitor for VMs och tjänstkarta-lösningen som nås från din arbets yta eller instrument panels panel.

Om du väljer att aktivera prestanda räknarna manuellt i arbets ytan kan du se data i några av våra prestanda diagram som visas från Azure Monitor. När den nya lösningen släpps uppdaterar vi våra prestanda diagram för att fråga data som lagras i `InsightsMetrics` tabellen. Om du vill se data från tabellen i dessa diagram måste du uppgradera till den nya versionen av Azure Monitor for VMs.

Ändringarna för att flytta data från `ServiceMapComputer_CL` och `ServiceMapProcess_CL` påverkar både Tjänstkarta och Azure Monitor for VMs, så du måste ändå planera för den här uppdateringen.

Om du väljer att inte uppgradera till **VMInsights** -lösningen kommer vi fortsätta att tillhandahålla äldre versioner av arbets böckerna för prestanda som refererar till data i `Perf`s tabellen.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Kommer Tjänstkarta data uppsättningar också att lagras i InsightsMetrics?

Data uppsättningarna kommer inte att dupliceras om du använder båda lösningarna. Båda erbjudandena delar de data uppsättningar som ska lagras i `VMComputer` (tidigare ServiceMapComputer_CL), `VMProcess` (tidigare ServiceMapProcess_CL), `VMConnection`och `VMBoundPort` tabeller för att lagra de kart data uppsättningar som vi samlar in.  

`InsightsMetrics`s tabellen lagrar data uppsättningar för virtuella datorer, processer och tjänster som vi samlar in och kommer bara att fyllas i om du använder Azure Monitor for VMs och lösningen för VM-insikter. Tjänstkarta lösning samlar inte in eller lagrar data i `InsightsMetrics`s tabellen.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Får jag dubbla avgifter om jag har Tjänstkarta-och VMInsights-lösningar i min arbets yta?

Nej, de två lösningarna delar mappnings data uppsättningar som vi lagrar i `VMComputer` (tidigare ServiceMapComputer_CL), `VMProcess` (tidigare ServiceMapProcess_CL), `VMConnection`och `VMBoundPort`. Du debiteras inte för dessa data om du har båda lösningarna på arbets ytan.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Om jag tar bort antingen Tjänstkarta-eller VMInsights-lösningen tas mina data bort?

Nej, de två lösningarna delar mappnings data uppsättningar som vi lagrar i `VMComputer` (tidigare ServiceMapComputer_CL), `VMProcess` (tidigare ServiceMapProcess_CL), `VMConnection`och `VMBoundPort`. Om du tar bort en av lösningarna märker dessa data uppsättningar att det fortfarande finns en lösning på plats som använder data och den finns kvar i arbets ytan Log Analytics. Du måste ta bort båda lösningarna från din arbets yta för att data ska kunna tas bort från den.

## <a name="when-will-this-update-be-released"></a>När frigörs den här uppdateringen?

Vi förväntar dig att lansera uppdateringen för Azure Monitor for VMs i början januari 2020. Som vi får närmare lanserings datumet i januari kommer vi att publicera uppdateringar här och presentera meddelanden i Azure Portal när du öppnar Azure Monitor.

## <a name="health-feature-is-in-limited-public-preview"></a>Hälso funktionen är i begränsad offentlig för hands version

Vi har fått mycket bra feedback från kunder om vår funktions uppsättning för VM-hälsa. Intresset för den här funktionen är stort och många ser fram emot möjligheten att övervaka arbetsflöden. Vi planerar att göra ett antal ändringar för att utöka funktionerna och integrera den feedback vi fått. 

Vi har flyttat den här funktionen till en **begränsad offentlig för hands version**för att minimera effekten av dessa ändringar för nya kunder. Den här uppdateringen skedde i oktober 2019.

Vi planerar att starta om denna hälso funktion i 2020 efter Azure Monitor for VMs är i GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hur kommer befintliga kunder att få åtkomst till hälso funktionen?

Befintliga kunder som använder hälso funktionen fortsätter att ha åtkomst till den, men den erbjuds inte till nya kunder.  

För att få åtkomst till funktionen kan du lägga till följande funktions flagga `feature.vmhealth=true` till Azure Portal URL- [https://portal.azure.com](https://portal.azure.com). Exempel `https://portal.azure.com/?feature.vmhealth=true`.

Du kan också använda den här korta URL: en, som ställer in funktions flaggan automatiskt: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Som en befintlig kund kan du fortsätta att använda hälso funktionen på virtuella datorer som är anslutna till en befintlig installation av arbets ytan med hälso funktionen.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Jag använder VM-hälsa nu med en miljö och vill distribuera den till en ny

Om du är en befintlig kund som använder hälso funktionen och vill använda den för en ny lansering kan du kontakta oss på vminsights@microsoft.com för att begära instruktioner.

## <a name="next-steps"></a>Nästa steg

För att förstå de krav och metoder som hjälper dig att övervaka dina virtuella datorer, granska [distribuera Azure Monitor för virtuella datorer](vminsights-enable-overview.md).
