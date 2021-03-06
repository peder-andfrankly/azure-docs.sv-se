---
title: 'Snabbstart: Använda SDK för Webbsökning i Bing för Node.js'
titleSuffix: Azure Cognitive Services
description: SDK för Webbsökning i Bing gör det enkelt att integrera Webbsökning i Bing i Node.js-programmet. I den här snabbstarten lär du dig att instansiera en klient, skicka en begäran och skriva ut svaret.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 059862255ca2cf0ce435496bf22d866a37c8be71
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977173"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Snabbstart: Använda SDK för Webbsökning i Bing för Node.js

SDK för Webbsökning i Bing gör det enkelt att integrera Webbsökning i Bing i Node.js-programmet. I den här snabbstarten lär du dig att instansiera en klient, skicka en begäran och skriva ut svaret.

Vill du se koden på en gång? [Exemplen med SDK för Webbsökning i Bing för Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) finns på GitHub.

## <a name="prerequisites"></a>Krav
Här följer några saker som du behöver innan du kör den här snabbstarten:

* [Node.js 6](https://nodejs.org/en/download/) eller senare
* En prenumerationsnyckel  

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>Konfigurera utvecklingsmiljön

Vi börjar med att konfigurera utvecklingsmiljön för Node.js-projektet.

1. Skapa en ny katalog för projektet:

    ```console
    mkdir YOUR_PROJECT
    ```

1. Skapa en ny paketfil:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. Nu installerar vi några Azure-moduler och lägger till dem i `package.json`:

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Skapa ett projekt och deklarera nödvändiga moduler

I samma katalog som din `package.json` skapar du ett nytt Node.js-projekt med hjälp av valfri IDE eller redigeringsprogram. Till exempel: `sample.js`.

Kopiera den här koden till projektet. Den läser in de moduler som installerades i föregående avsnitt.

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Instansiera klienten

Den här koden instansierar en klient och använder modulen `azure-cognitiveservices-websearch`. Se till att du anger en giltig prenumerationsnyckel för ditt Azure-konto innan du fortsätter.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Göra en begäran och skriva ut resultatet

Använd klienten för att skicka en sökfråga till Webbsökning i Bing. Om svaret innehåller resultat för något av objekten `properties`-matrisen skrivs `result.value` ut till konsolen.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Köra programmet

Det sista steget är att köra programmet!

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det här projektet bör du ta bort din prenumerationsnyckel från programmets kod.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Exempel med Cognitive Services SDK för Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Se också

* [Azure Node SDK-referens](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/)
