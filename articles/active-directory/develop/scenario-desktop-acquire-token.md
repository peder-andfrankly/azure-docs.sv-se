---
title: 'Hämta token för skrivbordsappar som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en stationär app som anropar webb-API: er (hämtar en token för appen |)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33eed25f79d90bd513e79b23619fd4c575bc874
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920234"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Skriv bords app som anropar webb-API: er – hämta en token

När du har skapat en instans av det offentliga klient programmet använder du den för att hämta en token som du sedan använder för att anropa ett webb-API.

## <a name="recommended-pattern"></a>Rekommenderat mönster

Webb-API: et definieras av `scopes`. Vad du kan göra i ditt program, vilket mönster du vill använda är:

- Försöker systematiskt hämta en token från token-cachen genom att anropa `AcquireTokenSilent`
- Om det här anropet Miss lyckas använder du `AcquireToken` flödet som du vill använda (här representeras av `AcquireTokenXX`)

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

### <a name="in-msalnet"></a>I MSAL.NET

```CSharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();
    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>I MSAL för iOS och macOS

Mål-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Införliva

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```
---

Här är nu detalj rikedomen av de olika sätten att hämta token i ett Skriv bords program

## <a name="acquiring-a-token-interactively"></a>Att förvärva en token interaktivt

I följande exempel visas minimal kod för att få en token interaktivt för att läsa användarens profil med Microsoft Graph.

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)
### <a name="in-msalnet"></a>I MSAL.NET

```CSharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Obligatoriska parametrar

`AcquireTokenInteractive` har bara en obligatorisk parameter ``scopes``, som innehåller en uppräkning av strängar som definierar de omfång som en token krävs för. Om token är för Microsoft Graph, finns de obligatoriska omfattningarna i API-referensen för varje Microsoft Graph API i avsnittet med namnet "Permissions". Om du till exempel vill [lista användarens kontakter](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)måste omfattningen "User. Read", "Contacts. Read" användas. Se även [Microsoft Graph behörighets referens](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

På Android måste du också ange den överordnade aktiviteten (med `.WithParentActivityOrWindow`, se nedan) så att token återgår till den överordnade aktiviteten efter interaktionen. Om du inte anger det uppstår ett undantag när du anropar `.ExecuteAsync()`.

### <a name="specific-optional-parameters-in-msalnet"></a>Vissa valfria parametrar i MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Som är interaktiva är användar gränssnittet viktigt. `AcquireTokenInteractive` har en särskild valfri parameter som aktiverar för att ange, för plattformar som stöder den, det överordnade användar gränssnittet. När det används i ett Skriv bords program har `.WithParentActivityOrWindow` en annan typ beroende på plattform:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

!

- I .NET standard är den förväntade `object` en `Activity` på Android, en `UIViewController` på iOS, en `NSWindow` på MAC och en `IWin32Window` eller `IntPr` i Windows.
- I Windows måste du anropa `AcquireTokenInteractive` från UI-tråden så att den inbäddade webbläsaren får rätt kontext för UI-synkronisering.  Att inte anropa från UI-tråden kan orsaka att meddelanden inte pumpas korrekt och/eller deadlock med användar gränssnittet. Ett sätt att anropa MSAL från UI-tråden om du inte är i UI-tråden är redan att använda `Dispatcher` på WPF.
- Om du använder WPF för att hämta ett fönster från en WPF-kontroll, kan du använda `WindowInteropHelper.Handle`-klass. Anropet är sedan från en WPF-kontroll (`this`):

  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` används för att styra interaktivitet med användaren genom att ange en prompt

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Klassen definierar följande konstanter:

