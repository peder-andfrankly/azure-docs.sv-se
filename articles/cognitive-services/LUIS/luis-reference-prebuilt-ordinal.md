---
title: Ordnings fördefinierad entitet – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller fördefinierad enhets information för ordning i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b2a2d9e78a0b152da14bb737079cf0dfdef0dc05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491248"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Fördefinierad ordnings enhet för en LUIS-app
Ordnings talet är en numerisk representation av ett objekt inuti en mängd: `first`, `second``third`. Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller ordnings tal till program avsikterna. Ordnings tal enheten stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typer av ordnings tal
Ordnings talet hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Lösning för fördefinierad ordinal

Följande enhets objekt returneras för frågan:

`Order the second option`

#### <a name="v3-responsetabv3"></a>[V3-svar](#tab/V3)

Följande JSON är med parametern `verbose` som har angetts till `false`:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 utförlig Response](#tab/V3-verbose)
Följande JSON är med parametern `verbose` som har angetts till `true`:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetabv2"></a>[V2-svar](#tab/V2)

I följande exempel visas upplösningen för enheten **Builtin. ordnings tal** .

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * * 

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [telefonnummer](luis-reference-prebuilt-phonenumber.md)och [temperatur](luis-reference-prebuilt-temperature.md) enheter. 
