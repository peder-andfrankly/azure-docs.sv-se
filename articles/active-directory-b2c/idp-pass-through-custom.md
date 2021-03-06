---
title: Skicka en åtkomsttoken via en anpassad princip till din app
titleSuffix: Azure AD B2C
description: Lär dig hur du kan skicka en åtkomsttoken för OAuth 2,0-identitets leverantörer som ett anspråk via en anpassad princip till ditt program i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8f7122035f8d70cb91f4ec4f64e1dd4f7b2842b8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949848"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Skicka en åtkomsttoken via en anpassad princip till ditt program i Azure Active Directory B2C

En [anpassad princip](active-directory-b2c-get-started-custom.md) i Azure Active Directory B2C (Azure AD B2C) ger användare av programmet en möjlighet att registrera sig eller logga in med en identitets leverantör. När detta inträffar tar Azure AD B2C [emot en åtkomsttoken](active-directory-b2c-reference-tokens.md) från identitets leverantören. Azure AD B2C använder denna token för att hämta information om användaren. Du lägger till en anspråks typ och ett utgående anspråk till din anpassade princip för att skicka token till de program som du registrerar i Azure AD B2C.

Azure AD B2C stöder överföring av åtkomsttoken för [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) och [OpenID Connect](active-directory-b2c-reference-oidc.md) Identity providers. För alla andra identitets leverantörer returneras anspråket tomt.

## <a name="prerequisites"></a>Krav

* Din anpassade princip konfigureras med en OAuth 2,0-eller OpenID Connect-identitetsprovider.

## <a name="add-the-claim-elements"></a>Lägg till anspråks elementen

1. Öppna filen *TrustframeworkExtensions. XML* och Lägg till följande **claimType** -element med en identifierare för `identityProviderAccessToken` till **ClaimsSchema** -elementet:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Lägg till elementet **OutputClaim** i **TechnicalProfile** -elementet för varje OAuth 2,0-identitetsprovider som du vill ha åtkomsttoken för. I följande exempel visas det element som har lagts till i den nya Facebook-tekniska profilen:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Spara filen *TrustframeworkExtensions. XML* .
4. Öppna den förlitande part princip filen, till exempel *SignUpOrSignIn. XML*, och Lägg till **OutputClaim** -elementet i **TechnicalProfile**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Spara princip filen.

## <a name="test-your-policy"></a>Testa principen

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha Azure AD B2C-token som returneras till `https://jwt.ms` för att kunna granska anspråk i det.

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att klicka på filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **ramverk för identitets upplevelse**.
5. På sidan anpassade principer klickar du på **Ladda upp princip**.
6. Välj **Skriv över principen om den finns**och Sök sedan efter och välj *TrustframeworkExtensions. XML-* filen.
7. Välj **Överför**.
8. Upprepa steg 5 till 7 för den förlitande part filen, till exempel *SignUpOrSignIn. XML*.

### <a name="run-the-policy"></a>Kör principen

1. Öppna den princip som du har ändrat. Till exempel *B2C_1A_signup_signin*.
2. För **program**väljer du ditt program som du har registrerat tidigare. Om du vill se token i exemplet nedan ska **svars-URL:** en Visa `https://jwt.ms`.
3. Välj **Kör nu**.

    Du bör se något som liknar följande exempel:

    ![Avkodad token i jwt.ms med idp_access_token block markerat](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Nästa steg

Läs mer om tokens i [Azure Active Directory B2C token-referens](active-directory-b2c-reference-tokens.md).
