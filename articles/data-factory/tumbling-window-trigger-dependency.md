---
title: Skapa rullande Window trigger-beroenden
description: Lär dig hur du skapar beroende för en rullande fönster utlösare i Azure Data Factory.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 3a4d31cb6986f8fc841a6afe20388e40e9f28c9b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926680"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Skapa ett beroende för utlösare för rullande fönster

Den här artikeln innehåller steg för att skapa ett beroende på en utlösare för rullande fönster. Allmän information om utlösare för rullande fönster finns i [så här skapar du utlösare för rullande Window](how-to-create-tumbling-window-trigger.md).

För att skapa en beroende kedja och se till att en utlösare körs endast när en annan utlösare har körts i data fabriken, använder du den här avancerade funktionen för att skapa ett rullande fönster beroende.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Skapa ett beroende i Data Factory gränssnittet

Om du vill skapa ett beroende på en utlösare väljer du **Utlösare > avancerad > nytt**och väljer sedan utlösaren så att den är beroende av lämplig förskjutning och storlek. Välj **Slutför** och publicera data Factory-ändringarna för att beroendena ska börja gälla.

![Beroende skapande](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Beroende skapande")

## <a name="tumbling-window-dependency-properties"></a>Egenskaper för rullande Window-beroende

En utlösare för rullande fönster med ett beroende har följande egenskaper:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

Följande tabell innehåller en lista med attribut som behövs för att definiera ett rullande fönster-beroende.

| **Egenskaps namn** | **Beskrivning**  | **Typ** | **Obligatoriskt** |
|---|---|---|---|
| typ  | Alla befintliga rullande fönster-utlösare visas i den här List rutan. Välj utlösaren att ta beroende av.  | TumblingWindowTriggerDependencyReference eller SelfDependencyTumblingWindowTriggerReference | Ja |
| redovisningsmotkonto | Förskjutning av beroende utlösare. Ange ett värde i intervall format och både negativa och positiva förskjutningar tillåts. Den här egenskapen är obligatorisk om utlösaren är beroende av sig själv och i alla andra fall är den valfri. Self-Dependency måste alltid vara en negativ förskjutning. Om inget värde anges är fönstret detsamma som själva utlösaren. | Tidsintervall<br/>(hh: mm: SS) | Själv-beroende: Ja<br/>Övrigt: Nej |
| size | Storlek på fönstret beroende rullande. Ange ett positivt TimeSpan-värde. Den här egenskapen är valfri. | Tidsintervall<br/>(hh: mm: SS) | Nej  |

> [!NOTE]
> En utlösare för rullande fönster kan bero på högst två andra utlösare.

## <a name="tumbling-window-self-dependency-properties"></a>Rullande-fönster egenskaper för Self-Dependency

I scenarier där utlösaren inte ska fortsätta till nästa fönster förrän det föregående fönstret har slutförts, skapar du ett självtestat. En självberoende utlösare som är beroende av att de tidigare körningarna av sig själv i föregående HR har följande egenskaper:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Användnings scenarier och exempel

Nedan visas illustrationer av scenarier och användning av rullande-fönster egenskaper för beroende.

### <a name="dependency-offset"></a>Beroende förskjutning

![Förskjutnings exempel](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Förskjutnings exempel")

### <a name="dependency-size"></a>Beroende storlek

![Storleks exempel](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Storleks exempel")

### <a name="self-dependency"></a>Själv beroende

![Själv beroende](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Själv beroende")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Beroende av en annan utlösare för rullande fönster

En daglig telemetri bearbetnings jobb beroende på ett annat dagligt jobb som sammanställer de senaste sju dagarna och genererar löpande fönster i sju dagar:

![Beroende exempel](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Beroende exempel")

### <a name="dependency-on-itself"></a>Beroende av sig själv

Ett dagligt jobb utan luckor i jobbets utdata:

![Självbetjänings exempel](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Självbetjänings exempel")

## <a name="monitor-dependencies"></a>Övervaka beroenden

Du kan övervaka beroende kedjan och motsvarande fönster från sidan utlösare för att köra övervakning. Gå till **övervakning > trigger-körningar**. I kolumnen åtgärder kan du köra utlösaren igen eller visa dess beroenden.

![Övervaka utlösarens körningar](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Övervaka utlösarkörningar")

Om du klickar på Visa utlösare beroenden kan du se status för beroenden. Om en av beroende utlösare Miss lyckas, måste du köra om det för att den beroende utlösaren ska köras. En utlösare för rullande fönster väntar på beroenden i sju dagar innan tids gränsen uppnåddes.

![Övervaka beroenden](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Övervaka beroenden")

Om du vill visa ett mer visuellt objekt för utlösare för beroende väljer du vyn Gantt-schema.

![Övervaka beroenden](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Övervaka beroenden")

## <a name="next-steps"></a>Nästa steg

* Läs [om hur du skapar en utlösare för rullande fönster](how-to-create-tumbling-window-trigger.md)
