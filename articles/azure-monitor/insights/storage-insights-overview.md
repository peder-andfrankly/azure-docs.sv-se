---
title: Övervaka Azure Storage tjänster med Azure Monitor för lagring (för hands version) | Microsoft Docs
description: Den här artikeln beskriver Azure Monitor för lagrings funktionen som ger lagrings administratörer en snabb förståelse för prestanda-och användnings problem med deras Azure Storage-konton.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/15/2019
ms.openlocfilehash: aaf7d1a38d4b809b904b6c607a4cfc23efd4dde5
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286385"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>Övervaka lagrings tjänsten med Azure Monitor för lagring (för hands version)

Azure Monitor för lagring (för hands version) ger omfattande övervakning av dina Azure Storage-konton genom att leverera en enhetlig vy över dina Azure Storage tjänsters prestanda, kapacitet och tillgänglighet. Du kan se lagrings kapacitet och prestanda på två sätt, Visa direkt från ett lagrings konto eller vy från Azure Monitor för att se flera grupper av lagrings konton. 

Den här artikeln hjälper dig att förstå upplevelsen Azure Monitor för lagring (för hands version) som ger dig möjlighet att härleda användbara kunskaper om hälsa och prestanda för lagrings konton i stor skala, med möjlighet att fokusera på hotspots och diagnostisera svars tid, begränsning, och tillgänglighets problem.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>Introduktion till Azure Monitor för lagring (för hands version)

Innan du simhopp i upplevelsen bör du förstå hur den visar och visualiserar information. Oavsett om du väljer lagrings funktionen direkt från ett lagrings konto eller från Azure Monitor, ger Azure Monitor för lagring en konsekvent upplevelse. 

Kombinerat IT ger:

* **I skalnings perspektiv** visas en Snapshot-vy över deras tillgänglighet baserat på hälso tillståndet för lagrings tjänsten eller API-åtgärden, användning som visar det totala antalet begär Anden som lagrings tjänsten tar emot och svars tiden visar den genomsnittliga tiden som lagrings tjänsten eller API-åtgärds typen tar att bearbeta begär Anden. Du kan också Visa kapaciteten efter BLOB, fil, tabell och kö.

* **Detaljgranska analys** av ett visst lagrings konto för att hjälpa till att diagnostisera problem eller utföra detaljerad analys efter kategori tillgänglighet, prestanda, fel och kapacitet. Om du väljer något av dessa alternativ visas en djupgående vy över mått.  

* **Anpassningsbara** där du kan ändra vilka mått du vill se, ändra eller ange tröskelvärden som överensstämmer med dina gränser och Spara som en egen arbets bok. Diagram i arbets boken kan fästas på Azure-instrumentpanelen.  

Den här funktionen kräver inte att du aktiverar eller konfigurerar något, lagrings måtten från dina lagrings konton samlas in som standard. Om du inte känner till mått som är tillgängliga på Azure Storage kan du Visa beskrivningen och definitionen i Azure Storage mått genom att granska [Azure Storage-mått](../../storage/common/storage-metrics-in-azure-monitor.md).

>[!NOTE]
>Det kostar inget att få åtkomst till den här funktionen och du debiteras bara för Azure Monitor viktiga funktioner som du konfigurerar eller aktiverar, enligt beskrivningen på sidan [pris information för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) .

>[!NOTE]
>Azure Monitor för lagring stöder inte [generella v1-konton](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts).
>

## <a name="view-from-azure-monitor"></a>Visa från Azure Monitor

Från Azure Monitor kan du Visa information om transaktion, svars tid och kapacitet från flera lagrings konton i din prenumeration och hjälpa till att identifiera prestanda, kapacitets problem och fel.

