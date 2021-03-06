---
title: Testa Azure Stream Analytics frågor lokalt i Visual Studio
description: Den här artikeln beskriver hur du testar frågor lokalt med Azure Stream Analytics verktyg för Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 01383531293635af8c15f891928eec004e05b54d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925044"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testa Stream Analytics frågor lokalt med Visual Studio

Du kan använda Azure Stream Analytics verktyg för Visual Studio för att testa dina Stream Analytics-jobb lokalt med exempel data eller [real tids data](stream-analytics-live-data-local-testing.md). 

Använd den här [snabb](stream-analytics-quick-create-vs.md) starten för att lära dig hur du skapar ett Stream Analytics jobb med Visual Studio.

## <a name="test-your-query"></a>Testa frågan

I Azure Stream Analytics-projektet dubbelklickar du på **script. asaql** för att öppna skriptet i redigeraren. Du kan kompilera frågan för att se om det finns några syntaxfel. Frågeredigeraren stöder IntelliSense, mönstermatchningssyntax och en fel markör.

![Frågeredigeraren](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Lägg till lokal indatamängd

Om du vill verifiera din fråga mot lokala statiska data högerklickar du på indata och väljer **Lägg till lokal inmatning**.
   
![Lägg till lokal indatamängd](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
I popup-fönstret väljer du exempel data från din lokala sökväg och **Spara**.
   
![Lägg till lokal indatamängd](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
En fil med namnet **local_EntryStream. JSON** läggs automatiskt till i mappen indata.
   
![Lista över lokala filer för indatafiler](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Välj **Kör lokalt** i Frågeredigeraren. Du kan också trycka på F5.
   
![Kör lokalt](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Utdata kan visas i ett tabell format direkt från Visual Studio.

![Utdata i tabell format](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Du kan hitta sökvägen för utdata från konsolens utdata. Tryck på valfri tangent för att öppna mappen result.
   
![Lokal körning](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Kontrol lera resultaten i den lokala mappen.
   
![Resultat från lokal mapp](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Exempel på inmatade
Du kan också samla in exempel indata från dina indata-källor till en lokal fil. Högerklicka på konfigurations filen för indata och välj **exempel data**. 

![Exempeldata](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Du kan bara hämta data direkt från Event Hubs eller IoT-hubbar. Andra ingångs källor stöds inte. I dialog rutan popup fyller du i den lokala sökvägen för att spara exempel data och väljer **exempel**.

![Exempel på data konfiguration](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Du kan se förloppet i fönstret **utdata** . 

![Exempel data utdata](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Nästa steg


* [Snabb start: skapa ett Stream Analytics jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio (för hands version)](stream-analytics-live-data-local-testing.md)
* [Självstudie: Distribuera ett Azure Stream Analytics jobb med CI/CD med hjälp av Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrera och utveckla kontinuerligt med Stream Analytics-verktyg](stream-analytics-tools-for-visual-studio-cicd.md)