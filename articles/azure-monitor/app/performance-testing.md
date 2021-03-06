---
title: Prestanda-och belastnings testning med Azure Application Insights | Microsoft Docs
description: Konfigurera prestanda-och belastnings test med Azure Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 9c86b69239bed1a15c754ce28232b97e8439942b
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819100"
---
# <a name="performance-testing"></a>Prestandatestning

> [!NOTE]
> Den molnbaserade belastnings test tjänsten är föråldrad. Mer information om utfasningen, tillgänglighet för tjänsten och alternativa tjänster finns [här](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Med Application Insights kan du generera belastnings test för dina webbplatser. Som [tillgänglighets test](monitor-web-app-availability.md)kan du skicka antingen grundläggande förfrågningar eller [flera steg](availability-multistep.md) från Azure test agenter runtom i världen. Med prestandatester kan du simulera upp till 20 000 samtidiga användare i upp till 60 minuter.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

För att kunna skapa ett prestanda test måste du först skapa en Application Insights-resurs. Fortsätt till nästa avsnitt om du redan har skapat en resurs.

Från Azure Portal väljer du **skapa en resurs** > **utvecklarverktyg** > **Application Insights** och skapar en Application Insights resurs.

## <a name="configure-performance-testing"></a>Konfigurera prestanda testning

Om det här är första gången du skapar prestandatest väljer du **Ange organisation** och väljer en Azure DevOps-organisation som källa för dina prestandatester.

Under **Konfigurera**går du till **prestanda testning** och klickar på **ny** för att skapa ett test.

![Fyll åtminstone i URL:en för din webbplats](./media/performance-testing/new-performance-test.png)

Om du vill skapa ett grundläggande prestandatest väljer du en test typ för **manuellt test** och fyller i önskade inställningar för ditt test.

|Inställning| Max värde
|----------|------------|
| Användar belastning | 20 000 |
| Varaktighet (minuter)  | 60 |  

När testet har skapats klickar du på **Kör test**.

När testet är klart visas resultat som liknar resultaten nedan:

![Testresultat](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Konfigurera Visual Studio-webbtest

Application Insights avancerade funktioner för prestanda testning skapas ovanpå Visual Studio-prestanda och belastnings test projekt.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Nästa steg

* [Webbtester med flera steg](availability-multistep.md)
* [Ping-test för URL](monitor-web-app-availability.md)