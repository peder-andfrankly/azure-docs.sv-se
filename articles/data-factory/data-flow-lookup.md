---
title: Mappa transformering av data flödes sökning
description: Azure Data Factory mappning av data flödes uppslags omvandling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/03/2019
ms.openlocfilehash: 5cc54c95759ba1490f498305f05cc49a4411686d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930322"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory mappning av data flödes uppslags omvandling

Använd lookup för att lägga till referens data från en annan källa till ditt data flöde. Uppslags transformeringen kräver en definierad källa som pekar på din referens tabell och matchar nyckel fält.

![Sök omvandling](media/data-flow/lookup1.png "Sökning")

Välj de nyckel fält som du vill matcha på mellan inkommande data Ströms fält och fälten från referens källan. Du måste först ha skapat en ny källa på data flödets design arbets yta som ska användas som den högra sidan för sökningen.

När matchningar hittas kommer de resulterande raderna och kolumnerna från referens källan att läggas till i ditt data flöde. Du kan välja vilka fält av intresse som du vill ta med i din mottagare i slutet av ditt data flöde. Du kan också använda en SELECT-omvandling efter sökningen för att rensa fält listan så att endast de fält från båda strömmar som du vill behålla lagras.

Transformationen lookup utför motsvarigheten till en vänster yttre koppling. Så du ser att alla rader från din vänstra källa kombineras med matchningar från din högra sida. Om du har flera matchande värden i sökningen, eller om du vill anpassa uppslags uttrycket, är det bättre att växla till en kopplings omvandling och använda en kors koppling. På så sätt undviker du eventuella kartesiska produkt fel vid körning.

## <a name="match--no-match"></a>Matcha/ingen matchning

Efter uppslags omvandlingen kan du använda efterföljande omvandlingar för att granska resultaten av varje matchande rad genom att använda Expression-funktionen `isMatch()` för att göra ytterligare val i din logik baserat på om sökningen resulterade i en rad matchning eller inte.

![Sök mönster](media/data-flow/lookup111.png "Sök mönster")

När du har använt lookup-omvandlingen kan du lägga till en villkorlig delning för omvandling i ```isMatch()``` funktionen. I exemplet ovan går matchande rader genom den översta strömmen och icke-matchande rader flödar genom ```NoMatch``` strömmen.

## <a name="first-or-last-value"></a>Första eller sista värdet

Om du har flera matchningar från sökningen kanske du vill minska de flera matchade raderna genom att antingen välja den första eller sista matchningen. Du kan göra detta med hjälp av en aggregerad omvandling efter sökningen.

I det här fallet används den sammanställda omvandlingen ```PickFirst``` för att välja det första värdet från söknings matchningarna.

![Slå samman mängd](media/data-flow/lookup333.png "Slå samman mängd")

![Sök först](media/data-flow/lookup444.png "Sök först")

## <a name="optimizations"></a>Optimeringar

I Data Factory körs data flöden i uppskalade Spark-miljöer. Om din data uppsättning kan anpassas till arbets ytans minnes utrymme kan vi optimera dina uppslags prestanda.

![Sändnings anslutning](media/data-flow/broadcast.png "Sända anslutning")

### <a name="broadcast-join"></a>Sändnings anslutning

Välj vänster och/eller höger sändnings anslutning för att begära ADF för att skicka hela data uppsättningen från endera sidan av Sök relationen till minnet. För mindre data uppsättningar kan detta förbättra dina uppslags prestanda avsevärt.

### <a name="data-partitioning"></a>Datapartitionering

Du kan också ange partitionering av dina data genom att välja "Ange partitionering" på fliken Optimize i lookup-omvandlingen för att skapa uppsättningar med data som passar bättre i minnet per arbets plats.

## <a name="next-steps"></a>Nästa steg

* [Anslut](data-flow-join.md) och [existerar](data-flow-exists.md) transformeringar utför liknande uppgifter i data flöden för ADF-mappning. Ta en titt på dessa omvandlingar härnäst.
* Använd en [villkorlig delning](data-flow-conditional-split.md) med ```isMatch()``` för att dela upp rader för matchande och icke-matchande värden
