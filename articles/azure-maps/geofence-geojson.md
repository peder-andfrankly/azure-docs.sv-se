---
title: Geografiskt data format för geografiskt JSON i Azure Maps | Microsoft Docs
description: Lär dig mer om ett geografiskt JSON-dataformat i Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5946180c161a38a30f44e235ce0b626fd70a5400
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735131"
---
# <a name="geofencing-geojson-data"></a>Polystaket, polyjson-data

Med API: erna Azure Maps [Hämta gräns](/rest/api/maps/spatial/getgeofence) -och [postbaserade gränser](/rest/api/maps/spatial/postgeofence) kan du hämta närhet av en koordinat i förhållande till ett givet geografiskt avgränsnings tecken eller en uppsättning avgränsningar. Den här artikeln beskriver hur du förbereder de avgränsnings data som kan användas i Azure Maps GET-och POST-API.

Data för avgränsning eller uppsättning av `Feature` gränser representeras av objekt och `FeatureCollection` objekt i `GeoJSON` format, som definieras i [rfc7946](https://tools.ietf.org/html/rfc7946). Förutom det:

* Objekt typen för typen interjson kan vara `Feature` ett objekt eller `FeatureCollection` ett objekt.
* Objekt typen `Point`Geometry kan vara a `LineString`, `MultiPoint` `MultiLineString` `Polygon` `GeometryCollection`,,,, och. `MultiPolygon`
* Alla funktions egenskaper måste innehålla en `geometryId`, som används för att identifiera den här gränsen.
* Funktionen med `Point`, `MultiPoint`, `LineString` ,måsteinnehålla`radius`iegenskaper. `MultiLineString` `radius`värdet mäts i meter, `radius` värdet sträcker sig från 1 till 10000.
* Funktionen med `polygon` och `multipolygon` geometri typen har ingen RADIUS-egenskap.
* `validityTime`är en valfri egenskap som låter användaren ange utgångna tid och giltighets tid för de olika avgränsnings data. Om detta inte anges upphör data aldrig att gälla och är alltid giltigt.
* `expiredTime` Är förfallo datum och-tid för avgränsning av data. Om värdet för `userTime` i begäran är senare än det här värdet betraktas motsvarande avgränsnings data som utgångna data och inte efter frågas. Då kommer geometryId för dessa avgränsnings data att ingå i `expiredGeofenceGeometryId` matrisen inom ett gräns värde.
* `validityPeriod` Är en lista över tids perioden för giltighets tiden för den här gränsen. Om värdet för `userTime` i begäran faller utanför giltighets perioden betraktas motsvarande avgränsnings information som ogiltig och kommer inte att frågas. GeometryId för dessa avgränsnings data ingår i `invalidPeriodGeofenceGeometryId` matrisen inom ett gräns värde. I följande tabell visas egenskaperna för validityPeriod-elementet.

| Name | type | Obligatoriskt  | Beskrivning |
| :------------ |:------------: |:---------------:| :-----|
| startTime | DateTime  | true | Start datum tiden för giltighets tids perioden. |
| endTime   | DateTime  | true |  Slutdatumet för giltighets tids perioden. |
| recurrenceType | sträng | false |   Periodens upprepnings typ. Värdet `Daily`kan vara `Weekly` ,`Monthly`, eller `Yearly`. Standardvärdet `Daily`är.|
| businessDayOnly | Boolesk | false |  Ange om data endast är giltiga under arbets dagar. Standardvärdet `false`är.|


* Alla koordinater-värden visas som [longitud, Latitude] definierade `WGS84`i.
* För varje funktion, som innehåller `MultiPoint`, `MultiLineString`, `MultiPolygon` eller `GeometryCollection`, används egenskaperna för alla element. exempel: Alla punkter i `MultiPoint` använder samma radie för att bilda ett geografiskt stängsel.
* I punkt-cirkel-scenariot kan en cirkel geometri representeras `Point` med hjälp av ett Geometry-objekt med egenskaper som har [utökats i utökning av Geometries](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Följande är en exempel begär ande text för ett område som representeras av en cirkels `GeoJSON` geometriska geometri i med hjälp av en mitt punkt och en radie. Den giltiga tids perioden för avgränsnings data börjar från 2018-10-22, 9 till 17, upprepas varje dag förutom helgen. `expiredTime`indikerar att dessa avgränsnings data kommer att anses vara förfallna, om `userTime` i begäran är senare än. `2019-01-01`  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
