---
title: Filter omvandling i data flöde för mappning
description: Filtrera bort rader med hjälp av filter omvandlingen i Azure Data Factory mappa data flöde
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 84b935d1646ea4f13d5926fc68797fd8efd03dea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930356"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filter omvandling i data flöde för mappning

Filtret Transforms tillåter rad filtrering baserat på ett villkor. Utdataströmmen innehåller alla rader som matchar filtrerings villkoret. Filter omvandlingen liknar en WHERE-sats i SQL.

## <a name="configuration"></a>Konfiguration

Använd data flödets uttrycks verktyg för att ange ett uttryck för filter villkoret. Öppna uttrycks verktyget genom att klicka på den blå rutan. Filter villkoret måste vara av typen Boolean. Mer information om hur du skapar ett uttryck finns i [uttrycks verktygets](concepts-data-flow-expression-builder.md) dokumentation.

![Filtrera omvandling](media/data-flow/filter1.png "Filtrera omvandling")

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan är en filter omvandling med namnet `FilterBefore1960` som tar i inkommande data ström `CleanData`. Filter villkoret är uttrycket `year <= 1960`.

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Filtrera omvandling](media/data-flow/filter1.png "Filtrera omvandling")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Nästa steg

Filtrera ut kolumner med [Select-omvandlingen](data-flow-select.md)