Utför följande steg för att visa användning och tillgänglighet för dina lagrings konton för alla dina prenumerationer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **övervaka** i rutan till vänster i Azure Portal och välj **lagrings konton (förhands granskning)** under avsnittet **insikter** .

    ![Vy över flera lagrings konton](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Översikt över arbets bok

I tabellen **Översikt** för den valda prenumerationen visar tabellen interaktiva lagrings mått och tjänst tillgänglighets tillstånd för upp till 10 lagrings konton grupperade i prenumerationen. Du kan filtrera resultaten baserat på de alternativ du väljer i följande List rutor:

* **Prenumerationer – endast** prenumerationer som har lagrings konton visas.  

* **Lagrings konton** – som standard väljs 10 lagrings konton i förväg. Om du väljer alla eller flera lagrings konton i omfångs väljaren returneras upp till 200 lagrings konton. Om du till exempel har totalt 573 lagrings konton över tre prenumerationer som du har valt visas bara 200-konton. 

* **Tidsintervall** – som standard visar de senaste 4 timmarna med information baserat på motsvarande val som gjorts.

Panelen räknare under List rutorna slår upp det totala antalet lagrings konton i prenumerationen och visar hur många av summan som väljs. Det finns en villkorsstyrd färg kodning eller termiska kartor för kolumner i arbets boken som rapporterar transaktions mått eller fel. Den djupaste färgen har det högsta värdet och en ljusare färg baseras på de lägsta värdena. För de felbaserade kolumnerna är värdet i rött och för måttbaserade kolumner är värdet i blått.

Välj ett värde i kolumn **tillgänglighet**, **E2E-latens**, **Server svars tid**och **transaktions fel typ/fel** för att dirigera dig till en rapport som är anpassad till den angivna typen av lagrings mått som matchar den valda kolumnen för det lagrings kontot. Mer information om arbets böckerna för varje kategori finns i avsnittet [detaljerade lagrings arbets böcker](#detailed-storage-workbooks) nedan. 

>[!NOTE]
>Information om vilka fel som kan visas i rapporten finns i schema för [svars typ](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) och Sök efter svars typer som **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError**. Beroende på vilka lagrings konton som valts, om det finns fler än tre typer av fel som rapporter ATS, visas **alla andra fel i kategorin.**

Standard tröskelvärdet för **tillgänglighet** är:

* Varning-99%
* Kritisk – 90%

Om du vill ange ett tröskelvärde för tillgänglighet baserat på resultatet av din observation eller dina krav granskar [du ändra tröskelvärdet för tillgänglighet](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Arbets bok för kapacitet

Välj **kapacitet** överst på sidan och **kapacitets** arbets boken öppnas. Det visar den totala mängden lagrings utrymme som används för kontot och kapaciteten som används av varje data tjänst i kontot för att identifiera över och under utnyttjat lagrings utrymme.

![Arbets bok för kapacitet för flera lagrings konton](./media/storage-insights-overview/storage-account-capacity-02.png) 

Det finns en villkorsstyrd färg kodning eller termiska kartor för kolumner i arbets boken som rapporterar kapacitets mått med ett blått värde. Den djupaste färgen har det högsta värdet och en ljusare färg baseras på de lägsta värdena.

När du väljer ett värde under någon av kolumnerna i arbets boken kan du öka detalj nivån för **kapacitets** arbets boken för lagrings kontot. Mer information om detalj nivå rapporten beskrivs i avsnittet [detaljerade lagrings arbets böcker](#detailed-storage-workbooks) nedan. 

## <a name="view-from-a-storage-account"></a>Visa från ett lagrings konto

Få åtkomst till Azure Monitor for VMs direkt från ett lagrings konto:

1. I Azure Portal väljer du lagrings konton.

2. Välj ett lagrings konto i listan. I avsnittet övervakning väljer du insikter (för hands version).

    ![Sidan vald översikt över lagrings konto](./media/storage-insights-overview/storage-account-direct-overview-01.png)

I arbets boken **Översikt** för lagrings kontot visar det flera lagrings prestanda mått som hjälper dig att snabbt utvärdera:

* Hälso tillståndet för lagrings tjänsten för att omedelbart se om ett problem utanför kontrollen påverkar lagrings tjänsten i den region som den distribueras till, som anges under **sammanfattnings** kolumnen.

* Interaktiva prestanda diagram som visar de viktigaste detaljerna som rör lagrings kapacitet, tillgänglighet, transaktioner och svars tider.  

* Panelerna för mått-och status paneler markerar tjänstens tillgänglighet, totalt antal transaktioner till lagrings tjänsten, E2E-svars tid och Server svars tid.

Om du markerar någon av knapparna för att **Miss lyckas**, **prestanda**, **tillgänglighet**och **kapacitet** öppnas respektive arbets bok. 

![Sidan vald översikt över lagrings konto](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Detaljerade lagrings arbets böcker

Oavsett om du har valt ett värde i kolumnernas **tillgänglighet**, **E2E svars tid**, **Server svars tid**och **transaktions fel typ/fel** från arbets boken för flera lagrings konton, eller om du väljer någon av knapparna för **fel**, **prestanda**, **tillgänglighet**och **kapacitet** i **översikts** **arbets boken från** ett särskilt lagrings konto, ger vardera en uppsättning interaktiv lagrings information som är anpassad till den kategorin.  

* **Tillgänglighet** öppnar **tillgänglighets** arbets boken. Den visar det aktuella hälso tillståndet för Azure Storage tjänst, en tabell som visar det tillgängliga hälso tillståndet för varje objekt kategoriserade efter data tjänst som definierats i lagrings kontot med en trend linje som representerar tidsintervallet som valts och ett tillgänglighets trend diagram för varje data tjänst i kontot.  

    ![Exempel på tillgänglighets rapport](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E svars tid** och **Server svars** tid öppnar arbets boken **prestanda** . Den innehåller en sammanfattnings status panel som visar E2E svars tid och Server svars tid, ett prestanda diagram över E2E jämfört med Server svars tid och en tabell över svars tids fördröjningar för lyckade anrop via API Kategoriserad av data tjänst som definierats i lagrings kontot

    ![Exempel på prestanda rapport](./media/storage-insights-overview/storage-account-performance-01.png)

* Genom att välja någon av de fel kategorier som visas i rutnätet öppnar du arbets boken **fel** . Rapporten visar mått paneler för alla andra klients IDE fel utom beskrivna och lyckade förfrågningar, fel i klient begränsning, ett prestanda diagram för transaktions **svars typen** dimensions mått som är särskilt för attributet ClientOtherError och två tabeller – **transaktioner efter API-namn** och **transaktioner efter svars typ**.

   ![Exempel på rapport om haveri](./media/storage-insights-overview/storage-account-failures-01.png)

* **Kapacitet** öppnar arbets boken för **kapacitet** . Den visar den totala mängden lagrings utrymme som används för varje lagrings data objekt i kontot i panelerna och i diagrammet samt hur många data objekt som lagras i kontot.  

    ![Sidan för vald lagrings konto kapacitet](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Fäst och exportera

Du kan fästa något av mått avsnitten på en Azure-instrumentpanel genom att välja kartnål-ikonen längst upp till höger i avsnittet.

![Mått avsnitt fäst vid instrument panelen exempel](./media/storage-insights-overview/workbook-pin-example.png)

**Översikten över** flera prenumerationer och lagrings konton har stöd för att exportera resultatet i Excel-format genom att välja ikonen nedåtpil till höger om ikonen kartnål.

![Exempel på export av arbets bokens rutnäts resultat](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>Anpassa Azure Monitor för lagring (för hands version)

I det här avsnittet beskrivs vanliga scenarier för att redigera arbets boken för att anpassa stöd för dina data analys behov:

* Omfånget som arbets boken ska alltid välja en viss prenumeration eller ett eller flera lagrings konton
* Ändra mått i rutnätet
* Ändra tröskelvärdet för tillgänglighet
* Ändra färg åter givningen

Anpassningarna sparas i en anpassad arbets bok för att förhindra att standard konfigurationen skrivs över i vår publicerade arbets bok. Arbets böcker sparas i en resurs grupp, antingen i avsnittet **Mina rapporter** som är privat för dig eller i avsnittet **delade rapporter** som är tillgängliga för alla som har åtkomst till resurs gruppen. När du har sparat den anpassade arbets boken måste du gå till arbets boks galleriet för att starta den.

![Starta arbets boks galleriet från kommando fältet](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Ange en prenumeration eller ett lagrings konto

Du kan konfigurera en **Översikt över** flera prenumerationer och lagrings konton för att omfånget till en viss **prenumeration eller ett** lagrings konto på varje körning genom att utföra följande steg.

1. Välj **övervakare** från portalen och välj sedan **lagrings konton (förhands granskning)** i den vänstra rutan.

2. I **översikts** arbets boken väljer du **Redigera**i kommando fältet.

3. Välj i list rutan **prenumerationer** en eller flera prenumerationer som du vill att den ska vara standard för. Kom ihåg att arbets boken har stöd för att välja upp till totalt 10 prenumerationer.  

4. Välj i list rutan **lagrings konton** ett eller flera konton som du vill att det ska vara standard för. Kom ihåg att arbets boken har stöd för att välja upp till totalt 200 lagrings konton. 

5. Välj **Spara som** från kommando fältet för att spara en kopia av arbets boken med dina anpassningar och klicka sedan på **klar redigering** för att återgå till läsläge.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Ändra mått och färger i arbets boken

De färdiga arbets böckerna innehåller mått data och du har möjlighet att ändra eller ta bort en av visualiseringarna och anpassa till teamets speciella behov.

I vårt exempel arbetar vi med arbets boken med flera prenumerationer och lagrings konto kapacitet för att demonstrera hur du:

* Ta bort ett mått
* Ändra färg åter givning

Du kan utföra samma ändringar mot något av de förbyggda **felen**, **prestanda**, **tillgänglighet**och **kapacitets** arbets böcker.

1. Välj **övervakare** från portalen och välj sedan **lagrings konton (förhands granskning)** i den vänstra rutan.

2. Välj **kapacitet** för att växla till arbets boken kapacitet och i kommando fältet väljer du **Redigera** från kommando fältet.

    ![Välj Redigera för att ändra en arbets bok](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Klicka på **Redigera**bredvid avsnittet mått.

    ![Välj Redigera för att ändra arbets boks mått för kapacitet](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. Vi kommer att ta bort kolumnen **Använd kapacitets tids linje för kontot** så välj **kolumn inställningar** i mått rutnätet.

    ![Redigera kolumn inställningar](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. I fönstret **Redigera kolumn inställningar** väljer du under avsnittet **kolumner** **Microsoft. Storage/Storageaccounts-Capacity-UsedCapacity Timeline $ | Konto för Använd kapacitet tids linje $** och välj **dold**under List rutans **kolumn åter givning** .

6. Välj **Spara och Stäng** för att bekräfta ändringen.

Nu ska vi ändra färg temat för kapacitets måtten i rapporten för att använda grönt i stället för blått.

1. Välj **kolumn inställningar** i rutnätet mått.

2. I fönstret **Redigera kolumn inställningar** väljer du under avsnittet **kolumner** **Microsoft. Storage/storageaccounts-Capacity-UsedCapacity $ | Microsoft. Storage/storageaccounts/blobservices-Capacity-BlobCapacity $ | Microsoft. Storage/storageaccounts/FileServices-Capacity-FileCapacity $ | Microsoft. Storage/storageaccounts/queueservices-Capacity-QueueCapacity $ | Microsoft. Storage/storageaccounts/tableservices-Capacity-TableCapacity $** . Välj **grönt**under List rutans **färgpalett.**

3. Välj **Spara och Stäng** för att bekräfta ändringen.

4. Välj **Spara som** från kommando fältet för att spara en kopia av arbets boken med dina anpassningar och klicka sedan på **klar redigering** för att återgå till läsläge.  

### <a name="modify-the-availability-threshold"></a>Ändra tröskelvärdet för tillgänglighet

I det här exemplet arbetar vi med arbets boken lagrings konto kapacitet och demonstrerar hur du ändrar tröskelvärdet för tillgänglighet. Som standard konfigureras tillgänglighets verktyget för panelen och rutnäts procenten med ett minimi tröskelvärde på 90 och maximalt tröskelvärde på 99. Vi kommer att ändra det minsta tröskelvärdet för **tillgänglighet%** i rutnätet **tillgänglighet efter API-namn** till 85%, vilket innebär att hälso tillståndet ändras till kritiskt om tröskelvärdet är mindre än 85 procent. 

1. Välj **lagrings konton** från portalen och välj sedan ett lagrings konto i listan.

2. Välj **Insights (förhands granskning)** i det vänstra fönstret.

3. I arbets boken väljer du **tillgänglighet** för att växla till tillgänglighets arbets boken och väljer sedan **Redigera** från kommando fältet. 

4. Rulla ned till slutet av sidan och på den vänstra sidan bredvid rutnäts rutnätet **tillgänglighet** , Välj **Redigera**.

    ![Redigera tillgänglighet efter API-namn rutnäts inställningar](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Välj **kolumn inställningar** och sedan i fönstret **Redigera kolumn inställningar** , under avsnittet **kolumner** väljer du **tillgänglighet (%) (Tröskelvärden + formaterat)** .

6. Ändra värdet för **kritiskt** hälso tillstånd från **90** till **85** och klicka sedan på **Spara och Stäng**.

    ![Ändra tröskelvärdet för tillgänglighet för kritiskt tillstånd](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Välj **Spara som** från kommando fältet för att spara en kopia av arbets boken med dina anpassningar och klicka sedan på **klar redigering** för att återgå till läsläge.

## <a name="troubleshooting"></a>Felsökning

I det här avsnittet får du hjälp med diagnos och fel sökning av några vanliga problem som kan uppstå när du använder Azure Monitor för lagring (för hands version). Använd listan nedan för att hitta den information som är relevant för det aktuella problemet.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Lösa problem med prestanda, kapacitet eller tillgänglighet

Information om hur du felsöker eventuella lagrings problem som du identifierar med Azure Monitor för lagring (för hands version) finns i [fel söknings vägledningen](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)för Azure Storage.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Varför kan jag bara se 200-lagrings konton?

Antalet valda lagrings konton har en gräns på 200, oavsett hur många prenumerationer som har valts.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Vad händer när jag klickar på en nyligen fäst panel på instrument panelen?

* Om du klickar någonstans på panelen kommer du till fliken där panelen har fästs från. Om du till exempel fäster en graf i fliken "lagrings konto översikt" och sedan klickar på den panelen på instrument panelen öppnas den som standardvy, men om du fäster ett diagram från din egna sparade kopia öppnas vyn Sparad kopia.
* Filter ikonen längst upp till vänster i rubriken öppnar fliken "Konfigurera panel inställningar".
* Ellips-ikonen längst upp till höger ger dig alternativen "anpassa rubrik data", "anpassa", "uppdatera" och "ta bort från instrument panelen".

### <a name="what-happens-when-i-save-a-workbook"></a>Vad händer när jag sparar en arbets bok?

* När du sparar en arbets bok kan du skapa en ny kopia av arbets boken med dina ändringar och ändra rubriken. Om du sparar skrivs inte arbets boken över, den aktuella arbets boken är alltid standardvy.
* En **osparad** arbets bok är bara standardvyn.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Varför visas inte alla mina prenumerationer i portalen?

Portalen visar endast data för de valda prenumerationerna på Portal lansering. Om du vill ändra vilka prenumerationer som är markerade går du till det övre högra hörnet och klickar på antecknings boken med en filter ikon. Fliken Katalog + prenumerationer visas.

![Katalog + prenumeration](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Hur ändrar du färg och tröskel för tillgänglighet?

I avsnittet [ändra tröskeln för tillgänglighet](storage-insights-overview.md#modify-the-availability-threshold) finns detaljerade anvisningar om hur du ändrar färg och tröskelvärden för tillgänglighet.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Hur analyserar och felsöker du data som visas i Azure Monitor för lagring?

 Information om hur du analyserar och felsöker Azure Storage data som visas i Azure Monitor för lagring finns i [övervaka, diagnostisera och felsöka Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) artikel.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Varför visas inte alla typer av fel i mått?

För närvarande visas upp till tre olika typer av fel och resten av felen grupperas tillsammans i en enda Bucket. Den styrs med hjälp av splitByLimit och kan ändras. För att ändra den här egenskapen:

1. Klicka på Redigera arbets bok.
2. Gå till mått, klicka på Redigera och välj sedan **transaktioner, Summa** eller de mått som du vill redigera.

    ![Gå till mått och klicka på Redigera sedan på "transaktioner, summor"](./media/storage-insights-overview/fqa7.png)

1. Ändra sedan antalet delningar.

    ![Välj mått parametrar "](./media/storage-insights-overview/fqa7-2.png)

Om du vill se n olika typer av fel än att ange splitByLimit som n + 1, 1 extra för resten av felen.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Jag sparade min arbets bok på ett lagrings konto. Varför kan jag inte hitta det nu?

Varje arbets bok sparas i det lagrings konto som du sparade det i. Försök att hitta det lagrings konto som användaren sparade arbets boken i. Annars finns det inget sätt att hitta en speciell arbets bok utan att känna till resursen (lagrings kontot).

### <a name="what-is-time-range"></a>Vad är tidsintervallet?

Tidsintervallet visar data från en viss tidsram. Om tidsintervallet exempelvis är 24 timmar visar det data från de senaste 24 timmarna.

### <a name="what-is-time-granularity-time-grain"></a>Vad är tids kornig het (tids kornig het)?

Tids kornig het är tids skillnaden mellan två data punkter. Om tids kornigheten till exempel är en sekund som innebär att mått samlas in varje sekund.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Vad är tids kornigheten när vi fäster någon del av arbets böckerna på en instrument panel?

Standard tids kornig het är inställd på automatisk, den går för närvarande inte att ändra just nu.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Hur gör jag för att ändra tidsintervall/tidsintervall för arbets bokens steg på min instrument panel?

Som standard är TimeSpan/tidsintervallet på instrument panelen inställd på 24 timmar. om du vill ändra detta klickar du på ellipserna längst upp till höger. Välj **Anpassa panel data**, markera kryss rutan Åsidosätt inställningarna för instrument panelen på rubrik nivå och välj sedan ett TimeSpan med hjälp av list menyn.  

![Välj ellipserna i det högra hörnet av panelen och välj anpassa dessa data](./media/storage-insights-overview/fqa-data-settings.png)

![I Konfigurera panel inställningar väljer du List rutan TimeSpan för att ändra tidsintervall/tidsintervall](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Hur gör jag för att ändra titeln på arbets boken eller ett arbets boks steg som jag har fäst på en instrument panel?

Rubriken på arbets bokens eller arbets bokens steg som fästs på en instrument panel behåller samma namn som den hade i arbets boken. Om du vill ändra titeln måste du spara din egen kopia av arbets boken. Sedan kan du namnge arbets boken innan du trycker på Spara.

![Välj Spara längst upp för att spara en kopia av arbets boken och ändra namnet på den](./media/storage-insights-overview/fqa-change-workbook-name.png)

Om du vill ändra namnet på ett steg i din sparade arbets bok väljer du redigera under steget och väljer sedan växeln längst ned i inställningarna.

![på Redigera längst ned i ett arbets boks steg för att öppna inställningarna](./media/storage-insights-overview/fqa-edit.png)
![i Inställningar Välj växeln längst ned för att kunna ändra steget](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Nästa steg

* Konfigurera [mått aviseringar](../platform/alerts-metric.md) och [meddelanden om tjänst hälsa](../../service-health/alerts-activity-log-service-notifications.md) för att ställa in automatiserad avisering som hjälper till att identifiera problem.

* Lär dig mer om arbets böckerna i scenarier är utformade för att stödja, hur du skapar nya och anpassar befintliga rapporter och mer genom att granska [skapa interaktiva rapporter med Azure Monitor arbets böcker](../app/usage-workbooks.md).

* En djupgående guide om hur du använder Lagringsanalys och andra verktyg för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem finns i [övervaka, diagnostisera och felsöka Microsoft Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md).
