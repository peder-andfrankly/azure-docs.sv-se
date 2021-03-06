---
title: Utveckla moduler för Azure IoT Edge | Microsoft Docs
description: Utveckla anpassade moduler för Azure IoT Edge som kan kommunicera med runtime och IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 37f55165d1fea8a69d10003baeb0006199326cba
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456614"
---
# <a name="develop-your-own-iot-edge-modules"></a>Utveckla dina egna IoT Edge-moduler

Azure IoT Edge moduler kan ansluta till andra Azure-tjänster och bidra till din större data pipeline i molnet. I den här artikeln beskrivs hur du kan utveckla moduler för att kommunicera med IoT Edge Runtime och IoT Hub, och därmed resten av Azure-molnet. 

## <a name="iot-edge-runtime-environment"></a>IoT Edge-körningsmiljö
IoT Edge-runtime tillhandahåller infrastrukturen som att integrera funktionerna i flera IoT Edge-moduler och distribuera dem till IoT Edge-enheterna. På hög nivå, kan alla program vara paketerad som en IoT Edge-modul. Om du vill dra full nytta av IoT Edge kommunikations- och hanteringsfunktioner, men anslutas ett program som körs i en modul till lokala IoT Edge hub, integrerade i IoT Edge-körningen.

## <a name="using-the-iot-edge-hub"></a>Med hjälp av IoT Edge hub
IoT Edge hub ger två huvudsakliga funktioner: proxy till IoT Hub och lokala kommunikation.

### <a name="iot-hub-primitives"></a>IoT Hub primitiver
IoT-hubben ser en modulinstans på samma sätt som en enhet i meningen som:

* den har en modul med dubbla som är distinkt och isolerad från [enheten](../iot-hub/iot-hub-devguide-device-twins.md) , och den andra modulen är sammanflätad med enheten.
* den kan skicka [meddelanden från enheten till molnet](../iot-hub/iot-hub-devguide-messaging.md).
* den kan ta emot [direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md) som riktar sig specifikt till identiteten.

För närvarande kan inte en modul ta emot meddelanden från moln till enhet eller använda filuppladdningsfunktionen.

När du skriver en modul kan du använda [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md) för att ansluta till IoT Edge Hub och använda ovanstående funktioner som du skulle göra när du använde IoT Hub med ett enhets program, den enda skillnaden är att du måste referera till modulens identitet i stället för enhetens identitet.

### <a name="device-to-cloud-messages"></a>Meddelanden från enheten till molnet
För att möjliggöra komplex bearbetning av meddelanden från enheten till molnet tillhandahåller IoT Edge Hub deklarativ routning av meddelanden mellan moduler, och mellan moduler och IoT Hub. Deklarativ routning låter moduler att fånga upp och bearbeta meddelanden som skickas av andra moduler och sprida dem till komplexa pipelines. Mer information finns i [distribuera moduler och upprätta vägar i IoT Edge](module-composition.md).

En IoT Edge-modul, till skillnad från en normal IoT Hub device program, kan ta emot meddelanden från enheten till molnet som körs via en proxy av sin lokala IoT Edge hub för att bearbeta dem på.

IoT Edge Hub sprider meddelandena till modulen baserat på deklarativ vägar som beskrivs i [distributions manifestet](module-composition.md). När du utvecklar en IoT Edge-modul, kan du ta emot dessa meddelanden genom att ange meddelandehanterare.

För att förenkla skapandet av vägar IoT Edge lägger till konceptet *indata* och *utdata* -slutpunkter för moduler. En modul kan ta emot alla meddelanden från enheten till molnet dirigeras till den utan att ange några indata och kan skicka meddelanden från enheten till molnet utan att ange några utdata. Med hjälp av explicita indata och utdata, men gör routningsregler enklare att förstå. 

Slutligen är enhet-till-moln-meddelanden som hanteras av Edge hub stämplad med följande Systemegenskaper för:

| Egenskap | Beskrivning |
| -------- | ----------- |
| $connectionDeviceId | Enhets-ID för den klient som skickade meddelandet |
| $connectionModuleId | Modul-ID för den modul som skickade meddelandet |
| $inputName | Indata som har fått det här meddelandet. Kan vara tom. |
| $outputName | De utdata som används för att skicka meddelandet. Kan vara tom. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ansluter till IoT Edge hub från en modul
Ansluter till lokala IoT Edge hub från en modul omfattar två steg: 
1. Skapa en ModuleClient-instans i ditt program.
2. Kontrollera att ditt program godkänner certifikatet som presenteras av IoT Edge-hubben på den enheten.

Skapa en ModuleClient-instans för att ansluta modulen till IoT Edge Hub som körs på enheten, på liknande sätt som DeviceClient-instanser ansluter IoT-enheter till IoT Hub. Mer information om klassen ModuleClient och dess kommunikations metoder finns i API-referensen för ditt prioriterade SDK-språk: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)eller [Node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Stöd för språk och arkitektur

IoT Edge stöder flera operativ system, enhets arkitekturer och utvecklings språk så att du kan bygga upp det scenario som passar dina behov. Använd det här avsnittet för att förstå alternativen för att utveckla anpassade IoT Edge-moduler. Du kan lära dig mer om verktygs support och krav för varje språk för [att förbereda utvecklings-och test miljön för IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

För alla språk i följande tabell IoT Edge stöder utveckling för AMD64-och ARM32 Linux-enheter. 

| Utvecklingsspråk | Utvecklingsverktyg |
| -------------------- | ----------------- |
| C | Visual Studio-koden<br>Visual Studio 2017/2019 |
| C# | Visual Studio-koden<br>Visual Studio 2017/2019 |
| Java | Visual Studio-koden |
| Node.js | Visual Studio-koden |
| Python | Visual Studio-koden |

>[!NOTE]
>Att utveckla och felsöka stöd för ARM64 Linux-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Mer information finns i [utveckla och FELSÖKA ARM64 IoT Edge moduler i Visual Studio Code (för hands version)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

För alla språk i följande tabell IoT Edge stöder utveckling för AMD64 Windows-enheter.

| Utvecklingsspråk | Utvecklingsverktyg |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (inga fel söknings funktioner)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Nästa steg

[Förbered din utvecklings-och test miljö för IoT Edge](development-environment.md)

[Använd Visual Studio för att C# utveckla moduler för IoT Edge](how-to-visual-studio-develop-module.md)

[Använd Visual Studio Code för att utveckla moduler för IoT Edge](how-to-vs-code-develop-module.md)

[Förstå och Använd Azure IoT Hub SDK: er](../iot-hub/iot-hub-devguide-sdks.md)