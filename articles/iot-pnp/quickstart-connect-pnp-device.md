---
title: Ansluta IoT Plug and Play förhandsgranska exempel enhets kod till IoT Hub (Windows) | Microsoft Docs
description: Skapa och kör IoT Plug and Play för hands versions exempel enhets kod i Windows som ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0d89be9da55c97a5b49157251896d3a513c2c6db
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152063"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Snabb start: ansluta ett exempel på IoT Plug and Play för hands versions program som körs på Windows till IoT Hub (C-fönster)

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet skrivs i C och ingår i Azure IoT Hub Device C SDK. En lösnings utvecklare kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten måste du installera följande program vara på den lokala datorn:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **NuGet Package Manager** -komponenten och **Skriv bords utveckling med C++**  arbets belastning när du installerar Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT Explorer

Hämta och installera den senaste versionen av **Azure IoT Explorer** från verktygets [databas](https://github.com/Azure/azure-iot-explorer/releases) sida genom att välja. msi-filen under "till gångar" för den senaste uppdateringen.

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och bygga Azure IoT Hub Device C SDK.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Azure IoT C SDK: er och bibliotek](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen på den här platsen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Den här åtgärden kan förväntas ta flera minuter att slutföra.

## <a name="build-the-code"></a>Skapa koden

Du kan använda enhets-SDK: n för att bygga den inkluderade exempel koden. Det program som du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa en `cmake` under katalog i rotmappen för enhetens SDK och navigera till mappen:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa enhets-SDK och den genererade koden stub:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Om cmake inte kan hitta C++ din kompilator får du build-fel när du kör föregående kommando. Om det händer kan du prova att köra det här kommandot i [Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)-Kommandotolken.

## <a name="run-the-device-sample"></a>Kör enhets exemplet

Kör ett exempel program i SDK för att simulera en IoT Plug and Play-enhet som skickar telemetri till IoT Hub. Kör exempel programmet genom att använda dessa kommandon och skicka _enhets anslutnings strängen_ som en parameter.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

1. Öppna Azure IoT Explorer. Du ser sidan **app-konfigurationer** .

1. Ange din _IoT Hub anslutnings sträng_ och välj **Anslut**.

1. När du har anslutit visas sidan **enhets** översikt.

1. Om du vill se till att verktyget kan läsa definitionerna för gränssnitts modellen från enheten väljer du **Inställningar**. På menyn Inställningar kan det hända att **den anslutna enheten** redan visas i Plug and Play konfigurationer. om den inte gör det väljer du **+ Lägg till modulens definitions källa** och sedan **på den anslutna enheten** för att lägga till den.

1. På sidan **enhets** Översikt hittar du enhets identiteten som du skapade tidigare. När enhets programmet fortfarande körs i kommando tolken kontrollerar du att enhetens **anslutnings status** i Azure IoT Explorer rapporterar som _ansluten_ (om inte klickar du på **Uppdatera** tills den är). Välj enheten om du vill visa mer information.

1. Expandera gränssnittet med ID **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** om du vill visa gränssnittet och IoT plug and Play-primitiver – egenskaper, kommandon och telemetri.

1. Välj sidan **telemetri** och tryck på _börja_ för att visa de telemetridata som enheten skickar.

1. Välj sidan **egenskaper (inte skrivbar)** om du vill visa de icke skrivbara egenskaper som rapporteras av enheten.

1. Välj sidan **egenskaper (skrivbar)** om du vill visa de skrivbara egenskaperna som du kan uppdatera.

1. Expandera egenskaps **namn**, uppdatera med ett nytt namn och välj **Uppdatera skrivbar egenskap**. 

1. Om du vill se det nya namnet som visas i kolumnen **rapporterad egenskap** väljer du knappen **Uppdatera** överst på sidan.

1. Välj sidan **kommandon** om du vill visa alla kommandon som enheten stöder.

1. Expandera kommandot **blinkar** och ange ett nytt intervall för blinknings tid. Välj **kommandot Skicka** för att anropa kommandot på enheten.

1. Gå till kommando tolken simulerad enhet och Läs igenom de utskrivna bekräftelse meddelandena för att kontrol lera att kommandona har körts som förväntat.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Anvisningar: ansluta till och interagera med en IoT Plug and Play för hands version](howto-develop-solution.md)
