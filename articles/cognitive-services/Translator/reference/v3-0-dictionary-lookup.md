---
title: Sök metod för Translator Text API ord lista
titleSuffix: Azure Cognitive Services
description: Sök metoden för ord listan innehåller alternativa översättningar för ett ord och ett litet antal idiomatiskt-fraser.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: bd725d41f75bdfb1048b5bee7e8224679dbece4c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837254"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Text API 3,0: Sök efter ord lista

Innehåller alternativa översättningar för ett ord och ett litet antal idiomatiskt-fraser. Varje översättning har en del av tal och en lista över tillbaka-översättningar. Back-translations gör det möjligt för en användare att förstå översättningen i sammanhanget. Exempel åtgärden för [ord listan](./v3-0-dictionary-examples.md) ger ytterligare ökad detalj nivå för att se exempel som använder sig av varje översättnings par.

## <a name="request-url"></a>URL för begäran

Skicka en `POST`-begäran till:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametrar för begäran

Parametrarna för begäran som skickades till frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-version</td>
    <td>*Obligatorisk parameter*.<br/>Den version av API: t som klienten begär. Värdet måste vara `3.0`.</td>
  </tr>
  <tr>
    <td>som</td>
    <td>*Obligatorisk parameter*.<br/>Anger språket för inmatad text. Käll språket måste vara ett av de [språk som stöds](./v3-0-languages.md) som ingår i `dictionary`s omfånget.</td>
  </tr>
  <tr>
    <td>till</td>
    <td>*Obligatorisk parameter*.<br/>Anger språket för utmatnings texten. Mål språket måste vara ett av de [språk som stöds](./v3-0-languages.md) som ingår i `dictionary`s omfånget.</td>
  </tr>
</table>

