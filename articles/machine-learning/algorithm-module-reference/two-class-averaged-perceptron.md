---
title: 'Besluts skogs regression: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder den genomsnittliga Perceptron-modulen med två klasser i Azure Machine Learning för att skapa en Machine Learning-modell som baseras på den genomsnittliga Perceptron-algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 379cddd9654cc897b49fd085d8df55fcd77a7ce8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490358"
---
# <a name="two-class-averaged-perceptron-module"></a>Genomsnittlig Perceptron-modul med två klasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att skapa en Machine Learning-modell som baseras på den genomsnittliga Perceptron-algoritmen.  
  
Den här klassificerings algoritmen är en övervakad inlärnings metod och kräver en *Taggad data uppsättning*som innehåller en etikett kolumn. Du kan träna modellen genom att tillhandahålla modellen och den taggade data uppsättningen som indata för att [träna modellen](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya ingångs exemplen.  

### <a name="about-averaged-perceptron-models"></a>Om Genomsnittligt antal Perceptron-modeller

Den *genomsnittliga Perceptron-metoden* är en tidig och enkel version av ett neurala-nätverk. I den här metoden klassificeras indata i flera möjliga utdata baserat på en linjär funktion och kombineras sedan med en uppsättning vikter som härleds från funktions vektorn, och därför namnet "Perceptron".

De enklare Perceptron-modellerna är lämpliga för inlärning av linjärt särskiljande mönster, medan neurala-nätverk (särskilt djup neurala-nätverk) kan modellera mer komplexa klass gränser. Perceptrons är dock snabbare, och eftersom de behandlar ärenden seriellt kan perceptrons användas med kontinuerlig utbildning.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Konfigurera genomsnittlig Perceptron i två klasser

1.  Lägg till den **genomsnittliga Perceptron-modulen med två klasser** i din pipeline.  

2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enskild parameter**: Ange en viss uppsättning värden som argument om du vet hur du vill konfigurera modellen.
  
3.  För **inlärnings frekvens**anger du ett värde för *inlärnings takten*. Inlärnings takt svärdet styr storleken på det steg som används i Stochastic gradient-brantaste varje gång modellen testas och korrigeras.
  
     Genom att göra hastigheten mindre testar du modellen oftare, med den risk att du kan fastna i en lokal platå. Genom att göra steget större kan du konvergera snabbare, vid risken för att den sanna minimi överskrids.
  
4.  För **maximalt antal iterationer**anger du hur många gånger du vill att algoritmen ska undersöka tränings data.  
  
     Att stoppa tidigt ger ofta bättre generalisering. Genom att öka antalet iterationer kan du förbättra anpassningen vid risk för överanpassning.
  
5.  Om du vill använda **slumpmässigt antal frön**kan du ange ett heltals värde som ska användas som start värde. Vi rekommenderar att du använder ett Dirigerings värde om du vill säkerställa att pipelinen löper över.  
  
1.  Anslut en data uppsättning för utbildning och en av inlärnings modulerna:
  
    -   Om du ställer in **skapa utbildare** för **en parameter**använder du modulen [träna modell](train-model.md) .

## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill se en översikt över modellens parametrar, tillsammans med funktions vikten från träningen, högerklickar du på utdata från [träna modell](./train-model.md).


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 