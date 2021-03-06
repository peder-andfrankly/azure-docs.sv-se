---
title: Självstudie – Konfigurera Azure CDN cachelagringsregler | Microsoft Docs
description: I den här självstudiekursen får du ställa in en global regel för cachelagring i Azure CDN och en anpassad regel för cachelagring.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5330c99f4d42f591944ddc5c58ac98bea6387da5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278151"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>Självstudie: Konfigurera Azure CDN-cachelagringsregler

> [!NOTE] 
> Reglerna för cachelagring är bara tillgängliga för **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** -profiler. För **Azure CDN från Microsoft** -profiler måste du använda [standard regel motorn](cdn-standard-rules-engine-reference.md) för att **Azure CDN Premium från Verizon** -profiler, du måste använda [verktyget Verizon Premium regler](cdn-rules-engine.md) i **hanterings** portalen för liknande funktioner.
 

Den här självstudien beskriver hur du kan använda cachelagringsregler i Azure Content Delivery Network (CDN) för att ange eller ändra standardbeteendet för giltighetstid för cache, både globalt och med anpassade villkor, till exempel en URL-sökväg och ett filnamnstillägg. Azure CDN har två typer av cachelagringsregler:
- Globala cachelagringsregler: Du kan ange en regel för globala cachelagringsregler för varje slutpunkt i din profil, vilket påverkar alla förfrågningar till slutpunkten. Den globala cachelagringsregeln åsidosätter eventuella huvuden för HTTP-cachedirektiv, om angivna.

- Anpassade cachelagringsregler: Du kan ange en eller flera anpassade cachelagringsregler för varje slutpunkt i din profil. Anpassade cachelagringsregler matchar specifika sökvägar och filnamnstillägg, bearbetas i ordning och åsidosätter den globala cacheregeln om sådan finns. 

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> - Öppna sidan för cachelagringsregler.
> - Skapa en global cachelagringsregel.
> - Skapa en anpassad regel för cachelagring.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Innan du kan slutföra stegen i den här kursen måste du först skapa en CDN-profil och minst en CDN-slutpunkt. Mer information finns i [Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt](cdn-create-new-endpoint.md).

## <a name="open-the-azure-cdn-caching-rules-page"></a>Öppna sidan för Azure CDN-cachelagringsregler

1. I [Azure Portal](https://portal.azure.com), välj en CDN-profil och sedan väljer du en slutpunkt.

2. I det vänstra fönstret under inställningar, väljer du **Cachelagringsregler**.

   ![Knappen CDN-cachelagringsregler](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Sidan **Cachelagringsregler** visas.

   ![Sidan CDN-cachelagringsregler](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>Ange globala cachelagringsregler

Skapa en global cachelagringsregel enligt följande:

1. Under **Globala cachelagringsregler**, ange **Frågesträng för cachelagringsbeteende** till **Ignorera frågesträngar**.

2. Ange **Cachelagringsbeteende** till **Ange om det saknas**.
       
3. För **Giltighetstid för cache**, skriver du 10 i fältet **Dagar**.

    Den globala cacheregeln påverkar alla förfrågningar till slutpunkten. Den här regeln godkänner ursprungliga huvuden för cache-direktiv, om de finns (`Cache-Control` eller `Expires`), annars, om de inte har angetts, anges cachen till 10 dagar. 

    ![Globala cachelagringsregler](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>Ange anpassade cachelagringsregler

Skapa en anpassad cachelagringsregel enligt följande:

1. Under **Anpassade cachelagringsregler**, ange **Matchar villkor** till **Sökväg** och **Matcha värdet** till `/images/*.jpg`.
    
2. Ange **Cachelagringsbeteende** till **Åsidosätt** och ange 30 i fältet **Dagar**.
       
    Den här anpassade cacheregeln anger cachevaraktighet på 30 dagar på alla `.jpg`-bildfiler i `/images`-mappen för din slutpunkt. Den åsidosätter eventuella `Cache-Control` -eller `Expires`-HTTP-huvuden som skickats av den ursprungliga servern.

    ![Anpassade cachelagringsregler](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du cachelagringsregler. Om du inte längre vill använda cachelagringsreglerna kan du ta bort dem genom att utföra dessa steg:
 
1. Välj en CDN-profil, och välj sedan den slutpunkt med cachelagringsregler som du vill ta bort.

2. I det vänstra fönstret under inställningar, väljer du **Cachelagringsregler**.

3. Under **Globala cachelagringsregler**, ange **Cachelagringsbeteende** till **Inte angivet**.
 
4. Under **Anpassade cachelagringsregler**, markera kryssrutan bredvid den regel som du vill ta bort.

5. Välj **Ta bort**.

6. Överst på sidan väljer du **Spara**.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Öppna sidan för cachelagringsregler.
> - Skapa en global cachelagringsregel.
> - Skapa en anpassad regel för cachelagring.

Gå vidare till nästa artikeln för att lära du dig hur du konfigurerar ytterligare inställningar för cachelagringsregler.

> [!div class="nextstepaction"]
> [Kontrollera funktionssättet för Azure CDN-cachelagring med cachelagringsregler](cdn-caching-rules.md)



