---
title: Identifiering av avbildnings typ – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp relaterade till funktionen för identifiering av avbildnings typer i API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e89fd977129113fa88af1acccd6b05f0bbe90243
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945214"
---
# <a name="detecting-image-types-with-computer-vision"></a>Identifiera avbildnings typer med Visuellt innehåll

Med [analys avbildnings](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) -API: et kan visuellt innehåll analysera innehålls typen för bilder, vilket anger om en bild är ClipArt eller en linje ritning.

## <a name="detecting-clip-art"></a>Identifiera ClipArt

Visuellt innehåll analyserar en bild och mäter sannolikheten för att bilden ska vara en ClipArt-fil på en skala på 0 till 3, enligt beskrivningen i följande tabell.

| Value | Betydelse |
|-------|---------|
| 0 | Non-clip-art |
| 1 | Registrerade |
| 2 | Normal-ClipArt |
| 3 | Lämplig-ClipArt |

### <a name="clip-art-detection-examples"></a>Exempel på identifiering av ClipArt

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när du klassificerar sannolikheten för att exempel bilderna ska vara ClipArt.

![En ClipArt-bild av en sektor av ost](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Ett blått hus och en främre meter](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Identifiera rad ritningar

Visuellt innehåll analyserar en bild och returnerar ett booleskt värde som anger om bilden är en linje ritning.

### <a name="line-drawing-detection-examples"></a>Identifierings exempel för linje ritning

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när anger om exempel bilderna är linje ritningar.

![En linje ritnings bild av en Lion](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![En vit blomma med grön bakgrund](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om att identifiera avbildnings typer finns i referens dokumentationen för att [analysera avbildning](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
