---
title: Kund data begär Anden för Azure IoT Hub-enheter
description: De flesta av de enheter som hanteras i Azure IoT Hub är inte personliga, men vissa är. Den här artikeln talar om för administratörer som kan exportera eller ta bort personliga data från en enhet.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 29b3ed46ffe7f2236fc63d65ed49385b29b1a08a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889470"
---
# <a name="summary-of-customer-data-request-features"></a>Översikt över funktioner för begäran om kund information

Azure IoT Hub är en REST API-baserad moln tjänst som är riktad till företags kunder och möjliggör säker dubbelriktad kommunikation mellan miljon tals enheter och en partitionerad Azure-tjänst.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Enskilda enheter tilldelas ett enhets-ID (enhets-ID) av en klient administratör. Enhets data baseras på det tilldelade enhets-ID: t. Microsoft har ingen information och har ingen åtkomst till data som skulle tillåta användar korrelation i enhets-ID.

Många av de enheter som hanteras i Azure IoT Hub är inte personliga enheter, till exempel en Office-termostat eller en fabriks robot. Kunder kan dock överväga att vissa enheter ska vara personligt identifierbara och att de kan behålla sina egna till gångar eller inventerings spårnings metoder som knyter enheter till individer. Azure IoT Hub hanterar och lagrar alla data som är kopplade till enheter som om de vore personliga data.

Klient organisations administratörer kan använda antingen Azure Portal eller tjänstens REST-API: er för att uppfylla informations förfrågningar genom att exportera eller ta bort data som är associerade med ett enhets-ID.

Om du använder funktionen routning i Azure IoT Hub-tjänsten för att vidarebefordra enhets meddelanden till andra tjänster, måste data begär Anden utföras av klient administratören för varje Dirigerings slut punkt för att slutföra en fullständig begäran om en specifik enhet. Mer information finns i referens dokumentationen för varje slut punkt. Mer information om slut punkter som stöds finns i [referens-IoT Hub slut punkter](iot-hub-devguide-endpoints.md).

Om du använder funktionen Azure Event Grid integrering i Azure IoT Hub-tjänsten måste data begär Anden utföras av klient organisations administratören för varje prenumerant av dessa händelser. Mer information finns i [reagera på IoT Hub händelser med event Grid](iot-hub-event-grid.md).

Om du använder Azure Monitor integrerings funktionen i Azure IoT Hub-tjänsten för att skapa diagnostikloggar måste data begär Anden utföras av klient administratören mot de lagrade loggarna. Mer information finns i [övervaka hälsan för Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Tar bort kund information

Klient organisations administratörer kan använda bladet IoT-enheter i Azure IoT Hub-tillägget i Azure Portal för att ta bort en enhet som tar bort de data som är associerade med enheten.

Du kan också utföra borttagnings åtgärder för enheter med hjälp av REST-API: er. Mer information finns i [tjänst-ta bort enhet](/rest/api/iothub/service/deletedevice).

## <a name="exporting-customer-data"></a>Exportera kund information

Klient organisations administratörer kan använda kopiera och klistra in i fönstret IoT-enheter i Azure IoT Hub-tillägget i Azure Portal för att exportera data som är associerade med en enhet.

Det går också att utföra export åtgärder för enheter som använder REST-API: er. Mer information finns i [tjänst-Hämta enhet](/rest/api/iothub/service/getdevice).

> [!NOTE]
> När du använder Microsofts företags tjänster genererar Microsoft viss information, som kallas system genererade loggar. Vissa Azure IoT Hub system-genererade loggar kan inte nås eller exporteras av klient administratörer. Dessa loggar utgör faktiska åtgärder som utförs i tjänsten och diagnostikdata som är relaterade till enskilda enheter.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Fullständig dokumentation för Azure IoT Hub service-API: er finns i [IoT Hub service-API: er](https://docs.microsoft.com/rest/api/iothub/service).
