---
title: Faktisk slutanvändarmätning med webb sidor – Azure Traffic Manager
description: I den här artikeln lär du dig hur du konfigurerar dina webb sidor för att skicka Faktisk slutanvändarmätning till Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 6e3cf5af5aaa894b1595d67c0056073a458b0a88
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040307"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Så här skickar du Faktisk slutanvändarmätning till Azure Traffic Manager med webb sidor

Du kan konfigurera dina webb sidor så att de skickar Faktisk slutanvändarmätning till Traffic Manager genom att skaffa en Faktisk slutanvändarmätning (RUM) nyckel och bädda in den genererade koden på webb sidan.

## <a name="obtain-a-real-user-measurements-key"></a>Hämta en Faktisk slutanvändarmätning nyckel

De mätningar du tar och skickar till Traffic Manager från ditt klient program identifieras av tjänsten med hjälp av en unik sträng, som kallas **faktisk slutanvändarmätning (Rom) nyckel**. Du kan hämta en RUM nyckel med hjälp av Azure Portal, en REST API eller med hjälp av PowerShell eller Azure CLI.

Hämta RUM-nyckeln med hjälp av Azure Portal:
1. Logga in på Azure Portal i en webbläsare. Om du inte redan har ett konto kan du registrera dig för en kostnads fri utvärderings period på en månad.
2. I portalens sökfält söker du efter namnet på Traffic Manager profilen som du vill ändra och klickar sedan på Traffic Manager profilen i resultatet som visas.
3. I bladet Traffic Manager profil klickar du på **faktisk slutanvändarmätning** under **Inställningar**.
4. Klicka på **generera nyckel** för att skapa en ny rum nyckel.
 
   ![Generera Faktisk slutanvändarmätning nyckel](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Bild 1: Faktisk slutanvändarmätning nyckel skapande**

5. Bladet visar nu att RUM-nyckeln har genererats och att ett JavaScript-kodfragment måste vara inbäddat i din HTML-sida.
 
    ![JavaScript-kod för Faktisk slutanvändarmätning nyckel](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Bild 2: skript för att Faktisk slutanvändarmätning nyckel och mätning**
 
6. Klicka på **kopierings** knappen för att kopiera JavaScript-koden. 

>[!IMPORTANT]
> Använd det genererade JavaScript-kommandot för Faktisk slutanvändarmätning funktionen för att fungera korrekt. Eventuella ändringar av det här skriptet eller skripten som används av Faktisk slutanvändarmätning kan leda till oförutsägbara beteenden.

## <a name="embed-the-code-to-an-html-web-page"></a>Bädda in koden på en HTML-webbsida

När du har fått nyckeln för RUM är nästa steg att bädda in det kopierade JavaScript-kommandot på en HTML-sida som dina slutanvändare besöker. Redigera HTML kan göras på många sätt och med olika verktyg och arbets flöden. Det här exemplet visar hur du uppdaterar en HTML-sida för att lägga till det här skriptet. Du kan använda den här vägledningen för att anpassa den till ditt arbets flöde för hantering av HTML-datakälla.

1.  Öppna HTML-sidan i en text redigerare
2.  Klistra in JavaScript-koden som du kopierade i föregående steg till avsnittet BRÖDTEXT i HTML (den kopierade koden finns på rad 8 & 9, se bild 3).
 
    ![Bädda in JavaScript-kod på webb sidan för Faktisk slutanvändarmätning](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Bild 3: enkel HTML med Embedded Faktisk slutanvändarmätning Java Script**

3.  Spara HTML-filen och placera den på en webb server som är ansluten till Internet. 
4. Nästa gång den här sidan renderas i en webbläsare laddas det JavaScript-skript som refereras till och skriptet kör mät-och rapporterings åtgärderna.


## <a name="next-steps"></a>Nästa steg
- Läs mer om [faktisk slutanvändarmätning](traffic-manager-rum-overview.md)
- Lär dig [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Läs mer om [metoderna för trafik-routning](traffic-manager-routing-methods.md) som stöds av Traffic Manager
- Lär dig hur du [skapar en Traffic Manager-profil](traffic-manager-create-profile.md)

