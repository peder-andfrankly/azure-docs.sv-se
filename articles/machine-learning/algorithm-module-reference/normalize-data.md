---
title: 'Normalisera data: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen normalisera data i Azure Machine Learning för att transformera en data uppsättning via *normalisering*..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: bdf2fa700b96c6110143a4557985ac1a30ed9096
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466054"
---
# <a name="normalize-data-module"></a>Normalisera datamodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att transformera en data uppsättning via *normalisering*.

Normalisering är en teknik som ofta används som en del av data förberedelsen för maskin inlärning. Målet med normalisering är att ändra värdena för numeriska kolumner i data uppsättningen till att använda en gemensam skala, utan att förvränga skillnader i värde intervallen eller förlora information. Normalisering krävs också för att vissa algoritmer ska kunna modellera data korrekt.

Anta till exempel att indata-datauppsättningen innehåller en kolumn med värden mellan 0 och 1, och en annan kolumn med värden som sträcker sig från 10 000 till 100 000. Den stora skillnaden i *skalan* i talen kan orsaka problem när du försöker kombinera värdena som funktioner under modellering.

*Normalisering* förhindrar de här problemen genom att skapa nya värden som upprätthåller den allmänna distributionen och förhållandet i källdata, samtidigt som värden i en skala används för alla numeriska kolumner som används i modellen.

I den här modulen finns flera alternativ för att transformera numeriska data:

- Du kan ändra alla värden till en 0-1-skala eller transformera värdena genom att representera dem som percentils rang snarare än absoluta värden.
- Du kan tillämpa normalisering på en enskild kolumn eller flera kolumner i samma data uppsättning.
- Om du behöver upprepa pipelinen eller tillämpa samma normaliserings steg för andra data kan du spara stegen som en normaliserings omvandling och tillämpa den på andra data uppsättningar som har samma schema.

> [!WARNING]
> Vissa algoritmer kräver att data normaliseras innan du tränar en modell. Andra algoritmer utför sin egen data skalning eller normalisering. När du väljer en Machine Learning-algoritm som ska användas för att skapa en förutsägelse modell måste du därför se över data kraven för algoritmen innan du använder normalisering på tränings data.

##  <a name="configure-normalize-data"></a>Konfigurera normaliserade data

Du kan bara använda en normaliserings metod i taget med den här modulen. Därför används samma normaliserings metod för alla kolumner som du väljer. Om du vill använda olika normaliserings metoder använder du en andra instans av **normalisering av data**.

1. Lägg till modulen **normalisera data** i din pipeline. Du hittar modulen i Azure Machine Learning under **data omvandling**i kategorin **skala och minska** .

2. Anslut en data uppsättning som innehåller minst en kolumn med alla tal.

3. Använd kolumn väljaren för att välja de numeriska kolumner som ska normaliseras. Om du inte väljer enskilda kolumner inkluderas som standard **alla** numeriska typ kolumner i inaktuella inaktuella kolumner och samma normaliserings process tillämpas på alla markerade kolumner. 

    Detta kan leda till onormala resultat om du inkluderar numeriska kolumner som inte ska normaliseras! Kontrol lera alltid kolumnerna noggrant.

    Om inga numeriska kolumner identifieras kontrollerar du kolumnens metadata för att kontrol lera att kolumnens datatyp är en numerisk typ som stöds.

    > [!TIP]
    > För att säkerställa att kolumner av en speciell typ tillhandahålls som indata kan du prova att använda modulen [Välj kolumner i data uppsättning](./select-columns-in-dataset.md) innan du **normaliserar data**.

4. **Använd 0 för konstanta kolumner när markerad**: Välj det här alternativet när en numerisk kolumn innehåller ett enskilt värde som inte ändras. Detta säkerställer att sådana kolumner inte används i normaliserings åtgärder.

5. I list rutan **omvandlings metod** väljer du en enskild matematisk funktion som ska användas för alla markerade kolumner. 
  
    - **Zscore**: konverterar alla värden till ett z-poäng.
    
      Värdena i kolumnen omvandlas med hjälp av följande formel:  
  
      ![normalisering med z&#45;-resultat](media/module/aml-normalization-z-score.png)
  
      Medelvärdet och standard avvikelsen beräknas för varje kolumn separat. Populationens standard avvikelse används.
  
    - **MinMax**: minimum-Max-normalisering skalas linjärt om varje funktion till intervallet [0, 1].
    
      Omskalning till [0, 1]-intervallet görs genom att ändra värdena för varje funktion så att det minimala värdet är 0, och dividera sedan med det nya maximala värdet (vilket är skillnaden mellan de ursprungliga maximala och minimala värdena).
      
      Värdena i kolumnen omvandlas med hjälp av följande formel:  
  
      ![normalisering med minsta&#45;Max-funktionen](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logistisk**: värdena i kolumnen omvandlas med hjälp av följande formel:

      ![formel för normalisering efter logistisk funktion](media/module/aml-normalization-logistic.png "AML_normalization-logistik")  
  
    - **Lognormala**: med det här alternativet konverteras alla värden till en lognormala skala.
  
      Värdena i kolumnen omvandlas med hjälp av följande formel:
  
      ![normal distribution&#45;i formel logg](media/module/aml-normalization-lognormal.png "AML_normalization – lognormala")
    
      Här μ och σ är parametrarna för distributionen, beräknade empiriskt från data som maximal sannolikhets uppskattningar, för varje kolumn separat.  
  
    - **TanH**: alla värden konverteras till en hyperbolisk tangens.
    
      Värdena i kolumnen omvandlas med hjälp av följande formel:
    
      ![normalisering med funktionen tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Kör pipelinen eller dubbelklicka på modulen **normalisera data** och välj **Kör vald**. 

## <a name="results"></a>Resultat

**Normaliserings data** module genererar två utdata:

- Om du vill visa transformerade värden högerklickar du på modulen, väljer **transformerad data uppsättning**och klickar på **visualisera**.

    Som standard omvandlas värdena på plats. Om du vill jämföra omvandlade värden till de ursprungliga värdena använder du modulen [Lägg till kolumner](./add-columns.md) för att kombinera data uppsättningarna igen och Visa kolumnerna sida vid sida.

- Om du vill spara omvandlingen så att du kan använda samma normaliserings metod på en annan liknande data uppsättning, högerklickar du på modulen, väljer **omvandlings funktion**och klickar på **Spara som transformering**.

    Du kan sedan läsa in de sparade omvandlingarna från gruppen **transformeringar** i det vänstra navigerings fönstret och tillämpa den på en data uppsättning med samma schema med hjälp av [./Apply-omvandling](apply-transformation.md).  


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 