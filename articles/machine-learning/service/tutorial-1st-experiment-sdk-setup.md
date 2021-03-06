---
title: 'Självstudie: skapa ditt första ML experiment'
titleSuffix: Azure Machine Learning
description: I den här självstudien kommer du att komma igång med Azure Machine Learning python SDK som körs i Jupyter Notebooks.  I del 1 skapar du en arbets yta där du kan hantera experiment och ML-modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: d2247ba965c5e6db6e2da0218859d207d2fc61b8
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276630"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Självstudie: kom igång med att skapa ditt första ML-experiment med python SDK
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här självstudien slutför du stegen från slut punkt till slut punkt för att komma igång med Azure Machine Learning python SDK som körs i Jupyter Notebooks. Den här självstudien är **del ett i en själv studie kurs i två delar**, och täcker konfiguration och konfiguration av python-miljön, samt skapa en arbets yta för att hantera experiment och maskin inlärnings modeller. [**Del två**](tutorial-1st-experiment-sdk-train.md) bygger på detta för att träna flera Machine Learning-modeller och introducera modell hanterings processen med hjälp av både Azure Machine Learning Studio och SDK.

I den här kursen har du:

> [!div class="checklist"]
> * Skapa en [Azure Machine Learning-arbetsyta](concept-workspace.md) som ska användas i nästa självstudie.
> * Klona självstudiernas antecknings böcker till din mapp i arbets ytan.
> * Skapa en molnbaserad Notebook-baserad virtuell dator med Azure Machine Learning python SDK installerat och förkonfigurerat.


Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

En Azure Machine Learning arbets yta är en grundläggande resurs i molnet som du använder för att experimentera, träna och distribuera maskin inlärnings modeller. Den binder din Azure-prenumeration och resurs grupp till ett enkelt förbrukat objekt i tjänsten. 

Du skapar en arbets yta via Azure Portal, en webbaserad konsol för att hantera dina Azure-resurser. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anteckna din **arbets yta** och din **prenumeration**. Du behöver dessa för att se till att du skapar experimentet på rätt plats. 


## <a name="azure"></a>Klona en Notebook-mapp

I det här exemplet används en moln antecknings server i din arbets yta för en installation utan kostnad och förkonfigurerad upplevelse. Använd [din egen miljö](how-to-configure-environment.md#local) om du vill ha kontroll över din miljö, dina paket och beroenden.

Du har slutfört följande experiment med att ställa in och köra steg i Azure Machine Learning Studio, ett konsoliderat gränssnitt som innehåller maskin inlärnings verktyg för att utföra data vetenskaps scenarier för utbildnings nivåer för data vetenskap.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).

1. Välj din prenumeration och arbets ytan du skapade.

1. Välj **antecknings böcker** till vänster.

1. Öppna mappen **samples** .

1. Öppna mappen **python** .

1. Öppna mappen med ett versions nummer.  Det här talet representerar den aktuella versionen för python SDK.

1. Välj **"..."** till höger om mappen **självstudier** och välj sedan **klona**.

    ![Klona mapp](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. En lista över mappar visar alla användare som har åtkomst till arbets ytan.  Välj din mapp för att klona **självstudierna** där.

## <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">öppna den klonade antecknings boken

1. Under **användarfiler** öppnar du mappen och öppnar sedan mappen klonade **självstudier** .

    ![Öppna mappen självstudier](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Du kan visa antecknings böcker i mappen **exempel** , men du kan inte köra en antecknings bok därifrån.  För att kunna köra en antecknings bok, se till att du öppnar den klonade versionen av antecknings boken i avsnittet **användarfiler** .
    
1. Välj **självstudien – 1st-experiment-SDK-träna. ipynb** -filen i mappen med **självstudier** .

1. I det översta fältet väljer du en Notebook-VM som ska användas för att köra antecknings boken. De här virtuella datorerna är förkonfigurerade med allt du behöver för att köra Azure Machine Learning. Du kan välja en virtuell dator som skapats av valfri användare av din arbets yta. 

1. Om inga virtuella datorer hittas väljer du **+ Lägg till virtuell dator** för att skapa den virtuella datorn. 

    1. Ange ett namn när du skapar en virtuell dator.  Namnet måste innehålla mellan 2 och 16 tecken. Giltiga tecken är bokstäver, siffror och tecken och måste också vara unika i din Azure-prenumeration.

    1. Välj sedan **Skapa**. Det kan ta ungefär fem minuter att konfigurera den virtuella datorn.

1. När den virtuella datorn är tillgänglig visas den i det översta verktygsfältet.  Nu kan du köra antecknings boken med **Kör alla** i verktygsfältet, eller genom att använda **SKIFT + RETUR** i kod cellerna i antecknings boken.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du slutfört följande uppgifter:

* En Azure Machine Learning-arbetsyta har skapats.
* Skapat och konfigurerat en molnbaserad Notebook-server i din arbets yta.

I **del två** av självstudien kör du koden i `tutorial-1st-experiment-sdk-train.ipynb` för att träna en maskin inlärnings modell. 

> [!div class="nextstepaction"]
> [Självstudie: träna din första modell](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Om du inte planerar följande del 2 av den här själv studie kursen eller någon annan själv studie kurs bör du [stoppa den virtuella datorn i molnet Notebook-servern](tutorial-1st-experiment-sdk-train.md#clean-up-resources) när du inte använder den för att minska kostnaderna.


