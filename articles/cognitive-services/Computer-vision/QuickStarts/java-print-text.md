---
title: 'Snabb start: extrahera utskriven text – REST, Java'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten extraherar du tryckt text från en bild med hjälp av API:et för visuellt innehåll med Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b976002ee185a87daedf37a93f3d02a166eb6f8a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974605"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-java"></a>Snabb start: extrahera utskriven text (OCR) med hjälp av Visuellt innehåll REST API och Java

> [!NOTE]
> Om du extraherar engelsk språk text bör du överväga att använda den nya [Läs åtgärden](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text). Det finns en [Java-snabb start](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/java-hand-text) . 

I den här snabb starten ska du extrahera utskriven text med optisk tecken igenkänning (OCR) från en bild med hjälp av Visuellt innehåll REST API. Med metoden [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) kan du identifiera tryckt text i en bild och extrahera de tecken som identifieras i en teckenström som kan användas på en dator.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Krav

- Du måste ha [Java&trade; Platform, Standard Edition Development Kit 7 eller 8](https://aka.ms/azure-jdks) (JDK 7 eller 8) installerat.
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnads fri utvärderings nyckel från [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på visuellt innehåll och hämta din nyckel. Skapa sedan [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och tjänst slut punkts strängen, med namnet `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample-application"></a>Skapa och kör exempelappen

Så här skapar du och kör exemplet:

1. Skapa ett nytt Java-projekt i din favorit-IDE eller i ett redigeringsprogram. Om du har det här alternativet kan du skapa Java-projektet från en kommandoradsmall.
1. Importera följande bibliotek till Java-projektet. Om du använder Maven så tillhandahålls Maven-koordinaterna för varje bibliotek.
   - [Apache HTTP-klienten](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Apache HTTP-kärnan](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [JSON-biblioteket](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Lägg till följande `import`-instruktioner i filen som innehåller public-klassen `Main` för projektet.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Ersätt `Main` offentliga klassen med följande kod.
1. Du kan också ersätta värdet för `imageToAnalyze` med webbadressen till en annan bild som du vill extrahera tryckt text ur.
1. Spara och kompilera sedan Java-projektet.
1. Om du använder en IDE kör du `Main`. Annars öppnar du en kommandotolk och kör den kompilerade klassen med kommandot `java`. Till exempel `java Main`.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    String endpoint = ("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + 
            "vision/v2.1/ocr";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" +
            "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation", "true");

            // Request parameters.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>Granska svaret

Ett svar som anger att åtgärden lyckades returneras i JSON. Exempelprogrammet parsar och visar ett lyckat svar i konsolfönstret enligt följande exempel:

```json
REST Response:

{
  "orientation": "Up",
  "regions": [{
    "boundingBox": "21,16,304,451",
    "lines": [
      {
        "boundingBox": "28,16,288,41",
        "words": [{
          "boundingBox": "28,16,288,41",
          "text": "NOTHING"
        }]
      },
      {
        "boundingBox": "27,66,283,52",
        "words": [{
          "boundingBox": "27,66,283,52",
          "text": "EXISTS"
        }]
      },
      {
        "boundingBox": "27,128,292,49",
        "words": [{
          "boundingBox": "27,128,292,49",
          "text": "EXCEPT"
        }]
      },
      {
        "boundingBox": "24,188,292,54",
        "words": [{
          "boundingBox": "24,188,292,54",
          "text": "ATOMS"
        }]
      },
      {
        "boundingBox": "22,253,297,32",
        "words": [
          {
            "boundingBox": "22,253,105,32",
            "text": "AND"
          },
          {
            "boundingBox": "144,253,175,32",
            "text": "EMPTY"
          }
        ]
      },
      {
        "boundingBox": "21,298,304,60",
        "words": [{
          "boundingBox": "21,298,304,60",
          "text": "SPACE."
        }]
      },
      {
        "boundingBox": "26,387,294,37",
        "words": [
          {
            "boundingBox": "26,387,210,37",
            "text": "Everything"
          },
          {
            "boundingBox": "249,389,71,27",
            "text": "else"
          }
        ]
      },
      {
        "boundingBox": "127,431,198,36",
        "words": [
          {
            "boundingBox": "127,431,31,29",
            "text": "is"
          },
          {
            "boundingBox": "172,431,153,36",
            "text": "opinion."
          }
        ]
      }
    ]
  }],
  "textAngle": 0,
  "language": "en"
}
```

## <a name="next-steps"></a>Nästa steg

Utforska ett Java Swing-program som använder Visuellt innehåll för att utföra optisk teckenläsning (OCR), skapa miniatyrbilder med smart beskärning och identifiera, kategorisera, tagga och beskriv visuella funktioner, inklusive ansikten, i en bild. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Självstudie: API för visuellt innehåll med Java](../Tutorials/java-tutorial.md)
