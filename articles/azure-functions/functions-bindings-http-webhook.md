---
title: Azure Functions HTTP-utlösare och bindningar
description: Förstå hur du använder HTTP-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: f1bb2731f5f14b80ca46f4fb28b9b9cb4284c4d7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972378"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Azure Functions HTTP-utlösare och bindningar

Den här artikeln förklarar hur du arbetar med HTTP-utlösare och utgående bindningar i Azure Functions.

En HTTP-utlösare kan anpassas för att svara på [Webhooks](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

Koden i den här artikeln är som standard syntax som använder .NET Core, som används i functions version 2. x och högre. Information om syntaxen för 1. x finns i [mallarna för 1. x-funktioner](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

HTTP-bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet-paketet, version 1. x. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

HTTP-bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. http NuGet-](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) paketet, version 3. x. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Utlösare

Med HTTP-utlösaren kan du anropa en funktion med en HTTP-begäran. Du kan använda en HTTP-utlösare för att bygga API: er utan server och svara på Webhooks.

Som standard returnerar en HTTP-utlösare HTTP 200 OK med en tom brödtext i funktioner 1. x eller HTTP 204 inget innehåll med en tom brödtext i funktionerna 2. x och högre. Konfigurera en [http-utgående bindning](#output)om du vill ändra svaret.

## <a name="trigger---example"></a>Utlösare - exempel

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som söker efter en `name`-parameter antingen i frågesträngen eller i bröd texten i http-begäran. Observera att returvärdet används för utgående bindning, men ett attribut för retur värde är inte obligatoriskt.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en trigger-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen söker efter en `name` parameter antingen i frågesträngen eller i bröd texten i HTTP-begäran.

Här är den *function.json* fil:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är C# skript koden som binder till `HttpRequest`:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Du kan binda till ett anpassat objekt i stället för att `HttpRequest`. Det här objektet skapas från bröd texten i begäran och tolkas som JSON. På samma sätt kan en typ skickas till HTTP-svarets utgående bindning och returneras som svars text, tillsammans med en status kod för 200.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen söker efter en `name` parameter antingen i frågesträngen eller i bröd texten i HTTP-begäran.

Här är den *function.json* fil:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I följande exempel visas en trigger-bindning i en *Function. JSON* -fil och en [python-funktion](functions-reference-python.md) som använder bindningen. Funktionen söker efter en `name` parameter antingen i frågesträngen eller i bröd texten i HTTP-begäran.

Här är den *function.json* fil:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är python-koden:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

# <a name="javatabjava"></a>[Java](#tab/java)

* [Läs parameter från frågesträngen](#read-parameter-from-the-query-string)
* [Läs brödtext från en POST-begäran](#read-body-from-a-post-request)
* [Läs parameter från en väg](#read-parameter-from-a-route)
* [Läs POJO-brödtext från en POST-begäran](#read-pojo-body-from-a-post-request)

I följande exempel visas HTTP trigger-bindningen i en *Function. JSON* -fil och de respektive [Java-funktioner](functions-reference-java.md) som använder bindningen. 

Här är den *function.json* fil:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

#### <a name="read-parameter-from-the-query-string"></a>Läs parameter från frågesträngen

Det här exemplet läser en parameter, med namnet ```id```, från frågesträngen och använder den för att bygga ett JSON-dokument som returneras till klienten, med innehålls typen ```application/json```. 

```java
@FunctionName("TriggerStringGet")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("GET parameters are: " + request.getQueryParameters());

    // Get named parameter
    String id = request.getQueryParameters().getOrDefault("id", "");

    // Convert and display
    if (id.isEmpty()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String name = "fake_name";
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-body-from-a-post-request"></a>Läs brödtext från en POST-begäran

Det här exemplet läser bröd texten i en POST-begäran, som en ```String```och använder den för att bygga ett JSON-dokument som returneras till klienten, med innehålls typen ```application/json```.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route"></a>Läs parameter från en väg

Det här exemplet läser en obligatorisk parameter, med namnet ```id```och en valfri parameter ```name``` från väg Sök vägen och använder dem för att bygga ett JSON-dokument som returneras till klienten, med innehålls typen ```application/json```. T

```java
@FunctionName("TriggerStringRoute")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.GET}, 
            authLevel = AuthorizationLevel.ANONYMOUS,
            route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
        HttpRequestMessage<Optional<String>> request,
        @BindingName("id") String id,
        @BindingName("name") String name,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Route parameters are: " + id);

    // Convert and display
    if (id == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                        "\"description\": \"" + name + "\"}";
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(jsonDocument)
                        .build();
    }
}
```

#### <a name="read-pojo-body-from-a-post-request"></a>Läs POJO-brödtext från en POST-begäran

Här är koden för ```ToDoItem```-klassen som refereras i det här exemplet:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

I det här exemplet läses bröd texten i en POST-begäran. Begär ande texten avserialiseras automatiskt till ett ```ToDoItem```-objekt och returneras till klienten med innehålls typen ```application/json```. Parametern ```ToDoItem``` serialiseras av Functions-körningen när den tilldelas egenskapen ```body``` i ```HttpMessageResponse.Builder```-klassen.

```java
@FunctionName("TriggerPojoPost")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", 
            methods = {HttpMethod.POST}, 
            authLevel = AuthorizationLevel.ANONYMOUS)
        HttpRequestMessage<Optional<ToDoItem>> request,
        final ExecutionContext context) {
    
    // Item list
    context.getLogger().info("Request body is: " + request.getBody().orElse(null));

    // Check request body
    if (!request.getBody().isPresent()) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                        .body("Document not found.")
                        .build();
    } 
    else {
        // return JSON from to the client
        // Generate document
        final ToDoItem body = request.getBody().get();
        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(body)
                        .build();
    }
}
```

---

## <a name="trigger---attributes"></a>Utlösare - attribut

I [ C# klass bibliotek](functions-dotnet-class-library.md) och Java är `HttpTrigger` attributet tillgängligt för att konfigurera funktionen.

Du kan ställa in en autentiseringsnivå och tillåta HTTP-metoder i parametrar för attributhierarkier, webhook-typ och en Route-mall. Mer information om dessa inställningar finns i avsnittet om [Utlösar-konfiguration](#trigger---configuration).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Det här exemplet visar hur du använder attributet [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Ett fullständigt exempel finns i [utlösaren exempel](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

Det här exemplet visar hur du använder attributet [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) .

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Ett fullständigt exempel finns i [utlösaren exempel](#trigger---example).

---

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `HttpTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
| **typ** | Ej tillämpligt| Required-måste anges till `httpTrigger`. |
| **riktning** | Ej tillämpligt| Required-måste anges till `in`. |
| **Namn** | Ej tillämpligt| Obligatoriskt – variabel namnet som används i funktions koden för begäran eller begär ande texten. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Bestämmer vilka nycklar, om sådana, som måste finnas på begäran för att kunna anropa funktionen. Behörighets nivån kan vara något av följande värden: <ul><li><code>anonymous</code>&mdash;ingen API-nyckel krävs.</li><li><code>function</code>&mdash;en Function-Specific API-nyckel krävs. Detta är standardvärdet om inget anges.</li><li><code>admin</code>&mdash;huvud nyckeln måste anges.</li></ul> Mer information finns i avsnittet om [auktoriseringsregler](#authorization-keys). |
| **methods** |**Methods** | En matris med de HTTP-metoder som funktionen svarar på. Om inget anges svarar funktionen på alla HTTP-metoder. Se [Anpassa http-slutpunkten](#customize-the-http-endpoint). |
| **route** | **Route** | Definierar den vägfil som styr till vilka URL-adresser din funktion svarar. Standardvärdet om inget värde anges är `<functionname>`. Mer information finns i [Anpassa http-slutpunkten](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Stöds endast för version 1. x-körningsmiljön._<br/><br/>Konfigurerar HTTP-utlösaren så att den fungerar som en [webhook](https://en.wikipedia.org/wiki/Webhook) -mottagare för den angivna providern. Ange inte egenskapen `methods` om du anger den här egenskapen. Webhook-typen kan vara något av följande värden:<ul><li><code>genericJson</code>&mdash;en allmän webhook-slutpunkt utan logik för en speciell Provider. Den här inställningen begränsar begär anden till endast de som använder HTTP POST och med innehålls typen `application/json`.</li><li><code>github</code>&mdash;funktionen svarar på GitHub- [webhookar](https://developer.github.com/webhooks/). Använd inte egenskapen _authLevel_ med GitHub-webhookar. Mer information finns i avsnittet GitHub Webhooks senare i den här artikeln.</li><li><code>slack</code>&mdash;funktionen svarar på slack- [webhookar](https://api.slack.com/outgoing-webhooks). Använd inte _authLevel_ -egenskapen med slack-webhookar. Mer information finns i avsnittet om slack-webhookar längre fram i den här artikeln.</li></ul>|

## <a name="trigger---usage"></a>Utlösare - användning

Indatatypen trigger deklareras som antingen `HttpRequest` eller en anpassad typ. Om du väljer `HttpRequest`får du fullständig åtkomst till objektet Request. För en anpassad typ försöker körningen parsa JSON-begärantext för att ange objekt egenskaperna.

### <a name="customize-the-http-endpoint"></a>Anpassa HTTP-slutpunkten

Som standard när du skapar en funktion för en HTTP-utlösare kan funktionen adresseras med en Route i formuläret:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Du kan anpassa den här vägen med hjälp av den valfria `route`-egenskapen i HTTP-utlösaren inkommande bindning. Som exempel definierar följande *Function. JSON* -fil en `route`-egenskap för en http-utlösare:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Med den här konfigurationen kan funktionen nu adresseras med följande väg i stället för den ursprungliga vägen.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Detta gör att funktions koden stöder två parametrar i adress, _kategori_ och _ID_.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Du kan använda alla [begränsningar för webb-API-routning](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) med parametrarna. Följande C# funktions kod använder båda parametrarna.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Du kan använda alla [begränsningar för webb-API-routning](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) med parametrarna. Följande C# funktions kod använder båda parametrarna.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I Node tillhandahåller Functions-körningen begär ande texten från `context`-objektet. Mer information finns i exempel på [JavaScript-utlösare](#trigger---example).

I följande exempel visas hur du läser väg parametrar från `context.bindingData`.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Kontexten för körning av funktioner exponeras via en parameter som har deklarerats som `func.HttpRequest`. Den här instansen gör att en funktion kan komma åt data väg parametrar, fråga sträng värden och metoder som gör att du kan returnera HTTP-svar.

När det har definierats är väg parametrarna tillgängliga för funktionen genom att anropa metoden `route_params`.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Funktions körnings kontexten är egenskaper som deklarerats i `HttpTrigger`-attributet. Med attributet kan du definiera väg parametrar, auktoriseringsregler, HTTP-verb och instansen för inkommande begäran.

Väg parametrar definieras via `HttpTrigger`-attributet.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Som standard har alla funktions vägar prefixet med *API*. Du kan också anpassa eller ta bort prefixet med hjälp av egenskapen `http.routePrefix` i [Host. JSON](functions-host-json.md) -filen. I följande exempel tas *API* -väg-prefixet bort med en tom sträng för prefixet i *Host. JSON* -filen.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="using-route-parameters"></a>Använda väg parametrar

Väg parametrar definierade en funktions `route` mönster är tillgängliga för varje bindning. Om du till exempel har en väg definierad som `"route": "products/{id}"` kan en tabell lagrings bindning använda värdet för parametern `{id}` i bindnings konfigurationen.

Följande konfiguration visar hur `{id}`-parametern skickas till bindningens `rowKey`.

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```


### <a name="working-with-client-identities"></a>Arbeta med klient identiteter

Om din Function-app använder [App Service autentisering/auktorisering](../app-service/overview-authentication-authorization.md)kan du Visa information om autentiserade klienter från din kod. Den här informationen är tillgänglig som [begär ande huvuden som injiceras av plattformen](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Du kan också läsa denna information från data bindnings data. Den här funktionen är endast tillgänglig för functions-körningen i 2. x och högre. Det är också för närvarande endast tillgängligt för .NET-språk.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Information om autentiserade klienter är tillgänglig som en [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal är tillgänglig som en del av kontexten för begäran som visas i följande exempel:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternativt kan ClaimsPrincipal bara inkluderas som en ytterligare parameter i funktions under skriften:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Information om autentiserade klienter är tillgänglig som en [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal är tillgänglig som en del av kontexten för begäran som visas i följande exempel:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternativt kan ClaimsPrincipal bara inkluderas som en ytterligare parameter i funktions under skriften:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Den autentiserade användaren är tillgänglig via [http-huvuden](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="pythontabpython"></a>[Python](#tab/python)

Den autentiserade användaren är tillgänglig via [http-huvuden](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="javatabjava"></a>[Java](#tab/java)

Den autentiserade användaren är tillgänglig via [http-huvuden](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

### <a name="authorization-keys"></a>Auktoriseringsarkiv

Med funktioner kan du använda nycklar för att göra det svårare att komma åt HTTP-funktionerna under utvecklingen.  En standard-HTTP-utlösare kan kräva att en sådan API-nyckel finns i begäran. 

> [!IMPORTANT]
> Även om nycklar kan hjälpa dig att obfuscate dina HTTP-slutpunkter under utvecklingen, är de inte avsedda som ett sätt att skydda en HTTP-utlösare i produktion. Mer information finns i [skydda en HTTP-slutpunkt i produktion](#secure-an-http-endpoint-in-production).

> [!NOTE]
> I funktionerna 1. x runtime kan webhook-providers använda nycklar för att auktorisera begär Anden på olika sätt, beroende på vad providern stöder. Detta beskrivs i [Webhooks och Keys](#webhooks-and-keys). Functions-körningen i version 2. x och högre inkluderar inte inbyggt stöd för webhook-providrar.

Det finns två typer av nycklar:

* **Värd nycklar**: de här nycklarna delas av alla funktioner i Function-appen. När den används som en API-nyckel tillåter dessa åtkomst till alla funktioner i Function-appen.
* **Funktions tangenter**: dessa nycklar gäller endast för de funktioner som de är definierade under. När den används som en API-nyckel tillåter dessa endast åtkomst till den funktionen.

Varje nyckel namnges som referens och det finns en standard nyckel (med namnet "standard") på funktion-och värdnivå. Funktions tangenter prioriteras framför värd nycklar. När två nycklar definieras med samma namn används alltid funktions nyckeln.

Varje Function-app har också en särskild **huvud nyckel**. Den här nyckeln är en värd nyckel med namnet `_master`, som ger administrativ åtkomst till körnings-API: erna. Den här nyckeln kan inte återkallas. När du ställer in en autentiseringsnivå för `admin`måste begär Anden använda huvud nyckeln. andra viktiga resultat vid auktoriseringsfel.

> [!CAUTION]  
> På grund av de utökade behörigheterna i din Function-app som beviljats av huvud nyckeln bör du inte dela den här nyckeln med tredje part eller distribuera den i interna klient program. Var försiktig när du väljer administratörens autentiseringsnivå.

### <a name="obtaining-keys"></a>Hämta nycklar

Nycklar lagras som en del av din Function-app i Azure och är krypterade i vila. Om du vill visa dina nycklar, skapa nya eller rulla nycklar till nya värden, navigerar du till någon av dina HTTP-utlösta funktioner i [Azure Portal](https://portal.azure.com) och väljer **Hantera**.

![Hantera funktions nycklar i portalen.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Du kan få funktions nycklar genom programmering med hjälp av [API: er för nyckel hantering](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

### <a name="api-key-authorization"></a>Auktorisering av API-nyckel

De flesta mallar för HTTP-utlösare kräver en API-nyckel i begäran. Så din HTTP-begäran normalt ser ut som följande URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Nyckeln kan ingå i en frågesträng med namnet `code`, som ovan. Den kan också ingå i ett `x-functions-key` HTTP-huvud. Nyckelns värde kan vara vilken funktions nyckel som helst som har definierats för funktionen eller vilken värd nyckel som helst.

Du kan tillåta anonyma begär Anden som inte kräver nycklar. Du kan också kräva att huvud nyckeln ska användas. Du ändrar standard nivån för autentisering med hjälp av egenskapen `authLevel` i bindnings-JSON. Mer information finns i avsnittet om [Utlösar-konfiguration](#trigger---configuration).

> [!NOTE]
> När du kör funktioner lokalt är auktorisering inaktiverat oavsett vilken inställning för autentiseringsnivå som anges. När du har publicerat till Azure tillämpas `authLevel`s inställningen i utlösaren. Nycklar krävs fortfarande när de körs [lokalt i en behållare](functions-create-function-linux-custom-image.md#run-the-image-locally).


### <a name="secure-an-http-endpoint-in-production"></a>Skydda en HTTP-slutpunkt i produktion

Om du vill skydda funktions slut punkter i produktion fullständigt bör du överväga att implementera någon av följande funktioner på App-nivå:

* Aktivera App Service autentisering/auktorisering för din Function-app. App Services plattformen gör det möjligt att använda Azure Active Directory (AAD) och flera identitets leverantörer från tredje part för att autentisera klienter. Du kan använda detta för att implementera anpassade auktoriseringsregler för dina funktioner och du kan arbeta med användar information från funktions koden. Mer information finns i [autentisering och auktorisering i Azure App Service](../app-service/overview-authentication-authorization.md) och [arbeta med klient identiteter](#working-with-client-identities).

* Använd Azure API Management (APIM) för att autentisera begär Anden. APIM tillhandahåller flera olika API-säkerhetsalternativ för inkommande begär Anden. Läs mer i [API Management autentiseringsprinciper](../api-management/api-management-authentication-policies.md). Med APIM på plats kan du konfigurera Function-appen så att den endast accepterar begär Anden från IP-adressen för din APIM-instans. Mer information finns i [begränsningar för IP-adresser](ip-addresses.md#ip-address-restrictions).

* Distribuera din Function-app till en Azure App Service-miljön (ASE). ASE tillhandahåller en dedikerad värd miljö där funktionerna ska köras. Med ASE kan du konfigurera en enda klient dels-gateway som du kan använda för att autentisera alla inkommande begär Anden. Mer information finns i [Konfigurera en brand vägg för webbaserade program (WAF) för App Service-miljön](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

När du använder någon av dessa säkerhets metoder på App-nivå bör du ställa in den HTTP-utlösta funktions verifierings nivån på `anonymous`.

### <a name="webhooks"></a>Webhooks

> [!NOTE]
> Webhook-läge är bara tillgängligt för version 1. x av Functions-körningen. Den här ändringen gjordes för att förbättra prestanda för HTTP-utlösare i version 2. x och högre.

I version 1. x tillhandahåller webhook-mallar ytterligare verifiering för webhook-nyttolaster. I version 2. x och högre fungerar bas-HTTP-utlösaren fortfarande och är den rekommenderade metoden för Webhooks. 

#### <a name="github-webhooks"></a>GitHub-webhookar

Om du vill svara på GitHub Webhooks måste du först skapa en funktion med en HTTP-utlösare och ange egenskapen **webHookType** till `github`. Kopiera sedan dess URL och API-nyckel till sidan **Lägg till webhook** i GitHub-lagringsplatsen. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Slack-webhookar

Slack-webhooken genererar en token åt dig i stället för att låta dig ange den, så du måste konfigurera en funktions bestämd nyckel med token från slack. Se [auktoriseringsregler](#authorization-keys).

### <a name="webhooks-and-keys"></a>Webhooks och nycklar

Webhook-auktoriseringen hanteras av komponenten webhook receiver, en del av HTTP-utlösaren och mekanismen varierar beroende på typen av webhook. Varje mekanism förlitar sig på en nyckel. Som standard används funktions nyckeln med namnet "default". Om du vill använda en annan nyckel konfigurerar du webhook-providern för att skicka nyckel namnet med begäran på något av följande sätt:

* **Frågesträng**: providern överför nyckel namnet i parametern `clientid` frågesträng, till exempel `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Begär ande huvud**: providern överför nyckel namnet i `x-functions-clientid`s huvudet.

## <a name="trigger---limits"></a>Utlösare – gränser

Längden på HTTP-begäran är begränsad till 100 MB (104 857 600 byte) och URL-längden är begränsad till 4 KB (4 096 byte). De här gränserna anges av `httpRuntime`-elementet i [filen Web. config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)för körning.

Om en funktion som använder HTTP-utlösaren inte slutförs inom cirka 2,5 minuter, kommer gatewayen att gå ut och returnera ett HTTP 502-fel. Funktionen fortsätter att köras men kan inte returnera ett HTTP-svar. För långvariga funktioner rekommenderar vi att du följer asynkrona mönster och returnerar en plats där du kan pinga status för begäran. Information om hur länge en funktion kan köras finns i [plan för skalning och värd förbrukning](functions-scale.md#timeout).

## <a name="output"></a>Resultat

Använd bindningen för HTTP-utdata för att svara på avsändaren av HTTP-begäran. Den här bindningen kräver en HTTP-utlösare och du kan anpassa svaret som associeras med utlösarens begäran. Om ingen HTTP-databindning har angetts returnerar en HTTP-utlösare HTTP 200 OK med en tom brödtext i funktionerna 1. x eller HTTP 204 inget innehåll med en tom brödtext i funktionerna 2. x och högre.

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras de egenskaper för bindnings konfigurationen som du anger i filen *Function. JSON* . För C# klass bibliotek finns det inga egenskaper för attribut som motsvarar dessa *funktioner. JSON* -egenskaper.

|Egenskap  |Beskrivning  |
|---------|---------|
| **typ** |Måste anges till `http`. |
| **riktning** | Måste anges till `out`. |
| **Namn** | Variabel namnet som används i funktions koden för svaret, eller `$return` att använda returvärdet. |

## <a name="output---usage"></a>Utdata - användning

Om du vill skicka ett HTTP-svar använder du svars mönster för språk standard. I C# eller C# skript, gör du funktions retur typen `IActionResult` eller `Task<IActionResult>`. I C#, krävs inget attribut för retur värde.

Till exempel svar, se [utlösaren exempel](#trigger---example).

## <a name="hostjson-settings"></a>Host.JSON-inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exemplet Host. JSON-filen nedan innehåller bara version 2. x +-inställningarna för den här bindningen. Mer information om globala konfigurations inställningar i version 2. x och mer finns i [Host. JSON-referens för Azure Functions](functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x, se [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
| customHeaders|ingen|Gör att du kan ange anpassade rubriker i HTTP-svaret. I föregående exempel läggs `X-Content-Type-Options`-rubriken till i svaret för att undvika innehålls typs avlyssning. |
|dynamicThrottlesEnabled|Sant<sup>\*</sup>|När den här inställningen är aktive rad kommer pipelinen för bearbetning av begär Anden att regelbundet kontrol lera system prestanda räknare som anslutningar/trådar/processer/minne/processor/osv. om någon av dessa räknare är över en inbyggd hög tröskel (80%), avvisas begär Anden med en 429 "upptagen"-svar tills räknarna återgår till normala nivåer.<br/><sup>\*</sup> Standardvärdet i en förbruknings plan är `true`. Standardvärdet i en dedikerad plan är `false`.|
|HSTS|inte aktiverat|När `isEnabled` är inställt på `true`tillämpas [HSTS-beteendet (http Strict Transport Security) för .net Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) enligt definitionen i [`HstsOptions`-klassen](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). Exemplet ovan ställer också in [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) -egenskapen på 10 dagar. Följande egenskaper stöds för `hsts`: <table><tr><th>Egenskap</th><th>Beskrivning</th></tr><tr><td>excludedHosts</td><td>En sträng mat ris med värd namn som HSTS-huvudet inte har lagts till för.</td></tr><tr><td>includeSubDomains</td><td>Booleskt värde som anger om parametern includeSubDomain för rubriken Strict-Transport-Security är aktive rad.</td></tr><tr><td>maxAge</td><td>Sträng som definierar Max-ålders parametern för huvudet Strict-Transport-Security.</td></tr><tr><td>preload</td><td>Booleskt värde som anger om preload-parametern för ett strikt-Transport-Security-huvud är aktive rad.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Maximalt antal http-funktioner som körs parallellt. På så sätt kan du kontrol lera samtidighet, vilket kan hjälpa dig att hantera resursutnyttjande. Du kan till exempel ha en http-funktion som använder många system resurser (minne/processor/Sockets) så att den orsakar problem när samtidigheten är för hög. Eller så kanske du har en funktion som gör utgående begär anden till en tjänst från tredje part, och dessa anrop måste vara begränsade. I dessa fall kan det hjälpa att tillämpa en begränsning. <br/><sup>*</sup> Standardvärdet för en förbruknings plan är 100. Standardvärdet för en dedikerad plan är obundet (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|Det maximala antalet väntande begär Anden som innehas vid en specifik tidpunkt. Den här gränsen omfattar begär Anden som har placerats i kö, men som inte har börjat köras, samt alla pågående körningar. Inkommande begär anden över den här gränsen avvisas med ett svar på 429 "för upptagen". Det gör det möjligt för anropare att använda tidsbaserade återförsöks strategier och hjälper dig också att kontrol lera maximal fördröjning för begäran. Detta styr endast köer som inträffar inom skript värdens körnings Sök väg. Andra köer, t. ex. ASP.NET, kommer fortfarande att gälla och påverkas inte av den här inställningen. <br/><sup>\*</sup>\The standard för en förbruknings plan är 200. Standardvärdet för en dedikerad plan är obundet (`-1`).|
|routePrefix|api|Det väg-prefix som gäller för alla vägar. Använd en tom sträng för att ta bort standardprefixet. |


## <a name="next-steps"></a>Nästa steg

[Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
