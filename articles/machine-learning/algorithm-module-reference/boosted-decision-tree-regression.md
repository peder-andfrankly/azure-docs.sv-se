---
title: 'Förstärkt besluts träds regression: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Regressions modulen för besluts träd i Azure Machine Learning för att skapa en ensemble med Regressions träd med förstärkning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b04a44a82f793f39b24ffd94083ca8fd5e66cfb8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493884"
---
# <a name="boosted-decision-tree-regression-module"></a>Regressions modul för besluts träd

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att skapa en ensemble of Regressions träd med förstärkning. *Förstärkning* innebär att varje träd är beroende av föregående träd. Algoritmen lär sig genom att anpassa resten av de träd som föregår det. Därför är det bättre att öka noggrannheten i besluts trädets ensemblen för att förbättra noggrannheten med liten risk för mindre täckning.  
  
Den här Regressions metoden är en övervakad inlärnings metod och kräver därför en *etikettad data uppsättning*. Etikett kolumnen måste innehålla numeriska värden.  

> [!NOTE]
> Använd endast den här modulen med data uppsättningar som använder numeriska variabler.  

När du har definierat modellen tränar du den med hjälp av [träna-modellen](./train-model.md).

> [!TIP]
> Vill du veta mer om de träd som har skapats? När modellen har tränats högerklickar du på utdata från modulen [träna modell](./train-model.md) och väljer **visualisera** för att se trädet som skapades i varje iteration. Du kan öka detalj nivån i delningarna för varje träd och se reglerna för varje nod.  
  
## <a name="more-about-boosted-regression-trees"></a>Mer om Boosted Regressions träd  

Förstärkning är en av flera klassiska metoder för att skapa Ensemble-modeller, tillsammans med bagage, slumpmässiga skogar och så vidare.  I Azure Machine Learning använder förstärkta besluts träd en effektiv implementering av algoritmen för MART-gradient-Boost. Tonings förstärkning är en maskin inlärnings teknik för Regressions problem. Det skapar varje Regressions träd i ett steg-ett steg, med en fördefinierad förlust funktion för att mäta felet i varje steg och korrigera det i nästa steg. Därför är förutsägelse modellen faktiskt en ensemble med svagare förutsägelse modeller.  
  
Vid Regressions problem skapar förstärkningen en serie träd i ett stegvist sätt och väljer sedan det optimala trädet med en godtycklig differentiable-förlust funktion.  
  
Mer information finns i följande artiklar:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Den här Wikipedia-artikeln om tonings förstärkning ger viss bakgrund i förstärkta träd. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: från RankNet till LambdaRank till LambdaMART: en översikt. Av J.C. Burges.

Metoden för tonings förstärkning kan också användas för klassificerings problem genom att minska dem till regression med lämplig förlust funktion. Mer information om implementerade träd för klassificerings aktiviteter finns i [besluts trädet i två klasser](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Så här konfigurerar du Regressions trädet för besluts träd

1.  Lägg till modulen för **besluts träd med högre** till din pipeline. Du hittar den här modulen under **Machine Learning**, **initiera**under **Regressions** kategorin. 
  
2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enskild parameter**: Välj det här alternativet om du vet hur du vill konfigurera modellen och ange en viss uppsättning värden som argument.  
   
  
3. **Maximalt antal löv per träd**: Ange det maximala antalet terminalservrar (löv) som kan skapas i alla träd.  

    Genom att öka det här värdet kan du öka storleken på trädet och få bättre precision vid överanpassning och längre inlärnings tid.  

4. **Minsta antal sampel per lövnod**: Ange det minsta antal fall som krävs för att skapa en terminalsession (löv) i ett träd.

    Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst 5 fall som uppfyller samma villkor.

5. **Inlärnings pris**: Ange ett tal mellan 0 och 1 som definierar steg storleken under inlärningen. Inlärnings frekvensen avgör hur snabbt eller långsamt en elev konvergerar på den optimala lösningen. Om steg storleken är för stor kanske du överbelastar den optimala lösningen. Om steg storleken är för liten tar inlärningen längre tid att konvergera i den bästa lösningen.

6. **Antal skapade träd**: Ange det totala antalet besluts träd som ska skapas i ensemblen. Genom att skapa fler besluts träd kan du eventuellt få bättre täckning, men inlärnings tiden ökar.

    Det här värdet styr också antalet träd som visas vid visualisering av den tränade modellen. Om du vill se eller skriva ut ett enda träd kan du ange värdet till 1. dock skapas endast ett träd (trädet med den inledande uppsättningen parametrar) och inga ytterligare iterationer utförs.

7. Värde för **slumpmässig siffra**: Ange ett valfritt icke-negativt heltal som ska användas som det slumpmässiga startvärdet. Genom att ange ett utsäde säkerställer du reproducerbarhet i körningar som har samma data och parametrar.

    Som standard är det slumpmässiga startvärdet inställt på 0, vilket innebär att det första startvärdet hämtas från system klockan.
  
8. **Tillåt okända kategoriska-nivåer**: Välj det här alternativet om du vill skapa en grupp för okända värden i inlärnings-och validerings uppsättningarna. Om du avmarkerar det här alternativet kan modellen bara acceptera de värden som finns i tränings data. Modellen kan vara mindre exakt för kända värden, men den kan ge bättre förutsägelser för nya (okända) värden.

9. Lägg till en data uppsättning för utbildning och en av inlärnings modulerna:

    - Om du anger alternativet **skapa utbildare läge** till **en enskild parameter**använder du modulen [träna modell](train-model.md) .  
  
    

10. Köra en pipeline.  
  
## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill se trädet som skapades på varje iteration högerklickar du på utdata från modulen [träna modell](train-model.md) och väljer **visualisera**.
  
     Klicka på varje träd för att öka detalj nivån i delningarna och se reglerna för varje nod.  

+ Om du vill använda modellen för poängsättning ansluter du den till [Poäng modellen](./score-model.md)och förutsäger värdena för nya ingångs exempel.

+ Om du vill spara en ögonblicks bild av den tränade modellen högerklickar du på den **tränade modellens** utdata i modulen utbildning och väljer **Spara som**. Kopian av den tränade modellen som du sparar uppdateras inte vid efterföljande körningar av pipelinen.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
