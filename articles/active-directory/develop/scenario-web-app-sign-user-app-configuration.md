---
title: Konfigurera en webbapp som loggar in användare – Microsoft Identity Platform | Azure
description: Lär dig hur du skapar en webbapp som loggar in användare (kod konfiguration)
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
ms.openlocfilehash: b077a71a541d29c9b93778babc096ea40c3b43cb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964879"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Webbapp som loggar in användare: kod konfiguration

Lär dig hur du konfigurerar koden för din webbapp som loggar in användare.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotek för att skydda webb program

<!-- This section can be in an include for Web App and Web APIs -->
De bibliotek som används för att skydda en webbapp (och ett webb-API) är:

| Plattform | Bibliotek | Beskrivning |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identitets modells tillägg för .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Som används direkt av ASP.NET och ASP.NET Core föreslår Microsoft Identity Model-tillägg för .NET en uppsättning dll: er som körs på både .NET Framework och .NET Core. Från en ASP.NET-eller ASP.NET Core-webbapp kan du kontrol lera token-verifieringen med hjälp av **TokenValidationParameters** -klassen (särskilt i vissa partner scenarier). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Microsoft Authentication Library (MSAL) för Java. För närvarande i offentlig för hands version. |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL för python. För närvarande i offentlig för hands version. |

