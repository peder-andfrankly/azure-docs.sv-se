---
title: Definiera en OAuth2 teknisk profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en OAuth2 teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33bad4982d54eb18e91be28511fb9137223f4a91
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950977"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en OAuth2 teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) har stöd för OAuth2-protokollets identitets leverantör. OAuth2 är det primära protokollet för auktorisering och delegerad autentisering. Mer information finns i [RFC 6749 Authorization Framework för OAuth 2,0](https://tools.ietf.org/html/rfc6749). Med en OAuth2 teknisk profil kan du federera med en OAuth2-baserad identitets leverantör, t. ex. Facebook. Genom att federera med en identitets leverantör kan användare logga in med sina befintliga sociala identiteter eller företags identiteter.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `OAuth2`. Till exempel är protokollet för den tekniska profilen för **Facebook-OAUTH** `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Inmatade anspråk

Elementen **InputClaims** och **InputClaimsTransformations** krävs inte. Men du kanske vill skicka ytterligare parametrar till din identitets leverantör. I följande exempel läggs parametern **domain_hint** frågesträng till med värdet för `contoso.com` till auktoriseringsbegäran.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som returneras av OAuth2 Identity Provider. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i identitets leverantören. Du kan också ta med anspråk som inte returneras av identitets leverantören så länge som du ställer in `DefaultValue`-attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

I följande exempel visas de anspråk som returneras av Facebook Identity Provider:

- **First_name** -anspråk är mappat till **givenName** -anspråket.
- **Last_Name** -anspråk är mappat till anspråket efter **namn** .
- **DisplayName** -anspråket utan namn mappning.
- **E-** postanspråk utan namn mappning.

Den tekniska profilen returnerar även anspråk som inte returneras av identitets leverantören:

- **IdentityProvider** -anspråket som innehåller namnet på identitets leverantören.
- **AuthenticationSource** -anspråket med standardvärdet **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Ja | Program identifieraren för identitets leverantören. |
| IdTokenAudience | Nej | Id_tokenens mål grupp. Om det här alternativet anges kontrollerar Azure AD B2C om token finns i ett anspråk som returneras av identitets leverantören och är lika med det som anges. |
| authorization_endpoint | Ja | URL: en för Authorization-slutpunkten enligt RFC 6749. |
| AccessTokenEndpoint | Ja | URL: en för token-slutpunkten enligt RFC 6749. |
| ClaimsEndpoint | Ja | URL: en för slut punkten för användar information enligt RFC 6749. |
| AccessTokenResponseFormat | Nej | Formatet på slut punkts anropet för åtkomsttoken. Facebook kräver till exempel en HTTP GET-metod, men svaret om åtkomsttoken är i JSON-format. |
| AdditionalRequestQueryParameters | Nej | Parametrar för ytterligare begär ande fråga. Du kanske till exempel vill skicka ytterligare parametrar till din identitets leverantör. Du kan inkludera flera parametrar med hjälp av komma-avgränsare. |
| ClaimsEndpointAccessTokenName | Nej | Namnet på frågesträngparametern för åtkomsttoken. Vissa identitets-providers anspråks slut punkter stöder HTTP-begäran. I det här fallet skickas Bearer-token med hjälp av en frågesträngparametern i stället för Authorization-huvudet. |
| ClaimsEndpointFormatName | Nej | Namnet på parametern format Query String. Du kan till exempel ange namnet som `format` i den här LinkedIn-slutpunkten för anspråk `https://api.linkedin.com/v1/people/~?format=json`. |
| ClaimsEndpointFormat | Nej | Värdet för parametern format Query String. Du kan till exempel ange värdet som `json` i den här LinkedIn-slutpunkten för anspråk `https://api.linkedin.com/v1/people/~?format=json`. |
| ProviderName | Nej | Namnet på identitets leverantören. |
| response_mode | Nej | Metoden som identitets leverantören använder för att skicka tillbaka resultatet till Azure AD B2C. Möjliga värden: `query`, `form_post` (standard) eller `fragment`. |
| omfång | Nej | Omfattningen av begäran som definieras enligt OAuth2 Identity Provider-specifikationen. Till exempel `openid`, `profile`och `email`. |
| HttpBinding | Nej | Den förväntade HTTP-bindningen till åtkomsttoken och slut punkter för anspråks-token. Möjliga värden: `GET` eller `POST`.  |
| ResponseErrorCodeParamName | Nej | Namnet på parametern som innehåller fel meddelandet som returnerades över HTTP 200 (OK). |
| ExtraParamsInAccessTokenEndpointResponse | Nej | Innehåller de extra parametrar som kan returneras i svaret från **AccessTokenEndpoint** av vissa identitets leverantörer. Svaret från **AccessTokenEndpoint** innehåller till exempel en extra parameter, till exempel `openid`, som är en obligatorisk parameter förutom access_token i en frågesträng för **ClaimsEndpoint** . Flera parameter namn ska undantas och avgränsas med kommatecken ",". |
| ExtraParamsInClaimsEndpointRequest | Nej | Innehåller de extra parametrar som kan returneras i **ClaimsEndpoint** -begäran av vissa identitets leverantörer. Flera parameter namn ska undantas och avgränsas med kommatecken ",". |

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

**CryptographicKeys** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Klient hemligheten för Identity Provider-programmet. Den kryptografiska nyckeln krävs endast om **response_types** metadata har angetts till `code`. I det här fallet gör Azure AD B2C ett annat anrop till Exchange-auktoriseringskod för en åtkomsttoken. Om metadata har angetts till `id_token`kan du utelämna den kryptografiska nyckeln. |

## <a name="redirect-uri"></a>Omdirigerings-URI

När du konfigurerar den omdirigerings-URL: en för din identitetsprovider anger du `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Se till att ersätta **klient organisationen** med klient organisationens namn (till exempel contosob2c.onmicrosoft.com) och **policyId** med identifieraren för principen (till exempel b2c_1a_policy). Omdirigerings-URI: n måste vara i gemener.

Om du använder **b2clogin.com** -domänen i stället för **login.microsoftonline.com** ska du se till att använda b2clogin.com i stället för login.microsoftonline.com.

Exempel:

- [Lägg till Google + som en OAuth2-identitetsprovider med anpassade principer](active-directory-b2c-custom-setup-goog-idp.md)













