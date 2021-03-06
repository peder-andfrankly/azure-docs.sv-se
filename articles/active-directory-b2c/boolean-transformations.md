---
title: Transformerings exempel för booleska anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Transformerings exempel för booleska anspråk för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dcebcc3e2021938f3fd3bde236ef08e4f26b8a97
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949899"
---
# <a name="boolean-claims-transformations"></a>Omvandlingar med booleska anspråk

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder booleska anspråks omvandlingar av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Utför en och-åtgärd av två booleska inputClaims och anger outputClaim med resultatet av åtgärden.

| Objekt  | TransformationClaimType  | Datatyp  | Anteckningar |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolesk | Den första ClaimType som ska utvärderas. |
| InputClaim | inputClaim2  | boolesk | Den andra ClaimType som ska utvärderas. |
|OutputClaim | outputClaim | boolesk | Den ClaimTypes som ska skapas efter att den här anspråks omvandlingen har anropats (sant eller falskt). |

Följande anspråks omvandling visar hur och två booleska ClaimTypes: `isEmailNotExist`och `isSocialAccount`. Den utgående anspråks `presentEmailSelfAsserted` anges till `true` om värdet för båda indata-anspråk är `true`. I ett Orchestration-steg kan du använda ett villkor för att förinställa en självkontrollerad sida, bara om ett e-postmeddelande om sociala konton är tomt.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim1**: sant
    - **inputClaim2**: falskt
- Utgående anspråk:
    - **outputClaim**: falskt


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Kontrollerar att booleska värden för två anspråk är lika och genererar ett undantag om de inte är det.

| Objekt | TransformationClaimType  | Datatyp  | Anteckningar |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolesk | Den ClaimType som ska försäkras. |
| InputParameter |valueToCompareTo | boolesk | Värdet som ska jämföras (sant eller falskt). |

Omvandlingen av **AssertBooleanClaimIsEqualToValue** -anspråk körs alltid från en [teknisk verifierings profil](validation-technical-profile.md) som anropas av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md). **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** -metadata för självkontrollerad teknisk profil styr det fel meddelande som den tekniska profilen presenterar för användaren.

![AssertStringClaimsAreEqual-körning](./media/boolean-transformations/assert-execution.png)

Följande anspråks omvandling visar hur du kontrollerar värdet för en boolesk ClaimType med ett `true`-värde. Om värdet för `accountEnabled` ClaimType är falskt genereras ett fel meddelande.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Den tekniska profilen för `login-NonInteractive` verifiering anropar transformeringen för `AssertAccountEnabledIsTrue`-anspråk.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den självkontrollerade tekniska profilen anropar verifierings **inloggningen-inaktiv** teknisk profil.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: falskt
    - **valueToCompareTo**: sant
- Resultat: fel utlöst

## <a name="notclaims"></a>NotClaims

Utför en not-åtgärd för den booleska inputClaim och ställer in outputClaim med resultatet av åtgärden.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolesk | Det anspråk som ska användas. |
| OutputClaim | outputClaim | boolesk | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats (sant eller falskt). |

Använd den här anspråks omvandlingen för att utföra logisk negation på ett anspråk.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: falskt
- Utgående anspråk:
    - **outputClaim**: sant

## <a name="orclaims"></a>OrClaims

Beräknar en eller två booleska inputClaims och anger outputClaim med resultatet av åtgärden.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolesk | Den första ClaimType som ska utvärderas. |
| InputClaim | inputClaim2 | boolesk | Den andra ClaimType som ska utvärderas. |
| OutputClaim | outputClaim | boolesk | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats (sant eller falskt). |

Följande anspråks omvandling visar hur du `Or` två booleska ClaimTypes. I Orchestration-steget kan du använda ett villkor för att förinställa en självkontrollerad sida om värdet för ett av anspråken är `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim1**: sant
    - **inputClaim2**: falskt
- Utgående anspråk:
    - **outputClaim**: sant

