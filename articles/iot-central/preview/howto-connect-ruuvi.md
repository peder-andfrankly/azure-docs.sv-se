---
title: Ansluta en RuuviTag i Azure IoT Central | Microsoft Docs
description: Lär dig hur du ansluter en RuuviTag-miljö sensor till ditt IoT Central-program.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 9be8fbad8811d758f9ac4205c0d1e60e0d82e07f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895495"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Ansluta en RuuviTag-sensor till ditt Azure IoT Central-program

I den här artikeln beskrivs hur du kan ansluta en RuuviTag-sensor till ditt Microsoft Azure IoT Central program med hjälp av Solution Builder.

Vad är en Ruuvi-tagg?

RuuviTag är en avancerad sensor för öppen källkod som är utformad för att uppfylla behoven hos företags kunder, utvecklare, tillverkare, studenter och hobbyer. Enheten ställs in så att den fungerar så snart du tar den i sin låda och är redo att distribuera den där du behöver den. Det är en Bluetooth LE-Beacon med en miljö sensor och en accelerometer inbyggd.

RuuviTag kommunicerar via Bell (Bluetooth Low Energy) och kräver en gateway-enhet för att kommunicera med Azure IoT Central. Se till att du har en gateway-enhet, t. ex. Rigado kaskad 500, för att aktivera en RuuviTag för att ansluta till IoT Central.

Följ [anvisningarna här](./howto-connect-rigado-cascade-500.md) om du vill konfigurera en Rigado kaskad 500-gateway-enhet.

## <a name="prerequisites"></a>Krav

För att ansluta RuuviTag-sensorer behöver du följande resurser:

* En RuuviTag-sensor. Mer information finns på [RuuviTag](https://ruuvi.com/).
* En Rigado kaskad 500-enhet eller en annan Bell-Gateway. Mer information finns på [Rigado](https://www.rigado.com/).
* Ett Azure IoT Central-program som skapats från någon av Programmallarna för förhands granskning. Mer information finns i avsnittet [skapa ett nytt program](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Lägg till en mall för RuuviTag-enhet

Om du vill publicera en RuuviTag-sensor i din Azure IoT Central-programinstans måste du konfigurera en motsvarande enhets mall i ditt program.

Så här lägger du till en RuuviTag enhets mal len:

1. Gå till fliken ***mallar*** i det vänstra fönstret och välj **+ ny**: ![skapa ny enhets mall](./media/howto-connect-ruuvi/devicetemplate-new.png) sidan ger dig ett alternativ för att ***skapa en anpassad mall*** eller ***använda en förkonfigurerad enhets mall***
1. Välj enhets mal len RuuviTag i listan med förkonfigurerade enhetsspecifika mallar enligt nedan: ![välja RuuviTag Device Template](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Välj ***Nästa: anpassa*** för att fortsätta till nästa steg.
1. På nästa skärm väljer du ***skapa*** för att publicera enhets mal len C500 i ditt IoT Central-program.

## <a name="connect-a-ruuvitag-sensor"></a>Ansluta en RuuviTag-sensor

Som tidigare nämnts måste du konfigurera en gateway-enhet för att ansluta RuuviTag med ditt IoT Central-program. Stegen nedan förutsätter att du har konfigurerat en Rigado kaskad 500-gateway-enhet.  

1. Slå på din Rigado kaskad 500-enhet och Anslut den till din nätverks anslutning (via Ethernet eller trådlöst)
1. Stäng av RuuviTag och hämta fliken plast för att skydda anslutningen till batteriet.
1. Placera RuuviTagen nära en Rigado kaskad 500-gateway som redan har kon figurer ATS i IoT Central-programmet.
1. På bara några sekunder bör din RuuviTag visas i listan över enheter i IoT Central.  
    ![Lista över RuuviTag-enheter](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Du kan nu använda den här RuuviTag i ditt IoT Central-program.  

## <a name="create-a-simulated-ruuvitag"></a>Skapa en simulerad RuuviTag

Om du inte har någon fysisk RuuviTag-enhet kan du skapa en simulerad RuuviTag sensor som används för testning i ditt Azure IoT Central-program.

Så här skapar du en simulerad RuuviTag:

1. Välj **enheter > RuuviTag**.
1. Välj **+ ny**.
1. Ange ett unikt **enhets-ID** och ett eget **enhets namn**.  
1. Aktivera **simulerad** inställning.
1. Välj **Skapa**.  

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en RuuviTag till ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig hur du [anpassar ditt IoT Central-program](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) för att bygga en lösning för slut punkt till slut punkt.
