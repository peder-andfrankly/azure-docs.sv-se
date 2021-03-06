---
title: Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer i Azure Cosmos DB
description: Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: f241f243860635db443b732f94d12956bbe0f9d8
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990624"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisser avseende konsekvens, tillgänglighet och prestanda 

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, låg latens eller båda måste göra kompromisser. Kompromisserna är mellan Läs konsekvens jämfört med tillgänglighet, svars tid och data flöde.

Azure Cosmos DB närmar sig data konsekvens som ett spektrum av alternativ. Den här metoden innehåller fler alternativ än de två extrema och slutliga konsekvensen. Du kan välja mellan fem väldefinierade modeller i konsekvens spektrumet. Från starkast till svagaste är modellerna:

- *Kraftfull*
- *Begränsad föråldrad*
- *Sessionskatalog*
- *Konsekvent prefix*
- *Slutliga*

Varje modell ger tillgänglighets-och prestanda kompromisser och backas upp av omfattande service avtal.

## <a name="consistency-levels-and-latency"></a>Konsekvens nivåer och svars tid

Läs fördröjningen för alla konsekvens nivåer garanterar alltid att vara mindre än 10 millisekunder vid 99 percentil. Den här Läs fördröjningen backas upp av service avtalet. Den genomsnittliga Läs fördröjningen, vid den 50: e percentilen, är normalt 2 millisekunder eller mindre. Azure Cosmos-konton som sträcker sig över flera regioner och som har kon figurer ATS med stark konsekvens är ett undantag till denna garanti.

Skriv fördröjningen för alla konsekvens nivåer garanterar alltid att vara mindre än 10 millisekunder vid 99 percentil. Den här Skriv fördröjningen backas upp av service avtalet. Den genomsnittliga Skriv fördröjningen, vid den 50: e percentilen, är vanligt vis 5 millisekunder eller mindre.

För Azure Cosmos-konton som kon figurer ATS med stark konsekvens med mer än en region, garanteras Skriv fördröjningen att vara mindre än två gånger i tur och retur-tid mellan någon av de två precisaste regionerna, plus 10 millisekunder på 99 percentilen.

Den exakta svars tiden för försvars tid är en funktion av hastigheten-lätt avstånd och Azure-nätverk sto pol Ogin. Azure-nätverk ger ingen latens-service avtal för sökrutan mellan två Azure-regioner. För ditt Azure Cosmos-konto visas replikeringens fördröjningar i Azure Portal. Du kan använda Azure Portal (gå till mått bladet) för att övervaka fördröjningen mellan olika regioner som är associerade med ditt Azure Cosmos-konto.

## <a name="consistency-levels-and-throughput"></a>Konsekvens nivåer och data flöde

- För samma antal enheter för programbegäran ger sessionen, konsekvent prefixet och eventuella konsekvens nivåer ungefär två gånger Läs data flödet jämfört med stark och begränsad föråldrad.

- För en specifik typ av Skriv åtgärd, till exempel infoga, Ersätt, upsert och Delete, är Skriv data flödet för enheter för programbegäran identiskt för alla konsekvens nivåer.

## <a id="rto"></a>Konsekvens nivåer och data hållbarhet

I en globalt distribuerad databas miljö finns det ett direkt förhållande mellan konsekvens nivån och data hållbarhet i närvaro av ett områdes omfattande avbrott. När du utvecklar din verksamhets kontinuitets plan måste du förstå hur lång tid det tar innan programmet återställs fullständigt efter en störnings händelse. Tiden som krävs för att ett program ska återställas fullständigt kallas för **återställnings tids mål** (**RTO**). Du måste också förstå hur lång tid det tar för nya data uppdateringar som programmet kan tolerera vid återställning efter en störnings händelse. Tids perioden för uppdateringar som du kanske har råd att förlora kallas för **återställnings punkt mål (återställnings punkt mål** ).

I tabellen nedan definieras relationen mellan konsekvens modell och data hållbarhet i närvaro av hela regionens avbrott. Det är viktigt att notera att i ett distribuerat system, även med stark konsekvens, är det omöjligt att ha en distribuerad databas med återställnings-och RTO noll på grund av CAP-satsen. Mer information om varför finns [i konsekvens nivåer i Azure Cosmos DB](consistency-levels.md).

|**Region (er)**|**Replikeringsläget**|**Konsekvens nivå**|**BEGÄRT**|**RTO**|
|---------|---------|---------|---------|---------|
|1|En eller flera huvud|Vilken konsekvens nivå som helst|< 240 minuter|< 1 vecka|
|> 1|Enda huvud|Session, konsekvent prefix, eventuell|< 15 minuter|< 15 minuter|
|> 1|Enda huvud|Begränsad föråldring|*K*  & *t*|< 15 minuter|
|> 1|Enda huvud|Stark|0|< 15 minuter|
|> 1|Flera huvud servrar|Session, konsekvent prefix, eventuell|< 15 minuter|0|
|> 1|Flera huvud servrar|Begränsad föråldring|*K*  & *t*|0|

*K* = antalet *"K"* versioner (d.v.s. uppdateringar) för ett objekt.

*T* = tidsintervallet *"T"* sedan den senaste uppdateringen.

## <a name="strong-consistency-and-multi-master"></a>Stark konsekvens och flera huvud servrar

Cosmos-konton som kon figurer ATS för multi-master kan inte konfigureras för stark konsekvens eftersom det inte är möjligt för ett distribuerat system att leverera noll och en RTO på noll. Det finns dessutom inga fördelar med Skriv fördröjning för att använda stark konsekvens med multi-master som all skrivning till en region måste replikeras och allokeras till alla konfigurerade regioner i kontot. Detta resulterar i samma Skriv fördröjning som ett enda huvud konto.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om global distribution och allmän konsekvens kompromisser i distribuerade system. Se följande artiklar:

- [Konsekvens i moderna distribuerade databas system design](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Hög tillgänglighet](high-availability.md)
- [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
