---
title: Lägg till autentisering på Android
description: Lär dig hur du använder Azure App Service för att autentisera användare av Android-appen med identitets leverantörer som Google, Facebook, Twitter och Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f68b4f8477d5b21a7107270370af387a7e88756e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668950"
---
# <a name="add-authentication-to-your-android-app"></a>Lägg till autentisering i din Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera molntjänster i ditt mobilprogram kan du registrera dig med [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) i dag.

## <a name="summary"></a>Sammanfattning
I den här självstudien lägger du till autentisering i ToDoList snabb starts projekt på Android med hjälp av en identitets leverantör som stöds. Den här självstudien baseras på självstudien [Kom igång med Mobile Apps] , som du måste slutföra först.

## <a name="register"></a>Registrera din app för autentisering och konfigurera Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Lägg till din app i de tillåtna externa omdirigerings-URL: erna

Säker autentisering kräver att du definierar ett nytt URL-schema för din app. Detta gör att Authentication-systemet kan omdirigera tillbaka till din app när autentiseringen är klar. I den här självstudien använder _vi program_ varan för URL-program i alla. Du kan dock använda alla URL-scheman du väljer. Det bör vara unikt för det mobila programmet. Aktivera omdirigering på Server sidan:

1. I [Azure-portalen]väljer du App Service.

2. Klicka på meny alternativet **autentisering/auktorisering** .

3. I de **tillåtna externa omdirigerings-URL: erna**anger du `appname://easyauth.callback`.  _APPNAME_ i den här STRÄNGEN är URL-schemat för det mobila programmet.  Den bör följa normal URL-specifikation för ett protokoll (Använd bara bokstäver och siffror och börja med en bokstav).  Du bör anteckna den sträng som du väljer när du behöver justera koden för mobil program med URL-schemat på flera platser.

4. Klicka på **OK**

5. Klicka på **Save** (Spara).

## <a name="permissions"></a>Begränsa behörigheter till autentiserade användare
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* I Android Studio öppnar du projektet som du har slutfört med självstudien [Kom igång med Mobile Apps]. Från menyn **Kör** klickar du på **kör app**och kontrollerar att ett ohanterat undantag med status kod 401 (ej behörig) utlöses när appen startar.

     Detta undantag uppstår eftersom appen försöker komma åt Server delen som en oautentiserad användare, men *TodoItem* -tabellen kräver nu autentisering.

Sedan uppdaterar du appen för att autentisera användare innan du begär resurser från Mobile Apps Server del.

## <a name="add-authentication-to-the-app"></a>Lägg till autentisering i appen
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Cachelagra autentiseringstoken på klienten
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här grundläggande autentiseringen kan du fortsätta med någon av följande själv studie kurser:

* [Lägg till push-meddelanden i din Android-app](app-service-mobile-android-get-started-push.md).
  Lär dig hur du konfigurerar Mobile Apps Server del så att Azure Notification Hub används för att skicka push-meddelanden.
* [Aktivera synkronisering offline för din Android-app](app-service-mobile-android-get-started-offline-data.md).
  Lär dig hur du lägger till offline-stöd i din app med hjälp av en Mobile Apps Server del. Med offlinesynkronisering kan användare interagera med en mobilapp&mdash;Visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverks anslutning.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Kom igång med Mobile Apps]: app-service-mobile-android-get-started.md
[Azure-portalen]: https://portal.azure.com/
