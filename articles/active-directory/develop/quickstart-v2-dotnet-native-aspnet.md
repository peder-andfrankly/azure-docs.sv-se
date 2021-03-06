---
title: Anropa Azure AD Protected ASP.NET Web API-Microsoft Identity Platform
description: 'I den här snabb starten lär du dig att anropa ett ASP.NET webb-API som skyddas av Azure Active Directory från ett Windows Desktop-program (WPF). WPF-klienten autentiserar en användare, begär en åtkomsttoken och anropar webb-API: et.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe3301c3c91343277997be1ee554ced76884274a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963315"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-azure-ad"></a>Snabb start: anropa ett ASP.NET-webb-API som skyddas av Azure AD

I den här snabb starten exponerar du ett webb-API och skyddar det så att endast autentiserade användare kan komma åt den. Det här exemplet visar hur du exponerar ett ASP.NET-webb-API så att det kan acceptera token som utfärdats av personliga konton (inklusive outlook.com, live.com och andra) samt arbets-och skol konton från alla företag eller organisationer som har integrerat med Azure Active Directory.

Exemplet innehåller också en Windows Desktop program-klient (WPF) som visar hur du kan begära en åtkomsttoken för att få åtkomst till ett webb-API.

## <a name="prerequisites"></a>Krav

Om du vill köra det här exemplet behöver du följande:

* Visual Studio 2017 eller 2019.  Hämta [Visual Studio kostnads fritt](https://www.visualstudio.com/downloads/).

* Antingen ett [Microsoft-konto](https://www.outlook.com) -eller [Office 365-program för utvecklare](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Hämta eller klona det här exemplet

Du kan klona det här exemplet från ditt gränssnitt eller kommando raden:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Du kan också [Hämta exemplet som en zip-fil](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Registrera ditt webb-API i program registrerings portalen

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Välj den Azure AD-klient där du vill skapa dina program

Om du vill registrera dina appar manuellt, som ett första steg, måste du:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller en personligt Microsoft-konto.
1. Om ditt konto finns i fler än en Azure AD-klient väljer du din profil längst upp till höger på menyn längst upp på sidan och **byter sedan katalog**.
   Ändra din portal-session till önskad Azure AD-klient.

### <a name="register-the-service-app-todolistservice"></a>Registrera tjänst appen (TodoListService)

1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Välj **ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Ändra **konto typer som stöds** till **konton i valfri organisations katalog**.
   - Välj **Registrera** för att skapa programmet.

1. På sidan **Översikt över** appen letar du reda på **programmets ID-** värde och registrerar det för senare. Du behöver den för att konfigurera Visual Studio-konfigurationsfilen för det här projektet (`ClientId` i `TodoListService\Web.config`).
1. Välj avsnittet **exponera ett API** och:
   - Välj **Lägg till ett omfång**
   - Godkänn den föreslagna program-ID-URI: n (API://{clientId}) genom att välja **Spara och fortsätt**
   - Ange följande parametrar:
     - Använd `access_as_user` för **omfattnings namn**
     - Se till att alternativet **Administratörer och användare** är markerat för **vem som kan** godkänna
     - i typ av **visnings namn för administratörs medgivande** `Access TodoListService as a user`
     - i **Beskrivning av administratörs medgivande** `Accesses the TodoListService Web API as a user`
     - i **användar medgivande visnings namn** skriver `Access TodoListService as a user`
     - i **användar medgivande Beskrivning** typ `Accesses the TodoListService Web API as a user`
     - Behåll **status** som **aktive rad**
     - Välj **Lägg till omfattning**

### <a name="configure-the-service-and-client-projects-to-match-the-registered-web-api"></a>Konfigurera tjänst-och klient projekt så att de matchar det registrerade webb-API: et 

1. Öppna lösningen i Visual Studio och öppna filen **Web. config** under roten för **TodoListService** -projektet.
1. Ersätt värdet för `ida:ClientId` parameter med **klient-ID: t (program-ID)** från programmet som du precis registrerade i program registrerings portalen.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Lägg till det nya omfånget i *TodoListClient*app. config

1. Öppna filen **app. config** i **TodoListClient** -projektets rotmapp och klistra sedan in **program-ID** från programmet som du precis registrerade för din *TodoListService* under `TodoListServiceScope` parameter, och ersätt strängen `{Enter the Application ID of your TodoListService from the app registration portal}`.

   > Obs: kontrol lera att den använder följande format:
   >
   > `api://{TodoListService-Application-ID}/access_as_user` 
   >
   >(där {TodoListService-Application-ID} är GUID som representerar program-ID: t för din TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Registrera klient programmet (TodoListClient)

I det här steget konfigurerar du ditt *TodoListClient* -projekt genom att registrera ett nytt program i program registrerings portalen. I de fall där klienten och servern betraktas som *samma program* kan du också bara återanvända samma program som registrerats i "steg 2". Att använda samma program behövs om du vill att användarna ska logga in med Microsoft personal konton

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Registrera *TodoListClient* -programmet i *program registrerings portalen*

1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Välj **ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `NativeClient-DotNet-TodoListClient`.
   - Ändra **konto typer som stöds** till **konton i valfri organisations katalog**.
   - Välj **Registrera** för att skapa programmet.
1. Välj avsnittet **autentisering** på appens översikts sida.
   - I avsnittet **omdirigerings-URL: er** | **föreslagna omdirigerings-URL: er för offentliga klienter (Mobile, Desktop)** kontrollerar du **urn: IETF: WG: OAuth: 2.0: OOB**
   - Välj **Spara**.
1. Välj avsnittet **API-behörigheter**
   - Klicka på knappen **Lägg till en behörighet** och sedan
   - Välj fliken **Mina API: er** .
   - I listan över API: er väljer du `AppModelv2-NativeClient-DotNet-TodoListService API`eller det namn som du angav för webb-API: et.
   - Kontrol lera **access_as_user** behörigheten om den inte redan är markerad. Använd Sök fältet om det behövs.
   - Välj knappen **Lägg till behörigheter**

### <a name="configure-your-todolistclient-project"></a>Konfigurera ditt *TodoListClient* -projekt

1. På sidan **Översikt** i *program registrerings portalen*kopierar du värdet för **program-ID: t (klient)**
1. Öppna filen **app. config** som finns i **TodoListClient** -projektets rotmapp och klistra sedan in värdet i värdet för parametern `ida:ClientId`

## <a name="run-your-project"></a>Köra projektet

1. Tryck på `<F5>` för att köra projektet. Din *TodoListClient* bör vara öppen.
1. Välj **Logga** in längst upp till höger och logga in med samma användare som du har använt för att registrera ditt program, eller en användare i samma katalog.
1. Om du loggar in för första gången kan du vid det här tillfället uppmanas att godkänna *TodoListService* webb-API.
1. Inloggningen begär också åtkomst-token till *access_as_user* omfång för att komma åt *TodoListService* -webb-API och ändra *att göra-* listan.

## <a name="pre-authorize-your-client-application"></a>För att auktorisera ditt klient program

Ett sätt att tillåta användare från andra kataloger att komma åt ditt webb-API är genom att *förauktorisera* klient programmen för att få åtkomst till ditt webb-API genom att lägga till program-ID: n från klient program i listan över *förauktoriserade* program för ditt webb-API. Genom att lägga till en förauktoriserad klient behöver du inte användare som medgivande för att använda ditt webb-API. Följ stegen nedan för att auktorisera webb programmet:

1. Gå tillbaka till *program registrerings portalen* och öppna egenskaperna för din **TodoListService**.
1. I avsnittet **exponera ett API** klickar du på **Lägg till ett klient program** under avsnittet *auktoriserade klient program* .
1. I fältet *klient-ID* klistrar du in program-ID: t för `TodoListClient` programmet.
1. I avsnittet *auktoriserade omfattningar* väljer du omfattning för det här webb-API-`api://<Application ID>/access_as_user`.
1. Tryck på knappen **Lägg till program** längst ned på sidan.

## <a name="run-your-project"></a>Köra projektet

1. Tryck på `<F5>` för att köra projektet. Din *TodoListClient* bör vara öppen.
1. Välj **Logga** in längst upp till höger (eller Rensa cacheminne/logga in) och logga sedan in antingen med ett personligt Microsoft-konto (live.com eller hotmail.com) eller arbets-eller skol konto.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Valfritt: begränsa inloggnings åtkomst till ditt program

När du laddar ned kod exemplet och konfigurerar programmet till att använda Azure Active Directory v2-slutpunkten enligt föregående steg, så är både personliga konton-som outlook.com, live.com, och andra – arbets-eller skol konton från valfri organisationer som är integrerade med Azure AD kan begära tokens och få åtkomst till ditt webb-API. 

Använd något av alternativen för att begränsa vem som kan logga in till ditt program:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Alternativ 1: begränsa åtkomsten till en enskild organisation (en enda klient)

Du kan begränsa inloggnings åtkomsten för ditt program till endast användar konton som finns i en enda Azure AD-klient, inklusive *gäst konton* för den klient organisationen. Det här scenariot är ett vanligt för *branschspecifika program*:

1. Öppna filen **App_Start \STARTUP.auth** och ändra värdet för slut punkten för metadata som skickas till `OpenIdConnectSecurityTokenProvider` till `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (du kan också använda klient namnet, t. ex. `contoso.onmicrosoft.com`).
2. I samma fil ställer du in egenskapen `ValidIssuer` i `TokenValidationParameters` till `"https://sts.windows.net/{Tenant ID}/"` och `ValidateIssuer` argumentet till `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Alternativ 2: Använd en anpassad metod för att validera utfärdare

Du kan implementera en anpassad metod för att verifiera utfärdare med hjälp av parametern **IssuerValidator** . Mer information om hur du använder den här parametern finns i om [klassen TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om det skyddade webb-API-scenariot som Microsoft Identity Platform stöder:
> [!div class="nextstepaction"]
> [Skyddat webb-API-scenario](scenario-protected-web-api-overview.md)
