---
title: Så här lägger du till blobar till objekt – Azure Digitals flätade | Microsoft Docs
description: Lär dig hur du lägger till blobbar till användare, enheter och utrymmen i Azure Digitals.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2019
ms.custom: seodec18
ms.openlocfilehash: 6ab9d0ae07978e69bebb0fc24c8965cce971cfd5
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082350"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Lägga till blobbar till objekt i Azure Digitals, dubbla

Blobbar är ostrukturerade representationer av vanliga filtyper, t. ex. bilder och loggar. Blobbar spårar vilken typ av data de representerar genom att använda en MIME-typ (till exempel: "bild/JPEG") och metadata (namn, beskrivning, typ och så vidare).

Azure Digitals dubbla enheter stöder koppling av blobbar till enheter, utrymmen och användare. Blobbar kan representera en profil bild för en användare, ett enhets fotografi, en video, en karta, en zip-fil för inbyggd program vara, JSON-data, en logg osv.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Översikt över uppladdning av blobbar

Du kan använda multipart-begäranden för att ladda upp blobbar till vissa slut punkter och deras respektive funktioner.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob-metadata

Förutom **Content-Type** och **Content-disposition**måste Azure Digitals, dubbla BLOB-flerdelade begär Anden ange rätt JSON-brödtext. Vilken JSON-text som ska skickas beror på vilken typ av HTTP-begäran som utförs.

De fyra huvudsakliga JSON-scheman är:

