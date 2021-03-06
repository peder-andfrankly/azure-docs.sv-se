---
title: Skapa ett kunskaps lager (för hands version) i Azure Portal
titleSuffix: Azure Cognitive Search
description: Använd guiden Importera data för att skapa ett kunskaps lager som används för beständigt innehåll. Anslut till ett kunskaps lager för analys från andra appar eller skicka berikat innehåll till efterföljande processer. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: a8cc368b2949d9a65034ee4f989b8603dfa01027
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533945"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Snabb start: skapa ett kunskaps lager för Azure Kognitiv sökning i Azure Portal

> [!IMPORTANT] 
> Kunskaps lagret är för närvarande en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Knowledge Store är en funktion i Azure Kognitiv sökning som sparar utdata från en kognitiv skicklighets pipeline för efterföljande analyser eller underordnad bearbetning. 

En pipeline tar emot bilder och ostrukturerad text som RAW-innehåll, använder AI genom Cognitive Services (till exempel bild och naturlig språk bearbetning) och skapar ett berikat innehåll (nya strukturer och information) som utdata. En av de fysiska artefakterna som skapas av en pipeline är ett [kunskaps lager](knowledge-store-concept-intro.md), som du kan komma åt via verktyg för att analysera och utforska innehåll.

I den här snabb starten ska du kombinera tjänster och data i Azure-molnet för att skapa ett kunskaps lager. När allt är på plats kör du guiden **Importera data** i portalen för att hämta den tillsammans. Slut resultatet är ursprungligt och AI-genererat innehåll som du kan visa i portalen ([Storage Explorer](knowledge-store-view-storage-explorer.md)).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-services-and-load-data"></a>Skapa tjänster och läsa in data

I den här snabb starten används Azure Kognitiv sökning Azure Blob Storage och [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) för AI. 

Eftersom arbets belastningen är så liten är Cognitive Services i bakgrunden för att tillhandahålla kostnads fri bearbetning för upp till 20 transaktioner dagligen när de anropas från Azure Kognitiv sökning. Så länge du använder de exempel data som vi tillhandahåller kan du hoppa över att skapa eller bifoga en Cognitive Services-resurs.

