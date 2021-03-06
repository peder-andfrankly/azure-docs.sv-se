---
title: Maskin inlärnings funktion i Azure Datautforskaren
description: Använd Machine Learning-kluster för rotor Saks analys i Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769939"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Maskin inlärnings funktion i Azure Datautforskaren

Azure Datautforskaren, en stor data analys plattform, används för att övervaka tjänsters hälsa, QoS eller fel enheter för avvikande beteende med inbyggda funktioner för [avvikelse identifiering och prognostisering](/azure/data-explorer/anomaly-detection) . När ett avvikande mönster upptäcks utförs en rotor Saks analys (RCA) för att minimera eller lösa avvikelsen.

Diagnos processen är komplex och långvarig och utförs av domän experter. Processen inkluderar hämtning och anslutning av ytterligare data från olika källor under samma tidsram, som söker efter ändringar i fördelningen av värden på flera dimensioner, lägger till ytterligare variabler och andra tekniker som baseras på domän kunskaper och intuition. Eftersom dessa diagnos scenarier är vanliga i Azure Datautforskaren är Machine Learning-plugin-program tillgängliga för att göra diagnos fasen enklare och förkorta varaktigheten för RCA.

Azure Datautforskaren har tre Machine Learning-plugin-program: [`autocluster`](/azure/kusto/query/autoclusterplugin), [`basket`](/azure/kusto/query/basketplugin)och [`diffpatterns`](/azure/kusto/query/diffpatternsplugin). Alla plugin-program implementerar algoritmer för klustring. `autocluster`-och `basket`-plugin-programmet-kluster en enda post uppsättning och `diffpatterns`-plugin-programmet klustrar skillnaderna mellan två post uppsättningar.

## <a name="clustering-a-single-record-set"></a>Klustra en enda post uppsättning

Ett vanligt scenario är en data uppsättning som valts av ett särskilt villkor, till exempel tids period som innehåller avvikande beteende, läsningar av hög temperatur enheter, långa varaktighets kommandon och användare med främsta utgifter. Vi vill ha ett enkelt och snabbt sätt att hitta vanliga mönster (segment) i data. Mönster är en delmängd av den data uppsättning vars poster delar samma värden över flera dimensioner (kategoriska-kolumner). Följande fråga skapar och visar en tids serie med tjänst undantag under en vecka på tio minuters fack:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Timechart för tjänst undantag](media/machine-learning-clustering/service-exceptions-timechart.png)

Antalet undantag i tjänsten motsvarar den övergripande tjänst trafiken. Du kan tydligt se det dagliga mönstret, för arbets dagar på måndag till fredag, med ett antal undantag i tjänsten, och antalet träffar under natten. Fasta låg antal visas över helgen. Undantags toppar kan identifieras med hjälp av [avvikelse identifiering i Time Series](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) i Azure datautforskaren.

Den andra insamling i data sker på tisdag eftermiddag. Följande fråga används för att ytterligare diagnostisera denna insamling. Använd frågan för att rita om diagrammet runt toppen i högre upplösning (åtta timmar på en minut) för att kontrol lera om det är en skarp insamling och visa dess gränser.

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=) **\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Fokusera på insamling timechart](media/machine-learning-clustering/focus-spike-timechart.png)

Vi ser en smal smal ökning på två minuter från 15:00 till 15:02. I följande fråga räknas undantagen i detta fönster i två minuter:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Antal |
|---------|
|972    |

I följande fråga, sampla 20 undantag av 972:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Region | ScaleUnit | DeploymentId                     | Tracepoint | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | SCUS   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | SCUS   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | SCUS   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | SCUS   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | SCUS   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | SCUS   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Använd Autocluster () för kluster för enskild post uppsättning

Även om det finns mindre än ett tusen undantag är det fortfarande svårt att hitta vanliga segment eftersom det finns flera värden i varje kolumn. Du kan använda [`autocluster()`](/azure/kusto/query/autoclusterplugin) plugin-programmet för att snabbt extrahera en liten lista med vanliga segment och hitta de intressanta klustren inom de insamlade två minuterna som visas i följande fråga:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| Segment | Antal | Procent | Region | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | SCUS | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | SCUS | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Du kan se från resultaten ovan att det mest dominerande segmentet innehåller 65,74% av de totala undantags posterna och delar fyra dimensioner. Nästa segment är mycket mindre vanligt, innehåller endast 9,67% av posterna och delar tre dimensioner. De andra segmenten är ännu mindre vanliga. 

