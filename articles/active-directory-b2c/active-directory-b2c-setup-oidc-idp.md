---
title: Konfigurera registrering och inloggning med OpenID Connect
titleSuffix: Azure AD B2C
description: Konfigurera registrering och inloggning med valfri OpenID Connect Identity Provider (IdP) i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 471163fc8fe8c5bad550d0615683ef2b97b818dc
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950482"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med OpenID Connect med Azure Active Directory B2C

[OpenID Connect](active-directory-b2c-reference-oidc.md) är ett autentiseringsprotokoll som byggts ovanpå OAuth 2,0 och som kan användas för säker inloggning av användare. De flesta identitets leverantörer som använder det här protokollet stöds i Azure AD B2C. Den här artikeln förklarar hur du kan lägga till anpassade OpenID ansluta identitets leverantörer till dina användar flöden.

## <a name="add-the-identity-provider"></a>Lägg till identitets leverantören

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att klicka på filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **ny OpenID Connect-Provider**.

## <a name="configure-the-identity-provider"></a>Konfigurera identitets leverantören

Varje OpenID Connect Identity Provider beskriver ett Metadatadokumentet som innehåller merparten av den information som krävs för att utföra inloggning. Detta omfattar information som webb adresserna som ska användas och platsen för tjänstens offentliga signerings nycklar. OpenID Connect-Metadatadokumentet finns alltid i en slut punkt som slutar i `.well-known\openid-configuration`. För den OpenID Connect Identity-Provider som du vill lägga till anger du dess URL för metadata.

## <a name="client-id-and-secret"></a>Klient-ID och hemlighet

För att användarna ska kunna logga in kräver identitets leverantören utvecklare att registrera ett program i sin tjänst. Det här programmet har ett ID som kallas **klient-ID** och en **klient hemlighet**. Kopiera de här värdena från identitets leverantören och ange dem i motsvarande fält.

> [!NOTE]
> Klient hemligheten är valfri. Du måste dock ange en klient hemlighet om du vill använda ett [auktoriseringskod](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)som använder hemligheten för att byta kod för token.

## <a name="scope"></a>Omfång

Omfattning definierar den information och de behörigheter som du vill samla in från den anpassade identitets leverantören. OpenID Connect-begäranden måste innehålla `openid` omfattnings värde för att kunna ta emot ID-token från identitets leverantören. Utan ID-token kan användarna inte logga in på Azure AD B2C med hjälp av den anpassade identitets leverantören. Andra omfattningar kan läggas till åtskilda med blank steg. Se den anpassade identitets leverantörens dokumentation för att se vilka andra omfattningar som kan vara tillgängliga.

## <a name="response-type"></a>Svarstyp

Svars typen beskriver vilken typ av information som skickas tillbaka i det första anropet till `authorization_endpoint` för den anpassade identitets leverantören. Följande svars typer kan användas:

* `code`: som enligt [koden för auktoriseringskod](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)returneras en kod tillbaka till Azure AD B2C. Azure AD B2C fortsätter att anropa `token_endpoint` för att skicka koden för token.
* `id_token`: en ID-token returneras tillbaka till Azure AD B2C från den anpassade identitets leverantören.

## <a name="response-mode"></a>Svars läge

Svars läget definierar metoden som ska användas för att skicka tillbaka data från den anpassade identitets leverantören till Azure AD B2C. Följande svars lägen kan användas:

* `form_post`: det här svars läget rekommenderas för bästa säkerhet. Svaret överförs via HTTP-`POST` metoden, med koden eller token som kodas i texten med `application/x-www-form-urlencoded`-formatet.
* `query`: koden eller token returneras som en frågeparameter.

## <a name="domain-hint"></a>Domän tips

Du kan använda domän tipset för att hoppa direkt till inloggnings sidan för den angivna identitets leverantören, i stället för att låta användaren göra ett val bland listan över tillgängliga identitets leverantörer. Ange ett värde för domän tipset för att tillåta den här typen av beteende. Om du vill gå till den anpassade identitets leverantören lägger du till parametern `domain_hint=<domain hint value>` i slutet av din begäran när du anropar Azure AD B2C för inloggning.

## <a name="claims-mapping"></a>Anspråks mappning

När den anpassade identitets leverantören skickar tillbaka en ID-token till Azure AD B2C måste Azure AD B2C kunna mappa anspråk från den mottagna token till anspråk som Azure AD B2C känner igen och använder. För var och en av följande mappningar läser du dokumentationen för den anpassade identitets leverantören för att förstå de anspråk som returneras tillbaka i identitets leverantörens token:

* **Användar-ID**: Ange anspråket som tillhandahåller den *unika identifieraren* för den inloggade användaren.
* **Visnings namn**: Ange det anspråk som innehåller *visnings namnet* eller det *fullständiga namnet* för användaren.
* **Tilldelat namn**: Ange det anspråk som innehåller användarens *förnamn* .
* Efter **Namn: Ange**det anspråk som innehåller användarens *efter namn* .
* **E-post**: Ange det anspråk som tillhandahåller användarens *e-postadress* .