1. [Hämta HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Dessa data är hotell gransknings data som sparats i en CSV-fil (härstammar från Kaggle.com) och innehåller 19 stycken kundfeedback om ett enda hotell. 

1. [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) eller [hitta ett befintligt konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) under din aktuella prenumeration. Du använder Azure Storage för både det råa innehåll som ska importeras och kunskaps lagret som är slut resultatet.

   Det finns två krav för det här kontot:

   + Välj samma region som Azure Kognitiv sökning. 
   
   + Välj konto typen StorageV2 (General Purpose v2). 

1. Öppna BLOB Services-sidorna och skapa en behållare.  

1. Klicka på **Överför**.

    ![Överför data](media/knowledge-store-create-portal/upload-command-bar.png "Ladda upp Hotell recensioner")

1. Välj den **HotelReviews-Free. csv** -fil som du laddade ned i det första steget.

    ![Skapa Azure Blob-behållaren](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Skapa Azure Blob-behållaren")

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. Get a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

1. [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Du kan använda en kostnads fri tjänst för den här snabb starten.

Nu kan du gå vidare till guiden Importera data.

## <a name="run-the-import-data-wizard"></a>Kör guiden Importera data

På översikts sidan för Search-tjänsten klickar du på **Importera data** i kommando fältet för att skapa ett kunskaps lager i fyra steg.

  ![Kommandot Importera data](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

1. I **Anslut till dina data** väljer du **Azure Blob Storage** och väljer kontot och containern som du skapade. 
1. För **namnet**anger du `hotel-reviews-ds`.
1. För **tolknings läge**väljer du **avgränsad text**och markerar sedan kryss rutan **första raden innehåller rubrik** . Se till att **avgränsnings tecken** är ett kommatecken (,).
1. Ange din **anslutnings sträng** för lagrings tjänsten som du sparade i föregående steg.
1. Ange `hotel-reviews`för **container namn**.
1. Klicka på **Nästa: Lägg till AI-anrikning (valfritt)** .

      ![Skapa ett data käll objekt](media/knowledge-store-create-portal/hotel-reviews-ds.png "Skapa ett data käll objekt")

1. Fortsätt till nästa sida.

### <a name="step-2-add-cognitive-skills"></a>Steg 2: Lägga till kognitiva kunskaper

I den här guiden ska du skapa en färdigheter med kognitiva färdigheter. De kunskaper vi använder i det här exemplet extraherar viktiga fraser och identifierar språk-och sentiment. I ett senare steg kommer dessa att bli "projiceras" i ett kunskaps lager som Azure-tabeller.

1. Expandera **bifoga Cognitive Services**. **Kostnads fri (begränsade anrikninger)** är valt som standard. Du kan använda den här resursen eftersom antalet poster i HotelReviews-Free. csv är 19 och den här kostnads fria resursen tillåter upp till 20 transaktioner per dag.
1. Expandera **Lägg till kognitiva kunskaper**.
1. Ange `hotel-reviews-ss`för **namn på färdigheter**.
1. I **fältet käll data**väljer du **reviews_text**.
1. Välj **sidor (5000-segment)** för **detaljerad granularitet nivå**
1. Välj dessa kognitiva kunskaper:
    + **Extrahera nyckelfraser**
    + **Identifiera språk**
    + **Identifiera sentiment**

      ![Skapa en färdigheter](media/knowledge-store-create-portal/hotel-reviews-ss.png "Skapa en kunskapsuppsättning")

1. Expandera **Spara berikade kunskaper i kunskaps lagret**.
1. Ange **lagrings kontots anslutnings sträng** som du sparade i föregående steg.
1. Välj följande **Azure Table-projektioner**:
    + **Dokument**
    + **Sidor**
    + **Nyckel fraser**

    ![Konfigurera kunskaps lager](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurera kunskaps lager")

1. Fortsätt till nästa sida.

### <a name="step-3-configure-the-index"></a>Steg 3: Konfigurera indexet

I det här steget konfigurerar du ett index för valfria full texts öknings frågor. Guiden kommer att sampla data källan för att härleda fält och data typer. Du behöver bara välja attributen för det önskade beteendet. Attributet för **hämtning** kan till exempel tillåta Sök tjänsten att returnera ett fält värde medan **sökbart** aktiverar fullständig texts ökning i fältet.

1. Ange `hotel-reviews-idx`för **index namn**.
1. För attribut gör du följande val:
    + Välj **hämtnings** Bart för alla fält.
    + Välj **Filterable** och **fasettable** för dessa fält: *sentiment*, *språk*, diskussions *fraser*
    + Välj **sökbart** för följande fält: *stad*, *namn*, *reviews_text*, *språk*, diskussions *fraser*

    Ditt index bör se ut ungefär som på följande bild. Eftersom listan är lång visas inte alla fält i bilden.

    ![Konfigurera ett index](media/knowledge-store-create-portal/hotel-reviews-idx.png "Konfigurera ett index")

1. Fortsätt till nästa sida.

### <a name="step-4-configure-the-indexer"></a>Steg 4: Konfigurera indexeraren

I det här steget ska du konfigurera en indexerare som hämtar data källan, färdigheter och det index som du definierade i föregående steg i guiden.

1. I **namn**anger du `hotel-reviews-idxr`.
1. Behåll **standardvärdet**för **schema**.
1. Klicka på **Skicka** för att köra indexeraren. Data extrahering, indexering, program av kognitiva kunskaper alla sker i det här steget.

## <a name="monitor-status"></a>Övervaka status

Kognitiva kunskaps indexering tar längre tid än vanlig text baserad indexering. Guiden ska öppna listan med indexerare på översiktssidan så att du kan följa förloppet. För självnavigering går du till översikten över sidan och klickar på **indexerare**.

I Azure Portal kan du också övervaka aktivitets loggen för meddelanden som länkar till en klicknings bara **Azure kognitiv sökning meddelande** status. Det kan ta flera minuter att slutföra körningen.

## <a name="next-steps"></a>Nästa steg

Nu när du har berikat dina data med Cognitive Services och projicerat resultaten i ett kunskaps lager, kan du använda Storage Explorer eller Power BI för att utforska din omfattande data uppsättning.

Du kan visa innehåll i Storage Explorer eller ta ett steg ytterligare med Power BI för att få insikter genom visualisering.

> [!div class="nextstepaction"]
> [Visa med Storage Explorer](knowledge-store-view-storage-explorer.md)
> [ansluta till Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Om du vill upprepa den här övningen eller testa en annan AI-förhands granskning tar du bort indexet *hotell reidxrs-* . Om du tar bort indexeraren återställs den kostnads fria dagliga transaktions räknaren tillbaka till noll för Cognitive Services bearbetning.
