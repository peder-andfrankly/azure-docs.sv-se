---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712451"
---
Fluentd är en öppen källkod för datainsamling för enhetlig loggning. Den `Fluentd` inställningar hantera behållarens anslutning till en [Fluentd](https://www.fluentd.org) server. Behållaren innehåller en Fluentd loggningsprovider, vilket gör att din behållare att skriva loggar och eventuellt måttdata till en Fluentd-server.

I följande tabell beskrivs de konfigurationsinställningar som stöds den `Fluentd` avsnittet.

| Namn | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Host` | Sträng | IP-adressen eller DNS-värdnamn för Fluentd-servern. |
| `Port` | Integer | Porten som används av Fluentd-server.<br/> Standardvärdet är 24224. |
| `HeartbeatMs` | Integer | Pulsslag intervall i millisekunder. Om ingen händelse trafik har skickats innan det här intervallet upphör att gälla, skickas ett pulsslag till Fluentd-servern. Standardvärdet är 60000 millisekunder (1 minut). |
| `SendBufferSize` | Integer | Buffertutrymme för nätverk i byte som allokerats för skickar åtgärder. Standardvärdet är 32768 byte (32 kB). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Tidsgräns i millisekunder för att upprätta en SSL/TLS-anslutning med Fluentd-servern. Standardvärdet är 10 000 millisekunder (10 sekunder).<br/> Om `UseTLS` har angetts till false, ignoreras det här värdet. |
| `UseTLS` | Boolesk | Anger om behållaren ska använda SSL/TLS för att kommunicera med Fluentd-servern. Standardvärdet är FALSKT. |