Välj den flik som motsvarar den plattform som du är intresse rad av:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Kodfragment i den här artikeln och följande extraheras från den [stegvisa självstudien för ASP.net Core Web App, kapitel 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Du kanske vill referera till den här självstudien för fullständig implementerings information.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Kodfragment i den här artikeln och följande extraheras från [exemplet på ASP.net-webbappar](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Du kanske vill referera till det här exemplet för fullständig implementerings information.

# <a name="javatabjava"></a>[Java](#tab/java)

Kodfragment i den här artikeln och följande extraheras från [Java-webbprogrammet som anropar Microsoft Graph-](https://github.com/Azure-Samples/ms-identity-java-webapp) EXEMPLET i MSAL Java.

Du kanske vill referera till det här exemplet för fullständig implementerings information.

# <a name="pythontabpython"></a>[Python](#tab/python)

Kodfragment i den här artikeln och följande extraheras från [python-webbprogrammet som anropar Microsoft Graph-](https://github.com/Azure-Samples/ms-identity-python-webapp) EXEMPLET i MSAL python.

Du kanske vill referera till det här exemplet för fullständig implementerings information.

---

## <a name="configuration-files"></a>Konfigurationsfiler

Webb program som loggar in användare med hjälp av Microsoft Identity Platform konfigureras vanligt vis via konfigurationsfiler. De inställningar som du behöver fylla i är:

- Moln instansen (`Instance`) om du vill att din app ska köras i nationella moln, till exempel
- Mål gruppen i klient-ID: t (`TenantId`)
- Klient-ID (`ClientId`) för ditt program, som kopieras från Azure Portal

Ibland kan program parametrized av `Authority`, vilket är en sammanfogning av `Instance` och `TenantId`.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET Core finns de här inställningarna i filen [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) i avsnittet "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

I ASP.NET Core innehåller en annan fil ([properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) URL (`applicationUrl`) och SSL-porten (`sslPort`) för ditt program och olika profiler.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

I Azure Portal måste de svars-URI: er som du måste registrera på sidan **autentisering** för programmet matcha dessa URL: er. De två föregående konfigurationsfilerna är `https://localhost:44321/signin-oidc`. Orsaken är att `applicationUrl` är `http://localhost:3110`, men `sslPort` anges (44321). `CallbackPath` är `/signin-oidc`enligt definitionen i `appsettings.json`.

På samma sätt skulle utloggnings-URI: n vara inställd på `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

I ASP.NET konfigureras programmet via [Web. config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) -filen, rader 12 till 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

I Azure Portal måste de svars-URI: er som du måste registrera på sidan **autentisering** för programmet matcha dessa URL: er. Det vill säga att de ska vara `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

I Java finns konfigurationen i filen [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) , som finns under `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

I Azure Portal måste de svars-URI: er som du måste registrera på sidan **autentisering** för programmet matcha de `redirectUri`-instanser som programmet definierar. Det vill säga att de ska vara `http://localhost:8080/msal4jsample/secure/aad` och `http://localhost:8080/msal4jsample/graph/me`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Här är python-konfigurationsfilen i [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Den här snabb starten planerar att lagra klient hemligheten i konfigurations filen för enkelhetens skull. I din webbapp skulle du vilja använda andra sätt att lagra din hemliga information, till exempel ett nyckel valv eller en miljö variabel enligt beskrivningen i dokumentationen om [flaskor](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Initierings kod

Initierings koden skiljer sig beroende på plattform. För ASP.NET Core och ASP.NET delegeras inloggnings användare till OpenID Connect-mellanprogram. ASP.NET-eller ASP.NET Core-mallen genererar webb program för Azure Active Directory (Azure AD) v 1.0-slutpunkten. En del konfiguration krävs för att anpassa dem till slut punkten för Microsoft Identity Platform (v 2.0). När det gäller Java hanteras det av våren med programmets samarbete.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

I ASP.NET Core Web Apps (och webb-API: er) skyddas programmet eftersom du har ett `[Authorize]`-attribut på styrenheterna eller styrenhets åtgärderna. Det här attributet kontrollerar att användaren är autentiserad. Koden som initierar programmet finns i filen Startup.cs. 

Om du vill lägga till autentisering med Microsoft Identity Platform (tidigare Azure AD v 2.0) måste du lägga till följande kod. Kommentarerna i koden bör vara själv för klar Ande.

> [!NOTE]
> Om du startar ditt projekt med standard ASP.NET Core-webbprojektet i Visual Studio eller genom att använda `dotnet new mvc`, är metoden `AddAzureAD` tillgänglig som standard. Det beror på att de relaterade paketen läses in automatiskt.
>
> Om du skapar ett projekt från grunden och försöker använda följande kod, rekommenderar vi att du lägger till NuGet-paketet **Microsoft. AspNetCore. Authentication. AzureAD. UI** i projektet för att göra `AddAzureAD`-metoden tillgänglig.

Följande kod är tillgänglig från [Start. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```CSharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

`AddMicrosoftIdentityPlatformAuthentication` tilläggs metoden definieras i [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Företaget

- Lägger till Autentiseringstjänsten.
- Konfigurerar alternativ för att läsa konfigurations filen.
- Konfigurerar anslutnings alternativen för OpenID så att den använda utfärdaren är Microsoft Identity Platform (tidigare Azure AD v 2.0) slut punkt.
- Verifierar utfärdaren av token.
- Säkerställer att de anspråk som motsvarar namnet mappas från `preferred_username`-anspråk i ID-token.

Förutom konfigurationen kan du ange namnet på konfigurations avsnittet när du anropar `AddMicrosoftIdentityPlatformAuthentication`. Som standard är det `AzureAd`.

Spårning av OpenId Connect-händelser för mellanprogram kan hjälpa dig att felsöka ditt webb program om autentiseringen inte fungerar. Om du anger `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` till `true` visas hur information som skapas av en uppsättning ASP.NET Core mellanprogram när den fortskrider från HTTP-svaret till användarens identitet i `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

Med klassen `AadIssuerValidator` kan utfärdaren av token verifieras i många fall. Den här klassen fungerar med en v 1.0-eller v 2.0-token, ett program för en klient eller flera innehavare, eller ett program som loggar in användare med sina personliga Microsoft-konton i Azures offentliga moln eller i nationella moln. Den är tillgänglig från [Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Koden som är relaterad till autentisering i en ASP.NET-webbapp och webb-API: er finns i [App_Start/startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) -filen.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java-exemplet använder våren-ramverket. Programmet skyddas eftersom du implementerar ett filter, vilket fångar varje HTTP-svar. I snabb starten för Java-webbappar är det här filtret `AuthFilter` i `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`. 

Filtret bearbetar OAuth 2,0-auktoriseringskod och kontrollerar om användaren är autentiserad (`isAuthenticated()` metod). Om användaren inte är autentiserad, beräknar den URL: en för Azure AD-Auktoriseringens slut punkter och omdirigerar webbläsaren till denna URI.

När svaret anländer, som innehåller auktoriseringskod, kommer den att förvärva token med hjälp av MSAL Java. När den slutligen tar emot token från token-slutpunkten (på omdirigerings-URI: n) är användaren inloggad.

Mer information finns i `doFilter()`-metoden i [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Koden för `doFilter()` skrivs i en något annorlunda ordning, men flödet är det som beskrivs.

Mer information om det auktoriseringskod som den här metoden utlöser finns i [Microsoft Identity Platform och OAuth 2,0 Authorization Code Flow](v2-oauth2-auth-code-flow.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

Python-exemplet använder kolv. Avställningen av kolven och MSAL python görs i [appen. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Nästa steg

I nästa artikel får du lära dig hur du utlöser inloggning och utloggning.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Logga in och logga ut](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Logga in och logga ut](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Logga in och logga ut](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Logga in och logga ut](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
