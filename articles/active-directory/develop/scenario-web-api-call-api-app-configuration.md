---
title: 'Konfigurera ett webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar ett webb-API som anropar webb-API: er (appens kod konfiguration)'
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 219724186e3fa69fec35e89435af495b662c871d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919757"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Webb-API som anropar webb-API: er – kod konfiguration

När du har registrerat ditt webb-API kan du konfigurera koden för programmet.

Koden för att konfigurera ditt webb-API så att det anropar underordnade webb-API: er som bygger på den kod som används för att skydda ett webb-API. Mer information finns i [skydda Web API-app-konfiguration](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Kod prenumererad på OnTokenValidated

Ovanpå kod konfigurationen för alla skyddade webb-API: er måste du prenumerera på verifieringen av Bearer-token som tas emot när ditt API anropas:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>On-Behalf-Of-flöde

Metoden AddAccountToCacheFromJwt () måste vara:

- Instansiera ett MSAL-konfidentiellt klient program.
- Anropa `AcquireTokenOnBehalf` för att byta Bearer-token som har hämtats av klienten för webb-API: et mot en Bearer-token för samma användare, men för vår API för att anropa ett underordnat API.

### <a name="instantiate-a-confidential-client-application"></a>Instansiera ett konfidentiellt klient program

Det här flödet är bara tillgängligt i det konfidentiella klient flödet så att det skyddade webb-API: et tillhandahåller klientautentiseringsuppgifter (klient hemlighet eller certifikat) till [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) via `WithClientSecret`-eller `WithCertificate` metoderna.

![mallar](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

I stället för en klient hemlighet eller ett certifikat kan konfidentiella klient program också bevisa sin identitet med hjälp av klientens intyg.
Det här avancerade scenariot beskrivs i [klient kontroll](msal-net-client-assertions.md)

### <a name="how-to-call-on-behalf-of"></a>Så här anropar du på uppdrag av

On-of (OBO)-anropet görs genom att anropa [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) -metoden i `IConfidentialClientApplication`-gränssnittet.

`UserAssertion` skapas utifrån Bearer-token som tagits emot av webb-API: et från sina egna klienter. Det finns [två konstruktorer](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), en som tar en JWT Bearer-token och en som vidtar någon typ av användar kontroll (en annan typ av säkerhetstoken, vilken typ som sedan anges i en ytterligare parameter med namnet `assertionType`).

![mallar](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

I praktiken används ofta OBO-flödet för att hämta en token för ett underordnat API och lagra den i MSAL.NET för användar-token, så att andra delar av webb-API: n kan anropas senare i [åsidosättningar](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) av ``AcquireTokenOnSilent`` för att anropa de underordnade API: erna. Det här anropet kommer att uppdatera tokens, om det behövs.

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```

Du kan också se ett exempel på en räkning av flödes implementeringen i [NodeJS och Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokoll

Mer information om on-of-protokollet finns i [Microsoft Identity Platform och OAuth 2,0 på uppdrag av Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämtar en token för appen](scenario-web-api-call-api-acquire-token.md)
