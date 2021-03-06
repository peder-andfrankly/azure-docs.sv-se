---
title: Sammanfatta data
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen sammanfatta data i Azure Machine Learning-tjänsten för att skapa en grundläggande beskrivande statistik rapport för kolumnerna i en data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 503eb533b83cbeedcc16b73a9c1dbe821a4d4d94
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492576"
---
# <a name="summarize-data"></a>Sammanfatta data

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd modulen sammanfatta data för att skapa en uppsättning standard statistiska mått som beskriver varje kolumn i inmatnings tabellen.

Sammanfattnings statistik är användbar när du vill förstå egenskaperna för hela data uppsättningen. Du kan till exempel behöva känna till:

- Hur många saknade värden finns det i varje kolumn?
- Hur många unika värden finns i en funktions kolumn?
- Vad är medelvärdet och standard avvikelsen för varje kolumn?

Modulen beräknar de viktiga poängen för varje kolumn och returnerar en rad med sammanfattnings statistik för varje variabel (data kolumn) som angetts som indata.

## <a name="how-to-configure-summarize-data"></a>Konfigurera sammanfattande data  

1. Lägg till modulen **sammanfatta data** i din pipeline. Du hittar den här modulen i kategorin **statistiska funktioner** i designern.

1. Anslut den data uppsättning som du vill skapa en rapport för.

    Om du bara vill rapportera om vissa kolumner använder du modulen [Välj kolumner i data uppsättning](select-columns-in-dataset.md) för att projicera en delmängd med kolumner att arbeta med.

1. Inga ytterligare parametrar krävs. Som standard analyserar modulen alla kolumner som tillhandahålls som indata, och beroende på vilken typ av värden som finns i kolumnerna, matas ut en relevant uppsättning statistik enligt beskrivningen i avsnittet [resultat](#results) .

1. Kör pipelinen eller högerklicka på modulen och välj **Kör vald**.

## <a name="results"></a>Resultat

Rapporten från modulen kan innehålla följande statistik. 

|Kolumn namn|Beskrivning|
|------|------|  
|**Funktion**|Kolumnens namn|
|**Reparationer**|Antal rader|
|**Antal unika värden**|Antal unika värden i kolumnen|
|**Antal saknade värden**|Antal unika värden i kolumnen|
|**Minimum**|Lägsta värdet i kolumnen|  
|**Bekräftat**|Högsta värdet i kolumnen|
|**Anses**|Medelvärde för alla kolumn värden|
|**Genomsnittlig avvikelse**|Medelvärde för kolumn värden|
|**1: a kvartilen**|Värde vid första kvartilen|
|**Säte**|Kolumn värde för median|
|**tredje kvartilen**|Värde vid tredje kvartilen|
|**Offline**|Läge för kolumn värden|
|**Område**|Ett heltal som representerar antalet värden mellan högsta och lägsta värden|
|**Exempel avvikelse**|Varians för kolumn; Se Obs!|
|**Exempel på standard avvikelse**|Standard avvikelse för kolumn; Se Obs!|
|**Exempel skevning**|Skevning för kolumnen; Se Obs!|
|**Exempel-och-upplösning**|För kolumn; Se Obs!|
|**P 0,5**|0,5% percentil|
|**P1**|1% percentil|
|**P5**|5% percentil|
|**P95**|95% percentil|
|**P 99,5**|99,5% percentil |

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten.  
