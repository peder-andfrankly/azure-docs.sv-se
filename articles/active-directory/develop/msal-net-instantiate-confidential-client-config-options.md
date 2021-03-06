---
title: Instansiera en konfidentiell klient app (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du instansierar ett konfidentiellt klient program med konfigurations alternativ med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 749b81062fe542ae743e27483f6e9bd2323dafd9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921806"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Instansiera ett konfidentiellt klient program med konfigurations alternativ med MSAL.NET

Den här artikeln beskriver hur du instansierar ett [konfidentiellt klient program](msal-client-applications.md) med hjälp av Microsoft Authentication Library för .net (MSAL.net).  Programmet instansieras med konfigurations alternativ som definierats i en inställnings fil.

Innan du initierar ett program måste du först [Registrera](quickstart-register-app.md) det så att din app kan integreras med Microsoft Identity Platform. Efter registreringen kan du behöva följande information (som du hittar i Azure Portal):

- Klient-ID (en sträng som representerar ett GUID)
- Identitets leverantörens URL (kallas instansen) och inloggnings gruppen för ditt program. Dessa två parametrar kallas gemensamt för utfärdaren.
- Klient-ID: t om du skriver ett affärs program enbart för din organisation (även kallat program med en enda klient).
- Program hemligheten (klientens hemliga sträng) eller certifikat (av typen X509Certificate2) om det är ett konfidentiellt klient program.
- För webbappar, och ibland för offentliga klient program (särskilt när appen behöver använda en Service Broker), har du också angett redirectUri där identitets leverantören ska kontakta ditt program igen med säkerhetstoken.

## <a name="configure-the-application-from-the-config-file"></a>Konfigurera programmet från konfigurations filen
Namnet på egenskaperna för alternativen i MSAL.NET överensstämmer med namnet på egenskaperna för `AzureADOptions` i ASP.NET Core, så du behöver inte skriva någon LIMS kod.

En ASP.NET Core program konfiguration beskrivs i en *appSettings. JSON* -fil:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Från och med MSAL.NET v3. x kan du konfigurera ditt konfidentiella klient program från konfigurations filen.

I den klass där du vill konfigurera och instansiera ditt program måste du deklarera ett `ConfidentialClientApplicationOptions`-objekt.  Bind konfigurationen från källan (inklusive filen appconfig. JSON) till instansen av program alternativen med hjälp av metoden `IConfigurationRoot.Bind()` i [paketet Microsoft. Extensions. Configuration. binder NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Detta gör att innehållet i avsnittet "AzureAD" i filen *appSettings. JSON* kan bindas till motsvarande egenskaper för `ConfidentialClientApplicationOptions`-objektet.  Bygg sedan ett `ConfidentialClientApplication`-objekt:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Lägg till körnings konfiguration
I ett konfidentiellt klient program har du vanligt vis en cache per användare. Därför måste du hämta cacheminnet som är associerat med användaren och informera det program verktyg som du vill använda. På samma sätt kan du ha en dynamiskt Beräknad omdirigerings-URI. I det här fallet är koden följande:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

