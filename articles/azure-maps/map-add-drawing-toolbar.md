---
title: Lägg till ett verktygsfält för ritning i Azure Maps | Microsoft Docs
description: Så här lägger du till ett verktygsfält för ritning i en karta med Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6bc754c9a4f333da85e57c5ad9780da8df93e895
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309809"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Lägga till ett verktygsfält för rit verktyg i en karta

Den här artikeln visar hur du använder modulen för rit verktyg och hur du visar verktygsfältet Rita på kartan. [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) -kontrollen lägger till verktygsfältet Rita i kartan. Du får lära dig hur du skapar kartor med bara ett och alla rit verktyg och hur du anpassar åter givningen av ritnings formerna i Drawing Manager.

## <a name="add-drawing-toolbar"></a>Lägg till rit verktygsfält

Följande kod skapar en instans av Drawing Manager och visar verktygsfältet på kartan.

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Nedan visas det fullständiga kod exemplet för funktionerna ovan:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lägg till rit verktygsfält" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>verktygsfältet Lägg till rit objekt</a> genom att Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Begränsa visade verktygsfälts alternativ

Följande kod skapar en instans av Drawing Manager och visar verktygsfältet med bara ett polygonverktyget rit verktyg på kartan. 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Nedan visas det fullständiga kod exemplet för funktionerna ovan:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lägg till ett rit verktyg för polygon" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se rit <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>verktyget Lägg till ett polygon-verktyg</a> för polygon<a href='https://codepen.io/azuremaps'>@azuremaps</a>genom Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Ändra ritnings åter givnings format

Följande kod hämtar åter givnings skikten från Drawing Manager och ändrar åter givnings formatet för ritningen genom att ändra deras alternativ. I det här fallet kommer punkter att återges med en blå markör ikon, linjerna är röda och fyra bild punkter breda, polygoner har en grön fyllnings färg och en orange kontur.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

Nedan visas det fullständiga kod exemplet för funktionerna ovan:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ändra ritnings åter givnings format" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>åter givnings formatet för Penn ändrings ritning</a> efter<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)