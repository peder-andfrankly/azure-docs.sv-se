---
title: Kontrollera en enhet från snabbstart för Azure IoT Hub (Android) | Microsoft Docs
description: I den här snabbstarten kör du två Java-exempelprogram. Det enda programmet är ett tjänstprogram som kan fjärrstyra enheter som är anslutna till hubben. Det andra programmet körs på en fysisk eller simulerad enhet som är ansluten till hubben och kan fjärrstyras.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: d607608167e1287c7df35157ccb9870f40f22943
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516706"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Snabbstart: Kontrollera en enhet ansluten till en IoT Hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan hantera dina IoT-enheter från molnet och mata in stora mängder enhets telemetri till molnet för lagring eller bearbetning. I den här snabbstarten använder du en *direktmetod* för att styra en simulerad enhet som är ansluten till IoT Hub. Du kan använda direkta metoder för att fjärrändra beteendet hos en enhet ansluten till din IoT-hubb.

Snabbstarten använder två färdiga Java-program:

* Ett simulerat enhetsprogram som svarar på direkta metoder anropas från ett tjänstprogram på serverdelen. För att kunna ta emot direkta metodanrop ansluter det här programmet till en enhetsspecifik slutpunkt på din IoT-hubb.

* Ett tjänstprogram som anropar direktmetoden på Android-enheten. För att kunna anropa en direkt metod på en enhet ansluter det här programmet till en serverdelsslutpunkt på din IoT-hubb.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

* Android Studio från https://developer.android.com/studio/. Mer information om Android Studio-installationen finns i [Android-installation](https://developer.android.com/studio/install).

* Android SDK-27 används av exemplet i den här artikeln.

* Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen. IOT-tillägget lägger till IoT Hub-, IoT Edge-och IoT Device Provisioning-tjänst (DPS)-kommandon i Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* Två exempelprogram krävs för den här snabbstarten: [Android-exempelprogrammet för enhets-SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) och [Android-exempelprogrammet för tjänst-SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample). Båda dessa exempel är en del av lagringsplatsen azure-iot-samples-java på GitHub. Ladda ned eller klona lagringsplatsen [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-android.md) kan du hoppa över det här steget och använda den IoT-hubb som du redan har skapat.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-android.md) kan du hoppa över det här steget och använda samma enhet som registrerats i föregående snabbstart.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyAndroidDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyAndroidDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="retrieve-the-service-connection-string"></a>Hämta anslutningssträngen för tjänsten

Du behöver även ha en _tjänstanslutningssträng_ för att kunna aktivera tjänstprogrammet för serverdelen och ansluta till din IoT-hubb för att köra metoder och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

**YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Du kommer att använda det här värdet senare i snabbstarten. Den här tjänst anslutnings strängen skiljer sig från den enhets anslutnings sträng som du antecknade i föregående steg.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Exempelprogrammet för enhets-SDK kan köras på en fysisk Android-enhet eller en Android-emulator. Exemplet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar en bekräftelse tillbaka till hubben när den har kört den direkta metoden.

