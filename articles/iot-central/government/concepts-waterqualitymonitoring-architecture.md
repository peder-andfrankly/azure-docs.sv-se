---
title: Referens arkitektur för övervaknings lösning för vatten kvalitet som skapats med Azure IoT Central | Microsoft Docs
description: Lär dig begrepp för en lösning för övervakning av vatten kvalitet som skapats med Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: cffa571028dac192e8b8806ee9ec932cf89eb1fe
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498789"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Referensarkitektur för övervakning av vattenkvalitet 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Lösningar för övervakning av vatten kvalitet kan byggas med **Azure IoT Central app-mallen** som ett kick start programmet IoT-program. Den här artikeln innehåller en översikt över referens arkitektur på hög nivå om hur du skapar en lösning från slut punkt till slut punkt. 


![Arkitektur för övervakning av vatten kvalitet](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Koncept:

1. Enheter och anslutningar  
1. IoT Central 
2. Utökning och integrering
3. Affärsappar

Låt oss ta en titt på viktiga komponenter som vanligt vis spelar en del av en lösning för att övervaka vatten kvalitet.

## <a name="devices-and-connectivity"></a>Enheter och anslutningar 
I det här avsnittet kommer vi att referera till enheter som används för övervakning av vatten kvalitet eller vattenkonsumtion, vanligt vis som smarta vatten enheter. Smarta vatten enheter kan vara flödes mätare, vatten kvalitet övervakare, smarta ventiler, läckor osv.

Enheter som används i smarta vatten lösningar är vanligt vis anslutna via låg Energis LPWAN (Wide Area Network) via en nätverks operatör från tredje part. För dessa typer av enheter kan du utnyttja [azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) för att skicka enhets data till IoT-programmet i Azure IoT Central. Du kan också ha enhets-gatewayer som är IP-kompatibla och kan ansluta direkt till IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central är en IoT app-plattform som hjälper dig att snabbt komma igång med din IoT-lösning. Du kan märka, anpassa och integrera din lösning med tjänster från tredje part.
När du ansluter dina Smart vatten enheter till IoT Central får du enhets kommando och kontroll, övervakning och avisering, användar gränssnitt med inbyggda RBAC-och konfigurerbara insikter-instrument paneler och alternativ för utöknings barhet. 

## <a name="extensibility-and-integrations"></a>Utökning och integrering 
Du kan utöka IoT-programmet i IoT Central och om du vill:
* transformera och integrera IoT-data för avancerad analys, till exempel utbildning av maskin inlärnings modeller, genom kontinuerlig data export från IoT Central program
* Automatisera arbets flöden i andra system genom att utlösa åtgärder via Microsoft Flow eller Webhooks från IoT Central program
* program mässigt åtkomst till ditt IoT-program i IoT Central via IoT Central-API: er

## <a name="business-applications"></a>Affärsappar 
IoT-data kan användas för att driva olika affärs program i ett vatten verktyg. Om du vill lära dig hur du ansluter appen IoT Central vatten kvalitet med fält tjänster följer du artikeln om [hur du integrerar med fält tjänsterna i Dynamics 365](./how-to-configure-connected-field-services.md). 


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar ett program för övervakning av vatten kvalitet](./tutorial-water-quality-monitoring.md) IoT Central
* Läs mer om [mallar för IoT Central myndigheter](./overview-iot-central-government.md)
* Mer information om IoT Central finns i [IoT Central översikt](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

