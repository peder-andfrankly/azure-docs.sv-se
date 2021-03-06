---
title: Bygg en app med Azure AD AngularJS för inloggnings & Logga ut | Microsoft Docs
description: 'Lär dig hur du skapar en AngularJS-app med en enda sida som integrerar Azure AD för inloggning & utloggning & anropar Azure AD-skyddade API: er med hjälp av OAuth.'
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: javascript
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e66b51199d19ea6af2b88c48aa89a93c1ed731c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963536"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Snabbstart: Skapa en enkelsidig AngularJS-webbapp för inloggning och utloggning med Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

> [!IMPORTANT]
> [Microsoft Identity Platform](v2-overview.md) är en utveckling av Azure Active Directory (Azure AD) Developer Platform. Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter och hämtar tokens för att anropa Microsoft API:er som Microsoft Graph eller API:er som utvecklare har byggt.
> Om du behöver aktivera inloggning för personliga konton förutom arbets-och skol konton kan du använda *[Microsoft Identity Platform-slutpunkten](azure-ad-endpoint-comparison.md)* .
> Den här snabb starten är för den äldre Azure AD v 1.0-slutpunkten. Vi rekommenderar att du använder v 2.0-slutpunkten för nya projekt. Mer information finns i [den här själv studie kursen om Java scripts Spa](tutorial-v2-javascript-spa.md) och [den här artikeln](active-directory-v2-limitations.md) förklarar *slut punkten för Microsoft Identity Platform*.

Azure Active Directory (AD Azure) gör det enkelt att lägga till inloggning, utloggning och säkra OAuth API-anrop till enkelsidiga appar. Det gör att dina appar kan autentisera användare med deras Windows Server Active Directory-konton och använda valfritt webb-API som Azure AD hjälper till att skydda, till exempel Office 365-API:er eller Azure API.

För JavaScript-program som körs i en webbläsare tillhandahåller Azure AD Active Directory Authentication Library (ADAL), eller adal.js. Det enda syftet med adal.js är att göra det enkelt för din app för att få åtkomsttoken.

I den här snabbstarten lär du dig hur du skapar ett att göra-program med AngularJS som:

* Loggar in användaren till appen med hjälp av Azure AD som identitetsprovider.
* Visar viss information om användaren.
* På ett säkert sätt anropar appens att göra-API med hjälp av ägartoken från Azure AD.
* Loggar ut användaren ur appen.

Om du vill skapa ett komplett, fungerande program måste du:

1. Registrera appen med Azure AD.
2. Installera ADAL och konfigurera ensidesappen.
3. Använd ADAL för att säkra sidor i ensidesappen.

## <a name="prerequisites"></a>Krav

Kom igång genom att slutföra följande krav:

