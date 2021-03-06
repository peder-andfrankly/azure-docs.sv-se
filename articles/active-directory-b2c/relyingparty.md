---
title: RelyingParty – Azure Active Directory B2C | Microsoft Docs
description: Ange RelyingParty-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b1bfa945843d185a46f1f1d79fd4dab0e991c769
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063799"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**RelyingParty** -elementet anger användar resan som ska tillämpas på den aktuella begäran för att Azure Active Directory B2C (Azure AD B2C). Den anger också listan över anspråk som den förlitande parten (RP) behöver som en del av Utfärdad token. Ett RP-program, till exempel ett webb-, mobil-eller Skriv bords program, anropar RP-principagenten. RP-principagenten kör en speciell uppgift, som att logga in, återställa ett lösen ord eller redigera en profil. Flera program kan använda samma RP-princip och ett enda program kan använda flera principer. Alla RP-program får samma token med anspråk, och användaren går igenom samma användar resa.

I följande exempel visas ett **RelyingParty** -element i *B2C_1A_signup_signin* -princip filen:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Det valfria **RelyingParty** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Standard användar resan för RP-programmet. |
| UserJourneyBehaviors | 0:1 | Omfattningen av användar resans beteenden. |
| TechnicalProfile | 1:1 | En teknisk profil som stöds av RP-programmet. Den tekniska profilen innehåller ett kontrakt för RP-programmet att kontakta Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney` Elementet anger en referens till identifieraren för den användar resa som vanligt vis definieras i bas-eller tilläggs principen. I följande exempel visas den användar resa för registrering eller inloggning som anges i **RelyingParty** -elementet:

*B2C_1A_signup_signin* -princip:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* eller *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

**DefaultUserJourney** -elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| referenceId | Ja | En identifierare för användar resan i principen. Mer information finns i [användar resor](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

**UserJourneyBehaviors** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Omfattningen av beteendet för enkel inloggning (SSO) för en användar resa. |
| SessionExpiryType |0:1 | Autentiseringens funktions sätt. Möjliga värden: `Rolling` eller `Absolute`. `Rolling` Värdet (standard) visar att användaren är inloggad så länge användaren alltid är aktiv i programmet. `Absolute` Värdet anger att användaren tvingas att autentisera igen efter den tids period som anges av livs längden för programsessionen. |
| SessionExpiryInSeconds | 0:1 | Livs längden för Azure AD B2C's-sessionens cookie som anges som ett heltal lagrat i användarens webbläsare vid lyckad autentisering. |
| JourneyInsights | 0:1 | Den Azure Application Insights Instrumentation-nyckel som ska användas. |
| ContentDefinitionParameters | 0:1 | Listan över nyckel värdes par som ska läggas till i innehålls definitionens inläsnings-URI. |

### <a name="singlesignon"></a>SingleSignOn

**SingleSignOn** -elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| Omfång | Ja | Omfattningen av det enkla inloggnings beteendet. Möjliga värden: `Suppressed`, `Tenant`, `Application`eller. `Policy` `Suppressed` Värdet anger att beteendet ignoreras. Om du till exempel använder en enkel inloggning, upprätthålls ingen session för användaren och användaren uppmanas alltid att ange ett val av identitetsprovider. `TrustFramework` Värdet anger att beteendet tillämpas för alla principer i förtroende ramverket. En användare som navigerar genom två princip resor för ett förtroende Framework behöver till exempel inte ange något val av identitetsprovider. `Tenant` Värdet anger att beteendet tillämpas på alla principer i klienten. Till exempel behöver en användare som navigerar genom två princip resor för en klient inte uppmanas att välja ett val av identitetsprovider. `Application` Värdet anger att beteendet tillämpas på alla principer för programmet som gör begäran. Till exempel behöver en användare som navigerar genom två princip resor för ett program inte ange något val av en identitetsprovider. `Policy` Värdet anger att beteendet bara gäller för en princip. Till exempel, en användare som navigerar genom två princip resor för ett förtroende Framework, uppmanas du att ange en identitets leverantör när du växlar mellan principer. |
| KeepAliveInDays | Ja | Styr hur länge användaren förblir inloggad. Om du anger värdet 0 inaktive ras KMSI avgör-funktionen. Mer information finns i [Håll mig inloggad](active-directory-b2c-reference-kmsi-custom.md). |

## <a name="journeyinsights"></a>JourneyInsights

**JourneyInsights** -elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| TelemetryEngine | Ja | Värdet måste vara `ApplicationInsights`. |
| InstrumentationKey | Ja | Strängen som innehåller Instrumentation-nyckeln för Application Insights-elementet. |
| DeveloperMode | Ja | Möjliga värden: `true` eller `false`. Om `true`Application Insights påskyndar Telemetrin genom bearbetnings pipelinen. Den här inställningen är praktisk för utveckling, men är begränsad till hög volym de detaljerade aktivitets loggarna är endast utformade för att hjälpa till med utveckling av anpassade principer. Använd inte utvecklings läge i produktion. Loggar samlar in alla anspråk som skickas till och från identitets leverantörerna under utvecklingen. Om den används i produktion, antar utvecklaren ansvaret för PII (privat identifierbar information) som samlats in i den App Insights-logg som de äger. Dessa detaljerade loggar samlas endast in när det här värdet är inställt på `true`.|
| ClientEnabled | Ja | Möjliga värden: `true` eller `false`. `true`Skickar Application Insights klient sidans skript för att spåra sid visning och fel på klient sidan. |
| ServerEnabled | Ja | Möjliga värden: `true` eller `false`. Skickar den befintliga UserJourneyRecorder -JSONsomenanpassadhändelsetillApplicationInsights.`true` |
| TelemetryVersion | Ja | Värdet måste vara `1.0.0`. |

Mer information finns i [samla in loggar](active-directory-b2c-troubleshoot-custom.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Genom att använda anpassade principer i Azure AD B2C kan du skicka en parameter i en frågesträng. Genom att skicka parametern till HTML-slutpunkten kan du dynamiskt ändra sidinnehållet. Du kan till exempel ändra bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram. Azure AD B2C skickar Frågesträngens parametrar till din dynamiska HTML-fil, till exempel aspx-fil.

I följande exempel skickas en parameter med `campaignId` namnet med `hawaii` värdet i frågesträngen:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

**ContentDefinitionParameters** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | En sträng som innehåller nyckel värdes paret som läggs till i frågesträngen för en inläsnings-URI för innehålls definition. |

**ContentDefinitionParameter** -elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| Name | Ja | Namnet på nyckel värdes paret. |

Mer information finns i [Konfigurera gränssnittet med dynamiskt innehåll med hjälp av anpassade principer](active-directory-b2c-ui-customization-custom-dynamic.md)

## <a name="technicalprofile"></a>TechnicalProfile

**TechnicalProfile** -elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| ID | Ja | Värdet måste vara `PolicyProfile`. |

**TechnicalProfile** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Den sträng som innehåller namnet på den tekniska profil som visas för användarna. |
| Beskrivning | 0:1 | Den sträng som innehåller beskrivningen av den tekniska profil som visas för användarna. |
| Protocol | 1:1 | Protokollet som används för federationen. |
| Metadata | 0:1 | Insamling av *objekt* med nyckel/värde-par som används av protokollet för att kommunicera med slut punkten under en transaktion för att konfigurera interaktion mellan den förlitande parten och andra Community-deltagare. |
| OutputClaims | 0:1 | En lista med anspråks typer som tas ut i den tekniska profilen. Vart och ett av dessa element innehåller en referens till en **claimType** som redan har definierats i avsnittet **ClaimsSchema** eller i en princip som den här princip filen ärver. |
| SubjectNamingInfo | 0:1 | Ämnes namnet som används i tokens. |

**Protokoll** elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| Name | Ja | Namnet på ett giltigt protokoll som stöds av Azure AD B2C som används som en del av den tekniska profilen. Möjliga värden: `OpenIdConnect` eller `SAML2`. `OpenIdConnect` Värdet representerar OpenID Connect 1,0-protokoll standard som per OpenID Foundation-specifikation. `SAML2` Representerar SAML 2,0-protokoll standarden enligt Oasis-specifikationen. Använd inte en SAML-token i produktion. |

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | Namnet på en förväntad anspråks typ i listan över stödda för principen som den förlitande parten prenumererar på. Detta påstående fungerar som utdata för den tekniska profilen. |

**OutputClaim** -elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | En referens till en **claimType** som redan har definierats i **ClaimsSchema** -avsnittet i princip filen. |
| Standar | Nej | Ett standardvärde som kan användas om anspråks värdet är tomt. |
| PartnerClaimType | Nej | Skickar anspråket i ett annat namn som har kon figurer ATS i definitions definitionen för ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Med **SubjectNameingInfo** -elementet kan du styra värdet för token-ämnet:
- **JWT-token** – `sub` anspråket. Detta är ett huvud konto som används för att kontrollera information, t. ex. användare av ett program. Värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Den kan användas för att utföra säkra verifierings kontroller, till exempel när token används för att få åtkomst till en resurs. Som standard fylls ämnes anspråket med objekt-ID: t för användaren i katalogen. Mer information finns i [konfiguration av token, session och enkel inloggning](active-directory-b2c-token-session-sso.md).
- **SAML-token** – det `<Subject><NameID>` element som identifierar ämnes elementet.

**SubjectNamingInfo** -elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| ClaimType | Ja | En referens till ett utgående anspråks **PartnerClaimType**. De utgående anspråken måste definieras i **OutputClaims** -samlingen för förlitande part princip. |

I följande exempel visas hur du definierar en OpenID Connect-förlitande part. Ämnes namnets information konfigureras som `objectId`:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
JWT-token inkluderar `sub` anspråk med användaren ObjectID:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


