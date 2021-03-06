---
title: 'Konfigurera daemon-appar som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du konfigurerar koden för daemon-programmet som anropar webb-API: er (app-konfiguration)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: b0fd50f730c604ba1359218cf5268bd20e570d3c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962652"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Daemon-app som anropar webb-API: er – kod konfiguration

Lär dig hur du konfigurerar koden för daemon-programmet som anropar webb-API: er.

## <a name="msal-libraries-supporting-daemon-apps"></a>MSAL-bibliotek med stöd för daemon-appar

Microsoft-bibliotek som stöder daemon-appar är:

  MSAL-bibliotek | Beskrivning
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Plattformar som stöds för att bygga ett daemon-program är .NET Framework och .NET Core-plattformar (inte UWP, Xamarin. iOS och Xamarin. Android som dessa plattformar används för att bygga offentliga klient program)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL python | Utveckling pågår – i offentlig för hands version
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Utveckling pågår – i offentlig för hands version

## <a name="configuration-of-the-authority"></a>Behörighets konfiguration

Med tanke på att daemon-programmen inte använder delegerade behörigheter, men program behörigheter, kan deras *konto typ som stöds* inte vara *konton i någon organisations katalog och personliga Microsoft-konton (till exempel Skype, Xbox, Outlook.com)* . Det finns ingen innehavaradministratör för att ge ett medgivande till daemon-programmet för Microsoft-personliga konton. Du måste välja *konton i min organisation* eller *konton i alla organisationer*.

Den auktoritet som anges i program konfigurationen ska därför vara klient-Ed (ange ett klient-ID eller ett domän namn som är kopplat till din organisation).

Om du är en ISV och vill tillhandahålla ett verktyg för flera innehavare kan du använda `organizations`. Men tänk på att du även måste förklara för kunderna hur de ska bevilja administrativt medgivande. Mer information finns i [begära medgivande för en hel klient](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) . Det finns även en begränsning i MSAL: `organizations` tillåts endast när klientautentiseringsuppgifterna är en program hemlighet (inte ett certifikat).

## <a name="application-configuration-and-instantiation"></a>Program konfiguration och instansiering

I MSAL-bibliotek skickas klientens autentiseringsuppgifter (hemlighet eller certifikat) som en parameter för den konfidentiella klient program konstruktionen.

> [!IMPORTANT]
> Även om ditt program är ett konsol program som körs som en tjänst, om det är ett daemon-program måste det vara ett konfidentiellt klient program.

### <a name="configuration-file"></a>Konfigurationsfil

Konfigurations filen definierar:

- utfärdaren eller moln instansen och tenantId
- ClientID som du fick från program registreringen
- antingen en klient hemlighet eller ett certifikat

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

[appSettings. JSON](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) från daemon-exemplet för [.net Core Console](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Antingen anger du en clientSecret eller en certificateName. Båda inställningarna är exklusiva.

# <a name="pythontabpython"></a>[Python](#tab/python)

När du skapar en konfidentiell klient med klient hemligheter, är [parametrarna. JSON](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) -konfigurationsfilen i [python](https://github.com/Azure-Samples/ms-identity-python-daemon) -exemplet enligt följande.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

När du skapar en konfidentiell klient med certifikat, är [parametrarna. JSON](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) -konfigurationsfilen i [python daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) -exemplet följande.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Här är klassen som används i MSAL java dev-exempel för att konfigurera exemplen: [testdata](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

---

### <a name="instantiation-of-the-msal-application"></a>Instansiering av MSAL-programmet

Om du vill instansiera MSAL-programmet måste du:

- Lägg till, referera till eller importera MSAL-paketet (beroende på språk)
- Sedan är konstruktionen olika beroende på om du använder klient hemligheter eller certifikat (eller som ett avancerat scenario, signerade intyg)

#### <a name="reference-the-package"></a>Referera till paketet

Referera till MSAL-paketet i program koden.

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

Lägg till [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-paketet i ditt program.
I MSAL.NET representeras det konfidentiella klient programmet av `IConfidentialClientApplication`-gränssnittet.
Använd MSAL.NET-namnrymd i käll koden

```CSharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import msal
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-secrets"></a>Instansiera det konfidentiella klient programmet med klient hemligheter

Här är koden för att instansiera det konfidentiella klient programmet med en klient hemlighet:

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(TestData.CONFIDENTIAL_CLIENT_SECRET))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-certificate"></a>Instansiera det konfidentiella klient programmet med klient certifikat

Här är koden för att bygga ett program med ett certifikat:

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

I MSAL. Java det finns två byggprogram för att instansiera det konfidentiella klient programmet med certifikat:

```Java

InputStream pkcs12Certificate = ... ; /* containing PCKS12 formatted certificate*/
string certificatePassword = ... ;    /* contains the password to access the certificate */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(pkcs12Certificate, certificatePassword))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

eller

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(rsaPrivateKey, publicKeyCertificate))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="advanced-scenario---instantiate-the-confidential-client-application-with-client-assertions"></a>Avancerat scenario – instansiera det konfidentiella klient programmet med klient kontroll

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

I stället för en klient hemlighet eller ett certifikat kan det konfidentiella klient programmet också bevisa sin identitet med hjälp av klientens intyg.

MSAL.NET har två metoder för att tillhandahålla signerade kontroller till appen konfidentiell klient:

- `.WithClientAssertion()`
- `.WithClientClaims()`

När du använder `WithClientAssertion`måste du ange ett signerat JWT. Det här avancerade scenariot beskrivs i [klient kontroll](msal-net-client-assertions.md)

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

När du använder `WithClientClaims`kommer MSAL.NET att beräkna en signerad kontroll som innehåller de anspråk som förväntas av Azure AD plus ytterligare klient anspråk som du vill skicka.
Här är ett kodfragment om hur du gör det:

```CSharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Mer information finns i [klient kontroll](msal-net-client-assertions.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

I MSAL python kan du ange klient anspråk med anspråken som ska signeras av den här `ConfidentialClientApplication`privata nyckeln.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Mer information finns i MSAL python: s referens dokumentation för [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java finns i en offentlig för hands version. Signerade kontroller stöds inte ännu.

---

## <a name="next-steps"></a>Nästa steg

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon-app – hämtar token för appen](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon-app – hämtar token för appen](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon-app – hämtar token för appen](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
