---
title: Alla färdiga entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller listor över fördefinierade entiteter som ingår i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a5fcccc18a453101bd1166f2c85b0c3cdc747c3a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464961"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entiteter per kultur i din LUIS-modell

Language Understanding (LUIS) tillhandahåller färdiga entiteter. När en fördefinierad entitet ingår i ditt program, innehåller LUIS motsvarande enhets förutsägelse i slut punkts svaret. Alla exempel-yttranden är också märkta med entiteten. Det **går inte** att ändra beteendet för förbyggda entiteter. Om inget annat anges är fördefinierade entiteter tillgängliga i alla LUIS program-språkområden (kulturer). I följande tabell visas de fördefinierade entiteter som stöds för varje kultur.

|Kultur|Underkulturer|Anteckningar|
|--|--|--|
|Kinesiska|[zh-CN](#chinese-entity-support)||
|Nederländska|[nl-NL](#dutch-entity-support)||
|Svenska|[en-US (American)](#english-american-entity-support)||
|Franska|[fr-ca (Kanada)](#french-canadian-entity-support), [fr-fr (Frankrike)](#french-france-entity-support), ||
|Tyska|[de-DE](#german-entity-support)||
|Italienska|[det – IT](#italian-entity-support)||
|Japanska|[ja-JP](#japanese-entity-support)||
|Koreanska|[ko-KR](#korean-entity-support)||
|Portugisiska|[pt-BR (Brasilien)](#portuguese-brazil-entity-support)||
|Spanska|[es-es (Spanien)](#spanish-spain-entity-support), [es-MX (Mexiko)](#spanish-mexico-entity-support)||
|Turkiska|[Turkiska](#turkish-entity-support)|Inga fördefinierade entiteter stöds i turkiska|

## <a name="prediction-endpoint-runtime"></a>Körning av förutsägelse slut punkt

Tillgängligheten för en fördefinierad entitet i ett särskilt språk bestäms av körnings versionen av förutsägelse slut punkten. 

## <a name="chinese-entity-support"></a>Stöd för kinesiska entiteter

Följande entiteter stöds:

|Fördefinierad entitet|```zh-CN``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    V2, V3   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    V2, V3   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Många](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Stöd för nederländsk entitet

Följande entiteter stöds:

|Fördefinierad entitet|```nl-NL``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    V2, V3   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    V2, V3   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Många](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Stöd för engelska (American) entiteter

Följande entiteter stöds:

|Fördefinierad entitet|```en-US``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    V2, V3   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    V2, V3   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Många](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Stöd för franska (Frankrike) entitet

Följande entiteter stöds:

|Fördefinierad entitet|```fr-FR``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    V2, V3   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    V2, V3   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Många](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Stöd för franska (kanadensisk) entitet

Följande entiteter stöds:

|Fördefinierad entitet|```fr-CA``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    V2, V3   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    V2, V3   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Många](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Stöd för tyska entiteter

Följande entiteter stöds:

|Fördefinierad entitet|```de-DE``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    V2, V3   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    V2, V3   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Många](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Stöd för italienska entiteter

Italiensk fördefinierad ålder, valuta, dimension, nummer, procentuell _matchning_ har ändrats från v2 och v3 för hands version.

Följande entiteter stöds:

|Fördefinierad entitet|```it-IT``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    V2, V3   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    V2, V3   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Många](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Stöd för japanska entiteter

Följande entiteter stöds:

|Fördefinierad entitet|```ja-JP``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    V2,-   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    V2,-   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    V2,-   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2,-   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    V2,-   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Många](luis-reference-prebuilt-percentage.md)   |    V2,-   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    V2,-   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Stöd för koreanska entiteter

Följande entiteter stöds:

|Fördefinierad entitet|```ko-KR``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    -   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    -   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    -   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Många](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Stöd för portugisiska (Brasilien) entitet

Följande entiteter stöds:

|Fördefinierad entitet|```pt-BR``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    V2, V3   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    V2, V3   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Många](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Stöd för spanska (Spanien) entitet

Följande entiteter stöds:

|Fördefinierad entitet|```es-ES``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    V2, V3   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    V2, V3   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Många](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Stöd för spanska (Mexiko) entitet

Följande entiteter stöds:

|Fördefinierad entitet|```es-MX``` |
------|:------:|
[Ålder](luis-reference-prebuilt-age.md):<br>år<br>månaderna<br>Vecka<br>dagen   |    -   |
[Valuta (belopp)](luis-reference-prebuilt-currency.md):<br>pengarna<br>fraktions enhet (t. ex.: öres)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>datum<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volym<br>Skriv<br>Vägt<br>information (t. ex. bit/byte)<br>längd (t. ex. mätar)<br>hastighet (t. ex.: mil per timme)  |    -   | 
[E-post](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[Riskfras](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numret](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Många](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>grader<br>rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Se kommentarer om [inaktuella förbyggda entiteter](luis-reference-prebuilt-deprecated.md)

En fras är inte tillgänglig i alla under odlingar av portugisiska (Brasilien)-```pt-BR```.

## <a name="turkish-entity-support"></a>Stöd för turkiska entiteter

**Det finns inga färdiga entiteter som stöds i turkiska.** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Bidra till färdiga entiteter för entiteter
De förinställda entiteterna utvecklas i projekt med öppen källkod i igenkännings läge. [Bidra](https://github.com/Microsoft/Recognizers-Text) till projektet. Det här projektet innehåller exempel på valuta per kultur. 

GeographyV2 och PersonName ingår inte i identifierare – text projekt. Öppna en [supportbegäran](../../azure-supportability/how-to-create-azure-support-request.md)om du har problem med dessa fördefinierade entiteter. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om enheterna [Number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)och [Currency](luis-reference-prebuilt-currency.md) . 
