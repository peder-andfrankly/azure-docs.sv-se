---
title: Anpassa anspråk i Azure AD-appens SAML-token
titleSuffix: Microsoft identity platform
description: Lär dig hur du anpassar anspråk som utfärdats i SAML-token för företags program i Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14e1a466902ccb47d57e5f9590ba94bf08c76ec5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918448"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Gör så här: anpassa anspråk som utfärdats i SAML-token för företags program

Idag har Azure Active Directory (Azure AD) stöd för enkel inloggning (SSO) med de flesta företags program, inklusive båda program som är förintegrerade i Azure AD App-galleriet samt anpassade program. När en användare autentiserar till ett program via Azure AD med hjälp av SAML 2,0-protokollet skickar Azure AD en token till programmet (via ett HTTP-inlägg). Därefter verifierar och använder appen token för att logga användaren i stället för att begära ett användar namn och lösen ord. Dessa SAML-tokens innehåller delar av information om den användare som kallas för *anspråk*.

Ett *anspråk* är information som en identitets leverantör anger för en användare inom den token som de utfärdar för den användaren. I [SAML-token](https://en.wikipedia.org/wiki/SAML_2.0)finns dessa data vanligt vis i instruktionen SAML Attribute. Användarens unika ID visas vanligt vis i SAML-ämnet även kallat namn identifierare.

Som standard utfärdar Azure AD en SAML-token till ditt program som innehåller ett `NameIdentifier`-anspråk med ett värde för användarens användar namn (även kallat User Principal Name) i Azure AD, vilket kan identifiera användaren unikt. SAML-token innehåller också ytterligare anspråk som innehåller användarens e-postadress, förnamn och efter namn.

Om du vill visa eller redigera de anspråk som utfärdats i SAML-token till programmet öppnar du programmet i Azure Portal. Öppna sedan avsnittet **användarattribut &-anspråk** .

![Öppna avsnittet användarattribut &-anspråk i Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token:

* Programmet kräver att `NameIdentifier`-eller NameID-anspråk är något annat än användar namnet (eller User Principal Name) som lagras i Azure AD.
* Programmet har skrivits för att kräva en annan uppsättning av anspråk-URI: er eller anspråks värden.

## <a name="editing-nameid"></a>Redigera nameID

Så här redigerar du NameID (namn-ID-värde):

1. Öppna sidan **namn på identifierat värde** .
1. Välj det attribut eller den omvandling som du vill använda för attributet. Alternativt kan du ange det format som du vill att NameID-anspråket ska ha.

   ![Redigera värdet för NameID (namn identifierare)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID-format

Om SAML-begäran innehåller elementet NameIDPolicy med ett särskilt format, kommer Azure AD att respektera formatet i begäran.

Om SAML-begäran inte innehåller ett element för NameIDPolicy utfärdar Azure AD NameID med det format som du anger. Om inget format har angetts använder Azure AD standard käll formatet som är associerat med den valda anspråks källan.

I list rutan **Välj namn identifierare format** kan du välja något av följande alternativ.

| NameID-format | Beskrivning |
|---------------|-------------|
| **Standard** | Standardvärdet för käll formatet används i Azure AD. |
| **Bestående** | Azure AD kommer att använda beständigt som NameID-format. |
| **E-postadress** | Azure AD kommer att använda EmailAddress som NameID-format. |
| **Ospecificerad** | Azure AD kommer att använda ospecificerat som NameID-format. |
| **Kvalificerat namn för Windows-domän** | Azure AD kommer att använda WindowsDomainQualifiedName som NameID-format. |

En tillfällig NameID stöds också, men är inte tillgänglig i list rutan och kan inte konfigureras på Azures sida. Mer information om attributet NameIDPolicy finns i [SAML-protokoll för enkel inloggning](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attribut

Välj önskad källa för `NameIdentifier` (eller NameID)-anspråket. Du kan välja bland följande alternativ.

| Namn | Beskrivning |
|------|-------------|
| E-post | Användarens e-postadress |
| userprincipalName | Användarens huvud namn (UPN) |
| onpremisessamaccount | SAM-kontonamn som har synkroniserats från den lokala Azure AD |
| objectID | ObjectID för användaren i Azure AD |
| employeeid | Användarens medarbetar-ID |
| Katalogtillägg | Katalog tillägg som har [synkroniserats från lokala Active Directory med Azure AD Connect synkronisering](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Attribut för tillägg 1-15 | Attribut för lokala tillägg som används för att utöka Azure AD-schemat |

Mer information finns i [tabell 3: giltiga ID-värden per källa](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Du kan också tilldela ett konstant värde (statisk) till alla anspråk som du definierar i Azure AD. Följ stegen nedan för att tilldela ett konstant värde:

1. I [Azure Portal](https://portal.azure.com/)i avsnittet **användarattribut &-anspråk** klickar du på **Redigera** -ikonen för att redigera anspråken.

1. Klicka på det obligatoriska anspråk som du vill ändra.

1. Ange det konstanta värdet utan citat tecken i **källattributet** enligt din organisation och klicka på **Spara**.

    ![Öppna avsnittet användarattribut &-anspråk i Azure Portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Det konstanta värdet visas som nedan.

    ![Öppna avsnittet användarattribut &-anspråk i Azure Portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Särskilda anspråk – transformeringar

Du kan också använda funktionerna för anspråks omvandlingar.

| Funktion | Beskrivning |
|----------|-------------|
| **ExtractMailPrefix()** | Tar bort domänsuffix från antingen e-postadressen eller User Principal Name. Detta extraherar bara den första delen av användar namnet som skickas genom (till exempel "joe_smith" i stället för joe_smith@contoso.com). |
| **Anslut ()** | Ansluter till ett attribut med en verifierad domän. Om värdet för den valda användar identifieraren har en domän extraheras användar namnet för att lägga till den valda verifierade domänen. Om du till exempel väljer e-postmeddelandet (joe_smith@contoso.com) som värde för användar-ID och väljer contoso.onmicrosoft.com som verifierade domän leder detta till joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Konverterar tecknen i det valda attributet till gemener. |
| **ToUpper()** | Konverterar tecknen i det valda attributet till versaler. |

## <a name="adding-application-specific-claims"></a>Lägga till programspecifika anspråk

Så här lägger du till programspecifika anspråk:

1. I användarattribut **& anspråk**väljer du **Lägg till nytt anspråk** för att öppna sidan **hantera användar anspråk** .
1. Ange anspråkets **namn** . Värdet behöver inte absolut följa ett URI-mönster, enligt SAML-specifikationen. Om du behöver ett URI-mönster kan du ange det i fältet **namn område** .
1. Välj **källan** där anspråket ska hämta sitt värde. Du kan välja ett användarattribut från List rutan källattribut eller tillämpa en omvandling till användarattribut innan du avger det som ett anspråk.

### <a name="claim-transformations"></a>Anspråks omvandlingar

Så här använder du en omvandling till ett användar-attribut:

1. I **Hantera anspråk**väljer du *omvandling* som anspråks källa för att öppna sidan **Hantera omvandling** .
2. Välj funktionen i list rutan för omvandling. Beroende på vilken funktion du väljer måste du ange parametrar och ett konstant värde som ska utvärderas i omvandlingen. Se tabellen nedan för mer information om tillgängliga funktioner.
3. Om du vill använda flera omvandlingar klickar du på **Lägg till omvandling**. Du kan använda högst två omvandlingar till ett anspråk. Du kan till exempel först extrahera e-postprefixet för `user.mail`. Skapa sedan strängen versaler.

   ![Redigera värdet för NameID (namn identifierare)](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Du kan använda följande funktioner för att transformera anspråk.

| Funktion | Beskrivning |
|----------|-------------|
| **ExtractMailPrefix()** | Tar bort domänsuffix från antingen e-postadressen eller User Principal Name. Detta extraherar bara den första delen av användar namnet som skickas genom (till exempel "joe_smith" i stället för joe_smith@contoso.com). |
| **Anslut ()** | Skapar ett nytt värde genom att koppla ihop två attribut. Du kan också använda en avgränsare mellan de två attributen. Vid omvandling av NameID-anspråk är kopplingen begränsad till en verifierad domän. Om värdet för den valda användar identifieraren har en domän extraheras användar namnet för att lägga till den valda verifierade domänen. Om du till exempel väljer e-postmeddelandet (joe_smith@contoso.com) som värde för användar-ID och väljer contoso.onmicrosoft.com som verifierade domän leder detta till joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Konverterar tecknen i det valda attributet till gemener. |
| **ToUpper()** | Konverterar tecknen i det valda attributet till versaler. |
| **Contains ()** | Matar ut ett attribut eller en konstant om indata matchar det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Om du till exempel vill generera ett anspråk där värdet är användarens e-postadress om den innehåller domänen "@contoso.com", annars vill du spara User Principal Name. Det gör du genom att konfigurera följande värden:<br/>*Parameter 1 (indata)* : User. email<br/>*Värde*: "@contoso.com"<br/>Parameter 2 (utdata): User. email<br/>Parameter 3 (utdata om det inte finns någon matchning): User. UserPrincipalName |
| **EndWith()** | Matar ut ett attribut eller en konstant om indata slutar med det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Om du till exempel vill generera ett anspråk där värdet är användarens medarbetar-ID om anställnings-ID: t slutar med "000", annars vill du spara ett attribut för tillägg. Det gör du genom att konfigurera följande värden:<br/>*Parameter 1 (indata)* : User. Anställningsnr<br/>*Värde*: "000"<br/>Parameter 2 (utdata): User. Anställningsnr<br/>Parameter 3 (utdata om det inte finns någon matchning): User. extensionAttribute1 |
| **StartWith()** | Matar ut ett attribut eller en konstant om indatan börjar med det angivna värdet. Annars kan du ange en annan utdata om det inte finns någon matchning.<br/>Om du till exempel vill generera ett anspråk där värdet är användarens anställnings-ID om landet/regionen börjar med "US", annars vill du spara ett attribut för tillägg. Det gör du genom att konfigurera följande värden:<br/>*Parameter 1 (indata)* : användare. land<br/>*Värde*: "US"<br/>Parameter 2 (utdata): User. Anställningsnr<br/>Parameter 3 (utdata om det inte finns någon matchning): User. extensionAttribute1 |
| **Extrahera ()-efter matchning** | Returnerar del strängen när den matchar det angivna värdet.<br/>Om exempelvis värdet för indata är "Finance_BSimon" är det matchande värdet "Finance_", och anspråkets utdata är "BSimon". |
| **Extrahera ()-före matchning** | Returnerar del strängen tills den matchar det angivna värdet.<br/>Om exempelvis värdet för indata är "BSimon_US" är det matchande värdet "_US", och anspråkets utdata är "BSimon". |
| **Extrahera () – mellan matchning** | Returnerar del strängen tills den matchar det angivna värdet.<br/>Exempel: om indatans värde är "Finance_BSimon_US" är det första matchande värdet "Finance_", det andra matchning svärdet är "_US", och anspråkets utdata är "BSimon". |
| **ExtractAlpha ()-prefix** | Returnerar den alfabetiska delen av strängen.<br/>Om värdet till exempel är "BSimon_123" returnerar det "BSimon". |
| **ExtractAlpha() - Suffix** | Returnerar den alfabetiska delen för suffixet i strängen.<br/>Om värdet till exempel är "123_Simon" returnerar det "Simon". |
| **ExtractNumeric ()-prefix** | Returnerar prefixets numeriska del av strängen.<br/>Om värdet till exempel är "123_BSimon" returneras "123". |
| **ExtractNumeric ()-suffix** | Returnerar suffixets numeriska del av strängen.<br/>Om värdet till exempel är "BSimon_123" returneras "123". |
| **IfEmpty()** | Matar ut ett attribut eller en konstant om indata är null eller tomt.<br/>Om du till exempel vill mata ut ett attribut som lagras i en extensionattribute om medarbetar-ID: t för en specifik användare är tomt. Det gör du genom att konfigurera följande värden:<br/>Parameter 1 (indata): User. Anställningsnr<br/>Parameter 2 (utdata): User. extensionAttribute1<br/>Parameter 3 (utdata om det inte finns någon matchning): User. Anställningsnr |
| **IfNotEmpty()** | Matar ut ett attribut eller en konstant om indata inte är null eller tomma.<br/>Om du till exempel vill mata ut ett attribut som lagras i en extensionattribute om medarbetar-ID: t för en specifik användare inte är tomt. Det gör du genom att konfigurera följande värden:<br/>Parameter 1 (indata): User. Anställningsnr<br/>Parameter 2 (utdata): User. extensionAttribute1 |

Om du behöver ytterligare transformeringar skickar du din idé i feedback- [forumet i Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) under kategorin *SaaS-program* .

## <a name="emitting-claims-based-on-conditions"></a>Avsändnings anspråk baserat på villkor

Du kan ange källan för ett anspråk baserat på användar typ och gruppen som användaren tillhör. 

Användar typen kan vara:
- **Alla**: alla användare får åtkomst till programmet.
- **Medlemmar**: klient organisationens interna medlem
- **Alla gäster**: användaren förs över från en extern organisation med eller utan Azure AD.
- **AAD-gäster**: gäst användare tillhör en annan organisation som använder Azure AD.
- **Externa gäster**: gäst användare tillhör en extern organisation som inte har Azure AD.


Ett scenario där detta är användbart är när källan till ett anspråk är annorlunda för en gäst och en medarbetare som har åtkomst till ett program. Du kanske vill ange att om användaren är en medarbetare kommer NameID från User. e-post, men om användaren är en gäst, kommer NameID från User. extensionAttribute1.

Så här lägger du till ett anspråks villkor:

1. I **Hantera anspråk**expanderar du anspråks villkoren.
2. Välj användar typ.
3. Välj de grupper som användaren ska tillhöra. Du kan välja upp till 10 unika grupper i alla anspråk för ett angivet program. 
4. Välj **källan** där anspråket ska hämta sitt värde. Du kan välja ett användarattribut från List rutan källattribut eller tillämpa en omvandling till användarattribut innan du avger det som ett anspråk.

Den ordning som du lägger till villkoren i är viktiga. Azure AD utvärderar villkoren från uppifrån och ned för att bestämma vilket värde som ska ställas in i anspråket. 

Till exempel är Brita Simon en gäst användare i Contoso-klienten. Hon tillhör en annan organisation som också använder Azure AD. Under den här konfigurationen av programmet Fabrikam när Brita försöker logga in på Fabrikam, kommer Azure AD att utvärdera villkoren enligt följande.

Först verifierar Azure AD om Brita användar typ är `All guests`. Eftersom detta är sant tilldelar Azure AD källan för anspråk att `user.extensionattribute1`. För det andra verifierar Azure AD om Brita användar typ är `AAD guests`, eftersom detta också är sant tilldelar Azure AD källan för anspråk att `user.mail`. Slutligen genereras anspråket med värde `user.email` för Brita.

![Villkorlig konfiguration av anspråk](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Nästa steg

* [Program hantering i Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurera enkel inloggning för program som inte finns i Azure AD-programgalleriet](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Felsöka SAML-baserad enkel inloggning](howto-v1-debug-saml-sso-issues.md)
