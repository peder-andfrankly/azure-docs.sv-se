---
title: Skapa webhookar för regler i Azure IoT Central | Microsoft Docs
description: Skapa Webhooks i Azure IoT Central för att automatiskt meddela andra program när regler utlöses.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5c2bef7f3eb8d6f8d6d78755d839a33556259b65
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953673"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Skapa webhook-åtgärder på regler i Azure IoT Central

*Det här avsnittet gäller för byggare och administratörer.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Med Webhooks kan du ansluta din IoT Central-app till andra program och tjänster för fjärrövervakning och-aviseringar. Webhooks meddelar automatiskt andra program och tjänster som du ansluter när en regel utlöses i IoT Central-appen. Din IoT Central-App skickar en POST-begäran till det andra programmets HTTP-slutpunkt när en regel utlöses. Nytto lasten innehåller information om enhets information och regel utlösare.

## <a name="set-up-the-webhook"></a>Konfigurera webhooken

I det här exemplet ansluter du till RequestBin för att få meddelanden när regler som aktive ras med Webhooks.

1. Öppna [RequestBin](https://requestbin.net/).

1. Skapa en ny RequestBin och kopiera **bin-URL: en**.

1. Skapa en [regel för telemetri](howto-create-telemetry-rules.md) eller en [händelse regel](howto-create-event-rules.md). Spara regeln och Lägg till en ny åtgärd.

    ![Skärm bild för skapande av webhook](media/howto-create-webhooks/webhookcreate.png)

1. Välj åtgärden webhook och ange ett visnings namn och klistra in URL-adressen som återanrops-URL.

1. Spara regeln.

Nu när regeln utlöses visas en ny begäran i RequestBin.

## <a name="payload"></a>Innehållet

När en regel utlöses görs en HTTP POST-begäran till återanrops-URL: en med en JSON-nyttolast med mätningarna, enheten, regeln och programinformationen. För en telemetri-regel ser nytto lasten ut ungefär så här:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Kända begränsningar

För närvarande finns det inget programmerings sätt för att prenumerera/avslutning från dessa webhookar via ett API.

Om du har idéer om hur du kan förbättra den här funktionen kan du publicera dina förslag till vårt [UserVoice-forum](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar och använder Webhooks är det föreslagna nästa steg att utforska [Bygg arbets flöden i Microsoft Flow](howto-add-microsoft-flow.md).
