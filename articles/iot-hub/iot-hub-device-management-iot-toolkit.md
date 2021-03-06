---
title: Azure IoT-enhets hantering med Azure IoT-verktyg för VSCode
description: Använd Azure IoT-verktyg för Visual Studio Code för Azure IoT Hub Device Management, med de direkta metoderna och de dubbla alternativen för önskade egenskaper hantering.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 03997bb6999af9ebaad36cc95f7834e3b9ed4569
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953195"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Använda Azure IoT-verktyg för Visual Studio Code för Azure IoT Hub enhets hantering

![Diagram från slut punkt till slut punkt](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) är ett användbart kod tillägg för Visual Studio som gör det enklare att utveckla IoT Hub-hantering och IoT-program. Den innehåller hanterings alternativ som du kan använda för att utföra olika uppgifter.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Hanterings alternativ          | Aktivitet                    |
|----------------------------|--------------------------------|
| Direkta metoder             | Se till att en enhet fungerar som att starta eller stoppa sändning av meddelanden eller starta om enheten.                                        |
| Läs enhetens dubbla           | Hämta rapporterat tillstånd för en enhet. Enheten rapporterar till exempel LAMPAn att blinka nu.                                    |
| Uppdatera enhet, dubbla         | Placera en enhet i vissa tillstånd, till exempel att ställa in en indikator på grönt eller ställa in ett intervall för telemetri-sändning till 30 minuter.         |
| Meddelanden från moln till enhet   | Skicka meddelanden till en enhet. Till exempel "det är mycket sannolikt att regna idag. Glöm inte att ta ett paraply. "              |

Mer detaljerad information om skillnaderna och vägledning om hur du använder dessa alternativ finns i [kommunikations vägledning för enhet till moln](iot-hub-devguide-d2c-guidance.md) och [kommunikation från moln till enhet](iot-hub-devguide-c2d-guidance.md).

Enhetstvillingar är JSON-dokument som lagrar information om enhetstillstånd (metadata, konfigurationer och villkor). IoT Hub sparar en enhet för varje enhet som ansluter till den. Mer information om enhets uppflätade finns i [Kom igång med enhets dubbla](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du får lära dig att använda Azure IoT-verktyg för Visual Studio Code med olika hanterings alternativ på din utvecklings dator.

## <a name="what-you-do"></a>Vad du gör

Kör Azure IoT-verktyg för Visual Studio Code med olika hanterings alternativ.

## <a name="what-you-need"></a>Vad du behöver

* En aktiv Azure-prenumeration.
* En Azure IoT-hubb under din prenumeration.
* [Visual Studio-kod](https://code.visualstudio.com/)
* [Azure IoT Tools för vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) eller [öppna den här länken i Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

1. I **trädvyn med** vs Code expanderar du avsnittet **Azure IoT Hub-enheter** i det nedre vänstra hörnet.

2. Klicka på **välj IoT Hub** i snabb menyn.

3. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

4. När du har loggat in visas din Azure-prenumeration och välj sedan Azure-prenumeration och IoT Hub.

5. Enhets listan visas på fliken **Azure IoT Hub Devices** på några sekunder.

   > [!Note]
   > Du kan också slutföra konfigurationen genom att välja **Set IoT Hub Connection String** (Ange IoT Hub-anslutningssträng). Ange anslutnings strängen för **iothubowner** -principen för den IoT-hubb som din IoT-enhet ansluter till i popup-fönstret.

## <a name="direct-methods"></a>Direkta metoder

1. Högerklicka på enheten och välj **anropa Direct-metod**. 

2. Ange metod namn och nytto Last i inmatade rutan.

3. Resultaten visas i **utdata** > **Azure IoT Hub Toolkit** -vyn.

## <a name="read-device-twin"></a>Läs enhetens dubbla

1. Högerklicka på enheten och välj **Redigera enhets telefon**. 

2. En **Azure-IoT-enhets-dubbla. JSON** -fil öppnas med enhetens innehåll.

## <a name="update-device-twin"></a>Uppdatera enhet, dubbla

1. Gör vissa redigeringar av **taggar** eller **Egenskaper. önskat** fält.

2. Högerklicka på filen **Azure-IoT-Device-dubbla. JSON** .

3. Välj **Uppdatera enheten med dubbla** för att uppdatera enheten.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Följ dessa steg om du vill skicka ett meddelande från din IoT-hubb till din enhet:
 
1. Högerklicka på enheten och välj **Skicka C2D-meddelande till enhet**. 

2. Ange meddelandet i rutan indatamängd.

3. Resultaten visas i **utdata** > **Azure IoT Hub Toolkit** -vyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder Azure IoT tools-tillägget för Visual Studio Code med olika hanterings alternativ.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
