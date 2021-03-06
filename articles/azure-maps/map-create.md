---
title: Skapa en karta med Azure Maps | Microsoft Docs
description: Så här skapar du en karta med Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 932c244ff41e757413a05cde019ee7ee1a82232d
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976124"
---
# <a name="create-a-map"></a>Skapa en karta

Den här artikeln visar hur du skapar en karta och animerar en karta.  

## <a name="loading-a-map"></a>Läser in en karta

Om du vill läsa in en karta skapar du en ny instans av [kart klassen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Vid initieringen av kartan skickas ett DIV-element-ID för att återge kartan och en uppsättning alternativ som ska användas vid inläsning av kartan. Om standard information om autentisering inte anges i `atlas` namn området måste den här informationen anges i kart alternativen vid inläsning av kartan. Kartan läser in flera resurser asynkront för prestanda. Därför kan du när du har skapat kart instansen `ready` bifoga `load` en eller-händelse till kartan och sedan lägga till ytterligare kod som samverkar med kartan i den händelse hanteraren. `ready` Händelsen utlöses så snart kartan har tillräckligt med resurser för att kunna interagera med program mässigt. `load` Händelsen utlöses efter att den ursprungliga kart visningen har lästs in helt. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Grundläggande kart belastning" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se den <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>grundläggande kart</a> inläsningen från Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Flera Maps kan läsas in på samma sida och var och en kan använda samma eller olika inställningar för autentisering och språk.

## <a name="show-a-single-copy-of-the-world"></a>Visa en enskild kopia av världen

När kartan zoomas ut på en bred skärm visas flera kopior av världen vågrätt. Detta är bra för de flesta scenarier, men vissa för vissa program kan vara önskvärda att bara se en enda kopia av världen. Detta kan göras genom att ställa in alternativet `renderWorldCopies` Maps till `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = falskt" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Styra kart kameran

Det finns två sätt att ställa in det visade avsnittet i kartan med kameran. Du kan ange kamera alternativ som centrera och zooma när du läser in kartan, eller så anropar `setCamera` du alternativet när kartan har lästs in för att program mässigt uppdatera Map-vyn.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ange kamera

I följande kod skapas ett [kart objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) och alternativen för centrum och zoomning är inställda. Kart egenskaper som Center och zoomnings nivå är en del av [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Skapa en karta via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom att <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>skapa en karta `CameraOptions` via </a>Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ange kamerans gränser

I följande kod skapas ett [kart objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) via `new atlas.Map()`. Kart egenskaper som `CameraBoundsOptions` kan definieras via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) -funktionen för kart klassen. Egenskaper för gränser och utfyllnad anges med hjälp `setCamera`av.

<br/>

<iframe height='500' scrolling='no' title='Skapa en karta via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom att <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>skapa en karta `CameraBoundsOptions` via </a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animera Map-vy

I följande kod skapar det första kod blocket en karta och anger kopplings formatet, värdena för centrum och zoomning. I det andra kod blocket skapas en klick händelse hanterare för knappen animera. När du klickar på den här knappen anropas funktionen setCamera med vissa slumpmässiga värden för [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animera Map-vy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Map- <a href='https://codepen.io/azuremaps/pen/WayvbO/'>vyn</a> för pen-animering efter<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Testa koden

Ta en titt på exempel koden ovan. Du kan redigera JavaScript-koden på **fliken JS** till vänster och se ändringarna i Map-vyn på **fliken resultat** till höger. Du kan också klicka på knappen **Redigera på CodePen** och redigera koden i CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Se kod exempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Ändra stil på kartan](choose-map-style.md)

> [!div class="nextstepaction"]
> [Lägg till kontroller till kartan](map-add-controls.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)