1. Öppna Android-exempelprojektet från GitHub i Android Studio. Projektet finns i följande katalog för din klonade eller nedladdade kopia av lagringsplatsen [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. I Android Studio öppnar du *gradle. Properties* för exempelprojektet och ersätter plats hållaren **Device_Connection_String** med enhets anslutnings strängen som du antecknade tidigare.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. I Android Studio klickar du på **Arkiv** > **Synkronisera projekt med Gradle-filer**. Kontrollera att bygget slutförs.

   > [!NOTE]
   > Om Project-synkroniseringen Miss lyckas kan det bero på någon av följande orsaker:
   >
   > * De versioner av Android Gradle-plugin-programmet och Gradle som refereras i projektet är inaktuella för din version av Android Studio. Följ [dessa instruktioner](https://developer.android.com/studio/releases/gradle-plugin) om du vill referera till och installera rätt versioner av plugin-och Gradle för din installation.
   > * Licens avtalet för Android SDK har inte signerats. Följ instruktionerna i build-utdata för att signera licens avtalet och Hämta SDK: n.

4. När bygget har slutförts klickar du på **Kör** > **Kör app**. Konfigurera appen att köras på en fysisk Android-enhet eller en Android-emulator. Mer information om att köra en Android-app på en fysisk enhet eller emulator finns i [Köra appen](https://developer.android.com/training/basics/firstapp/running-app).

5. När appen har lästs in klickar du på knappen **Starta** för att börja skicka telemetri till din IoT-hubb:

    ![Exempel skärm bild av en Android-app för klient enheter](media/quickstart-control-device-android/sample-screenshot.png)

Den här appen måste köras på en fysisk enhet eller emulator när du kör Service SDK-exemplet för att uppdatera telemetri-intervallet under körningen.

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

I det här avsnittet ska du använda Azure Cloud Shell med IoT- [tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) för att övervaka de meddelanden som skickas av Android-enheten.

1. Med Azure Cloud Shell kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Följande skärmbild visar utdata när IoT-hubben tar emot telemetri som skickas av Android-enheten:

      ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-control-device-android/read-data.png)

Som standard skickar telemetri-appen telemetri från Android-enheten var femte sekund. I nästa avsnitt använder du ett direktmetodsanrop för att uppdatera telemetriintervallet för Android IoT-enheten.

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Tjänstprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metod anrop till en enhet via din IoT Hub och lyssnar efter bekräftelser.

Kör den här appen på en separat fysisk Android-enhet eller en Android-emulator.

Ett IoT Hub backend-tjänstprogrammet körs vanligt vis i molnet, där det är enklare att minimera riskerna som är kopplade till den känsliga anslutnings strängen som styr alla enheter på en IoT Hub. I det här exemplet kör vi det som en Android-app endast i demonstrationssyfte. De andra språk versionerna av den här snabb starten innehåller exempel som är mer likt ett typiskt Server dels tjänst program.

1. Öppna Android-tjänstexempelprojektet från GitHub i Android Studio. Projektet finns i följande katalog för din klonade eller nedladdade kopia av lagringsplatsen [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Öppna *gradle. Properties* för exempelprojektet i Android Studio. Uppdatera värdena för **ConnectionString** -och **DeviceID** -egenskaperna med den anslutnings sträng som du noterade tidigare och det Android-enhets-ID som du har registrerat.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. I Android Studio klickar du på **Arkiv** > **Synkronisera projekt med Gradle-filer**. Kontrollera att bygget slutförs.

   > [!NOTE]
   > Om Project-synkroniseringen Miss lyckas kan det bero på någon av följande orsaker:
   >
   > * De versioner av Android Gradle-plugin-programmet och Gradle som refereras i projektet är inaktuella för din version av Android Studio. Följ [dessa instruktioner](https://developer.android.com/studio/releases/gradle-plugin) om du vill referera till och installera rätt versioner av plugin-och Gradle för din installation.
   > * Licens avtalet för Android SDK har inte signerats. Följ instruktionerna i build-utdata för att signera licens avtalet och Hämta SDK: n.

4. När bygget har slutförts klickar du på **Kör** > **Kör app**. Konfigurera appen att köras på en separat fysisk Android-enhet eller en Android-emulator. Mer information om att köra en Android-app på en fysisk enhet eller emulator finns i [Köra appen](https://developer.android.com/training/basics/firstapp/running-app).

5. När appen har lästs in uppdaterar du värdet **Set Messaging Interval** (Ange meddelandeintervall) till **1000** och klickar på **Invoke** (Anropa).

    Meddelandeintervallet för telemetri anges i millisekunder. Standardintervallet för telemetri för enhetsexemplet är inställt på 5 sekunder. Den här ändringen uppdaterar Android IoT-enheten så att telemetri skickas varje sekund.

    ![Ange telemetriintervall](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Appen får en bekräftelse som anger om metoden har körts eller inte.

    ![Bekräftelse av direkt metod](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du anropat en direktmetod på en enhet från ett serverdelsprogram och svarat på det direkta metodanropet i ett simulerat enhetsprogram.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudier: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)
