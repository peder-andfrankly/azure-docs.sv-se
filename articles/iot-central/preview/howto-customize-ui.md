---
title: Anpassa Azure IoT Central-ANVÄNDARGRÄNSSNITTET | Microsoft Docs
description: Anpassa temat och hjälp Länkar för ditt Azure IoT Central-program
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3aed1b47e2bce2eda60b953e5a4c8d6d7ee5c59f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976272"
---
# <a name="customize-the-azure-iot-central-ui-preview-features"></a>Anpassa Azure IoT Central UI (för hands versions funktioner)

Den här artikeln beskriver hur du, som administratör, kan anpassa användar gränssnittet för ditt program genom att använda anpassade teman och ändra hjälp länkar så att de pekar på dina egna anpassade hjälp resurser. 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

På följande skärm bild visas en sida med standard temat:

![Standard IoT Central-tema](./media/howto-customize-ui/standard-ui.png)

På följande skärm bild visas en sida med en anpassad skärm bild med de anpassade GRÄNSSNITTs elementen markerade:

![Anpassat IoT Central-tema](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Skapa tema

Om du vill skapa ett anpassat tema navigerar du till sidan **Anpassa ditt program** i avsnittet **Administration** :

![IoT Central teman](./media/howto-customize-ui/themes.png)

På den här sidan kan du anpassa följande aspekter av ditt program:

### <a name="application-logo"></a>Program logo typ

En PNG-bild, som inte är större än 1 MB, med en genomskinlig bakgrund. Logo typen visas till vänster i namn listen för IoT Centrals programmet.

Om logo typens bild innehåller namnet på ditt program kan du dölja texten i program namnet. Mer information finns i [Hantera ditt program](./howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Webb läsar ikon (favicon)

En PNG-bild, inte större än 32 x 32 pixlar, med en genomskinlig bakgrund. En webbläsare kan använda den här avbildningen i adress fältet, historik, bok märken och fliken webbläsare.

### <a name="browser-colors"></a>Webb läsar färger

Du kan ändra färg på sidhuvudet och färgen som används för accent knappar och andra höjd punkter. Använd ett hexadecimalt färg värde med sex bokstäver i formatet `##ff6347`. Mer information om färg notation för **HEX-värde** finns i [HTML-färger](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Du kan alltid återgå till standard alternativen på sidan **Anpassa ditt program** .

### <a name="changes-for-operators"></a>Ändringar för operatörer

Om en administratör skapar ett anpassat tema kan operatörer och andra användare av programmet inte längre välja ett tema i **Inställningar**.

## <a name="replace-help-links"></a>Ersätt hjälp länkar

Om du vill tillhandahålla anpassad hjälp information till dina operatörer och andra användare kan du ändra länkarna på programmets **Hjälp** -meny.

Om du vill ändra hjälp länkarna navigerar du till sidan **Anpassa hjälp** i avsnittet **Administration** :

![Anpassa IoT Central hjälp länkar](./media/howto-customize-ui/help-links.png)

Du kan också lägga till nya poster på Hjälp-menyn och ta bort standard poster:

![Anpassad IoT Central hjälp](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Du kan alltid återgå till standard hjälp länkarna på sidan **Anpassa hjälp** .

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du anpassar användar gränssnittet i ditt IoT Central-program, är det här några förslag på nästa steg:

- [Administrera ditt program](./howto-administer.md)
- [Lägga till paneler på din instrument panel](../core/howto-add-tiles-to-your-dashboard.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)