---
title: Exportera ett Azure IoT Central-program | Microsoft Docs
description: Som lösnings hanterare vill jag exportera en Programmall för att kunna återanvända den.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 9870b18be9fb75e25941a7e1d05e4164239aacbb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977530"
---
# <a name="export-your-application-preview-features"></a>Exportera ditt program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

I den här artikeln beskrivs hur, som en lösnings hanterare, för att exportera ett IoT Central program för att kunna återanvända det.

Du kan välja mellan två alternativ:

- Du kan skapa en kopia av ditt program om du bara behöver skapa en kopia av ditt program.
- Du kan skapa en Programmall från ditt program om du planerar att skapa flera kopior.

## <a name="copy-your-application"></a>Kopiera ditt program

Du kan skapa en kopia av alla program, minus enhets instanser, enhets data historik och användar data. Kopieringen är ett program som du betalar per användning som du debiteras för. Du kan inte skapa ett utvärderings program genom att kopiera ett program.

Välj **Kopiera**. I dialog rutan anger du information för det nya programmet betala per användning. Välj sedan **Kopiera** för att bekräfta att du vill fortsätta. Mer information om fälten i formuläret finns i snabb start för att [skapa ett program](quick-deploy-iot-central.md) .

> [!NOTE]
> Du kan bara **Kopiera ditt program** till **Europa** och **amerikanska** platser.

![Sidan program inställningar](media/howto-use-app-templates/appcopy2.png)

När kopieringen av appen lyckas kan du navigera till det nya programmet med hjälp av länken.

![Sidan program inställningar](media/howto-use-app-templates/appcopy3a.png)

När du kopierar ett program kopieras även definitionen av regler och e-poståtgärden. Vissa åtgärder, till exempel Flow och Logic Apps, är knutna till vissa regler via regel-ID: t. När en regel kopieras till ett annat program får den ett eget regel-ID. I det här fallet måste användarna skapa en ny åtgärd och sedan koppla den nya regeln till den. I allmänhet är det en bra idé att kontrol lera reglerna och åtgärderna för att se till att de är uppdaterade i den nya appen.

> [!WARNING]
> Om en instrument panel innehåller paneler som visar information om specifika enheter visas **inte den begärda resursen** i det nya programmet. Du måste konfigurera om panelerna för att visa information om enheter i det nya programmet.

## <a name="create-an-application-template"></a>Skapa en Programmall

När du skapar ett Azure IoT Central-program kan du välja mellan inbyggda exempel mallar. Du kan också skapa egna programmallar från befintliga IoT Central-program. Du kan sedan använda dina egna programmallar när du skapar nya program.

När du skapar en Programmall innehåller den följande objekt från ditt befintliga program:

- Instrument panelen för standard program, inklusive instrument panelens layout och alla paneler som du har definierat.
- Mallar för enheter, inklusive mätningar, inställningar, egenskaper, kommandon och instrument paneler.
- Uttryck. Alla regel definitioner ingår. Åtgärder, förutom e-poståtgärder, ingår dock inte.
- Enhets uppsättningar, inklusive deras villkor och instrument paneler.

> [!WARNING]
> Om en instrument panel innehåller paneler som visar information om specifika enheter visas **inte den begärda resursen** i det nya programmet. Du måste konfigurera om panelerna för att visa information om enheter i det nya programmet.

När du skapar en Programmall innehåller den inte följande objekt:

- Enheter
- Användare
- Jobb definitioner
- Definitioner av kontinuerliga data exporter

Lägg till dessa objekt manuellt i alla program som skapats från en Programmall.

Så här skapar du en Programmall från ett befintligt IoT Central-program:

1. Gå till avsnittet **Administration** i ditt program.
1. Välj **export av program mal len**.
1. Ange ett namn och en beskrivning för mallen på sidan **export av program mal len** .
1. Välj knappen **Exportera** för att skapa program mal len. Nu kan du kopiera den **delnings bara länken** som gör att någon kan skapa ett nytt program från mallen:

![Skapa en Programmall](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Använd en program mall

Om du vill använda en Programmall för att skapa ett nytt IoT Central-program behöver du en tidigare skapad **delnings bara länk**. Klistra in den **delnings bara länken** i webbläsarens Adress fält. Sidan **skapa ett program** visas med din anpassade program mal len vald:

![Skapa ett program från en mall](media/howto-use-app-templates/create-app.png)

Välj din betalnings plan och fyll i de andra fälten i formuläret. Välj sedan **skapa** för att skapa ett nytt IoT Central program från program mal len.

> [!NOTE]
> När ett program skapas med hjälp av en **delnings länk** är de tillgängliga platserna **Europa** och **USA**.

### <a name="manage-application-templates"></a>Hantera programmallar

På sidan **Exportera program mal len** kan du ta bort eller uppdatera program mal len.

Om du tar bort en Programmall kan du inte längre använda den delnings bara länken som skapats tidigare för att skapa nya program.

Om du vill uppdatera program mal len ändrar du mallens namn eller beskrivning på export sidan för **program mal len** . Välj sedan knappen **Exportera** igen. Den här åtgärden skapar en ny **delnings bara länk** och invaliderar alla tidigare **delnings** bara URL-adresser.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder programmallar är det föreslagna nästa steg att lära dig hur du [hanterar IoT Central från Azure Portal](../core/howto-manage-iot-central-from-portal.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