- ``SelectAccount``: tvingar STS att Visa dialog rutan för val av konto som innehåller konton som användaren har en session för. Det här alternativet är användbart när program utvecklare vill låta användaren välja mellan olika identiteter. Det här alternativet driver MSAL för att skicka ``prompt=select_account`` till identitets leverantören. Det här alternativet är standard och det gör det bra att tillhandahålla den bästa möjliga upplevelsen baserat på tillgänglig information (konto, närvaro av en session för användaren och så vidare. ...). Ändra det inte om du inte har en lämplig anledning att göra det.
- ``Consent``: gör att programutvecklaren kan tvinga användaren att tillfrågas om godkännande även om medgivande beviljades tidigare. I det här fallet skickar MSAL `prompt=consent` till identitets leverantören. Det här alternativet kan användas i vissa säkerhets fokuserade program där organisationens styrning kräver att användaren visas medgivande dialog rutan varje gången programmet används.
- ``ForceLogin``: gör det möjligt för programutvecklaren att användaren uppmanas att ange autentiseringsuppgifter för tjänsten även om denna användar fråga inte behövs. Det här alternativet kan vara användbart om hämtning av en token Miss lyckas, så att användaren kan logga in igen. I det här fallet skickar MSAL `prompt=login` till identitets leverantören. Nu har vi sett att det används i vissa säkerhetsfokuserade program där organisationens styrning kräver att användaren loggar in varje gång de kommer åt specifika delar av ett program.
- ``Never`` (endast för .NET 4,5 och WinRT) uppmanas inte användaren, utan kommer istället att försöka använda cookien som lagras i den dolda inbäddade vyn (se nedan: Webbvyer i MSAL.NET). Det går inte att använda det här alternativet, och i så fall kommer `AcquireTokenInteractive` att utlösa ett undantag för att meddela att en användar gränssnitts interaktion krävs, och du behöver använda en annan `Prompt`-parameter.
- ``NoPrompt``: det går inte att skicka någon prompt till identitets leverantören. Det här alternativet är bara användbart för Azure AD B2C Redigera profil principer (se [B2C-information](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Den här modifieraren används i ett avancerat scenario där du vill att användaren ska godkänna flera resurser på förhand (och inte vill använda det stegvisa godkännandet som normalt används med MSAL.NET/Microsoft Identity Platform). Mer information finns i [så här gör du för att få användar medgivande för flera resurser](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Ett webb gränssnitt är en mekanism för att anropa en webbläsare. Den här mekanismen kan vara en särskild WebBrowser-kontroll för gränssnitt eller ett sätt att delegera öppning av webbläsaren.
MSAL tillhandahåller webb GRÄNSSNITTs implementeringar för de flesta plattformar, men det finns fortfarande fall där du kanske vill vara värd för webbläsaren:

- plattformar som inte uttryckligen omfattas av MSAL, till exempel blixt, Unity, mono på Station ära datorer
- du vill testa ditt program och vill använda en automatiserad webbläsare som kan användas med selen
- webbläsaren och appen som kör MSAL finns i separata processer

##### <a name="at-a-glance"></a>En snabbtitt

För att åstadkomma detta ger du MSAL en `start Url`som måste visas i en valfri webbläsare så att slutanvändaren kan ange sitt användar namn osv. När autentiseringen är klar måste appen skickas tillbaka till MSAL `end Url`, som innehåller en kod från Azure AD.
Värden för `end Url` är alltid `redirectUri`. För att avlyssna `end Url` kan du:

- övervaka webbläsaren omdirigeras tills `redirect Url` har nåtts eller
- låta webbläsaren omdirigera till en URL som du övervakar

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi är en utöknings punkt

`WithCustomWebUi` är en utöknings punkt som gör det möjligt att tillhandahålla ditt eget användar gränssnitt i offentliga klient program och att låta användaren gå igenom/Authorize-slutpunkten för identitets leverantören och låta dem logga in och godkänna. MSAL.NET kan sedan lösa in autentiseringsmetoden och hämta en token. Det är en instans som används i Visual Studio för att låta electrons-program (t. ex. feedback) tillhandahålla webb interaktionen, men lämna den till MSAL.NET för att göra merparten av arbetet. Du kan också använda den om du vill tillhandahålla UI-automatisering. I offentliga klient program använder MSAL.NET PKCE-standarden ([RFC 7636 – bevis nyckel för kod utbyte av offentliga OAuth-klienter](https://tools.ietf.org/html/rfc7636)) för att säkerställa att säkerheten respekteras: endast MSAL.net kan lösa in koden.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Använda WithCustomWebUi

För att kunna använda `.WithCustomWebUI`måste du:

  1. Implementera `ICustomWebUi`-gränssnittet (se [här](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Du behöver huvudsakligen implementera en metod `AcquireAuthorizationCodeAsync` att acceptera URL: en för auktoriseringskod (beräknad av MSAL.NET), vilket gör att användaren kan gå igenom interaktionen med identitets leverantören och sedan återgå till den URL som identitets leverantören skulle ha anropat din implementering tillbaka (inklusive auktoriseringskod). Om du har problem bör din implementering resultera i ett `MsalExtensionException` undantag för att samar beta med MSAL.
  2. I ditt `AcquireTokenInteractive`-anrop kan du använda `.WithCustomUI()` modifierare som skickar instansen av ditt anpassade webb gränssnitt

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Exempel på implementering av ICustomWebUi i Test Automation – SeleniumWebUI

MSAL.NET-teamet har skrivit om våra UI-tester för att utnyttja den här utöknings bara mekanismen. Om du är intresse rad kan du ta en titt på klassen [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) i käll koden för MSAL.net

##### <a name="providing-a-great-experience-with-systemwebviewoptions"></a>Ge en fantastisk upplevelse med SystemWebViewOptions

Från MSAL.NET 4,1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) kan du ange:

- URI: n för att navigera till (`BrowserRedirectError`) eller det HTML-fragment som ska visas (`HtmlMessageError`) vid inloggnings-eller medgivande fel i system webbläsare
- URI: n för att navigera till (`BrowserRedirectSuccess`) eller det HTML-fragment som ska visas (`HtmlMessageSuccess`) i händelse av lyckat inloggnings-eller medgivande.
- den åtgärd som ska köras för att starta system webbläsaren. För detta kan du ange en egen implementering genom att ange `OpenBrowserAsync` ombudet. Klassen tillhandahåller också en standard implementering för två webbläsare: `OpenWithEdgeBrowserAsync` och `OpenWithChromeEdgeBrowserAsync`för Microsoft Edge och [Microsoft Edge på krom](https://www.windowscentral.com/faq-edge-chromium).

Om du vill använda den här strukturen kan du skriva något som liknar följande:

```CSharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Andra valfria parametrar

Läs mer om alla andra valfria parametrar för `AcquireTokenInteractive` i referens dokumentationen för [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java tillhandahåller inte en interaktiv metod för att hämta token direkt. I stället måste programmet skicka en auktoriseringsbegäran i dess implementering av användar interaktions flödet för att få en auktoriseringskod som sedan kan skickas till `acquireToken` metod för att hämta token.

```java
AuthorizationCodeParameters parameters =  AuthorizationCodeParameters.builder(
                authorizationCode, redirectUri)
                .build();
CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();
    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL python tillhandahåller inte en interaktiv metod för att hämta token direkt. I stället måste programmet skicka en auktoriseringsbegäran i dess implementering av användar interaktions flödet för att få en auktoriseringskod som sedan kan skickas till `acquire_token_by_authorization_code` metod för att hämta token.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>I MSAL för iOS och macOS

Mål-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Införliva

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

Om du vill logga in en domän användare på en domän eller en Azure AD-ansluten dator måste du använda integrerad Windows-autentisering.

### <a name="constraints"></a>Villkor

- Integrerad Windows-autentisering (IWA) kan bara användas för **federerade** användare, det vill säga användare som skapats i en Active Directory och som backas upp av Azure Active Directory. Användare som skapats direkt i AAD, utan AD- **hanterade** användare, kan inte använda det här autentiseringsschemat. Den här begränsningen påverkar inte flödet av användar namn/lösen ord.
- IWA är avsedd för appar som är skrivna för .NET Framework, .NET Core och UWP-plattformar
- IWA kringgår inte MFA (Multi Factor Authentication). Om MFA konfigureras kan IWA Miss förväntas om en MFA-utmaning krävs, eftersom MFA kräver användar interaktion.
  > [!NOTE]
  > Det här är en knepig. IWA är icke-interaktivt, men MFA kräver användar-interaktivitet. Du styr inte när identitets leverantören begär MFA för att utföras, klient organisationens administratör gör. Från våra observationer krävs MFA när du loggar in från ett annat land, om du inte är ansluten via VPN till ett företags nätverk och ibland även när du är ansluten via VPN. Vänta inte en deterministisk uppsättning regler, Azure Active Directory använder AI för att kontinuerligt lära sig om MFA krävs. Du bör återgå till en användar prompt (interaktiv autentisering eller enhets kod flöde) om IWA Miss lyckas.

- Auktoriteten som skickas i `PublicClientApplicationBuilder` måste vara:
  - klient-Ed (av formuläret `https://login.microsoftonline.com/{tenant}/` där `tenant` är antingen det GUID som representerar klient-ID: t eller en domän som är associerad med klienten.
  - för arbets-och skol konton (`https://login.microsoftonline.com/organizations/`)
  - Microsoft-personliga konton stöds inte (du kan inte använda/vanliga-eller/consumers-klienter)

- Eftersom integrerad Windows-autentisering är ett tyst flöde:
  - användaren av ditt program måste tidigare ha samtyckt till att använda programmet
  - eller klient organisationens administratör måste tidigare ha samtyckt till alla användare i klienten för att kunna använda programmet.
  - Med andra ord:
    - antingen har du en utvecklare som har tryckt på knappen **tilldela** på Azure Portal själv,
    - eller också har en innehavaradministratör tryckt på knappen **bevilja/återkalla administratörs medgivande för {Tenant Domain}** på fliken **API-behörigheter** i registreringen för programmet (se [lägga till behörigheter för åtkomst till webb-API: er](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - eller så har du gett ett sätt för användare att godkänna programmet (se [begära individuell användar medgivande](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - eller så har du angett ett sätt för klient administratören att godkänna för programmet (se [administrativt medgivande](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Det här flödet är aktiverat för .net Desktop-, .net Core-och Windows Universal-appar (UWP).

Mer information om medgivande finns i [behörigheter för Microsoft Identity Platform och medgivande](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Hur du använder den

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

I MSAL.NET måste du använda

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Du behöver normalt bara en parameter (`scopes`). Beroende på hur din Windows-administratör har konfigurerat principerna kan det dock vara möjligt att program på din Windows-dator inte kan söka efter den inloggade användaren. I så fall kan du använda en andra metod `.WithUsername()` och skicka in användar namnet för den inloggade användaren som ett UPN-format – `joe@contoso.com`. I .NET Core är det bara den överbelastning som tar användar namnet tillgängligt, eftersom .NET Core Platform inte kan be användar namnet till operativ systemet.

Följande exempel visar det mest aktuella fallet med förklaringar av vilken typ av undantag som du kan hämta och deras begränsningar

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Listan över möjliga modifierare på AcquireTokenByIntegratedWindowsAuthentication finns i [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

# <a name="javatabjava"></a>[Java](#tab/java)

Detta är ett utdrag från [MSAL java-dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/). Här är klassen som används i MSAL java dev-exempel för att konfigurera exemplen: [testdata](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
         .authority(TestData.AUTHORITY_ORGANIZATION)
         .telemetryConsumer(new Telemetry.MyTelemetryConsumer().telemetryConsumer)
         .build();

 IntegratedWindowsAuthenticationParameters parameters =
         IntegratedWindowsAuthenticationParameters.builder(
                 Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE), TestData.USER_NAME)
                 .build();

 Future<IAuthenticationResult> future = app.acquireToken(parameters);

 IAuthenticationResult result = future.get();

 return result;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Det här flödet stöds inte ännu i MSAL python.

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Det här flödet gäller inte för MacOS.

---

## <a name="username--password"></a>Användar namn/lösen ord

Du kan också hämta en token genom att ange användar namn och lösen ord. Det här flödet är begränsat och rekommenderas inte, men det finns fortfarande användnings fall där det är nödvändigt.

### <a name="this-flow-isnt-recommended"></a>Det här flödet rekommenderas inte

Det här flödet **rekommenderas inte** eftersom ditt program som ber användaren om lösen ordet inte är säkert. Mer information om det här problemet finns i [den här artikeln](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Det prioriterade flödet för att hämta en token tyst på Windows-domänanslutna datorer är [integrerad Windows-autentisering](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Annars kan du även använda [enhets kod flödet](https://aka.ms/msal-net-device-code-flow)

> [!NOTE]
> Även om detta är användbart i vissa fall (DevOps-scenarier), om du vill använda användar namn/lösen ord i interaktiva scenarier där du anger ditt eget användar gränssnitt, bör du tänka på hur du ska flytta bort från det. Genom att använda användar namn/lösen ord kan du ge dig ett antal saker:
>
> - kärn Tenets av modern identitet: lösen ordet blir bevarat och spelas upp. Eftersom vi har det här begreppet en resurs hemlighet som kan fångas upp.
> Detta är inte kompatibelt med lösen ord.
> - användare som behöver göra MFA kan inte logga in (eftersom det inte finns någon interaktion)
> - Användare kan inte utföra enkel inloggning

### <a name="constraints"></a>Villkor

Följande begränsningar gäller också:

- Användar namnet/lösen ordet är inte kompatibelt med villkorlig åtkomst och Multi-Factor Authentication: om din app körs i en Azure AD-klient där klient organisationen kräver Multi-Factor Authentication, kan du inte använda det här flödet. Många organisationer gör det.
- Det fungerar bara för arbets-och skol konton (inte MSA)
- Flödet är tillgängligt på .net Desktop och .net Core, men inte på UWP.

### <a name="b2c-specifics"></a>B2C-information

[Mer information om hur du använder ROPC med B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Hur använder du den?

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

`IPublicClientApplication`innehåller metoden `AcquireTokenByUsernamePassword`

I följande exempel presenteras ett förenklat fall

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

Följande exempel visar det mest aktuella fallet med förklaringar av vilken typ av undantag som du kan hämta och deras begränsningar

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Mer information om alla modifierare som kan tillämpas på `AcquireTokenByUsernamePassword`finns i [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

# <a name="javatabjava"></a>[Java](#tab/java)

Detta är ett utdrag från [MSAL java-dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/). Här är klassen som används i MSAL java dev-exempel för att konfigurera exemplen: [testdata](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_ORGANIZATION)
        .build();

UserNamePasswordParameters parameters = UserNamePasswordParameters.builder(
        Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
        TestData.USER_NAME,
        TestData.USER_PASSWORD.toCharArray())
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();

    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Detta är ett utdrag från [MSAL python-dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Det här flödet stöds inte på MSAL för macOS.

---

## <a name="command-line-tool-without-web-browser"></a>Kommando rads verktyg (utan webbläsare)

### <a name="device-code-flow"></a>Enhets kod flöde

Om du skriver ett kommando rads verktyg (som inte har webb kontroller) och inte vill använda de tidigare flödena, måste du använda enhets kod flödet.

Interaktiv autentisering med Azure AD kräver en webbläsare (mer information finns i [användning av webbläsare](https://aka.ms/msal-net-uses-web-browser)). Men för att autentisera användare på enheter eller operativ system som inte tillhandahåller en webbläsare, tillåter enhets kod flödet att användaren använder en annan enhet (till exempel en annan dator eller mobil telefon) för att logga in interaktivt. Genom att använda enhets kod flödet hämtar programmet token via en två stegs process som är särskilt utformad för dessa enheter/operativ system. Exempel på sådana program är program som körs på iOT eller kommando rads verktyg (CLI). Tanken är att:

1. När användarautentisering krävs ger appen en kod och ber användaren att använda en annan enhet (till exempel en Internet-ansluten smartphone) för att navigera till en URL (till exempel `https://microsoft.com/devicelogin`), där användaren uppmanas att ange koden. På så sätt kommer webb sidan att leda användaren genom en normal autentisering, inklusive medgivande frågor och multifaktorautentisering om det behövs.

2. Vid lyckad autentisering får kommando rads appen nödvändiga token via en back kanal och använder den för att utföra de webb-API-anrop som IT behöver.

### <a name="how-to-use"></a>Hur ska jag använda?

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

`IPublicClientApplication`innehåller en metod med namnet `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Den här metoden tar som parametrar:

- `scopes` att begära en åtkomsttoken för
- Ett återanrop som tar emot `DeviceCodeResult`

  ![mallar](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Följande exempel kod visar det mest aktuella fallet med förklaringar av vilken typ av undantag som du kan hämta och hur de kan undvikas.

```CSharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="javatabjava"></a>[Java](#tab/java)

Detta är ett utdrag från [MSAL java-dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/). Här är klassen som används i MSAL java dev-exempel för att konfigurera exemplen: [testdata](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();

Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) -> {
    System.out.println(deviceCode.message());
};

CompletableFuture<IAuthenticationResult> future = app.acquireToken(
        DeviceCodeFlowParameters.builder(
                Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                deviceCodeConsumer)
                .build());

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception of type - " + ex.getClass());
        System.out.println("message - " + ex.getMessage());
        return "Unknown!";
    }
    System.out.println("Returned ok - " + res);

    return res;
});

future.join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Detta är ett utdrag från [MSAL python-dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Det här flödet gäller inte för MacOS.

---

## <a name="file-based-token-cache"></a>Filbaserad token-cache

I MSAL.NET anges en token i minnet som standard.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Serialisering är anpassningsbar i Windows-skrivbordsappar och Web Apps/webb-API: er

Om du inte gör något extra i .NET Framework-och .NET Core varar token i minnet i cacheminnet för programmets varaktighet. För att förstå varför serialisering inte har tillhandahållits från rutan kan du komma ihåg att MSAL .NET Desktop/Core-program kan vara konsol-eller Windows-program (som skulle ha åtkomst till fil systemet), **men även** webb program eller webb-API. Dessa webbappar och webb-API: er kan använda vissa cache-mekanismer som databaser, distribuerade cacheminnen, Redis-cache och så vidare. Om du vill ha ett program med beständig token cache i .NET Desktop eller Core måste du anpassa serialiseringen.

Klasser och gränssnitt som ingår i cachelagring av token cache är följande typer:

- ``ITokenCache``, som definierar händelser för att prenumerera på begär Anden om cachelagring av token, samt metoder för att serialisera eller deserialisera cachen i olika format (ADAL v 3.0, MSAL 2. x och MSAL 3. x = ADAL v 5.0)
- ``TokenCacheCallback`` är ett återanrop som skickas till händelserna så att du kan hantera serialiseringen. de anropas med argument av typen ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` tillhandahåller endast ``ClientId`` för programmet och en referens till den användare som token är tillgänglig för

  ![mallar](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET skapar token-cacheminnen för dig och ger dig `IToken` cache när du anropar ett programs `UserTokenCache`-och `AppTokenCache` egenskaper. Du ska inte implementera gränssnittet själv. Ditt ansvar när du implementerar en anpassad token cache-serialisering är att:
>
> - Reagera på `BeforeAccess` och `AfterAccess` "händelser" (eller de *asynkrona* motsvarigheterna). `BeforeAccess` ombudet ansvarar för deserialisering av cacheminnet, medan den `AfterAccess` som är ansvarig för serialisering av cachen.
> - En del av dessa händelser lagrar eller läser in blobbar som skickas genom händelse argumentet till vilken lagring du vill ha.

Strategierna skiljer sig beroende på om du skriver en cachelagring för token-cache för ett offentligt klient program (skriv bord) eller ett konfidentiellt klient program (webbapp/webb-API, daemon-app).

Eftersom MSAL v2. x har du flera alternativ, beroende på om du bara vill serialisera cachen till MSAL.NET-formatet (Unified format cache som är gemensamt med MSAL, men även på olika plattformar), eller om du även vill stödja den [äldre](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) token cache-serialiseringen av ADAL v3.

Anpassningen av cachelagring av token för att dela SSO-läget mellan ADAL.NET 3. x, ADAL.NET 5. x och MSAL.NET förklaras i följande exempel: [Active Directory-dotNet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Simple token cache-serialisering (endast MSAL)

Nedan visas ett exempel på en naïve-implementering av anpassad serialisering av ett token-cache för Skriv bords program. Här är användarens token cache i en fil i samma mapp som programmet.

När du har skapat programmet aktiverar du serialiseringen genom att anropa ``TokenCacheHelper.EnableSerialization()`` att skicka programmet `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Den här hjälp klassen ser ut som följande kodfragment:

```CSharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

En för hands version av en filbaserad cachelagring med token för produkt kvalitet för offentliga klient program (för Skriv bords program som körs på Windows, Mac och Linux) är tillgänglig från biblioteket [Microsoft. Identity. client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) med öppen källkod. Du kan ta med den i dina program från följande NuGet-paket: [Microsoft. Identity. client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Svar. Biblioteket Microsoft. Identity. client. Extensions. Msal är ett tillägg över MSAL.NET. Klasser i de här biblioteken kan göra sitt sätt i MSAL.NET i framtiden, som är eller med större ändringar.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Dubbla token cache-serialisering (MSAL Unified cache + ADAL v3)

Om du vill implementera serialisering av cachelagring både med det enhetliga cacheminnet (vanligt för ADAL.NET 4. x och MSAL.NET 2. x och med andra MSALs i samma plattform eller äldre), kan du bli inspirerad av följande kod :

```CSharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Den här gången ser hjälp klassen ut som följande kod:

```CSharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa ett webb-API från Skriv bords appen](scenario-desktop-call-api.md)
