---
title: Lägg till paneler på instrument panelen | Microsoft Docs
description: Som ett verktyg kan du läsa om hur du konfigurerar standard instrument panelen för Azure IoT Central-programmet.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: affd5b58c312e07177fbfdaaf88f689b1a8caebd
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954713"
---
# <a name="configure-the-application-dashboard"></a>Konfigurera program instrument panelen

**Instrument panelen** är den sida som läses in när användare som har åtkomst till programmet navigerar till programmets URL. Om du har skapat ditt program från någon av **Programmallarna**, kommer programmet att ha en fördefinierad instrument panel att starta. Om du har skapat ditt program från program mal len för **anpassade program** kommer din instrument panel att vara tom för att starta.

> [!NOTE]
> Användare kan [skapa flera instrument paneler](howto-create-personal-dashboards.md) förutom standard instrument panelen. Dessa instrument paneler kan endast vara personliga för användaren eller delas över alla användare av programmet. 

## <a name="add-tiles"></a>Lägg till paneler

På följande skärm bild visas instrument panelen i ett program som skapats från den **anpassade program** mal len. Om du vill anpassa standard instrument panelen för ditt program väljer du **Redigera** längst upp till vänster på sidan.

> [!div class="mx-imgBorder"]
> ![instrument panel för program som baseras på mallen "exempel contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Om du väljer **Redigera** öppnas panelen instrument panel bibliotek. Biblioteket innehåller panelerna och de primitiver som du kan använda för att anpassa instrument panelen.

> [!div class="mx-imgBorder"]
> bibliotek för ![instrument panel](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Du kan till exempel lägga till en **telemetri** -panel för enhetens aktuella temperatur. Gör så här:
1. Välj en **mall för enhet**
1. Välj en **enhets instans** för den enhet som du vill se på en panel på instrument panelen. Sedan visas en lista över enhetens egenskaper som kan användas på panelen.
1. Om du vill skapa panelen på instrument panelen klickar du på **temperatur** och drar den till instrument panels ytan. Du kan också klicka på kryss rutan bredvid **temperatur** och klicka på **kombinera**. Följande skärm bild visar hur du väljer en enhets mall och en enhets instans och sedan skapar en panel för temperatur telemetri på instrument panelen.
1. Välj **Spara** längst upp till vänster för att spara panelen på instrument panelen.

> [!div class="mx-imgBorder"]
> !["Konfigurera enhets information" med information för inställningar och egenskaper](media/howto-add-tiles-to-your-dashboard/device-details.png)

Nu när en operatör visar standard instrument panelen för programmet visas den nya panelen med enhetens **temperatur** . Varje panel har ett förvalt diagram, diagram osv. som ska visas när panelen skapas. Användare kan dock välja att redigera och ändra den här visualiseringen. 

> [!div class="mx-imgBorder"]
> !["instrument panel"-fliken med de inställningar och egenskaper som visas för panelen](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)


## <a name="edit-tiles"></a>Redigera paneler

Om du vill redigera en panel på instrument panelen, klickar du först på **Redigera** längst upp till vänster på sidan, vilket öppnar redigerings läget för instrument panelen och alla dess paneler. 

> [!div class="mx-imgBorder"]
> ![instrument panels skärm med aktiverat redigerings läge för en vald panel](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Klicka sedan på **kugg hjuls** ikonen i det övre högra hörnet på den panel som du vill redigera. Här kan du redigera aspekter av panelen, inklusive dess rubrik, visualisering, agg regering osv.

> [!div class="mx-imgBorder"]
> ![listruta för inställningar för panel samling](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Du kan också ändra diagrammets visualisering genom att klicka på **linjalens** ikon i panelen.

> [!div class="mx-imgBorder"]
> ![listruta för inställningar för panelens visualisering](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Panel typer

I följande tabell sammanfattas användningen av paneler i Azure IoT Central:
 
| Hörnruta | Instrumentpanel | Beskrivning
| ----------- | ------- | ------- |
| Innehåll | Instrument paneler för program-och enhets uppsättning |Markdown-paneler som stöds är klickade paneler som visar rubrik och beskrivnings text. Du kan också använda den här panelen som länk panel för att göra det möjligt för en användare att navigera till en URL som är relaterad till ditt program.|
| Bild | Instrument paneler för program-och enhets uppsättning |Bild paneler visar en anpassad bild och går att klicka på. Använd en bild panel för att lägga till grafik på en instrument panel och om du vill kan en användare navigera till en URL som är relevant för ditt program.|
| Etikett | Instrument paneler för program |Etikett paneler visar anpassad text på en instrument panel. Du kan välja storlek på texten. Använd en etikett panel för att lägga till relevant information på instrument panelen, till exempel beskrivningar, kontakt uppgifter eller hjälp.|
| Karta | Instrument paneler för program-och enhets uppsättning |Kart paneler visar plats och status för en enhet på en karta. Du kan till exempel Visa var en enhet är och om dess fläkt är avstängd.|
| Linje diagram | Instrument paneler för program och enheter |Linje diagram paneler visar ett diagram över mängd mått för en enhet under en tids period. Du kan till exempel visa ett linje diagram som visar genomsnitts temperaturen och trycket för en enhet under den senaste timmen.|
| Stapeldiagram | Instrument paneler för program och enheter |I stapeldiagram visas ett diagram över sammanställda mått för en enhet under en tids period. Du kan till exempel visa ett stapeldiagram som visar genomsnitts temperaturen och trycket för en enhet under den senaste timmen.|
| Cirkel diagram | Instrument paneler för program-och enhets uppsättning |I cirkel diagram paneler visas ett diagram över sammanställda mått för en enhet under en tids period.|
| Termisk karta | Instrument paneler för program-och enhets uppsättning |I panelerna för termisk karta visas information om enhets uppsättningen som visas som färger.|
| Händelse historik | Instrument paneler för program och enheter |Panelerna händelse historik visar händelserna för en enhet under en viss tids period. Du kan till exempel använda den för att visa alla temperatur ändringar för en enhet under den senaste timmen.|
| Tillstånds historik | Instrument paneler för program och enheter |Panelerna för tillstånds historik visar mått värden för en tids period. Du kan till exempel använda den för att visa temperatur värden för en enhet under den senaste timmen.|
| KPI | Instrument paneler för program och enheter | KPI-paneler visar en sammanställd telemetri eller händelse mätning för en tids period. Du kan till exempel använda den för att visa den maximala temperatur som nåtts för en enhet under den senaste timmen.|
| Senast kända värde | Instrument paneler för program och enheter |Sista kända värde paneler visar det senaste värdet för en telemetri-eller tillstånds mätning. Du kan till exempel använda den här panelen för att visa de senaste mätningarna av temperatur, tryck och fuktighet för en enhet.|

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar din Azure IoT Central standard instrument panel, kan du [lära dig hur du förbereder och laddar upp bilder](howto-prepare-images.md).