Begärandehuvuden innehåller:

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>Authentication-huvud (er)</td>
    <td><em>Begär ande huvud för begäran</em>.<br/>Se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Begär ande huvud för begäran*.<br/>Anger nytto lastens innehålls typ. Möjliga värden är: `application/json`.</td>
  </tr>
  <tr>
    <td>Innehålls längd</td>
    <td>*Begär ande huvud för begäran*.<br/>Längden på begär ande texten.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfritt*.<br/>Ett GUID som skapats av klienten för att unikt identifiera begäran. Du kan utelämna det här huvudet om du inkluderar spårnings-ID: t i frågesträngen med hjälp av en frågeparameter med namnet `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Bröd texten i begäran är en JSON-matris. Varje mat ris element är ett JSON-objekt med en sträng egenskap med namnet `Text`, vilket representerar den term som ska sökas.

```json
[
    {"Text":"fly"}
]
```

Följande begränsningar gäller:

* Matrisen får innehålla högst 10 element.
* Text värden för ett mat ris element får inte överstiga 100 tecken inklusive blank steg.

## <a name="response-body"></a>Svars text

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i den angivna matrisen. Ett resultat objekt innehåller följande egenskaper:

  * `normalizedSource`: en sträng som ger en normaliserad form av käll termen. Om förfrågan till exempel är JOHN, blir den normaliserade formen "John". Innehållet i det här fältet blir inmatat för att [hitta exempel](./v3-0-dictionary-examples.md).
    
  * `displaySource`: en sträng som ger käll termen i ett formulär som passar bäst för slut användar visning. Om indatatypen till exempel är JOHN, visar visnings formuläret den vanliga stavningen av namnet: "John". 

  * `translations`: en lista över översättningar för käll termen. Varje element i listan är ett objekt med följande egenskaper:

    * `normalizedTarget`: en sträng som ger normaliserad form av denna term på mål språket. Det här värdet ska användas som ininformation för att [söka efter exempel](./v3-0-dictionary-examples.md).

    * `displayTarget`: en sträng som ger termen på mål språket och i ett formulär som passar bäst för slut användar visning. I allmänhet skiljer sig detta bara från `normalizedTarget` med avseende på versaler. Till exempel, ett korrekt substantiv som "Juan" kommer att ha `normalizedTarget = "juan"` och `displayTarget = "Juan"`.

    * `posTag`: en sträng som associerar den här termen med en del av tal-taggen.

        | Taggnamn | Beskrivning  |
        |----------|--------------|
        | JUST      | Adjektiv   |
        | ADV      | Adverb      |
        | CONJ     | Konjunktioner |
        | DET      | Identifierare  |
        | ANDE    | Verb        |
        | Substantiv     | Substantiv        |
        | BEREDD     | Prepositioner |
        | PRON     | Pronomen     |
        | VERB     | Verb        |
        | ANDRA    | Övrigt        |

        Som en implementerings anteckning fastställdes dessa taggar av en del-av-tal-taggning av den engelska sidan och sedan den mest frekventa taggen för varje käll-/mål par. Så om människor ofta översätter ett spanskt ord till en annan del av tal-taggen på engelska kan taggarna bli fel (med avseende på det spanska ordet).

    * `confidence`: ett värde mellan 0,0 och 1,0 som representerar "konfidens" (eller kanske mer exakt, sannolikhet i tränings data) för översättnings paret. Summan av konfidens resultatet för ett käll ord kan eventuellt inte summeras till 1,0. 

    * `prefixWord`: en sträng som ger ordet att visas som prefix för översättningen. För närvarande är detta den ovan besägaiga avgöranden för substantiv, på språk som har en delvisad avgörs. Prefixet för det spanska ordet "Mosca" är till exempel "La", eftersom "Mosca" är ett Feminine Substantiv på spanska. Detta är endast beroende av översättningen och inte på källan. Om det inte finns något prefix kommer det att vara en tom sträng.
    
    * `backTranslations`: en lista över målets "tillbaka översättningar". Till exempel käll ord som målet kan översättas till. Listan är garanterat att innehålla det käll ord som begärdes (t. ex. om det ord som letas upp är "flyger", så garanterar vi att "flyg" visas i `backTranslations`s listan). Men det är inte säkert att det är i den första positionen, och det kommer ofta inte att vara det. Varje element i `backTranslations`s listan är ett objekt som beskrivs av följande egenskaper:

        * `normalizedText`: en sträng som ger normaliserad form av käll termen som är en back översättning av målet. Det här värdet ska användas som ininformation för att [söka efter exempel](./v3-0-dictionary-examples.md).        

        * `displayText`: en sträng som ger käll termen som är en back översättning av målet i ett formulär som passar bäst för slut användar visning.

        * `numExamples`: ett heltal som representerar antalet exempel som är tillgängliga för det här översättnings paret. Faktiska exempel måste hämtas med ett separat anrop till [Lookup-exempel](./v3-0-dictionary-examples.md). Talet är främst avsett för att under lätta visning i ett UX. Ett användar gränssnitt kan till exempel lägga till en hyperlänk i back-Translation om antalet exempel är större än noll och visar back översättning som oformaterad text om det inte finns några exempel. Observera att det faktiska antalet exempel som returneras av ett anrop till [uppslags exempel](./v3-0-dictionary-examples.md) kan vara mindre än `numExamples`, eftersom ytterligare filtrering kan tillämpas i farten för att ta bort "dåliga" exempel.
        
        * `frequencyCount`: ett heltal som representerar frekvensen för det här översättnings paret i data. Huvud syftet med det här fältet är att tillhandahålla ett användar gränssnitt med ett sätt att sortera tillbaka-översättningar så att de mest frekventa villkoren är första.

    > [!NOTE]
    > Om perioden som slås upp inte finns i ord listan är svaret 200 (OK), men `translations` listan är en tom lista.

## <a name="examples"></a>Exempel

Det här exemplet visar hur du kan söka efter alternativa översättningar på spanska på engelsk sikt `fly`.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

Svars texten (förkortat för tydlighetens skull) är:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Det här exemplet visar vad som händer när termen som letas upp inte finns i det giltiga ordboks paret.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Eftersom termen inte finns i ord listan, innehåller svars texten en tom `translations`s lista.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
