---
title: 'Konvertera till data uppsättning: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen konvertera till data uppsättning i Azure Machine Learning-tjänsten för att konvertera data indata till det interna data uppsättnings formatet som används av Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 81eb71da43623f6478e267f55d6576789d494f9b
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717250"
---
# <a name="convert-to-dataset"></a>Konvertera till data uppsättning

Den här artikeln beskriver hur du använder modulen konvertera till dataset i Azure Machine Learning designer (för hands version) för att konvertera data för en pipeline till designerns interna format.
  
Konvertering krävs inte i de flesta fall. Azure Machine Learning implicit konverterar data till det ursprungliga data uppsättnings formatet när en åtgärd utförs på data. 

Vi rekommenderar att du sparar data till data uppsättnings formatet om du har utfört någon typ av normalisering eller rensning på en uppsättning data, och du vill se till att ändringarna används i andra pipeliner.  
  
> [!NOTE]
> Konvertera till data uppsättning ändrar endast data formatet. En ny kopia av data sparas inte i arbets ytan. Om du vill spara data uppsättningen dubbelklickar du på utdataporten, väljer **Spara som data uppsättning**och anger ett nytt namn.  
  
## <a name="how-to-use-convert-to-dataset"></a>Använda konvertera till data uppsättning  

Vi rekommenderar att du använder modulen [Redigera metadata](edit-metadata.md) för att förbereda data uppsättningen innan du använder konvertera till data uppsättning. Du kan lägga till eller ändra kolumn namn, justera data typer och göra andra ändringar om det behövs.

1.  Lägg till modulen konvertera till data uppsättning i din pipeline. Du hittar den här modulen i kategorin **data omvandling** i designern. 

2. Anslut den till en modul som matar ut en data uppsättning.   

    Så länge data är [tabell](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py), kan du konvertera dem till en data uppsättning. Detta inkluderar data som läses in genom att [Importera data](import-data.md), data som skapats genom att [ange data manuellt](enter-data-manually.md)eller data uppsättningar omvandlas via [tillämpa omvandling](apply-transformation.md).

3.  I list rutan **åtgärd** anger du om du vill göra några rensningar av data innan du sparar data uppsättningen:  
  
    - **Ingen**: Använd datan som de är.  
  
    - **SetMissingValue**: Ange ett värde för ett värde som saknas i data uppsättningen. Standard plats hållaren är frågetecknet (?), men du kan använda alternativet **anpassat värde som saknas** för att ange ett annat värde. Om du till exempel anger **taxi** för **anpassat saknat värde**, kommer alla instanser av **taxin** i data uppsättningen att ändras till det saknade värdet.
  
    - **ReplaceValues**: Använd det här alternativet för att ange ett enda exakt värde som ska ersättas med andra exakta värden. Du kan ersätta saknade värden eller anpassade värden genom att ange **ersättnings** metoden:

      - **Saknas**: Välj det här alternativet om du vill ersätta saknade värden i indata-datauppsättningen. För **nytt värde**anger du det värde som du vill ersätta de saknade värdena med.
      - **Anpassad**: Välj det här alternativet om du vill ersätta anpassade värden i indata-datauppsättningen. För **anpassat värde**anger du det värde som du vill söka efter. Om dina data exempelvis innehåller strängen `obs` används som plats hållare för värden som saknas, anger du `obs`. För **nytt värde**anger du det nya värdet som ersätter den ursprungliga strängen med.
  
    Observera att **ReplaceValues** -åtgärden endast gäller för exakta matchningar. Dessa strängar kommer till exempel inte att påverkas: `obs.``obsolete`.  
 
  
5.  Kör pipelinen eller högerklicka på modulen konvertera till data uppsättning och välj **Kör vald**.  

## <a name="results"></a>Resultat

+  Om du vill spara den resulterande data uppsättningen med ett nytt namn högerklickar du på utdata från konvertera till data uppsättning och väljer **Spara som data uppsättning**.  
  
## <a name="technical-notes"></a>Tekniska anteckningar  

-   Alla moduler som tar en data uppsättning som indata kan också ta data i CSV-filen eller TSV-filen. Innan all kod körs förbehandlas indata. För bearbetning är detsamma som att köra modulen konvertera till data uppsättning i indata.  
  
-   Du kan inte konvertera från SVMLight-formatet till en data uppsättning.  
  
-   När du anger en anpassad ersättnings åtgärd, gäller åtgärden Sök och Ersätt för fullständiga värden. Partiella matchningar är inte tillåtna. Du kan till exempel ersätta en 3 med en-1 eller med 33, men du kan inte ersätta en 3 med tvåsiffriga tal som 35.  
  
-   För anpassade ersättnings åtgärder, kommer ersättningen att Miss varnas tyst om du använder som ersättning för alla bokstäver som inte överensstämmer med den aktuella data typen för kolumnen.  

  
## <a name="next-steps"></a>Nästa steg

Se de [moduler som är tillgängliga](module-reference.md) för tjänsten Azure Machine Learning. 