Autocluster använder sig av en patentskyddad algoritm för att vinna flera dimensioner och extrahera intressanta segment. "Intressant" innebär att varje segment har betydande täckning av både de angivna posterna och funktionerna. Segmenten är också fördelade, vilket innebär att var och en skiljer sig mycket från de andra. Ett eller flera av dessa segment kan vara relevanta för RCA-processen. För att minimera segment granskningen och utvärderingen extraherar Autocluster bara en lista med små segment.

### <a name="use-basket-for-single-record-set-clustering"></a>Använd korg () för kluster för enskild post uppsättning

Du kan också använda [`basket()`](/azure/kusto/query/basketplugin) -plugin-programmet som det visas i följande fråga:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| Segment | Antal | Procent | Region | ScaleUnit | DeploymentId | Tracepoint | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | SCUS | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | SCUS | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | SCUS |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Korgen implementerar Apriori-algoritmen för utvinning av objekt uppsättning och extraherar alla segment vars täckning av post uppsättningen är över ett tröskelvärde (standard 5%). Du kan se att fler segment har extraherats med liknande (till exempel segmenten 0, 1 eller 2, 3).

Båda plugin-programmen är kraftfulla och enkla att använda, men deras betydande begränsning är att de har en enda post i ett icke övervakat sätt (utan etiketter). Det är därför oklart om de extraherade mönstren kännetecknar den valda post uppsättningen (avvikande poster) eller den globala post uppsättningen.

## <a name="clustering-the-difference-between-two-records-sets"></a>Klustra skillnaden mellan två post uppsättningar

[`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) -plugin-programmet överträder begränsningen av `autocluster` och `basket`. `Diffpatterns` tar två post uppsättningar och extraherar de huvud segment som skiljer sig åt mellan dem. En uppsättning innehåller vanligt vis den avvikelse post som har undersökts (en analys av `autocluster` och `basket`). Den andra uppsättningen innehåller referens post uppsättningen (bas linje). 

I frågan nedan använder vi `diffpatterns` för att hitta intressanta kluster i ininsamlingens två minuter, som skiljer sig från kluster inom bas linjen. Vi definierar bas linje fönstret som åtta minuter före 15:00 (när insamling startade). Vi måste också utöka med en binär kolumn (AB) som anger om en särskild post tillhör bas linjen eller den avvikande uppsättningen. `Diffpatterns` implementerar en övervakad Learning-algoritm där de två klass etiketterna genererades av avvikelsen jämfört med bas linje flaggan (AB).

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| Segment | CountA | CountB | Procents ATS | PercentB | PercentDiffAB | Region | ScaleUnit | DeploymentId | Tracepoint |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65,74 | 1,7 | 64,04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17,18 | 44,16 | 26,97 | SCUS |  |  |  |
| 2 | 92 | 356 | 9,47 | 28,9 | 19,43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9,26 | 27,27 | 18,01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8,44 | 25,81 | 17,38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5,66 | 20,45 | 14,8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5,86 | 16,56 | 10,69 |  |  |  |  |

Det mest dominerande segmentet är samma segment som har extraherats av `autocluster`, dess täckning i två minuters avvikelse-fönstret är också 65,74%. Men dess täckning i åtta minuters bas linje fönstret är bara 1,7%. Skillnaden är 64,04%. Den här skillnaden verkar vara relaterad till den avvikande inökningen. Du kan kontrol lera detta antagande genom att dela det ursprungliga diagrammet i de poster som tillhör det problematiska segmentet jämfört med andra segment som visas i frågan nedan:

**\[** [**Klicka för att köra frågan**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Verifierar diffpattern segment timechart](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Det här diagrammet gör att vi kan se att inökningen på tisdag eftermiddag skedde på grund av undantag från det här aktuella segmentet, identifierade med hjälp av `diffpatterns` plugin-programmet.

## <a name="summary"></a>Sammanfattning

Azure Datautforskaren-Machine Learning-plugin-program är användbara för många scenarier. `autocluster` och `basket` implementera en övervakad Learning-algoritm och är lätt att använda. `Diffpatterns` implementerar övervakad Learning-algoritm och även om den är mer komplex, är det mer kraftfullt att extrahera differentierings segment för RCA.

Dessa plugin-program används interaktivt i ad hoc-scenarier och i automatiska övervaknings tjänster i real tid. I Azure Datautforskaren följs avvikelse identifiering i Time Series av en diagnos process som är mycket optimerad för att uppfylla nödvändiga prestanda standarder.
