---
title: Återställa appen från en ögonblicks bild
description: Lär dig hur du återställer din app från en ögonblicks bild. Återställ från oväntad data förlust i Premium-nivån med automatiska skugg kopior.
author: ahmedelnably
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: e5b48e05d6bd9a310b4527300126464c4b755c8d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671141"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Återställa en app i Azure från en ögonblicks bild
Den här artikeln visar hur du återställer en app i [Azure App Service](../app-service/overview.md) från en ögonblicks bild. Du kan återställa din app till ett tidigare tillstånd baserat på en av appens ögonblicks bilder. Du behöver inte aktivera säkerhets kopiering av ögonblicks bilder, plattformen sparar automatiskt en ögonblicks bild av alla appar för data återställnings syfte.

Ögonblicks bilder är stegvisa skugg kopior och de ger flera fördelar jämfört med regelbundna [säkerhets kopieringar](manage-backup.md):
- Inga fil kopierings fel på grund av fillås.
- Ingen begränsning för lagrings storlek.
- Ingen konfiguration krävs.

Att återställa från ögonblicks bilder är tillgängligt för appar som körs i **Premium** -nivån eller högre. Information om hur du skalar upp din app finns i [skala upp en app i Azure](manage-scale-up.md).

## <a name="limitations"></a>Begränsningar

- Funktionen är för närvarande en för hands version.
- Du kan bara återställa till samma app eller till en plats som hör till appen.
- App Service stoppar mål programmet eller mål platsen under återställningen.
- App Service behåller tre månader av ögonblicks bilder för plattforms data återställnings syfte.
- Du kan bara återställa ögonblicks bilder under de senaste 30 dagarna.
- App Services som körs på en App Service-miljön har inte stöd för ögonblicks bilder.
 

## <a name="restore-an-app-from-a-snapshot"></a>Återställa en app från en ögonblicks bild

1. På sidan **Inställningar** i appen i [Azure Portal](https://portal.azure.com)klickar du på **säkerhets kopiering** för att visa sidan **säkerhets kopior** . Klicka sedan på **Återställ** under avsnittet **ögonblicks bild (för hands version)** .
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. På sidan **Återställ** väljer du den ögonblicks bild som ska återställas.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Ange målet för appen Återställ i **återställnings målet**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Om du väljer **Skriv över**raderas och ersätts alla befintliga data i appens aktuella fil system. Kontrol lera att det är det du vill göra innan du klickar på **OK**.
   > 
   > 
      
   > [!Note]
   > På grund av aktuella tekniska begränsningar kan du bara återställa till appar i samma skalnings enhet. Den här begränsningen tas bort i en framtida version.
   > 
   > 
   
    Du kan välja en **befintlig app** för att återställa till en plats. Innan du använder det här alternativet bör du redan ha skapat en plats i din app.

4. Du kan välja att återställa plats konfigurationen.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Klicka på **OK**
