---
title: Filtrering, sortering och sid indelning av Media Services entiteter
titleSuffix: Azure Media Services
description: Lär dig mer om filtrering, sortering och växling av Azure Media Services entiteter.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 22b8c4e2454d6130ebcaf85346b767c843fbc1f0
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186249"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtrering, sortering och sid indelning av Media Services entiteter

I det här avsnittet beskrivs OData-frågealternativen och sid brytnings stöd som är tillgängligt när du visar Azure Media Services v3-entiteter.

## <a name="considerations"></a>Överväganden

* Egenskaper för entiteter som är av `Datetime` typen är alltid i UTC-format.
* Blank steg i frågesträngen ska vara URL-kodat innan du skickar en begäran.

## <a name="comparison-operators"></a>Jämförelseoperatorer

Du kan använda följande operatorer för att jämföra ett fält med ett konstant värde:

Likhets operatorer:

- `eq`: testa om ett fält är *lika* med ett konstant värde.
- `ne`: testa om ett fält *inte är lika* med ett konstant värde.

Intervall operatorer:

- `gt`: testa om ett fält är *större än* ett konstant värde.
- `lt`: testa om ett fält är *mindre än* ett konstant värde.
- `ge`: testa om ett fält är *större än eller lika* med ett konstant värde.
- `le`: testa om ett fält är *mindre än eller lika* med ett konstant värde.

## <a name="filter"></a>Filter

Använd `$filter` för att ange en OData filter-parameter för att bara hitta de objekt som du är intresse rad av.

Följande REST-exempel filtrerar på `alternateId` värde för en till gång:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Följande C# exempel filtrerar på till gångens skapade datum:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Sortera efter

Använd `$orderby` för att sortera de returnerade objekten med den angivna parametern. Till exempel:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Om du vill sortera resultaten i stigande eller fallande ordning lägger du till antingen `asc` eller `desc` till fält namnet, avgränsat med ett blank steg. Till exempel: `$orderby properties/created desc`.

## <a name="skip-token"></a>Hoppa över token

Om ett fråge svar innehåller många objekt returnerar tjänsten ett `$skiptoken` (`@odata.nextLink`)-värde som du använder för att hämta nästa sida med resultat. Använd den för att bläddra igenom hela resultat uppsättningen.

I Media Services v3 kan du inte konfigurera sid storleken. Sid storleken varierar beroende på typ av entitet. Läs de enskilda avsnitten som följer efter information.

Om entiteter skapas eller tas bort när du växlar genom samlingen visas ändringarna i de returnerade resultaten (om dessa ändringar finns i den del av samlingen som inte har hämtats).

> [!TIP]
> Du bör alltid använda `nextLink` för att räkna upp samlingen och inte är beroende av en viss sid storlek.
>
> `nextLink`-värdet är bara tillgängligt om det finns mer än en sida med entiteter.

Överväg följande exempel där `$skiptoken` används. Se till att ersätta *amstestaccount* med ditt konto namn och ange värdet för *API-version* till den senaste versionen.

Om du begär en lista över till gångar som detta:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Du får tillbaka ett svar som liknar detta:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Sedan kan du begära nästa sida genom att skicka en get-begäran för:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

I följande C# exempel visas hur du räknar upp igenom alla strömmande positioner i kontot.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Använda logiska operatorer för att kombinera frågealternativ

Media Services v3 stöder **eller** och **och** logiska operatorer. 

Följande REST-exempel kontrollerar jobbets status:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Du skapar samma fråga i C# så här: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Filtrerings-och sorterings alternativ för entiteter

Följande tabell visar hur du kan använda filtrerings-och sorterings alternativen för olika entiteter:

|Entitetsnamn|Egenskapsnamn|Filter|Beställa|
|---|---|---|---|
|[Tillgångar](https://docs.microsoft.com/rest/api/media/assets/)|namn|`eq`, `gt`, `lt`, `ge`, `le`|`asc` och `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||Properties.Created| `eq`, `gt`, `lt`| `asc` och `desc`|
|[Principer för innehålls nyckel](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|namn|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||Properties.Created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||properties.policyId|`eq`, `ne`||
|[Jobb](https://docs.microsoft.com/rest/api/media/jobs)| namn  | `eq`            | `asc` och `desc`|
||egenskaper. State        | `eq`, `ne`        |                         |
||Properties.Created      | `gt`, `ge`, `lt`, `le`| `asc` och `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` och `desc`| 
|[Strömmande positionerare](https://docs.microsoft.com/rest/api/media/streaminglocators)|namn|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||Properties.Created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||egenskaper. slut tid    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
|[Strömmande principer](https://docs.microsoft.com/rest/api/media/streamingpolicies)|namn|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||Properties.Created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
|[Omvandlar](https://docs.microsoft.com/rest/api/media/transforms)| namn | `eq`            | `asc` och `desc`|
|| Properties.Created      | `gt`, `ge`, `lt`, `le`| `asc` och `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` och `desc`|

## <a name="next-steps"></a>Nästa steg

* [Lista till gångar](https://docs.microsoft.com/rest/api/media/assets/list)
* [Lista med innehålls nyckel principer](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Lista jobb](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Lista över strömmande principer](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Lista över strömmande positionerare](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Strömma en fil](stream-files-dotnet-quickstart.md)
* [Kvoter och begränsningar](limits-quotas-constraints.md)
