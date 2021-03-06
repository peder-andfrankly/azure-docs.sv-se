---
title: Autentiseringsuppgifter för Azure AD-certifikat
titleSuffix: Microsoft identity platform
description: I den här artikeln beskrivs registreringen och användningen av autentiseringsuppgifter för programautentisering
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37b390e39d2b991ea01468feffbe39c9578af54
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963876"
---
# <a name="azure-ad-application-authentication-certificate-credentials"></a>Autentiseringsuppgifter för Azure AD Application Authentication-certifikat

Azure Active Directory (Azure AD) gör det möjligt för ett program att använda sina egna autentiseringsuppgifter för autentisering, t. ex. i OAuth 2,0-klientens autentiseringsuppgifter för tilldelnings flöde ([v 1.0](v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) och flöde på uppdrags nivå ([v 1.0](v1-oauth2-on-behalf-of-flow.md), [v 2.0](v2-oauth2-on-behalf-of-flow.md)).

En typ av autentiseringsuppgift som ett program kan använda för autentisering är en JSON Web Token (JWT) som är signerad med ett certifikat som programmet äger.

## <a name="assertion-format"></a>Intygs format

Om du vill beräkna försäkran kan du använda ett av de många [JSON Web token](https://jwt.ms/) biblioteken på valfritt språk. Informationen som utförs av token är följande:

### <a name="header"></a>Huvud

| Parameter |  Markera om |
| --- | --- |
| `alg` | Ska vara **RS256** |
| `typ` | Bör vara **JWT** |
| `x5t` | Ska vara X. 509-certifikatet SHA-1 tumavtryck |

### <a name="claims-payload"></a>Anspråk (nytto Last)

| Parameter |  Kommentarer |
| --- | --- |
| `aud` | Mål grupp: ska vara **https://login.microsoftonline.com/*tenant_Id*/OAuth2/token** |
| `exp` | Utgångs datum: det datum då token upphör att gälla. Tiden visas som antalet sekunder från 1 januari 1970 (1970-01-01T0:0: 0Z) UTC tills den tid då token giltighet upphör att gälla.|
| `iss` | Utfärdare: ska vara client_id (program-ID för klient tjänsten) |
| `jti` | GUID: JWT-ID: t |
| `nbf` | Inte före: det datum som token inte kan användas. Tiden visas som antalet sekunder från den 1 januari 1970 (1970-01-01T0:0: 0Z) UTC tills den tidpunkt då token utfärdades. |
| `sub` | Ämne: som för `iss`bör vara client_id (program-ID för klient tjänsten) |

### <a name="signature"></a>Signatur

Signaturen beräknas med hjälp av certifikatet enligt beskrivningen i [JSON Web token RFC7519-specifikationen](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Exempel på en avkodad JWT-kontroll

```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

## <a name="example-of-an-encoded-jwt-assertion"></a>Exempel på en kodad JWT-kontroll

Följande sträng är ett exempel på kodad kontroll. Om du ser noggrant kan du se tre avsnitt avgränsade med punkter (.):
* Det första avsnittet kodar rubriken
* Det andra avsnittet kodar nytto lasten
* Det sista avsnittet är den signatur som beräknas med certifikaten från innehållet i de första två avsnitten

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>Registrera ditt certifikat med Azure AD

Du kan associera certifikatets autentiseringsuppgifter med klient programmet i Azure AD via Azure Portal med någon av följande metoder:

### <a name="uploading-the-certificate-file"></a>Laddar upp certifikat filen

I Azure App-registreringen för klient programmet:
1. Välj **certifikat & hemligheter**. 
2. Klicka på **överför certifikat** och välj den certifikat fil som ska laddas upp.
3. Klicka på **Lägg till**.
  När certifikatet har laddats upp visas tumavtryck, start datum och förfallo värden. 

### <a name="updating-the-application-manifest"></a>Uppdaterar applikations manifestet

Med ett certifikat måste du beräkna:

- `$base64Thumbprint`, som är base64-kodningen för certifikatets hash
- `$base64Value`, som är base64-kodningen för certifikatets rå data

Du måste också ange ett GUID för att identifiera nyckeln i applikations manifestet (`$keyId`).

I Azure App-registreringen för klient programmet:
1. Välj **manifest** för att öppna applikations manifestet.
2. Ersätt egenskapen *autentiseringsuppgifter* med den nya certifikat informationen med hjälp av följande schema.

   ```
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Spara ändringarna i applikations manifestet och ladda upp manifestet till Azure AD. 

   Egenskapen `keyCredentials` har flera värden, så du kan ladda upp flera certifikat för bättre nyckel hantering.
   
## <a name="code-sample"></a>Kodexempel

> [!NOTE]
> Du måste beräkna X5T-rubriken genom att använda certifikatets hash och konvertera det till en Base64-sträng. I C# det skulle det se ut ungefär så här: `System.Convert.ToBase64String(cert.GetCertHash());`

Kod exemplet vid [autentisering till Azure AD i daemon-appar med certifikat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) visar hur ett program använder sina egna autentiseringsuppgifter för autentisering. Det visar också hur du kan [skapa ett självsignerat certifikat](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) med hjälp av `New-SelfSignedCertificate` PowerShell-kommandot. Du kan också dra nytta av och använda [appens skapande skript](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) för att skapa certifikat, beräkna tumavtryck och så vidare.