* [Ladda ned appens stomme](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) eller [ladda ned det färdiga exemplet](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).
* Ha en Azure AD-klientorganisation där du kan skapa användare och registrera en app. Om du inte redan har en klientorganisation kan du [läsa om hur du skaffar en](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Steg 1: Registrera DirectorySearcher-programmet

Om du vill göra så att appen kan autentisera användare och hämta token behöver du först registrera den i din Azure AD-klientorganisation:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Om du är inloggad i flera kataloger kan du behöva se till att du är i rätt katalog. Gör det genom att klicka på ditt konto i fältet längst upp. Under **kataloglistan** väljer du den Azure AD-klientorganisation där du vill registrera programmet.
1. Klicka på **Alla tjänster** i den vänstra fönsterrutan och välj sedan **Azure Active Directory**.
1. Klicka på **Appregistreringar**och välj sedan **ny registrering**.
1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
1. Välj **webb** plattformen under avsnittet **omdirigerings-URI** och ange värdet till `https://localhost:44326/` (platsen som Azure AD ska returnera token).
1. När det är klart väljer du **Registrera**. På appens sida **Översikt** antecknar du värdet för **Application (client) ID** (Program-ID (klient)).
1. Adal.js använder det implicita flödet för OAuth för att kommunicera med Azure AD. Du måste aktivera det implicita flödet för ditt program. I det vänstra navigeringsfönstret för det registrerade programmet väljer du **Autentisering**.
1. I **Avancerade inställningar** går du till **Implicit beviljande** och aktiverar kryssrutorna **ID-token** och **Åtkomsttoken**. ID-token och åtkomsttoken krävs eftersom den här appen behöver logga in användare och anropa ett API.
1. Välj **Spara**.
1. Ge behörigheter till hela klientorganisationen för programmet. Gå till **API-behörigheter**och välj knappen **bevilja administratörs medgivande** under **bevilja medgivande**.
1. Välj **Ja** för att bekräfta.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Steg 2: Installera ADAL och konfigurera ensidesappen

Nu när du har ett program i Azure AD kan du installera adal.js och skriva din identitetsrelaterade kod.

### <a name="configure-the-javascript-client"></a>Konfigurera JavaScript-klienten

Börja genom att lägga till adal.js i TodoSPA-projektet med hjälp av Package Manager-konsolen:

1. Ladda ned [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) och lägg till det i projektkatalogen `App/Scripts/`.
2. Ladda ned [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) och lägg till det i projektkatalogen `App/Scripts/`.
3. Läs in varje skript före slutet av `</body>` i `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurera backend-servern

För att ensidesappens serverdels att göra-API ska kunna acceptera token från webbläsaren behöver serverdelen konfigurationsinformation om appregistreringen. I TodoSPA-projektet öppnar du `web.config`. Ersätt värdena för elementen i avsnittet `<appSettings>` så att de speglar de värden som du använde i Azure-portalen. Koden kommer att referera till dessa värden när den använder ADAL.

   * `ida:Tenant` är domänen för din Azure AD-klientorganisation – till exempel contoso.onmicrosoft.com.
   * `ida:Audience` är det klient-ID för din app som du kopierade från portalen.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Steg 3: Använda ADAL för att säkra sidor i ensidesappen

Adal.js integreras med AngularJS-vägen och HTTP-providers, så du kan hjälpa till att säkra enskilda vyer i din ensidesapp.

1. I `App/Scripts/app.js` tar du med adal.js-modulen:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Initiera `adalProvider` genom att använda konfigurationsvärdena för din programregistrering, även i `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Skydda `TodoList`-vyn i appen med hjälp av endast en kodrad: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Sammanfattning

Nu har du en säker ensidesapp som kan logga in användare och skicka ägartokenskyddade begäranden till sitt serverdels-API. När en användare klickar **TodoList**-länken omdirigera adal.js automatiskt till Azure AD för inloggning om det behövs. Dessutom kommer adal.js att automatiskt koppla en åtkomsttoken till alla Ajax-begäranden som skickas till appens serverdel.

Föregående steg är minimikraven för att skapa en ensidesapp med hjälp av adal.js. Det finns dock några andra funktioner som är användbara i ensidesapp:

* För att explicit utfärda inloggnings-och utloggningsbegäranden kan du definiera funktioner i de styrenheter som anropar adal.js. Följande gäller i `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Det kan vara bra att visa användarinformation i appens användargränssnitt. ADAL-tjänsten har redan lagts till i `userDataCtrl`-styrenheten, så du kan komma åt objektet `userInfo` i den associerade vyn `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Det finns många scenarier där du vill veta om användaren är inloggad eller inte. Du kan även använda objektet `userInfo` för att samla in den här informationen. Till exempel kan du i `index.html` visa antingen knappen **Logga in** eller **Logga ut** baserat på autentiseringsstatus:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Din Azure AD-integrerade ensidesapp kan autentisera användare, anropa sin backend-server på ett säkert sätt med hjälp av OAuth 2.0 och få grundläggande information om användaren. Om du inte redan har gjort det är det dags att fylla i klientorganisationen med några användare. Kör din att göra-ensidesapp och logga in med någon av dessa användare. Lägg till aktiviteter i användarens att göra-lista, logga ut och logga in igen.

Adal.js gör det enkelt att inkorporera vanliga identitetsfunktioner i appen. Det tar hand om allt grovjobb åt dig: cachehantering, stöd för OAuth-protokoll, visa användaren ett användargränssnitt för inloggning, uppdatera token som gått ut och mer.

Som referens finns det slutförda exemplet (utan dina konfigurationsvärden) på [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med fler scenarier.

> [!div class="nextstepaction"]
> [Anropa ett CORS-webb-API från en ensidesapp](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).
