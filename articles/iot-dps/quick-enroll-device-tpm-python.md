---
title: Registrera TPM-enhet på Azure Device Provisioning-tjänsten med python
description: Snabb start – registrera TPM-enhet till Azure IoT Hub Device Provisioning Service (DPS) med hjälp av python-etablerings tjänstens SDK. Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 6266ef3479e74103d0989b8eb0286626da5eb28f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976801"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Snabb start: registrera TPM-enhet för att IoT Hub Device Provisioning Service med hjälp av python Provisioning service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

De här stegen visar hur du program mässigt skapar en enskild registrering för en TPM-enhet i Azure IoT Hub Device Provisioning Service med hjälp av [v1 python Provisioning service SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) med hjälp av ett exempel på python-program. Python-tjänst-SDK fungerar på både Windows- och Linux-datorer men i den här artikeln används en Windows-utvecklingsdator för att gå igenom registreringsprocessen.

> [!IMPORTANT]
> Den här artikeln gäller endast den inaktuella v1 python SDK. Enhets-och tjänst klienter för IoT Hub Device Provisioning-tjänsten är ännu inte tillgängliga i v2. Teamet är för närvarande hårt för att ge v2 till funktionens paritet.

Se till att [konfigurera IoT Hub Device Provisioning-tjänsten med Azure-portalen](./quick-setup-auto-provision.md) innan du fortsätter.


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Förbereda miljön 

1. Ladda ned och installera [Python 2.x eller 3.x](https://www.python.org/downloads/). Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. 

1. Välj ett av följande alternativ:

    - Bygg och kompilera **Azure IoT Python SDK**. Skapa Python-paketen med hjälp av [de här instruktionerna](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md). Om du använder Windows OS installerar du även [Visual C++ redistributable package](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) så att du kan använda interna DLL:er från Python.

    - [Installera eller uppgradera *pip*, pakethanteringssystemet för Python](https://pip.pypa.io/en/stable/installing/) och installera paketet med följande kommando:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Du behöver bekräftelsenyckeln för din enhet. Om du har följt snabbstarten om att [skapa och etablera en simulerad enhet](quick-create-simulated-device.md) för att skapa en simulerad TPM-enhet använder du nyckeln som skapats för den enheten. Du kan också använda följande bekräftelsenyckel som medföljer SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>Ändra Python-exempelkoden

I det här avsnittet visas hur du lägger till etableringsinformationen för TPM-enheten i exempelkoden. 

1. Skapa en ny **TpmEnrollment.py**-fil med en texteditor.

1. Lägg till följande `import`-instruktioner och variabler längst upp i **TpmEnrollment.py**-filen. Byt sedan `dpsConnectionString` mot din anslutningssträng som finns under **Policyer för delad åtkomst** i **Device Provisioning-tjänsten** på **Azure-portalen**. Byt ut `endorsementKey` med värdet du tidigare antecknade i [Förbereda miljön](quick-enroll-device-tpm-python.md#prepareenvironment). Skapa slutligen ett unikt `registrationid` och se till att det endast innehåller små bokstäver, siffror och bindestreck.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Lägg till följande funktion och funktionsanrop för att implementera skapandet av registreringsgruppen:
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. Spara och stäng **TpmEnrollment.py**-filen.
 

## <a name="run-the-sample-tpm-enrollment"></a>Kör exemplet på TPM-registrering

1. Öppna en kommandotolk och kör skriptet.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Se utdata för att se om registreringen har lyckats.

1. Navigera till etableringstjänsten i Azure Portal. Välj **Hantera registreringar**. Observera att TPM-enheten visas under fliken **Enskilda registreringar**, med namnet som `registrationid` skapade tidigare. 

    ![Verifiera lyckad TPM-registrering i portalen](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att utforska Java-tjänsteexemplet ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten.

1. Stäng utdatafönstret för Python-exemplet på datorn.
1. Om du har skapat en simulerad TPM-enhet stänger du TPM-simulatorfönstret.
1. Gå till enhets etablerings tjänsten i Azure Portal, Välj **Hantera registreringar**och välj sedan fliken **enskilda registreringar** . Markera kryss rutan bredvid *registrerings-ID* för registrerings posten som du skapade med den här snabb starten och klicka på knappen **ta bort** högst upp i fönstret.


## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du skapat en enskild registrerings post för en TPM-enhet genom programmering, och du kan också skapa en TPM-simulerad enhet på datorn och allokera den till IoT-hubben med hjälp av Azure-IoT Hub Device Provisioning Service. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen.

> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)
