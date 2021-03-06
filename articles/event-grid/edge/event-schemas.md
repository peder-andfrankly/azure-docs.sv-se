---
title: Händelse scheman – Azure Event Grid IoT Edge | Microsoft Docs
description: Händelse scheman i Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242460"
---
# <a name="event-schemas"></a>Händelsescheman

Event Grid-modulen accepterar och levererar händelser i JSON-format. Det finns för närvarande tre scheman som stöds av Event Grid:-

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

Du kan konfigurera det schema som en utgivare måste följa när ämnet skapas. Om inget värde anges används **EventGridSchema**som standard. Händelser som inte följer det förväntade schemat avvisas.

Prenumeranter kan också konfigurera det schema som de vill att de levererade händelserna ska skickas till. Om inget värde anges är standardvärdet för ämnets schema.
Schemat för prenumerations leverans måste matcha dess ämnes schema. 

## <a name="eventgrid-schema"></a>EventGrid-schema

EventGrid-schemat består av en uppsättning obligatoriska egenskaper som en publicerings enhet måste följa. Varje Utgivare måste fylla i fälten på översta nivån.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>EventGrid schema egenskaper

Alla händelser har följande data på översta nivån:

| Egenskap | Typ | Krävs | Beskrivning |
| -------- | ---- | ----------- |-----------
| ämne | sträng | Nej | Ska matcha avsnittet som det publiceras på. Event Grid fyller i den med namnet på ämnet som det publiceras på om det inte har angetts. |
| Motiv | sträng | Ja | Publisher-definierad sökväg till händelsens ämne. |
| Typ | sträng | Ja | Händelse typ för den här händelse källan, till exempel BlobCreated. |
| eventTime | sträng | Ja | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| ID | sträng | Nej | Unikt ID för händelsen. |
| data | objekt | Nej | Används för att avbilda händelse data som är speciella för entiteten publicering. |
| dataVersion | sträng | Ja | Data objektets schema version. Utgivaren definierar schema versionen. |
| metadataVersion | sträng | Nej | Schema versionen för händelsens metadata. Event Grid definierar schemat för de högsta nivå egenskaperna. Event Grid ger det här värdet. |

### <a name="example--eventgrid-schema-event"></a>Exempel – EventGrid schema event

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>CustomEvent-schema

I anpassat schema finns det inga obligatoriska egenskaper som tillämpas som EventGrid-schema. Publicerings entiteten kan endast styra själva händelse schemat. Det ger maximal flexibilitet och möjliggör scenarier där du redan har ett händelsebaserat system som redan finns på plats och vill återanvända befintliga händelser och/eller inte vill vara knutna till ett särskilt schema.

### <a name="custom-schema-properties"></a>Anpassade schema egenskaper

Inga obligatoriska egenskaper. Det är upp till entiteten publicering som fastställer nytto lasten.

### <a name="example--custom-schema-event"></a>Exempel – anpassad schema händelse

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent-schema

Förutom ovanstående scheman har Event Grid inbyggt stöd för händelser i [JSON-schemat CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents är en öppen specifikation för att beskriva händelse data. Det fören klar samverkan genom att tillhandahålla ett gemensamt händelse schema för publicering och användning av händelser. Den är en del av [CNCF](https://www.cncf.io/) och den version som för närvarande är tillgänglig är 1,0 – RC1.

### <a name="cloudevent-schema-properties"></a>CloudEvent schema egenskaper

Se [CloudEvents-specifikationen](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) på de obligatoriska kuvert egenskaperna.

### <a name="example--cloud-event"></a>Exempel – moln händelse
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
