---
title: Webb tjänst parametrar – Azure Machine Learning Studio (klassisk) | Microsoft Docs
description: Så här använder du Azure Machine Learning webb tjänst parametrar för att ändra funktions sättet för din modell när webb tjänsten nås.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: 62c6488cfcb30c969c388343c766c482cff7e03b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466984"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Använd Azure Machine Learning Studio (klassiska) webb tjänst parametrar
En Azure Machine Learning-webbtjänst skapas genom att publicera ett experiment som innehåller moduler med konfigurerbara parametrar. I vissa fall kanske du vill ändra modulens beteende medan webb tjänsten körs. Med *webb tjänst parametrar* kan du göra det här. 

Ett vanligt exempel är att konfigurera modulen [Importera data][reader] så att användaren av den publicerade webb tjänsten kan ange en annan data källa när webb tjänsten nås. Eller konfigurera modulen [Exportera data][writer] så att du kan ange ett annat mål. Några andra exempel är att ändra antalet bitar för modulens [hash][feature-hashing] -modul eller antalet önskade funktioner för modulen för [filtrerings-baserade funktions val][filter-based-feature-selection] . 

Du kan ange webb tjänst parametrar och associera dem med en eller flera parametrar i experimentet, och du kan ange om de är obligatoriska eller valfria. Användaren av webb tjänsten kan sedan ange värden för dessa parametrar när de anropar webb tjänsten. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Så här ställer du in och använder webb tjänst parametrar
Du definierar en webb tjänst parameter genom att klicka på ikonen bredvid parametern för en modul och välja "Ange som webb tjänst parameter". Detta skapar en ny webb tjänst parameter och ansluter den till denna modul-parameter. Sedan kan användaren, när webb tjänsten används, ange ett värde för webb tjänst parametern och den används för parametern modul.

När du har definierat en webb tjänst parameter är den tillgänglig för alla andra parametrar i experimentet. Om du definierar en webb tjänst parameter som är associerad med en parameter för en modul kan du använda samma webb tjänst parameter för alla andra moduler, förutsatt att parametern förväntar sig samma typ av värde. Om webb tjänst parametern till exempel är ett numeriskt värde kan den endast användas för parameter parametrar som förväntar sig ett numeriskt värde. När användaren anger ett värde för webb tjänst parametern, kommer den att tillämpas på alla associerade modul parametrar.

Du kan välja om du vill ange ett standardvärde för parametern för webb tjänsten. Om du gör det är parametern valfri för webb tjänstens användare. Om du inte anger något standardvärde måste användaren ange ett värde när webb tjänsten öppnas.

API-dokumentationen för webb tjänsten innehåller information för webb tjänst användaren om hur du anger webb tjänst parametern program mässigt när du ansluter till webb tjänsten.

> [!NOTE]
> API-dokumentationen för en klassisk webb tjänst tillhandahålls via länken **API-hjälp sida** på **instrument panelen** för webb tjänster i Machine Learning Studio (klassisk). API-dokumentationen för en ny webb tjänst tillhandahålls via [Azure Machine Learning Web Services-](https://services.azureml.net/Quickstart) portalen på API-sidorna **konsumera** och **Swagger** för din webb tjänst.
> 
> 

## <a name="example"></a>Exempel
Vi kan till exempel anta att vi har ett experiment med en modul för [export av data][writer] som skickar information till Azure Blob Storage. Vi definierar en webb tjänst parameter med namnet "blobb Sök väg" som gör det möjligt för webb tjänst användaren att ändra sökvägen till blob-lagringen när tjänsten nås.

1. I den klassiska versionen av Machine Learning Studio klickar du på modulen [Exportera data][writer] för att markera den. Egenskaperna visas i fönstret Egenskaper till höger om arbets ytan för experimentet.
2. Ange lagrings typ:
   
   * Under **ange data mål väljer du**Azure Blob Storage.
   * Under **Ange Autentiseringstyp väljer du**"konto".
   * Ange konto informationen för Azure Blob Storage. 

3. Klicka på ikonen till höger om **sökvägen till bloben som börjar med container parametern**. Det ser ut så här:
   
   ![Ikon för webb tjänst parameter](./media/web-service-parameters/icon.png)
   
   Välj "Ange som webb tjänst parameter".
   
   En post läggs till under **webb tjänst parametrar** längst ned i fönstret Egenskaper med namnet "path to BLOB Start with container". Det här är den webb tjänst parameter som nu är associerad med den här parametern för [export data][writer] modul.
4. Om du vill byta namn på parametern för webb tjänsten klickar du på namnet, anger "blobb Sök väg" och trycker på **RETUR** -tangenten. 
5. Ange ett standardvärde för webb tjänst parametern genom att klicka på ikonen till höger om namnet, välj "Ange standardvärde", ange ett värde (till exempel "container1/output1. csv") och tryck på **RETUR** -tangenten.
   
   ![Webb tjänst parameter](./media/web-service-parameters/parameter.png)
6. Klicka på **Run** (Kör). 
7. Klicka på **distribuera webb tjänst** och välj **distribuera webb tjänst [klassisk]** eller **distribuera webb tjänst [ny]** för att distribuera webb tjänsten.

> [!NOTE] 
> Om du vill distribuera en ny webb tjänst måste du ha tillräcklig behörighet i den prenumeration som du distribuerar webb tjänsten till. Mer information finns i [hantera en webb tjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md). 

Användaren av webb tjänsten kan nu ange ett nytt mål för modulen [Exportera data][writer] vid åtkomst till webb tjänsten.

## <a name="more-information"></a>Mer information
Ett mer detaljerat exempel finns i posten [Web Service Parameters](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) i [Machine Learning blogg](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Mer information om hur du kommer åt en Machine Learning-webbtjänst finns i [så här använder du en Azure Machine Learning-webb tjänst](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

