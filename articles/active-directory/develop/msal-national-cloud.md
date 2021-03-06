---
title: Använda MSAL i en nationell molnbaserad app | Azure
titleSuffix: Microsoft identity platform
description: 'Microsoft Authentication Library (MSAL) gör det möjligt för programutvecklare att förvärva tokens för att anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft API: er, partner webb-API: er eller ditt eget webb-API. MSAL stöder flera program arkitekturer och plattformar.'
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1d0d4511b95d56ae41bf9fbb1118318d8374bde
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916041"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Använda MSAL i en nationell moln miljö

[Nationella moln](authentication-national-cloud.md), även kallade suveräna moln, är fysiskt isolerade instanser av Azure. Dessa regioner i Azure bidrar till att se till att kraven på data placering, suveränitet och efterlevnad är uppfyllda inom geografiska gränser.

Förutom Microsofts världs omspännande moln, gör Microsoft Authentication Library (MSAL) det möjligt för programutvecklare i nationella moln att förvärva tokens för att autentisera och anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph eller andra Microsoft API: er.

Med det globala molnet distribueras Azure Active Directory (Azure AD) i följande nationella moln:  

- Azure Government
- Azure Kina 21Vianet
- Azure Germany

Den här guiden visar hur du loggar in på arbets-och skol konton, hämtar en åtkomsttoken och anropar Microsoft Graph API i [Azure Government moln](https://azure.microsoft.com/global-infrastructure/government/) miljö.

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrol lera att du uppfyller dessa krav.

### <a name="choose-the-appropriate-identities"></a>Välj lämpliga identiteter

[Azure Government](https://docs.microsoft.com/azure/azure-government/) program kan använda Azure AD myndighets identiteter och offentliga Azure AD-identiteter för att autentisera användare. Eftersom du kan använda någon av dessa identiteter måste du bestämma vilken auktoritets slut punkt du ska välja för ditt scenario:

- Azure AD Public: används ofta om din organisation redan har en offentlig Azure AD-klient som stöder Office 365 (offentlig eller GCC) eller något annat program.
- Azure AD myndigheter: används ofta om din organisation redan har en Azure AD myndighets-klient som stöder Office 365 (GCC High eller DoD) eller skapar en ny klient i Azure AD myndigheter.

När du har bestämt dig är det viktigt att du utför registreringen av appen. Om du väljer Azure AD-offentliga identiteter för ditt Azure Government-program måste du registrera programmet i din offentliga Azure AD-klient.

### <a name="get-an-azure-government-subscription"></a>Hämta en Azure Government-prenumeration

Information om hur du hämtar en Azure Government-prenumeration finns [i hantera och ansluta till din prenumeration i Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Om du inte har en Azure Government prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/global-infrastructure/government/request/) innan du börjar.

Om du vill ha mer information om hur du använder ett nationellt moln med ett visst programmeringsspråk väljer du den flik som matchar ditt språk:

## <a name="nettabdonet"></a>[NET](#tab/donet)

Du kan använda MSAL.NET för att logga in användare, Hämta token och anropa Microsoft Graph API i nationella moln.

Följande självstudier visar hur du skapar en .NET Core 2,2 MVC-webbapp. Appen använder OpenID Connect för att logga in användare med ett arbets-och skol konto i en organisation som tillhör ett nationellt moln.

- Om du vill logga in användare och hämta tokens följer du den här självstudien: [Bygg en ASP.net Core webbappens inloggnings användare i suveräna moln med Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Om du vill anropa API: et för Microsoft Graph följer du den här självstudien: [använda Microsoft Identity Platform för att anropa Microsoft Graph-API: et från en ASP.net Core 2. x-webbapp för en användar inloggning med sitt arbets-och skol konto i Microsofts nationella moln](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Så här aktiverar du ditt MSAL. js-program för suveräna moln:

### <a name="step-1-register-your-application"></a>Steg 1: Registrera din app

1. Logga in på [Azure-portalen](https://portal.azure.us/).
    
   Information om hur du hittar Azure Portal slut punkter för andra nationella moln finns i [slut punkter för registrering av appar](authentication-national-cloud.md#app-registration-endpoints).

1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet och ställer in din portal-session till önskad Azure AD-klient.
1. Gå till sidan [Appregistreringar](https://aka.ms/ra/ff) på Microsoft Identity Platform för utvecklare.
1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
1. Under **konto typer som stöds**väljer du **konton i valfri organisations katalog**.
1. I avsnittet **omdirigerings-URI** väljer du **webb** plattform och anger värdet till programmets URL-adress baserat på din webb server. I nästa avsnitt finns anvisningar om hur du ställer in och hämtar omdirigerings-URL: en i Visual Studio och Node.
1. Välj **Registrera**.
1. På appens sida **Översikt** antecknar du värdet för **Application (client) ID** (Program-ID (klient)).
1. I den här självstudien krävs att du aktiverar det [implicita tilldelnings flödet](v2-oauth2-implicit-grant-flow.md). I det vänstra fönstret i det registrerade programmet väljer du **autentisering**.
1. I **Avancerade inställningar**, under **implicit tilldelning**, markerar du kryss rutorna **ID-token** och **åtkomst-token** . ID-token och åtkomsttoken krävs eftersom den här appen måste logga in användare och anropa ett API.
1. Välj **Spara**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Steg 2: Konfigurera din webb server eller ditt projekt

- [Hämta projektfiler](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) för en lokal webb server, till exempel Node.

  eller

- [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Gå sedan vidare till [Konfigurera ditt Java Script-Spa](#step-4-configure-your-javascript-spa) för att konfigurera kod exemplet innan du kör det.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Steg 3: Använd Microsoft Authentication Library för att logga in användaren

Följ stegen i [guiden JavaScript-självstudie](tutorial-v2-javascript-spa.md#create-your-project) för att skapa projektet och integrera med MSAL för att logga in användaren.

### <a name="step-4-configure-your-javascript-spa"></a>Steg 4: Konfigurera ditt Java Script SPA

Lägg till program registrerings informationen i `index.html`-filen som skapades under projekt installationen. Lägg till följande kod överst i `<script></script>`-taggarna i bröd texten i `index.html`-filen:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

I den koden:

- `Enter_the_Application_Id_here` är **programmets (klient) ID-** värdet för det program som du har registrerat.
- `Enter_the_Tenant_Info_Here` har angetts till något av följande alternativ:
    - Om ditt program har stöd **för konton i den här organisations katalogen**ersätter du värdet med klient-ID eller klient namn (till exempel contoso.Microsoft.com).
    - Om ditt program har stöd **för konton i en organisations katalog**ersätter du värdet med `organizations`.
    
    För att hitta autentiserings slut punkter för alla nationella moln, se [Azure AD-autentiseringens slut punkter](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Personliga Microsoft-konton stöds inte i nationella moln.
  
- `graphEndpoint` är Microsoft Graph slut punkten för Microsoft-molnet för amerikanska myndigheter.

   Information om hur du hittar Microsoft Graph slut punkter för alla nationella moln finns i [Microsoft Graph slut punkter i nationella moln](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="pythontabpython"></a>[Python](#tab/python)

Så här aktiverar du din MSAL python-app för suveräna moln:

- Registrera ditt program på en specifik Portal, beroende på molnet. Mer information om hur du väljer Portal se [slut punkter för program registrering](authentication-national-cloud.md#app-registration-endpoints)
- Använd något av [exemplen](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) från lagrings platsen med några ändringar i konfigurationen, beroende på molnet, som nämns härnäst.
- Använd en specifik auktoritet, beroende på vilket moln du registrerade programmet i. För mer information om myndigheter för olika moln, se [Azure AD-autentiseringens slut punkter](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Här är en exempel utfärdare:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```
    
- För att anropa Microsoft Graph krävs en viss URL för diagrammets slut punkt som är beroende av vilket moln du använder. Information om hur du hittar Microsoft Graph slut punkter för alla nationella moln finns i [Microsoft Graph-och diagram Explorers tjänst rot slut punkter](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Här är ett exempel på en diagram slut punkt med omfattningen:
    
    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```
    
## <a name="javatabjava"></a>[Java](#tab/java)

Så här aktiverar du MSAL för Java-program för suveräna moln:

- Registrera ditt program på en specifik Portal, beroende på molnet. Mer information om hur du väljer Portal se [slut punkter för program registrering](authentication-national-cloud.md#app-registration-endpoints)
- Använd något av [exemplen](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) från lagrings platsen med några ändringar i konfigurationen, beroende på molnet, som nämns härnäst.
- Använd en specifik auktoritet, beroende på vilket moln du registrerade programmet i. För mer information om myndigheter för olika moln, se [Azure AD-autentiseringens slut punkter](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Här är en exempel utfärdare:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- För att anropa Microsoft Graph krävs en viss URL för diagrammets slut punkt som är beroende av vilket moln du använder. Information om hur du hittar Microsoft Graph slut punkter för alla nationella moln finns i [Microsoft Graph-och diagram Explorers tjänst rot slut punkter](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Här är ett exempel på en diagram slut punkt med omfattningen:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

MSAL för iOS och macOS kan användas för att hämta tokens i nationella moln, men det krävs ytterligare konfiguration när du skapar `MSALPublicClientApplication`.

Om du till exempel vill att ditt program ska vara ett program med flera innehavare i ett nationellt moln (här, amerikanska myndigheter) kan du skriva:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swifttabswift"></a>[Swift](#tab/swift)

MSAL för iOS och macOS kan användas för att hämta tokens i nationella moln, men det krävs ytterligare konfiguration när du skapar `MSALPublicClientApplication`.

Om du till exempel vill att ditt program ska vara ett program med flera innehavare i ett nationellt moln (här, amerikanska myndigheter) kan du skriva:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Autentisering i nationella moln](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure Kina 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
