---
title: Felsöka Azure cache för Redis-problem på klient Sidan
description: Lär dig hur du löser vanliga problem på klient sidan med Azure cache för Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: yegu
ms.openlocfilehash: a4fdbe9c0943e77719a9ee9da7dc358696284d99
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122610"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Felsöka Azure cache för Redis-problem på klient Sidan

Det här avsnittet beskriver fel söknings problem som uppstår på grund av ett villkor på Redis-klienten som används av programmet.

- [Minnes belastning på Redis-klienten](#memory-pressure-on-redis-client)
- [Trafik burst](#traffic-burst)
- [Hög CPU-användning för klienter](#high-client-cpu-usage)
- [Bandbredds begränsning på klient Sidan](#client-side-bandwidth-limitation)
- [Storlek på stor begäran eller svars tid](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Minnes belastning på Redis-klienten

Minnes belastningen på klient datorn leder till alla typer av prestanda problem som kan fördröja bearbetningen av svar från cachen. När minnes trycks träffar kan systemet använda data på disk. Den här _sidfel_ gör att systemet saktas ned avsevärt.

Identifiera minnes belastning på klienten:

- Övervaka minnes användningen på datorn för att säkerställa att den inte överskrider det tillgängliga minnet.
- Övervaka klientens `Page Faults/Sec` prestanda räknare. Under normal drift har de flesta system vissa sidfel. Toppar i sidfel som motsvarar timeout för begär Anden kan indikera minnes belastning.

Hög minnes belastning på klienten kan begränsas på flera sätt:

- Använd mönster för minnes användning för att minska minnes användningen på klienten.
- Uppgradera den virtuella klient datorn till en större storlek med mer minne.

## <a name="traffic-burst"></a>Trafik burst

Trafik trafik som kombineras med dåliga `ThreadPool` inställningar kan leda till fördröjningar i bearbetningen av data som redan har skickats av Redis-servern men som ännu inte har använts på klient sidan.

Övervaka hur `ThreadPool` statistik ändras med tiden med hjälp av [ett exempel `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Du kan använda `TimeoutException` meddelanden från StackExchange. Redis som nedan för att undersöka:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

I föregående undantag finns det flera problem som är intressanta:

- Observera att i avsnittet `IOCP` och avsnittet `WORKER` har du ett `Busy` värde som är större än `Min` svärdet. Den här skillnaden innebär att dina `ThreadPool` inställningar behöver justeras.
- Du kan också se `in: 64221`. Det här värdet anger att 64 211 byte har mottagits på klientens kernel socket-lager men inte lästs av programmet. Den här skillnaden innebär vanligt vis att ditt program (t. ex. StackExchange. Redis) inte läser data från nätverket så snabbt som servern skickar det till dig.

Du kan [Konfigurera dina `ThreadPool` inställningar](https://gist.github.com/JonCole/e65411214030f0d823cb) för att se till att trådpoolen skalas snabbt under burst-scenarier.

## <a name="high-client-cpu-usage"></a>Hög CPU-användning för klienter

Hög CPU-användning för klienter anger att systemet inte kan fortsätta med det arbete som den har blivit ombedd att göra. Även om cachen skickade svaret snabbt, kan klienten Miss lyckas med att bearbeta svaret inom rimlig tid.

Övervaka klientens processor användning på hela systemet med hjälp av mått som är tillgängliga i Azure Portal eller via prestanda räknare på datorn. Var noga med att inte övervaka *process* -CPU eftersom en enda process kan ha låg processor användning, men hela systemets CPU kan vara hög. Titta efter toppar i CPU-användning som motsvarar tids gränser. Hög processor kan också orsaka höga `in: XXX` värden i `TimeoutException` fel meddelanden enligt beskrivningen i avsnittet [Traffic burst](#traffic-burst) .

> [!NOTE]
> StackExchange. Redis 1.1.603 och senare innehåller `local-cpu` mått i `TimeoutException` fel meddelanden. Se till att du använder den senaste versionen av [stackexchange. Redis NuGet-paketet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns buggar som ständigt korrigeras i koden för att göra det mer stabilt för timeout så att den senaste versionen är viktig.
>

Minimera en klients höga CPU-användning:

- Undersök vad som orsakar CPU-toppar.
- Uppgradera klienten till en större VM-storlek med mer processor kapacitet.

## <a name="client-side-bandwidth-limitation"></a>Bandbredds begränsning på klient Sidan

Beroende på klient datorernas arkitektur kan de ha begränsningar för hur mycket nätverks bandbredd de har tillgängliga. Om klienten överskrider den tillgängliga bandbredden genom att överbelasta nätverks kapaciteten bearbetas inte data på klient sidan så snabbt som servern skickar den. Den här situationen kan leda till timeout.

Övervaka hur bandbredds användningen förändras över tid med hjälp av [ett exempel `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Den här koden kanske inte kan köras i vissa miljöer med begränsade behörigheter (t. ex. Azure Web Sites).

Minska, minska användningen av nätverks bandbredd eller öka storleken på klientens virtuella dator till en med mer nätverks kapacitet.

## <a name="large-request-or-response-size"></a>Storlek på stor begäran eller svars tid

En stor begäran/svar kan orsaka timeout. Anta till exempel att ditt timeout-värde som kon figurer ATS på klienten är 1 sekund. Ditt program begär två nycklar (till exempel "A" och "B") samtidigt (med samma fysiska nätverks anslutning). De flesta klienter stöder begäran "pipelining" där både begär Anden "A" och "B" skickas efter den andra utan att vänta på deras svar. Servern skickar Svaren tillbaka i samma ordning. Om svaret "A" är stort kan det få ut mesta möjliga av tids gränsen för senare förfrågningar.

I följande exempel skickas begäran "A" och "B" snabbt till-servern. Servern börjar skicka Svaren "A" och "B" snabbt. På grund av data överförings tider måste svars-B vänta efter svars tiden "A", trots att servern svarade snabbt.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Den här begäran/svaret är ett svårt att mäta. Du kan Instrumenta din klient kod för att spåra stora förfrågningar och svar.

Lösningar för stora svars storlekar varierar, men inkluderar:

1. Optimera ditt program för ett stort antal små värden i stället för några få stora värden.
    - Den bästa lösningen är att dela upp data i relaterade mindre värden.
    - Se inlägget [Vad är det idealiska värde storleks intervallet för Redis? Är 100 KB för stort?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) Mer information om varför mindre värden rekommenderas.
1. Öka storleken på den virtuella datorn för att få högre bandbredds kapacitet
    - Mer bandbredd på din klient eller virtuell serverdator kan minska data överförings tiden för större svar.
    - Jämför den aktuella nätverks användningen på båda datorerna med gränserna för din aktuella VM-storlek. Mer bandbredd på bara servern eller bara på klienten är kanske inte tillräckligt.
1. Öka antalet anslutnings objekt som programmet använder.
    - Använd en metod för resursallokering för att göra förfrågningar över olika anslutnings objekt.

## <a name="additional-information"></a>Ytterligare information

- [Felsöka Azure cache för Redis-problem på Server Sidan](cache-troubleshoot-server.md)
- [Hur kan jag mäta och testa prestanda för mitt cacheminne?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
