---
title: Fördefinierad valuta entitet – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller fördefinierad information om valutan i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 5b49dcc7e999757e119c399bdf01bed7cb312e02
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465043"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Fördefinierad valuta enhet för en LUIS-app
Den fördefinierade valuta enheten identifierar valuta i många valörer och länder/regioner, oavsett LUIS-programkultur. Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller valuta till program avsikterna. Currency-entiteten stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typer av valuta
Valutan hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Lösning för entiteten valuta

#### <a name="v3-responsetabv3"></a>[V3-svar](#tab/V3)

Följande JSON är med parametern `verbose` som har angetts till `false`:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 utförlig Response](#tab/V3-verbose)
Följande JSON är med parametern `verbose` som har angetts till `true`:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-responsetabv2"></a>[V2-svar](#tab/V2)

I följande exempel visas upplösningen för entiteten **Builtin. Currency** .

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimension](luis-reference-prebuilt-dimension.md)och [e-](luis-reference-prebuilt-email.md) postentiteter. 
