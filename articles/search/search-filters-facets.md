---
title: Aspekt filter för Sök navigering i appar
titleSuffix: Azure Cognitive Search
description: Filtrera villkor efter användarens säkerhets identitet, Geo-Location eller numeriska värden för att minska Sök resultaten för frågor i Azure Kognitiv sökning, en värdbaserad Sök tjänst i molnet på Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792902"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Så här skapar du ett aspekt filter i Azure Kognitiv sökning 

Fasettisk navigering används för självriktad filtrering på frågeresultat i en Sökapp där ditt program erbjuder GRÄNSSNITTs kontroller för omfångs sökning till grupper av dokument (till exempel kategorier eller varumärken) och Azure Kognitiv sökning tillhandahåller data strukturen för att få tillbaka upplevelsen. I den här artikeln går vi igenom de grundläggande stegen för att skapa en fasett-navigerings struktur som du kan använda för att återställa den Sök upplevelse som du vill tillhandahålla. 

> [!div class="checklist"]
> * Välj fält för filtrering och fasettering
> * Ange attribut för fältet
> * Bygga index och läsa in data
> * Lägga till fasett-filter till en fråga
> * Hantera resultat

Facets är dynamiska och returneras i en fråga. Sök svaren tar med dem de aspekt kategorier som används för att navigera i resultaten. Om du inte är bekant med FACET, är följande exempel en illustration av en aspekt navigerings struktur.

  ![](./media/search-filters-facets/facet-nav.png)

Är du nybörjare på att navigera och vill ha mer information? Se [hur du implementerar en aspektad navigering i Azure kognitiv sökning](search-faceted-navigation.md).

## <a name="choose-fields"></a>Välj fält

FACET kan beräknas över enskilda värde fält och samlingar. Fält som fungerar bäst i fasetter-navigeringen har låg kardinalitet: ett litet antal distinkta värden som upprepas i alla dokument i Sök sökkorpus (till exempel en lista över färger, länder/regioner eller märkes namn). 

Fasettering aktive ras baserat på fält när du skapar indexet genom att ange `facetable` attributet till `true`. Du bör vanligt vis också ange `filterable` attributet för att `true` för sådana fält så att ditt sökprogram kan filtrera på dessa fält baserat på FACET som slutanvändaren väljer. 

När du skapar ett index med hjälp av REST API markeras alla [fält typer](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) som kan användas i fasetten navigering som `facetable` som standard:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Numeriska fält typer: `Edm.Int32`, `Edm.Int64`, `Edm.Double`
+ Samlingar av ovanstående typer (till exempel `Collection(Edm.String)` eller `Collection(Edm.Double)`)

Du kan inte använda `Edm.GeographyPoint` eller `Collection(Edm.GeographyPoint)` fält i en fasett-navigering. FACET fungerar bäst på fält med låg kardinalitet. På grund av upplösningen av geo-koordinater är det sällsynt att två uppsättningar av co-koordinater kommer att vara identiska i en specifik data uppsättning. Därför stöds inte FACET för geo-koordinater. Du behöver ett stads-eller region fält för att fasetta efter plats.

## <a name="set-attributes"></a>Ange attribut

Indexera attribut som styr hur ett fält används läggs till i definitioner för enskilda fält i indexet. I följande exempel är fält med låg kardinalitet användbar för fasettering, består av: `category` (hotell, Motel, Hostel), `tags`och `rating`. Dessa fält har `filterable` och `facetable` attribut anges uttryckligen i följande exempel för att illustrera vad som är avsett. 

> [!Tip]
> Som bästa praxis för prestanda-och lagrings optimering kan du inaktivera fasettering för fält som aldrig ska användas som aspekt. I synnerhet bör sträng fält för unika värden, t. ex. ett ID eller ett produkt namn, anges till `"facetable": false` för att förhindra att deras oavsiktliga (och ineffektiva) användning används i en fasett-navigering.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Den här index definitionen kopieras från [skapa ett Azure kognitiv sökning-index med hjälp av REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Det är identiskt med undantag för ytliga skillnader i fält definitionerna. Attributen `filterable` och `facetable` läggs uttryckligen till i fälten `category`, `tags`, `parkingIncluded`, `smokingAllowed`och `rating`. I praktiken skulle `filterable` och `facetable` aktive ras som standard i dessa fält när du använder REST API. När du använder .NET SDK måste dessa attribut aktive ras explicit.

## <a name="build-and-load-an-index"></a>Bygga och läsa in ett index

Ett mellanliggande (och eventuellt uppenbart) steg är att du måste [bygga och fylla i indexet](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) innan du skapar en fråga. Vi nämner detta steg för att slutföra. Ett sätt att avgöra om indexet är tillgängligt är genom att kontrol lera listan index i [portalen](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Lägga till fasett-filter till en fråga

I program kod skapar du en fråga som anger alla delar av en giltig fråga, inklusive Sök uttryck, ansikts, filter, bedömnings profiler – allt som används för att formulera en begäran. I följande exempel skapas en begäran som skapar aspekt navigering baserat på typen av logi, klassificering och andra bekvämligheterna.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Returnera filtrerade resultat vid klicknings händelser

När slutanvändaren klickar på ett fasett-värde, ska hanteraren för händelsen Klickning använda ett filter uttryck för att använda ett filter uttryck för att realisera användarens avsikt. Med en `category` aspekt klickar du på kategorin "Motel" implementeras med ett `$filter` uttryck som väljer en typ av anpassningar. När en användare klickar på "Motel" för att ange att endast Motels ska visas, innehåller nästa fråga som programmet skickar `$filter=category eq 'motel'`.

Följande kodfragment lägger till kategori till filtret om en användare väljer ett värde från kategori aspekten.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Om användaren klickar på ett fasett-värde för ett samlings fält som `tags`, till exempel värdet "pool", ska programmet använda följande syntax för filter: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Tips och lösningar

### <a name="initialize-a-page-with-facets-in-place"></a>Initiera en sida med ansikte på plats

Om du vill initiera en sida med ansikte på plats kan du skicka en fråga som en del av sid initieringen för att dirigera sidan med en inledande aspekt struktur.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Bevara en aspekt navigerings struktur asynkront av filtrerade resultat

En av utmaningarna med aspekt navigering i Azure Kognitiv sökning är att Facet bara finns för aktuella resultat. I praktiken är det vanligt att behålla en statisk uppsättning Faces, så att användaren kan navigera i omvänd ordning, spåra steg för att utforska alternativa sökvägar med hjälp av Sök innehåll. 

Även om detta är ett vanligt användnings fall är det inte något som finns i aspekt navigerings strukturen för närvarande. Utvecklare som vill ha statiska FACET kan vanligt vis kringgå begränsningen genom att skicka två filtrerade frågor: en omfattning av resultaten, den andra som används för att skapa en statisk lista över fasetter för navigerings syfte.

## <a name="see-also"></a>Se också

+ [Filter i Azure Kognitiv sökning](search-filters.md)
+ [Skapa index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Sök dokument REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
