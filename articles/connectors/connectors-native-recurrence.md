---
title: Schemalägg återkommande uppgifter och arbets flöden
description: Schemalägg och kör återkommande automatiserade uppgifter och arbets flöden med upprepnings utlösaren i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0df34c0632875be3d2d3956ca90f615cb0a990ed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787207"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Skapa, Schemalägg och kör återkommande uppgifter och arbets flöden med upprepnings utlösaren i Azure Logic Apps

För att regelbundet köra aktiviteter, processer eller jobb enligt ett särskilt schema kan du starta ditt Logic app-arbetsflöde med den inbyggda **upprepnings schema** utlösaren. Du kan ange datum och tid samt en tidszon för att starta arbets flödet och en upprepning för att upprepa det arbets flödet. Om upprepningar missas av någon anledning fortsätter den här utlösaren att upprepas vid nästa schemalagda intervall. Mer information om inbyggda schema utlösare och åtgärder finns i [schemalägga och köra återkommande automatiserade uppgifter och arbets flöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Här följer några mönster som denna utlösare stöder tillsammans med mer avancerade upprepningar och komplexa scheman:

* Kör omedelbart och upprepa varje *n* antal sekunder, minuter, timmar, dagar, veckor eller månader.

* Starta vid ett visst datum och klock slag, kör och upprepa varje *n* sekunder, minuter, timmar, dagar, veckor eller månader.

* Kör och upprepa en eller flera gånger varje dag, till exempel kl. 8:00 och 5:00 PM.

* Kör och upprepa varje vecka, men endast för vissa dagar, till exempel lördag och söndag.

* Kör och upprepa varje vecka, men endast för vissa dagar och tidpunkter, som måndag till fredag kl. 8:00 och 5:00 PM.

För skillnader mellan den här utlösaren och den glidande fönster utlösaren eller mer information om schemaläggning av återkommande arbets flöden, se [schemalägga och köra återkommande automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Om du vill utlösa din Logic app och bara köra en gång i framtiden, se [Kör jobb en gång](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [Logic Apps](../logic-apps/logic-apps-overview.md). Lär dig [hur du skapar din första Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om du inte har använt Logic Apps igen.

## <a name="add-recurrence-trigger"></a>Lägg till upprepnings utlösare

1. Logga in på [Azure-portalen](https://portal.azure.com). Skapa en tom logikapp.

1. När Logic Apps Designer visas anger du "upprepning" som filter i sökrutan. Välj den här utlösaren i listan utlösare som det första steget i ditt Logic app-arbetsflöde: **upprepning**

   ![Välj utlösare för upprepning](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Ange intervall och frekvens för upprepningen. I det här exemplet anger du dessa egenskaper för att köra arbets flödet varje vecka.

   ![Ange intervall och frekvens](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Egenskap | Krävs | JSON-namn | Typ | Beskrivning |
   |----------|----------|-----------|------|-------------|
   | **Intervall** | Ja | interval | Integer | Ett positivt heltal som beskriver hur ofta arbets flödet körs baserat på frekvensen. Här följer de lägsta och högsta intervallen: <p>– Månad: 1-16 månader </br>– Dag: 1-500 dagar </br>– Timme: 1 – 12000 timmar </br>-Minute: 1 – 72000 minuter </br>-Sekund: 1 – 9999999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "månad", är upprepningen var 6: a månad. |
   | **Frekvens** | Ja | frequency | Sträng | Tidsenhet för upprepning: **sekund**, **minut**, **timme**, **dag**, **vecka**eller **månad** |
   ||||||

   Öppna listan **Lägg till ny parameter** om du vill ha fler alternativ för schemaläggning. 
   Alla alternativ som du väljer visas i utlösaren efter val.

   ![Avancerade alternativ för schemaläggning](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Egenskap | Krävs | JSON-namn | Typ | Beskrivning |
   |----------|----------|-----------|------|-------------|
   | **Tidszon** | Nej | Tidszon | Sträng | Gäller endast när du anger en start tid eftersom den här utlösaren inte accepterar [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Välj den tidszon som du vill använda. |
   | **Starttid** | Nej | startTime | Sträng | Ange start datum och-tid i följande format: <p>ÅÅÅÅ-MM-DDThh: mm: SS om du väljer en tidszon <p>ELLER <p>ÅÅÅÅ-MM-DDThh: mm: ssZ om du inte väljer en tidszon <p>Om du till exempel vill ha 18 september 2017 på 2:00 PM anger du "2017-09-18T14:00:00" och väljer en tidszon som Pacific, normal tid. Eller ange "2017-09-18T14:00:00Z" utan en tidszon. <p>**Obs:** Den här start tiden har högst 49 år i framtiden och måste följa [8601 ISO-tiden för datum/tid](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC-datum format](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Om du inte väljer en tidszon måste du lägga till bokstaven "Z" i slutet utan blank steg. Detta "Z" avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkla scheman är start tiden den första förekomsten, medan utlösaren i komplexa scheman inte utlöses tidigare än start tiden. [*Hur kan jag använda start datum och-tid?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Dessa dagar** | Nej | weekDays | Sträng eller sträng mat ris | Om du väljer "vecka" kan du välja en eller flera dagar när du vill köra arbets flödet: **måndag**, **tisdag**, **onsdag**, **torsdag**, **fredag**, **lördag**och **söndag** |
   | **Vid dessa timmar** | Nej | timmar | Heltals-eller heltals mat ris | Om du väljer "dag" eller "vecka" kan du välja ett eller flera heltal från 0 till 23 som de timmar på dagen då du vill köra arbets flödet. <p><p>Om du t. ex. anger "10", "12" och "14", får du 10 AM, 12 PM och 2 PM för timmar på dagen, men minuterna beräknas baserat på när upprepningen startar. Ange värdet för egenskapen **vid följande minuter** om du vill ange minuter på dagen. |
   | **Vid dessa minuter** | Nej | minuter | Heltals-eller heltals mat ris | Om du väljer "dag" eller "vecka" kan du välja ett eller flera heltal från 0 till 59 som minuter i timmen när du vill köra arbets flödet. <p>Du kan till exempel ange "30" som minut märke och använda föregående exempel för timmar på dagen, du får 10:30 AM, 12:30 PM och 2:30 PM. |
   |||||

   Anta till exempel att dagens datum är måndag, 4 september 2017. Följande upprepnings utlösare utlöses inte *tidigare* än start datum och-tid, vilket är måndag, 18 september 2017 kl. 8:00 PST. Däremot anges upprepnings schema för 10:30, 12:30 PM och 2:30 PM endast på måndagar. Första gången utlösaren utlöses och skapar en arbets flödes instans för logi Kap par är 10:30. Mer information om hur start tiden fungerar finns i [exemplen för start tid](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Framtida körningar sker vid 12:30 PM och 2:30 PM samma dag. Varje upprepning skapar en egen arbets flödes instans. Därefter upprepas hela schemat igen nästa måndag. [*Vad är några andra exempel på förekomster?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Exempel på avancerad schemaläggning](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Utlösaren visar en för hands version för den angivna upprepningen bara när du väljer "dag" eller "vecka" som frekvens.

1. Nu ska du bygga ditt återstående arbets flöde med andra åtgärder. Fler åtgärder som du kan lägga till finns i [kopplingar för Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Arbets flödes definition-upprepning

I din Logic Apps underliggande arbets flödes definition, som använder JSON, kan du Visa [definitionen av upprepnings utlösaren](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) med de alternativ som du har valt. Om du vill visa den här definitionen väljer du **kodvyn**i verktygsfältet designer. Om du vill gå tillbaka till designern väljer du verktygsfältet designer, **Designer**.

Det här exemplet visar hur en definition för upprepnings utlösare kan se ut i en underliggande arbets flödes definition:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Nästa steg

* [Pausa arbets flöden med fördröjnings åtgärder](../connectors/connectors-native-delay.md)
* [Anslutningar för Logic Apps](../connectors/apis-list.md)
