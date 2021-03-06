---
title: Definiera en RESTful teknisk profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en RESTful teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa14854807727506f5d697d7871c97e219c096a3
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950892"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en RESTful teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för din egen RESTful-tjänst. Azure AD B2C skickar data till RESTful-tjänsten i en inloggad anspråks samling och tar emot data tillbaka i en utgående anspråks samling. Med RESTful service integration kan du:

- **Verifiera** indata från användaren – förhindrar att felaktiga data behålls i Azure AD B2C. Om värdet från användaren inte är giltigt returnerar din RESTful-tjänst ett fel meddelande som uppmanar användaren att ange en post. Du kan till exempel kontrol lera att den e-postadress som användaren har angett finns i kundens databas.
- **Skriv över** inloggade anspråk – gör att du kan formatera om värden i inloggade anspråk. Om en användare till exempel anger det första namnet i gemener eller versaler, kan du formatera namnet med enbart den första bokstaven versaler.
- **Utöka användar data** – gör att du kan integrera med affärs program på företags nivå. Till exempel kan din RESTful-tjänst ta emot användarens e-postadress, fråga kundens databas och returnera användarens lojalitets nummer till Azure AD B2C. Retur anspråk kan lagras, utvärderas i nästa steg för att dirigera eller ingå i åtkomsttoken.
- **Kör anpassad affärs logik** – gör att du kan skicka push-meddelanden, uppdatera företags databaser, köra en användarmigrering, hantera behörigheter, granska databaser och utföra andra åtgärder.

Din princip kan skicka indatamängds anspråk till din REST API. REST API kan också returnera utgående anspråk som du kan använda senare i principen, eller så kan den utlösa ett fel meddelande. Du kan utforma integrationen med RESTful-tjänsterna på följande sätt:

- **Teknisk profil för validering** – en teknisk profil för validering anropar RESTful-tjänsten. Den tekniska verifierings profilen verifierar de data som anges av användaren innan användar resan fortsätter. Med den tekniska profilen för validering visas ett fel meddelande på en självkontrollerad sida och returneras i utgående anspråk.
- **Anspråk Exchange** – ett anrop görs till RESTful-tjänsten via ett Orchestration-steg. I det här scenariot finns det inget användar gränssnitt för att rendera fel meddelandet. Om REST API returnerar ett fel omdirigeras användaren tillbaka till det förlitande parts programmet med fel meddelandet.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary`. Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterings sammansättning som används av Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

I följande exempel visas en RESTful teknisk profil:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk att skicka till REST API. Du kan också mappa namnet på anspråket till namnet som definierats i REST API. I följande exempel visas mappningen mellan principen och REST API. **GivenName** -anspråket skickas till REST API som **FirstName** **, medan efter namn skickas** som **LastName**. **E-** postanspråket anges som det är.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan de skickas till REST API.

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som returneras av REST API. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i REST API. Du kan också inkludera anspråk som inte returneras av REST API identitets leverantören, förutsatt att du anger attributet `DefaultValue`.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

I följande exempel visas anspråk som returneras av REST API:

- **MembershipId** -anspråket som är mappat till namnet på **loyaltyNumber** -anspråket.

Den tekniska profilen returnerar även anspråk som inte returneras av identitets leverantören:

- **LoyaltyNumberIsNew** -anspråket som har ett standardvärde angivet till `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ServiceUrl | Ja | URL: en för REST API slut punkten. |
| AuthenticationType | Ja | Den typ av autentisering som utförs av RESTful-anspråks leverantören. Möjliga värden: `None`, `Basic`eller `ClientCertificate`. Värdet `None` anger att REST API inte är anonymt. Värdet `Basic` anger att REST API skyddas med HTTP Basic-autentisering. Endast verifierade användare, inklusive Azure AD B2C, har åtkomst till ditt API. Värdet `ClientCertificate` (rekommenderas) anger att REST API begränsar åtkomsten med autentisering av klient certifikat. Endast tjänster som har rätt certifikat, till exempel Azure AD B2C kan komma åt din tjänst. |
| SendClaimsIn | Nej | Anger hur inloggade anspråk skickas till RESTful-anspråks leverantören. Möjliga värden: `Body` (standard), `Form`, `Header`eller `QueryString`. `Body`-värdet är det inloggade anspråk som skickas i begär ande texten i JSON-format. `Form`-värdet är det inloggade anspråk som skickas i begär ande texten i ett et-tecken av typen & "avgränsat nyckel värde". `Header`-värdet är det inloggade anspråk som skickas i begär ande huvudet. `QueryString`-värdet är det inloggade anspråk som skickas i frågesträngen för begäran. |
| ClaimsFormat | Nej | Anger formatet för de utgående anspråken. Möjliga värden: `Body` (standard), `Form`, `Header`eller `QueryString`. `Body`-värdet är det utgående anspråket som skickas i begär ande texten i JSON-format. `Form`-värdet är det utgående anspråket som skickas i begär ande texten i ett et-tecken med formatet & "avgränsat nyckel värde". `Header`-värdet är det utgående anspråket som skickas i begär ande huvudet. `QueryString`-värdet är det utgående anspråket som skickas i frågesträngen för begäran. |
| DebugMode | Nej | Kör den tekniska profilen i fel söknings läge. I fel söknings läge kan REST API returnera mer information. Se avsnittet returnera fel meddelande. |

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

Om autentiseringstypen har angetts till `None`används inte elementet **CryptographicKeys** .

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Om typen av autentisering har angetts till `Basic`innehåller elementet **CryptographicKeys** följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Ja | Det användar namn som används för att autentisera. |
| BasicAuthenticationPassword | Ja | Det lösen ord som används för att autentisera. |

I följande exempel visas en teknisk profil med grundläggande autentisering:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Om typen av autentisering har angetts till `ClientCertificate`innehåller elementet **CryptographicKeys** följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Mängden | Ja | X509-certifikatet (RSA-nyckel uppsättning) som ska användas för att autentisera. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Returnerar fel meddelande

REST API kan behöva returnera ett fel meddelande, till exempel "användaren inte kunde hittas i CRM-systemet". I ett fel inträffar ska REST API returnera ett HTTP 409-fel meddelande (konflikt svars status kod) med följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| version | Ja | 1.0.0 |
| status | Ja | 409 |
| code | Nej | En felkod från RESTful-slutpunkt-providern, som visas när `DebugMode` är aktive rad. |
| requestId | Nej | En begärande-ID från RESTful-slutpunkt-providern, som visas när `DebugMode` är aktive rad. |
| userMessage | Ja | Ett fel meddelande som visas för användaren. |
| developerMessage | Nej | Utförlig beskrivning av problemet och hur du åtgärdar det, som visas när `DebugMode` har Aktiver ATS. |
| moreInfo | Nej | En URI som pekar på ytterligare information, som visas när `DebugMode` är aktive rad. |

I följande exempel visas ett REST API som returnerar ett fel meddelande som är formaterat i JSON:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

I följande exempel visas en C# klass som returnerar ett fel meddelande:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="examples"></a>Exempel:
- [Integrera REST API Claims-utbyten i Azure AD B2C användar resa som validering av användarindata](active-directory-b2c-custom-rest-api-netfw.md)
- [Skydda dina RESTful-tjänster med hjälp av HTTP Basic-autentisering](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [Skydda din RESTful-tjänst genom att använda klient certifikat](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa som verifiering vid användarindata](active-directory-b2c-rest-api-validation-custom.md)

 














