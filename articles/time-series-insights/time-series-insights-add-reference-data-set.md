---
title: Så här lägger du till referens data uppsättningar i miljön – Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du lägger till en referens data uppsättning för att utöka data i din Azure Time Series Insightss miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: seodec18
ms.openlocfilehash: d1bbfb43c6e2319706f5eeac15fa1d60791b62b9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807256"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Skapa en referens data uppsättning för din Time Series Insights-miljö med hjälp av Azure Portal

I den här artikeln beskrivs hur du lägger till en referens data uppsättning i din Azure Time Series Insightss miljö. Referens data är användbara för att ansluta till dina källdata för att öka värdena.

En referens data uppsättning är en samling objekt som utökar händelserna från din händelse källa. Time Series Insights ingress-motorn kopplar varje händelse från din händelse källa till motsvarande datarad i referens data uppsättningen. Den här förhöjda händelsen är sedan tillgängliga för frågor. Den här kopplingen baseras på primär nyckel kolumnerna som definierats i referens data uppsättningen.

Referens data har inte anslutits retroaktivt. Det innebär att endast aktuella och framtida ingångs data matchas och kopplas till den angivna referens tiden när den har kon figurer ATS och laddats upp.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Lär dig mer om Time Series Insights referens data modell.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Lägg till en referens data uppsättning

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Leta upp din befintliga Azure Time Series Insights-miljö. Välj **alla resurser** på menyn på vänster sida av Azure Portal. Välj Time Series Insights-miljö.

1. Välj **översikts** sidan. Leta upp **URL: en för Time Series Insights Explorer** och öppna länken.  

   Visa Utforskaren för din Time Series Insightss miljö.

1. Expandera miljö väljaren i Time Series Insights Explorer. Välj den aktiva miljön. Välj referens data ikonen längst upp till höger på sidan Utforskaren.

   [![lägga till referens data](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Klicka på knappen **+ Lägg till en data uppsättning** för att börja lägga till en ny data uppsättning.

   [![lägga till data uppsättning](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. På sidan **ny referens data uppsättning** väljer du data formatet:

   - Välj **CSV** för kommaavgränsade data. Den första raden behandlas som en rubrik rad.
   - Välj **JSON-matris** för JSON-formaterade data (Java Script Object Notation).

   [![Välj data format.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Ange data med någon av de två metoderna:

   - Klistra in data i text redigeraren. Välj sedan knappen **parsa referens data** .
   - Klicka på **Välj fil** om du vill lägga till data från en lokal textfil.

   Exempel: klistra in CSV-data: [![inklistrade CSV-data](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Exempel: klistra in JSON mat ris data: [![klistra in JSON-data](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Om det uppstår ett fel vid parsning av datavärdena visas felet i rött längst ned på sidan, till exempel `CSV parsing error, no rows extracted`.

1. När data har pars ATS visas ett data rutnät som visar de kolumner och rader som representerar data.  Granska data rutnätet för att säkerställa att det är korrekt.

   [![lägga till referens data](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Granska varje kolumn för att se data typen som antas och ändra data typen om det behövs.  Välj data typs symbolen i kolumn rubriken: **#** för Double (numeriska data), **t | F** för boolesk eller **ABC** för sträng.

   [![välja data typer i kolumn rubrikerna.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Byt namn på kolumn rubrikerna om det behövs. Nyckel kolumn namnet är nödvändigt för att kunna ansluta till motsvarande egenskap i din händelse källa. 

   > [!IMPORTANT]
   > Se till att kolumn namnen för referens data matchar exakt händelse namnet för inkommande data, inklusive Skift läges känslighet. Kolumn namn som inte är nyckel används för att utöka inkommande data med motsvarande referens data värden.

1. Skriv ett värde i fältet **filtrera fältet rader...** om du vill granska vissa rader efter behov. Filtret är användbart för att granska data, men används inte vid överföring av data.

1. Namnge data uppsättningen genom att fylla i fältet **data uppsättnings namn** ovanför data rutnätet.

    [![namnet på data uppsättningen.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Ange kolumnen **primär nyckel** i data uppsättningen genom att välja List rutan ovanför data rutnätet.

    [![Markera nyckel kolumnerna.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Valfritt)** Välj knappen **+** för att lägga till en sekundär nyckel kolumn som en sammansatt primär nyckel. Om du behöver ångra valet väljer du det tomma värdet i list rutan för att ta bort den sekundära nyckeln.

1. Om du vill överföra data väljer du knappen **Överför rader** .

    [![Ladda upp rader och bekräfta data.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    Sidan bekräftar att överföringen är klar och visar att meddelandet **har laddat ned data uppsättningen**.

    > [!WARNING]
    > Kolumner eller egenskaper som delas mellan referens data uppsättningar visar ett **duplicerat egenskaps namn** för överförings fel. Felet förhindrar inte lyckad uppladdning av referens data uppsättningar. Den kan tas bort genom att kombinera rader som delar det duplicerade egenskaps namnet.

1. Välj **Lägg till en rad**, **Mass import rader**eller **Lägg till en kolumn** för att lägga till fler referens data värden efter behov.

    [![lägga till en rad, Mass import rader eller Lägg till en kolumn.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Alla rader som delar en unik nyckel med en annan rad kommer att ha sina kolumner åsidosatta av den sista raden som delar den unika nyckeln.

   > [!NOTE]
   > Tillagda rader behöver **inte** vara *rektangulära* – de kan ha färre, större eller varierande kolumner från andra poster i referens data uppsättningen.

## <a name="next-steps"></a>Nästa steg

* [Hantera referensdata](time-series-insights-manage-reference-data-csharp.md) programmässigt.

* En fullständig API-referens, se dokumentet [Referensdata-API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) .
