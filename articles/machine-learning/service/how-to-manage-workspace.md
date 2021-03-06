---
title: Skapa Azure Machine Learning arbets ytor i portalen
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar, visar och tar bort Azure Machine Learning arbets ytor i Azure Portal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 80b554b7dd4f37e1a215892962d8ec5622d8ae5c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974044"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Skapa och hantera Azure Machine Learning arbets ytor i Azure Portal
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln skapar du, visar och tar bort [**Azure Machine Learning arbets ytor**](concept-workspace.md) i Azure Portal för [Azure Machine Learning](overview-what-is-azure-ml.md).  Portalen är det enklaste sättet att komma igång med arbets ytor, men när du behöver ändra eller krav på Automation-höjning kan du också skapa och ta bort arbets ytor [med hjälp av CLI](reference-azure-machine-learning-cli.md), [med python-kod](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) eller [via vs Code-tillägget](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Du behöver en Azure-prenumeration för att skapa en arbetsyta. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

1. Logga in på [Azure Portal](https://portal.azure.com/) med hjälp av autentiseringsuppgifterna för din Azure-prenumeration. 

1. I det övre vänstra hörnet av Azure Portal väljer du **+ skapa en resurs**.

      ![Skapa en ny resurs](../../../includes/media/aml-create-in-portal/create-workspace.gif)

1. Använd Sök fältet för att hitta **Machine Learning**.

1. Välj **Machine Learning**.

1. I fönstret **Machine Learning** väljer du **skapa** för att börja.

1. Ange följande information för att konfigurera din nya arbets yta:

   Fält|Beskrivning 
   ---|---
   Namn på arbetsyta |Ange ett unikt namn som identifierar din arbets yta. I det här exemplet använder vi **dokument-WS**. Namn måste vara unika i resurs gruppen. Använd ett namn som är enkelt att återkalla och särskilja från arbets ytor som skapats av andra.  
   Prenumeration |Ange den prenumeration som du vill använda.
   Resursgrupp | Använd en befintlig resursgrupp i din prenumeration eller ange ett namn för att skapa en ny resursgrupp. En resurs grupp innehåller relaterade resurser för en Azure-lösning. I det här exemplet använder vi **AML-dokument**. 
   Plats | Välj den plats som är närmast dina användare och data resurserna för att skapa din arbets yta.
   Arbetsyte version | Välj **Basic** eller **Enterprise**.  Den här arbets ytans utgåva avgör vilka funktioner du kommer att ha åtkomst till och prissättning på. Läs mer om [erbjudanden för Basic och Enterprise Edition](overview-what-is-azure-ml.md#sku). 

    ![Konfigurera din arbets yta](media/how-to-manage-workspace/select-edition.png)

1. När du är färdig med konfigurationen av arbets ytan väljer du **skapa**. 

   > [!Warning] 
   > Det kan ta flera minuter att skapa din arbets yta i molnet.

   När processen är klar visas ett meddelande om lyckad distribution. 
 
 1. Om du vill visa den nya arbets ytan väljer du **gå till resurs**.

### <a name="download-a-configuration-file"></a>Hämta en konfigurations fil

1. Hoppa över det här steget om du vill skapa en virtuell dator i [Notebook](tutorial-1st-experiment-sdk-setup.md#azure).

1. Om du planerar att använda kod i din lokala miljö som hänvisar till den här arbets ytan väljer du **Hämta config. JSON** från **översikts** avsnittet i arbets ytan.  

   ![Ladda ned config. JSON](./media/how-to-manage-workspace/configure.png)
   
   Placera filen i katalog strukturen med dina Python-skript eller Jupyter-anteckningsböcker. Det kan finnas i samma katalog, i en under katalog med namnet *. azureml*eller i en överordnad katalog. När du skapar en VM-anteckningsbok läggs den här filen till i rätt katalog på den virtuella datorn åt dig.

## <a name="upgrade"></a>Uppgradera till Enterprise Edition

Du kan uppgradera din arbets yta från Basic Edition till Enterprise Edition för att dra nytta av de förbättrade funktionerna i dessa miljöer och förbättrade säkerhetsfunktioner.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. Välj den arbets yta som du vill uppgradera.

1. Välj **Läs mer** längst upp till höger på sidan.

   [![uppgradera en arbets yta](media/how-to-manage-workspace/upgrade.png)](media/how-to-manage-workspace/upgrade.png#lightbox)

1. Välj **uppgradering** i fönstret som visas.


> [!IMPORTANT]
> Du kan inte nedgradera en Enterprise Edition-arbetsyta till en Basic Edition-arbetsyta. 

## <a name="view"></a>Hitta en arbets yta

1. I det övre Sök fältet skriver du **Machine Learning**.  

1. Välj **Machine Learning**.

   ![Sök efter Azure Machine Learning arbets yta](media/how-to-manage-workspace/find-workspaces.png)

1. Titta igenom listan över arbetsytor hittades. Du kan filtrera baserat på prenumerationen, resursgrupper och platser.  

1. Välj en arbets yta för att visa dess egenskaper.

## <a name="delete-a-workspace"></a>Ta bort en arbetsyta

Använd knappen Ta bort överst på arbetsytan som du vill ta bort.

  ![Knappen Ta bort](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den fullständiga självstudien får du lära dig hur du använder en arbets yta för att skapa, träna och distribuera modeller med Azure Machine Learning.

> [!div class="nextstepaction"]
> [Självstudie: Träna modeller](tutorial-train-models-with-aml.md)