[![JSON-scheman](media/how-to-add-blobs/blob-models-img.png)](media/how-to-add-blobs/blob-models-img.png#lightbox)

JSON-BLOB-metadata följer följande modell:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Attribut | Typ | Beskrivning |
| --- | --- | --- |
| **parentId** | Sträng | Den överordnade entiteten för att associera blobben med (utrymmen, enheter eller användare) |
| **name** |Sträng | Ett människo eget namn för blobben |
| **typ** | Sträng | Typ av BLOB-kan inte använda *typ* och *typeId*  |
| **typeId** | Integer | Blob-typ-ID: t kan inte använda *typ* och *typeId* |
| **subtype** | Sträng | BLOB-undertypen-det går inte att använda *undertyp* och *subtypeId* |
| **subtypeId** | Integer | Undertyp-ID för blobben-kan inte använda *undertyp* -och *subtypeId* |
| **description** | Sträng | Anpassad Beskrivning av blobben |
| **sharing** | Sträng | Om blobben kan delas med [`None``Tree``Global`] |

BLOB-metadata anges alltid som det första segmentet med **innehålls typ** `application/json` eller som en `.json` fil. Fildata anges i det andra segmentet och kan vara av alla MIME-typer som stöds.

Swagger-dokumentationen beskriver dessa modell scheman i fullständig detalj.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Lär dig mer om att använda referens dokumentationen genom att läsa om [hur du använder Swagger](./how-to-use-swagger.md).

### <a name="blobs-response-data"></a>BLOB-svars data

Individuellt returnerade blobbar följer följande JSON-schema:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Attribut | Typ | Beskrivning |
| --- | --- | --- |
| **ID** | Sträng | Unikt ID för blobben |
| **name** |Sträng | Ett människo eget namn för blobben |
| **parentId** | Sträng | Den överordnade entiteten för att associera blobben med (utrymmen, enheter eller användare) |
| **typ** | Sträng | Typ av BLOB-kan inte använda *typ* och *typeId*  |
| **typeId** | Integer | Blob-typ-ID: t kan inte använda *typ* och *typeId* |
| **subtype** | Sträng | BLOB-undertypen-det går inte att använda *undertyp* och *subtypeId* |
| **subtypeId** | Integer | Undertyp-ID för blobben-kan inte använda *undertyp* -och *subtypeId* |
| **sharing** | Sträng | Om blobben kan delas med [`None``Tree``Global`] |
| **description** | Sträng | Anpassad Beskrivning av blobben |
| **contentInfos** | Matris | Anger ostrukturerad metadatainformation, inklusive version |
| **fullName** | Sträng | Det fullständiga namnet på blobben |
| **spacePaths** | Sträng | Utrymmes Sök väg |

BLOB-metadata anges alltid som det första segmentet med **innehålls typ** `application/json` eller som en `.json` fil. Fildata anges i det andra segmentet och kan vara av alla MIME-typer som stöds.

### <a name="blob-multipart-request-examples"></a>Exempel på BLOB multipart-begäran

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Om du vill ladda upp en textfil som en blob och associera den med ett blank steg gör du en autentiserad HTTP POST-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Med följande text:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Värde | Ersätt med |
| --- | --- |
| USER_DEFINED_BOUNDARY | Namn på en flerdelade innehålls gränser |

Följande kod är en .NET-implementering av samma BLOB-uppladdning med klassen [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Slutligen kan [användarna i](https://curl.haxx.se/) form av en multipart-begäran på samma sätt:

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Värde | Ersätt med |
| --- | --- |
| YOUR_TOKEN | Din giltiga OAuth 2,0-token |
| YOUR_SPACE_ID | ID för utrymmet att associera blobben med |
| PATH_TO_FILE | Sökvägen till text filen |

[exempel på ![-spiral](media/how-to-add-blobs/curl-img.png)](media/how-to-add-blobs/curl-img.png#lightbox)

Ett lyckat inlägg returnerar ID: t för den nya blobben.

## <a name="api-endpoints"></a>API-slutpunkter

I följande avsnitt beskrivs de grundläggande BLOB-relaterade API-slutpunkterna och deras funktioner.

### <a name="devices"></a>Enheter

Du kan koppla blobbar till enheter. Följande bild visar Swagger-referens dokumentationen för dina hanterings-API: er. Den anger enhets-relaterade API-slutpunkter för BLOB-konsumtion och eventuella obligatoriska Sök vägs parametrar som ska skickas till dem.

[![enhets-blobbar](media/how-to-add-blobs/blobs-device-api-img.png)](media/how-to-add-blobs/blobs-device-api-img.png#lightbox)

Om du till exempel vill uppdatera eller skapa en blob och bifoga blobben till en enhet gör du en autentiserad HTTP-PATCH-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Det önskade BLOB-ID: t |

Lyckade förfrågningar returnerar ett JSON-objekt enligt [beskrivningen ovan](#blobs-response-data).

### <a name="spaces"></a>Områdena

Du kan också bifoga blobbar till blank steg. Följande bild visar en lista över alla blank stegs-API-slutpunkter som ansvarar för hantering av blobbar. Den visar också alla Sök vägs parametrar som skickas till dessa slut punkter.

[![utrymmes blobbar](media/how-to-add-blobs/blobs-space-api-img.png)](media/how-to-add-blobs/blobs-space-api-img.png#lightbox)

Om du till exempel vill returnera en blob som är ansluten till ett utrymme gör du en autentiserad HTTP GET-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Det önskade BLOB-ID: t |

Lyckade förfrågningar returnerar ett JSON-objekt enligt [beskrivningen ovan](#blobs-response-data).

En PATCH-begäran till samma slut punkt uppdaterar metadata-beskrivningar och skapar versioner av blobben. HTTP-begäran görs via KORRIGERINGs metoden, tillsammans med alla nödvändiga meta-och multiform-formulär data.

### <a name="users"></a>Användare

Du kan koppla blobbar till användar modeller (till exempel för att associera en profil bild). Följande bild visar relevanta användar-API-slutpunkter och eventuella obligatoriska Sök vägs parametrar, t. ex. `id`:

[![användar-blobar](media/how-to-add-blobs/blobs-users-api-img.png)](media/how-to-add-blobs/blobs-users-api-img.png#lightbox)

Om du till exempel vill hämta en blob som är kopplad till en användare gör du en autentiserad HTTP GET-begäran med alla nödvändiga formulär data för att:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Det önskade BLOB-ID: t |

Lyckade förfrågningar returnerar ett JSON-objekt enligt [beskrivningen ovan](#blobs-response-data).

## <a name="common-errors"></a>Vanliga fel

Ett vanligt fel innebär att du inte anger rätt rubrik information:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Du kan lösa det här felet genom att kontrol lera att den övergripande begäran har ett lämpligt **innehålls typs** huvud:

* `multipart/mixed`
* `multipart/form-data`

Kontrol lera också att varje multipart-segment har en motsvarande **innehålls typ** efter behov.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Swagger Reference documentation för Azure Digitals, läser du [använda Azure Digitals dubbla Swagger](how-to-use-swagger.md).

- Om du vill överföra blobbar via Postman läser [du så här konfigurerar du Postman](./how-to-configure-postman.md).