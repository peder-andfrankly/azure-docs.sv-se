---
title: Skapa en miljö – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att skapa en ny Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: seodec18
ms.openlocfilehash: cc99b469701b3974a5b8a2f235ee3a9edb798368
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790264"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Skapa en ny Time Series Insights-miljö i Azure Portal

I den här artikeln beskrivs hur du skapar en ny Time Series Insights miljö med hjälp av Azure Portal.

Med Time Series Insights kan du komma igång med att visualisera och fråga data som flödar till Azure IoT-hubbar och Event Hubs på några minuter, så att du kan fråga stora volymer av tids serie data på några sekunder.  Den har utformats för att skala Internet-of-in-information (IoT) och kan hantera terabyte data.

## <a name="steps-to-create-the-environment"></a>Anvisningar för att skapa miljön

Följ de här stegen för att skapa en miljö:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj knappen **+ skapa en resurs** .

1. Välj kategorin **Sakernas Internet** och välj **Time Series Insights**.

   [![skapa Time Series Insightss miljön](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. På sidan **Time Series Insights** väljer du **skapa**.

1. Fyll i de obligatoriska parametrarna. I följande tabell beskrivs varje parameter:
   
   [![skapa Time Series Insights resurs gruppen](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)
   
   Inställning|Föreslaget värde|Beskrivning
   ---|---|---
   Miljönamn | Ett unikt namn | Det här namnet representerar miljön i [Time Series Explorer](https://insights.timeseries.azure.com)
   Prenumeration | Din prenumeration | Om du har flera prenumerationer väljer du den prenumeration som innehåller din händelse källa helst. Time Series Insights kan automatiskt identifiera Azure IoT Hub- och Event Hub-resurser som existerar i samma prenumeration.
   Resursgrupp | Skapa en ny eller Använd befintlig | En resursgrupp är en samling med Azure-resurser som används tillsammans. Du kan välja en befintlig resurs grupp, till exempel en som innehåller händelsehubben eller IoT Hub. Eller så kan du skapa en ny om den här resursen inte är relaterad till andra resurser.
   Plats | Närmaste händelse källa | Helst kan du välja samma data Center plats som innehåller dina händelse källdata, i arbetet för att undvika att extra bandbredds kostnader mellan regioner och flera zoner och extra fördröjning används när du flyttar data från regionen.
   Prisnivå | S1 | Välj det genomflöde som behövs. För lägsta kostnad och start kapacitet väljer du S1.
   Kapacitet | 1 | Kapaciteten är multiplikatorn som gäller för ingångs pris, lagrings kapacitet och kostnad som är associerad med den valda SKU: n.  Du kan ändra kapacitet för en miljö när den har skapats. För lägsta kostnad väljer du en kapacitet på 1. 
  
1. Välj **skapa** för att påbörja etablerings processen. Det kan ta några minuter.

1. Välj **aviserings** symbolen (klock ikonen) för att övervaka distributions processen.

   [![se meddelanden](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Kontrol lera distributions konfigurations inställningarna i resurs **översikten**.

   [![skapa Time Series Insights fäst vid instrument panelen](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Valfritt)** Välj **PIN-ikonen** i det övre högra hörnet för att enkelt få åtkomst till din Time Series Insightss miljö i framtiden.

## <a name="next-steps"></a>Nästa steg

* [Definiera principer för data åtkomst](time-series-insights-data-access.md) för att skydda din miljö.

* [Lägg till händelse källan för Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md) i Azure Time Series Insightss miljön.

* [Skicka händelser](time-series-insights-send-events.md) till händelse källan.

* Visa din miljö i [Time Series Insights Explorer](https://insights.timeseries.azure.com).
