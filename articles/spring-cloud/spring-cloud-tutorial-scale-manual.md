---
title: 'Självstudie: skala ett program i Azure våren Cloud | Microsoft Docs'
description: I den här självstudien får du lära dig hur du skalar ett program i Azure våren Cloud på Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: cce7562c74577f6fd545bcaed3ee3e0968fd40b4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132913"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Självstudie: skala ett program i Azure våren Cloud

Den här självstudien visar hur du skalar ett mikrotjänstprogram med hjälp av moln instrument panelen för Azure våren i Azure Portal. Skala ditt program uppåt och nedåt genom att ändra antalet virtuella processorer (virtuella processorer) och mängden minne. Skala ditt program i och ut genom att ändra antalet instanser av programmet. När du är klar vet du hur du ska göra snabba manuella ändringar i varje program i din tjänst. Skalningen börjar gälla om några sekunder och kräver inga kod ändringar eller omdistributioner.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
* En distribuerad Azure våren Cloud Service-instans.  Följ vår [snabb start](spring-cloud-quickstart-launch-app-cli.md) för att komma igång.
* Minst ett program som redan har skapats i den tjänst instansen.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Gå till sidan skala i Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till **översikts** sidan för Azure våren-molnet.

1. Välj den resurs grupp som innehåller din tjänst.

1. Gå till fliken **appar** under rubriken **Inställningar** på menyn på vänster sida.

1. Välj det program som du vill skala. I det här exemplet ska vi skala programmet med namnet "Account-service". Detta bör gå till programmets **översikts** sida.

1. Gå till fliken **Scale (skala** ) under rubriken **Inställningar** på menyn på vänster sida. Du bör se alternativ för skalnings attribut som visas i följande avsnitt.

## <a name="scale-your-application"></a>Skala ditt program

Du kan ändra skalnings attributen. Behåll följande anteckningar i åtanke.

* **Processorer**: det högsta antalet tillåtna processorer är 4 per program instans. Det totala antalet processorer för ett program blir det värde som anges här multiplicerat med antalet program instanser.

* **Minne/GB**: den maximala mängd minne som tillåts är 8 GB per program instans.  Den totala mängden minne för ett program blir det värde som anges här multiplicerat med antalet program instanser.

* **Antal**instanser av app: du kan skala upp till 20 instanser på standard nivån. Det här värdet ändrar antalet separata instanser av mikrotjänst programmet som körs.

Se till att klicka på knappen **Spara** för att tillämpa skalnings inställningarna.

![Scale service i Azure Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Efter några sekunder visas ändringarna du gjort på sidan **Översikt** med mer information på fliken **program instanser** . skalning kräver inga kod ändringar eller omdistributioner.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du manuellt skalar dina Azure våren Cloud-program.  Fortsätt till nästa självstudie om du vill veta mer om hur du övervakar ditt program.

> [!div class="nextstepaction"]
> [Lär dig hur du övervakar ditt program](spring-cloud-tutorial-distributed-tracing.md)
