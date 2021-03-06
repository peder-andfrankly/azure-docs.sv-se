---
title: 'Lägg till rader: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Lägg till rader i Azure Machine Learning för att sammanfoga två data uppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a16dc0bfc3fae8852c46435f6407c56149e1b14e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493947"
---
# <a name="add-rows-module"></a>Lägg till Rows-modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att sammanfoga två data uppsättningar. I sammanfogningen läggs raderna i den andra data uppsättningen till i slutet av den första data mängden.  
  
Sammanfogning av rader är användbart i scenarier som dessa:  
  
+ Du har genererat en serie utvärderings statistik och vill kombinera dem till en tabell för enklare rapportering.  
  
+ Du har arbetat med olika data uppsättningar och du vill kombinera data uppsättningarna för att skapa en slutgiltig data uppsättning.  

## <a name="how-to-use-add-rows"></a>Så här använder du Lägg till rader  

Om du vill sammanfoga rader från två data uppsättningar måste raderna ha exakt samma schema. Det innebär att samma antal kolumner och samma typ av data i kolumnerna.

1.  Dra modulen **Lägg till rader** till din pipeline. du hittar den under **datatransformering**i kategorin för **manipulering** .

2. Anslut data uppsättningarna till de två portarna för indata. Den data uppsättning som du vill lägga till ska vara ansluten till den andra (högra) porten. 
  
3.  Köra en pipeline. Antalet rader i data uppsättningen för utdata ska vara lika med summan av raderna i båda data uppsättningarna.

    Om du lägger till samma data uppsättning i båda indata för modulen **Lägg till rader** dupliceras data uppsättningen. 

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 