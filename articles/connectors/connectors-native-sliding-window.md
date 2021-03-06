---
title: Schemalägg aktiviteter för att hantera sammanhängande data
description: Skapa och kör återkommande uppgifter som hanterar sammanhängande data med hjälp av glidande fönster i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786919"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Schemalägg och kör aktiviteter för sammanhängande data med hjälp av den glidande fönster utlösaren i Azure Logic Apps

För att regelbundet köra aktiviteter, processer eller jobb som måste hantera data i intilliggande segment, kan du starta ditt Logic app-arbetsflöde med den **glidande fönster** utlösaren. Du kan ange datum och tid samt en tidszon för att starta arbets flödet och en upprepning för att upprepa det arbets flödet. Om återkommande orsaker saknas, bearbetar den här utlösaren de missade upprepningarna. Om du till exempel synkroniserar data mellan databasen och lagringen av säkerhets kopior använder du utlösaren för Glidnings fönster så att data synkroniseras utan att det uppstår luckor. Mer information om inbyggda schema utlösare och åtgärder finns i [schemalägga och köra återkommande automatiserade uppgifter och arbets flöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Här följer några mönster som denna utlösare stöder:

* Kör omedelbart och *Upprepa vartannat antal* sekunder, minuter eller timmar.

* Starta vid ett visst datum och klock slag, och kör och upprepa varje *n* sekunder, minuter eller timmar. Med den här utlösaren kan du ange en start tid tidigare, som kör alla tidigare upprepningar.

* Fördröj varje upprepning för en viss varaktighet innan du kör.

Skillnader mellan den här utlösaren och upprepnings utlösaren eller mer information om hur du schemalägger återkommande arbets flöden finns i [schemalägga och köra återkommande automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Om du vill utlösa din Logic app och bara köra en gång i framtiden, se [Kör jobb en gång](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [Logic Apps](../logic-apps/logic-apps-overview.md). Lär dig [hur du skapar din första Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om du inte har använt Logic Apps igen.

## <a name="add-sliding-window-trigger"></a>Lägg till fönster utlösare för glidning

1. Logga in på [Azure-portalen](https://portal.azure.com). Skapa en tom logikapp.

1. När Logic Apps Designer visas anger du "glidande fönster" som filter i sökrutan. Välj den här utlösaren i listan utlösare som första steg i ditt Logic app-arbetsflöde: **glidande fönster**

   ![Välj "glidande fönster"-utlösare](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Ange intervall och frekvens för upprepningen. I det här exemplet anger du dessa egenskaper för att köra arbets flödet varje vecka.

   ![Ange intervall och frekvens](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Egenskap | Krävs | JSON-namn | Typ | Beskrivning |
   |----------|----------|-----------|------|-------------|
   | **Intervall** | Ja | interval | Integer | Ett positivt heltal som beskriver hur ofta arbets flödet körs baserat på frekvensen. Här följer de lägsta och högsta intervallen: <p>– Timme: 1 – 12000 timmar </br>-Minute: 1 – 72000 minuter </br>-Sekund: 1 – 9999999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "timme", är upprepningen var 6: e timme. |
   | **Frekvens** | Ja | frequency | Sträng | Tidsenhet för upprepning: **sekund**, **minut**eller **timme** |
   ||||||

   ![Avancerade alternativ för upprepning](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Du kan visa fler upprepnings alternativ genom att öppna listan **Lägg till ny parameter** . 
   Alla alternativ som du väljer visas i utlösaren efter val.

   | Egenskap | Krävs | JSON-namn | Typ | Beskrivning |
   |----------|----------|-----------|------|-------------|
   | **Förskjutning** | Nej | förskjutning | Sträng | Varaktigheten för att fördröja varje upprepning med hjälp av [ISO 8601 datum/tid-specifikation](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Tidszon** | Nej | Tidszon | Sträng | Gäller endast när du anger en start tid eftersom den här utlösaren inte accepterar [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Välj den tidszon som du vill använda. |
   | **Starttid** | Nej | startTime | Sträng | Ange start datum och-tid i följande format: <p>ÅÅÅÅ-MM-DDThh: mm: SS om du väljer en tidszon <p>ELLER <p>ÅÅÅÅ-MM-DDThh: mm: ssZ om du inte väljer en tidszon <p>Om du till exempel vill ha 18 september 2017 på 2:00 PM anger du "2017-09-18T14:00:00" och väljer en tidszon som Pacific, normal tid. Eller ange "2017-09-18T14:00:00Z" utan en tidszon. <p>**Obs:** Den här start tiden måste följa [ISO 8601-datum/tid-specifikationen](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC-datum format](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Om du inte väljer en tidszon måste du lägga till bokstaven "Z" i slutet utan blank steg. Detta "Z" avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkla scheman, är start tiden den första förekomsten, men för avancerade upprepningar utlöses inte utlösaren tidigare än start tiden. [*Hur kan jag använda start datum och-tid?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Nu ska du bygga ditt återstående arbets flöde med andra åtgärder. Fler åtgärder som du kan lägga till finns i [kopplingar för Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Arbets flödes definition – glidande fönster

I din Logic Apps underliggande arbets flödes definition, som använder JSON, kan du Visa utlösaren för glidning av fönster med de alternativ som du har valt. Om du vill visa den här definitionen väljer du **kodvyn**i verktygsfältet designer. Om du vill gå tillbaka till designern väljer du verktygsfältet designer, **Designer**.

Det här exemplet visar hur en definition av en glidande fönster utlösare kan se ut i en underliggande arbets flödes definition där fördröjningen för varje upprepning är fem sekunder för en upprepning per timme:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Nästa steg

* [Fördröj nästa åtgärd i arbets flöden](../connectors/connectors-native-delay.md)
* [Anslutningar för Logic Apps](../connectors/apis-list.md)