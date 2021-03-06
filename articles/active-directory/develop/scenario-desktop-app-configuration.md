---
title: 'Konfigurera en stationär app som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du konfigurerar koden för en stationär app som anropar webb-API: er'
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
ms.openlocfilehash: 0d31a70ebc63a5e9a16e0da00623bd5855f0a7d1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920267"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Skriv bords app som anropar webb-API: er – kod konfiguration

Nu när du har skapat ditt program får du lära dig hur du konfigurerar koden med programmets koordinater.

## <a name="msal-libraries"></a>MSAL-bibliotek

Microsoft-bibliotek som stöder Desktop-program är:

  MSAL-bibliotek | Beskrivning
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Har stöd för att skapa ett Skriv bords program på flera plattformar – Linux, Windows och MacOS
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL python | Har stöd för att skapa ett Skriv bords program på flera plattformar. Utveckling pågår – i offentlig för hands version
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Har stöd för att skapa ett Skriv bords program på flera plattformar. Utveckling pågår – i offentlig för hands version
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Stöd för Skriv bords program som körs på macOS

## <a name="public-client-application"></a>Offentligt klient program

I kodvyn är Skriv bords program offentliga klient program. Konfigurationen är lite annorlunda beroende på om du använder interaktiv autentisering eller inte.

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

Du behöver bygga och manipulera MSAL.NET-`IPublicClientApplication`.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Enbart med kod

Följande kod instansierar ett offentligt klient program, inloggnings användare i det Microsoft Azure offentliga molnet, med ett arbets-och skol konto eller en personlig Microsoft-konto.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Om du avser att använda interaktiv autentisering eller enhets kod flöde, som du ser ovan, vill du använda `.WithRedirectUri` modifieraren:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Använda konfigurationsfiler

Följande kod instansierar ett offentligt klient program från ett konfigurations objekt, som kan fyllas i program mässigt eller läses från en konfigurations fil

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Mer avancerad konfiguration

Du kan utveckla program byggnaden genom att lägga till ett antal modifierare. Om du till exempel vill att ditt program ska vara ett program med flera innehavare i ett nationellt moln (här, amerikanska myndigheter) kan du skriva:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET innehåller också en modifierare för ADFS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Slutligen, om du vill hämta token för en Azure AD B2C-klient, kan du ange din klient som visas i följande kodfragment:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Läs mer

Lär dig mer om hur du konfigurerar ett MSAL.NET Desktop-program:

- Listan över alla modifierare som är tillgängliga på `PublicClientApplicationBuilder`finns i referens dokumentationen [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- För en beskrivning av alla alternativ som visas i `PublicClientApplicationOptions` se [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), i referens dokumentationen

### <a name="complete-example-with-configuration-options"></a>Slutför exempel med konfigurations alternativ

Föreställ dig ett .NET Core-konsolprogram med följande `appsettings.json` konfigurations fil:

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Du har lite kod för att läsa den här filen med .NET-angivet konfigurations ramverk.

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

För att skapa programmet behöver du nu bara skriva följande kod:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

innan anropet till `.Build()`-metoden kan du åsidosätta konfigurationen med anrop till `.WithXXX` metoder som visas ovan.

# <a name="javatabjava"></a>[Java](#tab/java)

Här är klassen som används i MSAL java dev-exempel för att konfigurera exemplen: [testdata](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Följande kod instansierar ett offentligt klient program, inloggnings användare i det Microsoft Azure offentliga molnet, med ett arbets-och skol konto eller en personlig Microsoft-konto.

### <a name="quick-configuration"></a>Snabb konfiguration

Mål-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Införliva
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Mer avancerad konfiguration

Du kan utveckla program byggnaden genom att lägga till ett antal modifierare. Om du till exempel vill att ditt program ska vara ett program med flera innehavare i ett nationellt moln (här, amerikanska myndigheter) kan du skriva:

Mål-C:

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

Införliva

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta en token för en desktop-app](scenario-desktop-acquire-token.md)
