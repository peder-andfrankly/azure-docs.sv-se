---
title: Sammansatt entitetstyp – LUIS
titleSuffix: Azure Cognitive Services
description: En sammansatt entitet består av andra entiteter, till exempel färdiga entiteter, enkla, reguljära uttryck och list enheter. Separata entiteter utgör en helhet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695155"
---
# <a name="composite-entity"></a>Sammansatt entitet 

En sammansatt entitet består av andra entiteter, till exempel färdiga entiteter, enkla, reguljära uttryck och list enheter. Separata entiteter utgör en helhet. 

**Den här entiteten passar bra när data:**

* Är relaterade till varandra. 
* De är relaterade till varandra i yttrandet.
* Använda en mängd olika typer av enheter.
* Måste grupperas och bearbetas av klient programmet som en informations enhet.
* Ha en rad olika användar yttranden som kräver maskin inlärning.

![Sammansatt entitet](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Exempel på JSON

Överväg en sammansatt entitet av fördefinierad `number` och `Location::ToLocation` med följande uttryck:

`book 2 tickets to cairo`

Observera att `2`, antalet, och `cairo`, ToLocation har ord mellan dem som inte tillhör någon av enheterna. Grön linje, används i en taggade uttryck i den [LUIS](luis-reference-regions.md) webbplats, anger en sammansatt entitet.

![Sammansatt entitet](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

Sammansatta entiteter returneras i en `compositeEntities` matris och alla enheter i sammansatt returneras också i de `entities` matris:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```    

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

Detta är JSON om `verbose=false` anges i frågesträngen:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Detta är JSON om `verbose=true` anges i frågesträngen:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 


|Dataobjekt|Entitetsnamn|Värde|
|--|--|--|
|Fördefinierade enhet - nummer|”builtin.number”|”2”|
|Fördefinierad entitet – GeographyV2|”Location::ToLocation”|Cairo|

## <a name="next-steps"></a>Nästa steg

I den här [självstudien](luis-tutorial-composite-entity.md)lägger du till en **sammansatt enhet** för att bunta extraherade data av olika typer till en enda som innehåller entiteten. Genom att paketera data extrahera klientprogrammet enkelt relaterade data i olika datatyper.
