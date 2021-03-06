---
title: Fråga på Azure IoT Hub meddelanderoutning | Microsoft Docs
description: Lär dig mer om det IoT Hub meddelande om meddelanderoutning som du kan använda för att använda omfattande frågor till meddelanden för att ta emot de data som är viktiga för dig.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 859b15954f64f8b481f6b86c04fc28b542599f02
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890503"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT Hub frågesyntaxen för meddelanderoutning

Med meddelanderoutning kan användare dirigera olika data typer, nämligen telemetri meddelanden, enhets livs cykel händelser och enhets dubbla ändrings händelser till olika slut punkter. Du kan också använda omfattande frågor till dessa data innan du dirigerar dem för att ta emot de data som är viktiga för dig. Den här artikeln beskriver frågespråket för IoT Hub Message Routning och innehåller några vanliga fråge mönster.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Med meddelanderoutning kan du fråga efter meddelande egenskaper och meddelande text samt enhetens dubbla Taggar och enhetens dubbla egenskaper. Om meddelande texten inte är JSON kan meddelande routning fortfarande dirigera meddelandet, men det går inte att använda frågor i meddelande texten.  Frågor beskrivs som booleska uttryck där ett booleskt värde gör att frågan lyckas och som dirigerar alla inkommande data och booleska falskt Miss lyckas frågan och inga data vidarebefordras. Om uttrycket utvärderas till null eller odefinierad, behandlas det som falskt och ett fel genereras i diagnostikloggar i händelse av ett fel. Frågesyntaxen måste vara korrekt för att vägen ska kunna sparas och utvärderas.  

## <a name="message-routing-query-based-on-message-properties"></a>Meddelande cirkulations fråga baserat på meddelande egenskaper 

IoT Hub definierar ett [gemensamt format](iot-hub-devguide-messages-construct.md) för all kommunikation från enhet till moln för samverkan mellan protokoll. IoT Hub meddelandet antar följande JSON-representation av meddelandet. System egenskaper läggs till för alla användare och identifierar innehållet i meddelandet. Användare kan selektivt lägga till program egenskaper i meddelandet. Vi rekommenderar att du använder unika egenskaps namn som IoT Hub enhet-till-moln-meddelanden inte är Skift läges känsliga. Om du till exempel har flera egenskaper med samma namn kommer IoT Hub endast att skicka en av egenskaperna.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Systemegenskaper

System egenskaper hjälper till att identifiera innehåll och källa för meddelandena. 

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Innehålls | sträng | Användaren anger meddelandets innehålls typ. Om du vill tillåta frågan på meddelande texten ska det här värdet ställas in Application/JSON. |
| contentEncoding | sträng | Användaren anger meddelandets kodnings typ. Tillåtna värden är UTF-8, UTF-16, UTF-32 om contentType är inställt på Application/JSON. |
| iothub-Connection-Device-ID | sträng | Det här värdet anges av IoT Hub och identifierar enhetens ID. Använd `$connectionDeviceId`för att fråga. |
| iothub – enqueuedtime | sträng | Det här värdet anges av IoT Hub och representerar den faktiska tiden för att köa meddelandet i UTC. Använd `enqueuedTime`för att fråga. |
| iothub-gränssnitt-namn | sträng | Det här värdet anges av användaren och representerar namnet på det digitala dubbla gränssnitt som implementerar telemetri-meddelandet. Använd `$interfaceName`för att fråga. Den här funktionen är tillgänglig som en del av [IoT plug and Play offentlig för hands version](../iot-pnp/overview-iot-plug-and-play.md). |

Som det beskrivs i [IoT Hub-meddelanden](iot-hub-devguide-messages-construct.md)finns det ytterligare system egenskaper i ett meddelande. Förutom **ContentType**, **contentEncoding**och **enqueuedTime**kan **connectionDeviceId** och **connectionModuleId** också frågas.

### <a name="application-properties"></a>Egenskaper för program

Program egenskaperna är användardefinierade strängar som kan läggas till i meddelandet. Dessa fält är valfria.  

### <a name="query-expressions"></a>Frågeuttryck

En fråga om meddelande system egenskaper måste föregås av `$` symbolen. Frågor om program egenskaper används med sitt namn och ska inte föregås av den `$`symbolen. Om ett program egenskaps namn börjar med `$`, kommer IoT Hub söka efter det i System egenskaperna, och det går inte att hitta det. därefter kommer det att se ut i program egenskaperna. Till exempel: 

Så här frågar du efter system egenskapen contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Så här frågar du efter program egenskapen processingPath:

```sql
processingPath = 'hot'
```

Om du vill kombinera dessa frågor kan du använda booleska uttryck och funktioner:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

En fullständig lista över operatörer och funktioner som stöds visas i [uttryck och villkor](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Meddelande cirkulations fråga baserat på meddelande text 

Om du vill aktivera frågor i meddelande texten ska meddelandet vara i en JSON-kodad antingen UTF-8, UTF-16 eller UTF-32. `contentType` måste anges till `application/JSON` och `contentEncoding` till en av de UTF-kodningar som stöds i system egenskapen. Om de här egenskaperna inte anges kommer IoT Hub inte att utvärdera frågeuttrycket i meddelande texten. 

I följande exempel visas hur du skapar ett meddelande med en korrekt utformad och kodad JSON-text: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

### <a name="query-expressions"></a>Frågeuttryck

En fråga om meddelande text måste ha prefixet `$body`. Du kan använda en Body-referens, en text mat ris referens eller flera text referenser i frågeuttrycket. Frågeuttrycket kan också kombinera en brödtext referens med meddelande system egenskaper och referens för meddelande program egenskaper. Till exempel är följande giltiga frågeuttryck: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Meddelande cirkulations fråga baserat på enhet, dubbla 

Med meddelanderoutning kan du fråga på [enhetens dubbla](iot-hub-devguide-device-twins.md) Taggar och egenskaper, som är JSON-objekt. Det finns inte stöd för att skicka frågor till modul "delad". Ett exempel på enhets dubbla Taggar och egenskaper visas nedan.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Frågeuttryck

En fråga om meddelande text måste ha prefixet `$twin`. Frågeuttrycket kan också kombinera en kombinations-eller egenskaps referens med en innehålls referens, meddelande system egenskaper och referens för meddelande program egenskaper. Vi rekommenderar att du använder unika namn i taggar och egenskaper eftersom frågan inte är Skift läges känslig. Du kan också avstå från att använda `twin`, `$twin`, `body`eller `$body`, som egenskaps namn. Till exempel är följande giltiga frågeuttryck: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>Nästa steg

* Lär dig [mer om meddelanderoutning](iot-hub-devguide-messages-d2c.md).
* Prova [själv studie kursen om meddelanderoutning](tutorial-routing.md).
