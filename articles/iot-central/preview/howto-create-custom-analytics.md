---
title: Utöka Azure IoT Central med anpassad analys | Microsoft Docs
description: Som en lösnings utvecklare konfigurerar du ett IoT Central program för att göra anpassade analyser och visualiseringar. Den här lösningen använder Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 59fb0dfbc44746853f25437e8e13a1cbc317e151
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895545"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks-preview-features"></a>Utöka Azure IoT Central med anpassad analys med hjälp av Azure Databricks (förhands gransknings funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här instruktions guiden visar dig som lösnings utvecklare och hur du kan utöka ditt IoT Central program med anpassade analyser och visualiseringar. Exemplet använder en [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) arbets yta för att analysera IoT Central telemetri-dataströmmen och för att generera visualiseringar som [låddiagram](https://wikipedia.org/wiki/Box_plot).

Den här instruktions guiden visar hur du utökar IoT Central bortom det som redan kan utföras med de [inbyggda analys verktygen](./howto-create-custom-analytics.md).

I den här instruktions guiden får du lära dig att:

* Strömma telemetri från ett IoT Central program med *kontinuerlig data export*.
* Skapa en Azure Databricks-miljö för att analysera och rita telemetri för enheter.

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här instruktions guiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

### <a name="iot-central-application"></a>IoT Central program

Skapa ett IoT Central-program på webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Betalnings plan | Användningsbaserad betalning |
| Programmall | In-Store Analytics – villkors övervakning |
| Programnamn | Acceptera standardvärdet eller Välj ditt eget namn |
| URL | Acceptera standardvärdet eller Välj ditt eget unika URL-prefix |
| Katalog | Din Azure Active Directory klient |
| Azure-prenumeration | Din Azure-prenumeration |
| Region | Din närmaste region |

I exemplen och skärm bilderna i den här artikeln används den **USA** regionen. Välj en plats nära dig och se till att du skapar alla resurser i samma region.

Den här program mal len innehåller två simulerade termostat-enheter som skickar telemetri.

### <a name="resource-group"></a>Resursgrupp

Använd [Azure Portal för att skapa en resurs grupp](https://portal.azure.com/#create/Microsoft.ResourceGroup) med namnet **IoTCentralAnalysis** som innehåller de andra resurser som du skapar. Skapa dina Azure-resurser på samma plats som ditt IoT Central-program.

### <a name="event-hubs-namespace"></a>Event Hubs-namnområde

Använd [Azure Portal för att skapa ett Event Hubs-namnområde](https://portal.azure.com/#create/Microsoft.EventHub) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namn på namn område |
| Prisnivå | Basic |
| Prenumeration | Din prenumeration |
| Resursgrupp | IoTCentralAnalysis |
| Plats | USA, östra |
| Dataflödesenheter | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks arbets yta

Använd [Azure Portal för att skapa en Azure Databricks tjänst](https://portal.azure.com/#create/Microsoft.Databricks) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn på arbetsyta    | Välj namn på arbets yta |
| Prenumeration | Din prenumeration |
| Resursgrupp | IoTCentralAnalysis |
| Plats | USA, östra |
| Prisnivå | Standard |

När du har skapat de resurser som krävs ser **IoTCentralAnalysis** -resurs gruppen ut som följande skärm bild:

![IoT Central analys resurs grupp](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan konfigurera ett IoT Central program för att kontinuerligt exportera telemetri till en Event Hub. I det här avsnittet skapar du en händelsehubben som tar emot telemetri från ditt IoT Central-program. Händelsehubben ger telemetri till din Stream Analytics jobb för bearbetning.

1. I Azure Portal navigerar du till Event Hubs namn området och väljer **+ Event Hub**.
1. Namnge Event Hub- **centralexport**och välj **skapa**.
1. I listan över händelse nav i namn området väljer du **centralexport**. Välj sedan **principer för delad åtkomst**.
1. Välj **+ Lägg till**. Skapa en princip med namnet **Lyssna** med **lyssnings** anspråket.
1. När principen är klar markerar du den i listan och kopierar sedan **anslutnings strängen – primär nyckel** värde.
1. Anteckna den här anslutnings strängen. du använder den senare när du konfigurerar din Databricks-anteckningsbok för att läsa från händelsehubben.

Event Hubs namn området ser ut som på följande skärm bild:

![Event Hubs-namnområde](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Konfigurera export i IoT Central

På webbplatsen [Azure IoT Central Application Manager](https://aka.ms/iotcentral) navigerar du till det IoT Central program som du skapade från contoso-mallen. I det här avsnittet konfigurerar du programmet för att strömma Telemetrin från dess simulerade enheter till händelsehubben. Konfigurera exporten:

1. Gå till sidan **data export** , Välj **+ ny**och sedan **Azure Event Hubs**.
1. Använd följande inställningar för att konfigurera exporten och välj sedan **Spara**:

    | Inställning | Värde |
    | ------- | ----- |
    | Visningsnamn | Exportera till Event Hubs |
    | Enabled | På |
    | Event Hubs-namnområde | Namnet på Event Hubs namn området |
    | Händelsehubb | centralexport |
    | Mått | På |
    | Enheter | Av |
    | Enhetsmallar | Av |

![Konfiguration av data export](media/howto-create-custom-analytics/cde-configuration.png)

Vänta tills export status är **igång** innan du fortsätter.

## <a name="configure-databricks-workspace"></a>Konfigurera Databricks-arbetsyta

I Azure Portal navigerar du till Azure Databricks-tjänsten och väljer **Starta arbets yta**. En ny flik öppnas i webbläsaren och du loggas in på din arbets yta.

### <a name="create-a-cluster"></a>Skapa ett kluster

På sidan **Azure Databricks** går du till listan med vanliga uppgifter och väljer **nytt kluster**.

Använd informationen i följande tabell för att skapa klustret:

| Inställning | Värde |
| ------- | ----- |
| Klusternamn | centralanalysis |
| Klusterläge | Standard |
| Databricks Runtime-version | 5,5 LTS (Scala 2,11, Spark 2.4.3) |
| Python-version | 3 |
| Aktivera automatisk skalning | Nej |
| Avsluta efter minuter av inaktivitet | 30 |
| Arbetartyp | Standard_DS3_v2 |
| Arbetare | 1 |
| Drivrutinstyp | Samma som arbetare |

Det kan ta flera minuter att skapa ett kluster, vänta tills klustret har skapats innan du fortsätter.

### <a name="install-libraries"></a>Installera bibliotek

På sidan **kluster** väntar du tills kluster status är **igång**.

Följande steg visar hur du importerar biblioteket som exempel behov i klustret:

1. På sidan **kluster** väntar du tills status för det interaktiva **Centralanalysis** -klustret **körs**.

1. Välj klustret och välj sedan fliken **bibliotek** .

1. Välj **installera ny**på fliken **bibliotek** .

1. På sidan **Installera bibliotek** väljer du **maven** som biblioteks källa.

1. I text rutan **koordinater** anger du följande värde: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Välj **Installera** för att installera biblioteket på klustret.

1. Bibliotekets status är nu **installerad**:

    ![Biblioteket är installerat](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importera en Databricks Notebook

Använd följande steg för att importera en Databricks-anteckningsbok som innehåller python-koden för att analysera och visualisera IoT Central telemetri:

1. Navigera till sidan för **arbets ytan** i din Databricks-miljö. Välj List rutan bredvid ditt konto namn och välj sedan **Importera**.

1. Välj att importera från en URL och ange följande adress: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Importera antecknings boken genom att välja **Importera**.

1. Välj **arbets ytan** för att visa den importerade antecknings boken:

    ![Importerad Notebook](media/howto-create-custom-analytics/import-notebook.png)

1. Redigera koden i den första python-cellen för att lägga till Event Hubs anslutnings strängen som du sparade tidigare:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Kör analys

Om du vill köra analysen måste du ansluta antecknings boken till klustret:

1. Välj **frånkopplad** och välj sedan **centralanalysis** -klustret.
1. Om klustret inte körs startar du det.
1. Starta antecknings boken genom att klicka på knappen Kör.

Du kan se ett fel i den sista cellen. I så fall, kontrol lera att de föregående cellerna körs, vänta en minut tills vissa data skrivs till lagring och kör sedan den sista cellen igen.

### <a name="view-smoothed-data"></a>Visa jämna data

Rulla ned till cell 14 i antecknings boken för att se ett diagram över den rullande genomsnittliga fuktighets typen per enhets typ. Den här ritningen uppdateras kontinuerligt när en strömmande telemetri tas emot:

![Diagram över mjuk telemetri](media/howto-create-custom-analytics/telemetry-plot.png)

Du kan ändra storlek på diagrammet i antecknings boken.

### <a name="view-box-plots"></a>Visa Box-kurvor

Rulla ned till cell 20 i antecknings boken för att se [rutorna](https://en.wikipedia.org/wiki/Box_plot). Rutorna baseras på statiska data så att du kan uppdatera dem du måste köra om cellen:

![Låddiagram](media/howto-create-custom-analytics/box-plots.png)

Du kan ändra storlek på ritytan i antecknings boken.

## <a name="tidy-up"></a>Städa upp

Ta bort resurs gruppen **IoTCentralAnalysis** i Azure Portal för att städa upp efter den här instruktionen och undvika onödiga kostnader.

Du kan ta bort IoT Central-programmet från **hanterings** sidan i programmet.

## <a name="next-steps"></a>Nästa steg

I den här instruktions guiden har du lärt dig att:

* Strömma telemetri från ett IoT Central program med *kontinuerlig data export*.
* Skapa en Azure Databricks-miljö för att analysera och rita telemetridata.

Nu när du vet hur du skapar anpassade analyser är det föreslagna nästa steg att lära dig att [visualisera och analysera dina Azure IoT Central-data på en Power BI instrument panel](../core/howto-connect-powerbi.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
