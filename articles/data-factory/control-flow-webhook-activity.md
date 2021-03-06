---
title: Webhook-aktivitet i Azure Data Factory
description: Webhook-aktiviteten fortsätter inte att köra pipelinen förrän den validerar den bifogade data uppsättningen med vissa villkor som användaren anger.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: b2f7c35e6ddb3c6ed0a3032d7ea6d4c53043c17b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122906"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-aktivitet i Azure Data Factory
Du kan använda en webhook-aktivitet för att styra körningen av pipeliner via din anpassade kod. Med webhook-aktiviteten kan kunder anropa en slut punkt och skicka en återanrops-URL. Pipeline-körningen väntar på att återanropet ska anropas innan nästa aktivitet fortsätter.

## <a name="syntax"></a>Syntax

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Typ egenskaper



Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namn på Web Hook-aktiviteten | Sträng | Ja |
typ | Måste vara inställd på **webhook**. | Sträng | Ja |
metod | REST API-metod för mål slut punkten. | nollängd. Typer som stöds: POST | Ja |
url | Mål slut punkt och sökväg | Sträng (eller uttryck med resultType för sträng). | Ja |
sidhuvud | Huvuden som skickas till begäran. Om du till exempel vill ange språk och typ på en begäran: "huvuden": {"Accept-language": "en-US", "innehålls typ": "Application/JSON"}. | Sträng (eller uttryck med resultType för sträng) | Ja, innehålls typ rubrik krävs. "huvuden": {"Content-Type": "Application/JSON"} |
brödtext | Representerar den nytto last som skickas till slut punkten. | Giltig JSON (eller uttryck med resultType för JSON). Se schemat för nytto lasten för begäran i [nytto Last schema](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) avsnittet. | Ja |
autentisering | Autentiseringsmetod som används för att anropa slut punkten. Typer som stöds är Basic eller ClientCertificate. Mer information finns i avsnittet [Authentication](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) . Om autentisering inte krävs utelämnar du den här egenskapen. | Sträng (eller uttryck med resultType för sträng) | Nej |
timeout | Hur länge aktiviteten ska vänta på att &#39;callBackUri&#39; anropas. Hur länge aktiviteten ska vänta tills "callBackUri" anropas. Standardvärdet är 10mins ("00:10:00"). Format är TimeSpan, t. ex. d. hh: mm: SS | Sträng | Nej |
Rapportera status vid motringning | Tillåter användaren att rapportera den misslyckade statusen för webhook-aktiviteten som markerar aktiviteten som misslyckad | Boolesk | Nej |

## <a name="additional-notes"></a>Ytterligare information

Azure Data Factory skickar ytterligare en egenskap "callBackUri" i bröd texten till URL-slutpunkten och förväntar sig att denna URI anropas innan timeout-värdet har angetts. Om URI: n inte anropas, kommer aktiviteten att Miss förväntas med status stängningsåtgärd.

Webhook-aktiviteten Miss lyckas när anropet till den anpassade slut punkten Miss lyckas. Eventuella fel meddelanden kan läggas till i bröd texten i återanropet och användas i en efterföljande aktivitet.

Texten som skickas tillbaka till återanrops-URI: n måste vara giltig JSON. Du måste ange innehålls typ rubriken till `application/json`.

När du använder alternativet rapportera status vid motringning måste du lägga till följande kodfragment i texten när du gör motringningen:

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>Nästa steg

Se andra kontroll flödes aktiviteter som stöds av Data Factory:

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webb aktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
