---
title: Microsoft Identity Platform-autentisering (v 1.0) | Azure
description: Lär dig grunderna om autentisering i Microsoft Identity Platform – appens modell, API, etablering och de vanligaste autentiserings scenarier som stöds av Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 445f301e2a526dc8f9e2c261e897fe8b1abe2f1e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966783"
---
# <a name="what-is-authentication"></a>Vad är autentisering?

*Autentisering* innebär att en part måste ange giltiga uppgifter och utgör grunden för att skapa ett säkerhetsobjekt som används för identitets- och åtkomstkontroll. Enklare sagt är det en process där du bevisar att du är den du säger att du är. Autentisering förkortas ibland AuthN.

*Auktorisering* innebär att bevilja ett autentiserat säkerhetsobjekt behörighet att göra något. Det anger vilka du data du får åtkomst till och vad du kan gör med dem. Auktorisering förkortas ibland AuthZ.

Microsoft Identity Platform fören klar autentiseringen för programutvecklare genom att tillhandahålla identitet som en tjänst, med stöd för bransch standard protokoll som OAuth 2,0 och OpenID Connect, samt bibliotek med öppen källkod för olika plattformar för att hjälper dig att snabbt börja koda.

Det finns två huvudsakliga användnings fall i programmerings modellen för Microsoft Identity Platform:

* Under ett beviljande flöde för OAuth 2.0-auktorisering – när resursägaren beviljar behörighet för klientprogrammet och tillåter klienten åtkomst till resursägarens resurser.
* Under resursåtkomst av klienten – enligt implementering av resursservern, med anspråksvärden som finns i åtkomsttoken för att fatta beslut om åtkomstkontroll utifrån dem.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>Grundläggande autentisering i Microsoft Identity Platform

Föreställ dig det enklaste scenariot där det krävs identitet: en användare i en webbläsare behöver autentiseras för ett webbprogram. Följande diagram visar det här scenariot:

![Översikt över inloggning till webbprogram](./media/v1-authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Det här behöver du känna till om olika komponenter som visas i diagrammet:

* Microsoft Identity Platform är identitets leverantören. Identitetsprovidern ansvarar för att verifiera identiteten för användare och program som finns i en organisations katalog och utfärdar säkerhetstoken vid utförd autentisering av de användarna och programmen.
* Ett program som vill kunna utföra autentisering av autentisering till Microsoft Identity Platform måste registreras i Azure Active Directory (Azure AD). Azure AD registrerar och identifierar unikt appen i katalogen.
* Utvecklare kan använda Microsoft Identity Platform Authentication-bibliotek med öppen källkod för att göra autentiseringen enkel genom att hantera protokoll informationen åt dig. Mer information finns i Microsoft Identity Platform [v 2.0 Authentication libraries](reference-v2-libraries.md) och [v 1.0-autentiseringsinställningar](active-directory-authentication-libraries.md).
* När en användare har autentiserats måste programmet validera användarens säkerhetstoken för att säkerställa att autentiseringen har utförts. Du hittar snabbstarter, självstudier och kodexempel på flera olika språk och ramverk som visar vad programmet måste göra.
  * Om du snabbt vill bygga en app och lägga till funktioner som att hämta tokens, uppdatera tokens, logga in en användare, visa användarinformation med mera läser du avsnittet **Snabbstarter** i dokumentationen.
  * Om du vill ha djupgående, scenariobaserade procedurer för vanliga uppgifter för auktoriseringsutveckling som att hämta åtkomsttoken och använda dem i anrop till Microsoft Graph API och andra API:er, implementera inloggning med Microsoft med en traditionell webbläsarbaserad app som använder OpenID Connect, med mera, läser du avsnittet **Självstudier** i dokumentationen.
  * Du kan ladda ned kodexempel på [GitHub](https://github.com/Azure-Samples?q=active-directory).
* Flödet för begäranden och svar för autentiseringsprocessen bestäms av autentiseringsprotokollet som du har använt, till exempel OAuth 2.0, OpenID Connect, WS-Federation eller SAML 2.0. Mer information om protokoll finns i avsnittet **begrepp > Authentication Protocol** i dokumentationen.

I exempelscenariot ovan kan du klassificera apparna enligt dessa två roller:

* Appar som behöver säker åtkomst till resurser
* Appar som har rollen av själva resursen

### <a name="how-each-flow-emits-tokens-and-codes"></a>Hur varje flöde avger tokens och koder

Beroende på hur din klient har skapats kan den använda en (eller flera) av de autentiserings flöden som stöds av Microsoft Identity Platform.  Dessa flöden kan skapa en mängd olika tokens (id_tokens, uppdatera tokens, åtkomsttoken) och auktoriseringsregler, och kräver olika token för att de ska fungera. Det här diagrammet innehåller en översikt:

|Flow | Kräver | id_token | Åtkomsttoken | uppdatera token | auktoriseringskod | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Flöde för auktoriseringskod](v1-protocols-oauth-code.md) | | x | x | x | x|  
|[Implicit flöde](v1-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrid OIDC-flöde](v1-protocols-openid-connect-code.md#get-access-tokens)| | x  | |          |            x   |
|[Uppdatera token-inlösen](v1-protocols-oauth-code.md#refreshing-the-access-tokens) | uppdatera token | x | x | x| |
|[On-Behalf-Of-flöde](v1-oauth2-on-behalf-of-flow.md) | Åtkomsttoken| x| x| x| |
|[Klientautentiseringsuppgifter](v1-oauth2-client-creds-grant-flow.md) | | | x (endast app-only)| | |

Token som utfärdas via det implicita läget har en längd begränsning på grund av att de skickas tillbaka till webbläsaren via URL: en (där `response_mode` är `query` eller `fragment`).  Vissa webbläsare har en gräns för storleken på URL: en som kan placeras i webbläsarens fält och inte fungerar när den är för lång.  Dessa tokens har därför inte `groups`-eller `wids`-anspråk. 

Nu när du har en översikt över grunderna kan du läsa om hur du kan förstå Identity app-modellen och API, hur etablering fungerar i Microsoft Identity Platform och länkar till detaljerad information om vanliga scenarier som stöds av Microsoft Identity Platform.

## <a name="application-model"></a>Programmodell

Microsoft Identity Platform representerar program som följer en speciell modell som är utformad för att uppfylla två huvud funktioner:

* **Identifiera appen enligt de autentiseringsprotokoll den stöder** – Detta innebär uppräkning av alla identifierare, URL:er, hemligheter och relaterad information som behövs vid identifieringen. Här är Microsoft Identity Platform:

    * Innehåller alla data som krävs för att stödja autentisering vid körning.
    * Innehåller alla data för att besluta vilka resurser en app kan behöva för åtkomst och om en viss begäran ska uppfyllas och under vilka omständigheter.
    * Tillhandahåller infrastrukturen för implementering av appetableringen i apputvecklarens klientorganisation och i andra Azure AD-klientorganisationer.

* **Hantera användar medgivande under Tokenbegäran och underlättar dynamisk etablering av appar över klient organisationer** – här, Microsoft Identity Platform:

    * Möjliggör för användare och administratörer att dynamiskt bevilja eller neka medgivande för appen att få åtkomst till resurser för deras räkning.
    * Möjliggör för administratörer att i slutänden bestämma vilka appar som tillåts göra vad och vilka användare som kan använda specifika appar samt hur åtkomsten till katalogresurserna går till.

I Microsoft Identity Platform beskriver ett **program objekt** ett program som en abstrakt entitet. Utvecklare kan arbeta med program. Vid distributions tillfället använder Microsoft Identity Platform ett angivet program objekt som skiss för att skapa ett **huvud namn för tjänsten**som representerar en konkret instans av ett program i en katalog eller klient. Det är tjänstens huvudnamn som definierar vad appen faktiskt kan göra i en specifik målkatalog, vilka som kan använda den, vilka resurser den har åtkomst till och så vidare. Microsoft Identity Platform skapar ett huvud namn för tjänsten från ett program objekt via **medgivande**.

Följande diagram visar ett förenklat etablerings flöde för Microsoft Identity Platform som drivs av medgivande.  Det finns två klienter (A och B), där klient organisation A äger programmet och klient B instansierar programmet via ett huvud namn för tjänsten.  

![Förenklat etableringsflöde som drivs av medgivande](./media/v1-authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

I det här etableringsflödet sker följande:

1. En användare från klient B försöker logga in med appen. slut punkten för auktorisering begär en token för programmet.
1. Autentiseringsuppgifterna för användaren förvärvas och verifieras för autentisering
1. Användaren uppmanas att ange medgivande för appen för att få åtkomst till klient B
1. Microsoft Identity Platform använder programobjektet i klient organisationen som en skiss för att skapa ett huvud namn för tjänsten i klient B
1. Användaren får begärd token

Du kan upprepa den här processen så många gånger du vill för andra klientorganisationer (C, D och så vidare). Klient organisation A behåller skissen för appen (program objekt). Användare och administratörer för alla andra klientorganisationer där appen får medgivande behåller kontrollen över vad programmet tillåts göra genom motsvarande tjänsthuvudobjekt i varje klientorganisation. Mer information finns i [program-och tjänst huvud objekt i Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>Anspråk i säkerhetstoken för Microsoft Identity Platform

Säkerhetstoken (åtkomst-och ID-token) som utfärdats av Microsoft Identity Platform innehåller anspråk eller intyg om information om ämnet som har autentiserats. Program kan använda anspråk för olika uppgifter, till exempel:

* Validera token
* Identifiera subjektets katalogklientorganisation
* Visa användarinformation
* Fastställa subjektets auktorisering

Anspråk som finns i alla angivna säkerhetstoken beror på typen av token, på typen av autentiseringsuppgift som används för att autentisera användaren och på programkonfigurationen.

En kort beskrivning av varje typ av anspråk som skickats av Microsoft Identity Platform finns i tabellen nedan. Mer detaljerad information [finns i åtkomsttoken och](access-tokens.md) [ID-token](id-tokens.md) som utfärdats av Microsoft Identity Platform.

| Begär | Beskrivning |
| --- | --- |
| Program-ID:t | Identifierar programmet som använder token. |
| Målgrupp | Identifierar mottagarresursen som token är avsedd för. |
| Application Authentication Context Class Reference | Anger hur klienten har autentiserats (offentlig eller konfidentiell klient). |
| Autentiseringstillfälle | Registrerar datum och tid när autentiseringen inträffade. |
| Autentiseringsmetod | Anger hur subjektet för token har autentiserats (lösenord, certifikat osv.). |
| Förnamn | Innehåller förnamnet på användaren som det anges i Azure AD. |
| Grupper | Innehåller objekt-ID:n för Azure AD-grupper som användaren är medlem i. |
| Identitetsprovider | Registrerar den identitetsprovider som har autentiserat subjektet för token. |
| Utfärdad | Registrerar tiden då token utfärdats, används ofta för tokens uppdateringsbarhet. |
| Utfärdare | Identifierar STS som genererat token samt Azure AD-klientorganisationen. |
| Efternamn | Innehåller efternamnet på användaren som det anges i Azure AD. |
| Namn | Innehåller ett läsbart värde som identifierar subjektet för token. |
| Objekt-ID | Innehåller en oföränderlig, unik identifierare för subjektet i Azure AD. |
| Roller | Innehåller egna namn på Azure AD-programroller som användaren har beviljats. |
| Omfång | Anger de behörigheter som beviljats för klientprogrammet. |
| Ämne | Anger huvudnamnet som token kontrollerar information om. |
| Klient-ID:t | Innehåller ett oföränderlig, unik identifierare för katalogklientorganisationen som har utfärdat token. |
| Tokenlivstid | Definierar tidsintervallet då token är giltig. |
| User Principal Name | Innehåller UPN för subjektet. |
| Version | Innehåller versionsnumret för token. |

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [program typer och scenarier som stöds i Microsoft Identity Platform](app-types.md)
