---
title: Enkel inloggning till program – Azure Active Directory | Microsoft Docs
description: Lär dig hur du väljer en enkel inloggnings metod när du konfigurerar program i Azure Active Directory (Azure AD). Använd enkel inloggning så att användarna inte behöver komma ihåg lösen ord för varje program och för att förenkla administrationen av konto hantering.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24a4209869d4c47f8ac73e250699ec55d006296
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786407"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Enkel inloggning till program i Azure Active Directory

Enkel inloggning (SSO) ger säkerhet och bekvämlighet när användare loggar in på program i Azure Active Directory (Azure AD). I den här artikeln beskrivs metoder för enkel inloggning och det hjälper dig att välja den lämpligaste SSO-metoden när du konfigurerar dina program.

- **Med enkel inloggning**loggar användare in en gång med ett konto för att komma åt domänanslutna enheter, företags resurser, SaaS-program (program vara som en tjänst) och webb program. När du har loggat in kan användaren starta program från Office 365-portalen eller Azure AD-appens åtkomst panel. Administratörer kan centralisera hanteringen av användar konton och automatiskt lägga till eller ta bort användar åtkomst till program baserat på grupp medlemskap.

- **Utan enkel inloggning**måste användare komma ihåg programspecifika lösen ord och logga in på varje program. IT-personalen måste skapa och uppdatera användar konton för varje program, till exempel Office 365, Box och Salesforce. Användarna måste komma ihåg sina lösen ord, samt ägna tid åt att logga in på varje program.

## <a name="choosing-a-single-sign-on-method"></a>Välja en metod för enkel inloggning

Det finns flera sätt att konfigurera ett program för enkel inloggning. Att välja en enkel inloggnings metod beror på hur programmet är konfigurerat för autentisering.

- Moln program kan använda OpenID Connect, OAuth, SAML, Password-based, Linked eller disabled för enkel inloggning. 
- Lokala program kan använda lösenordsbaserad, integrerad Windows-autentisering, rubrikbaserade, länkade eller inaktiverade metoder för enkel inloggning. De lokala alternativen fungerar när program har kon figurer ATS för programproxy.

Det här flödesschemat hjälper dig att bestämma vilken metod för enkel inloggning som passar bäst för din situation.

![Besluts flödes schema för enkel inloggnings metod](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

I följande tabell sammanfattas de enkla inloggnings metoderna och länkar till mer information.

| Metod för enkel inloggning | Programtyper | Används till att |
| :------ | :------- | :----- |
| [OpenID Connect och OAuth](#openid-connect-and-oauth) | endast moln | Använd OpenID Connect och OAuth när du utvecklar ett nytt program. Det här protokollet fören klar program konfigurationen, har lättanvända SDK: er och gör att ditt program kan använda MS Graph.
| [SAML](#saml-sso) | molnet och lokalt | Välj SAML när det är möjligt för befintliga program som inte använder OpenID Connect eller OAuth. SAML fungerar för program som autentiserar med ett av SAML-protokollen.|
| [Lösenordsbaserade](#password-based-sso) | molnet och lokalt | Välj Password-based när programmet autentiserar med användar namn och lösen ord. Lösenordsbaserad enkel inloggning möjliggör säker lagring av lösen ord för program och uppspelning med hjälp av ett webb läsar tillägg eller en mobilapp. Den här metoden använder den befintliga inloggnings processen som tillhandahålls av programmet, men gör det möjligt för en administratör att hantera lösen orden. |
| [Länkade](#linked-sign-on) | molnet och lokalt | Välj länkad inloggning när programmet har kon figurer ATS för enkel inloggning i en annan Identity Provider-tjänst. Med det här alternativet läggs inte enkel inloggning till i programmet. Programmet kan dock redan ha enkel inloggning implementerad med hjälp av en annan tjänst, till exempel Active Directory Federation Services (AD FS).|
| [Inaktiverad](#disabled-sso) | molnet och lokalt | Välj inaktive rad enkel inloggning när appen inte är redo att konfigureras för enkel inloggning. Användarna måste ange sitt användar namn och lösen ord varje gång de startar programmet.|
| [Integrerad Windows-autentisering (IWA)](#integrated-windows-authentication-iwa-sso) | endast lokalt | Välj IWA enkel inloggning för program som använder [integrerad Windows-autentisering (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)eller anspråks känsliga program. För IWA använder Application Proxy-kopplingarna Kerberos-begränsad delegering (KCD) för att autentisera användare till programmet. |
| [Rubrik baserad](#header-based-sso) | endast lokalt | Använd sidhuvud-baserad enkel inloggning när programmet använder rubriker för autentisering. För rubrik-baserad enkel inloggning krävs PingAccess för Azure AD. Programproxyn använder Azure AD för att autentisera användaren och sedan vidarebefordra trafik via anslutnings tjänsten.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect och OAuth

När du utvecklar nya program bör du använda moderna protokoll som OpenID Connect och OAuth för att uppnå bästa möjliga inloggnings upplevelse för din app över flera plattformar. Med OAuth kan användare eller administratörer [bevilja medgivande](configure-user-consent.md) för skyddade resurser som [Microsoft Graph](/graph/overview). Vi ger dig enkel att använda [SDK](../develop/reference-v2-libraries.md) : er för din app och dessutom är din app redo att använda [Microsoft Graph](/graph/overview).

Mer information finns här:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Microsoft Identity Platform Developer-Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO

Med **enkel inloggning i SAML**autentiserar Azure AD programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar inloggnings informationen till programmet via ett anslutnings protokoll. Med SAML-baserad enkel inloggning kan du mappa användare till specifika program roller baserat på regler som du definierar i dina SAML-anspråk.

Välj SAML-baserad enkel inloggning när programmet har stöd för det.

SAML-baserad enkel inloggning stöds för program som använder något av dessa protokoll:

- SAML 2.0
- WS-Federation

Information om hur du konfigurerar ett SaaS-program för SAML-baserad enkel inloggning finns i [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md). Många program vara som en tjänst (SaaS) har också en [programspecifik självstudie](../saas-apps/tutorial-list.md) som steg för steg tar dig igenom konfigurationen för SAML-baserad enkel inloggning.

Om du vill konfigurera ett program för WS-Federation följer du samma anvisningar för att konfigurera program för SAML-baserad enkel inloggning. mer information finns i [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md). I steget för att konfigurera programmet till att använda Azure AD måste du ersätta inloggnings-URL: en för Azure AD för slut punkten för WS-Federation-`https://login.microsoftonline.com/<tenant-ID>/wsfed`.

Om du vill konfigurera ett lokalt program för SAML-baserad enkel inloggning, se [SAML enkel inloggning för lokala program med Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Mer information om SAML-protokollet finns i [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Lösenordsbaserad SSO

Vid lösenordsbaserad inloggning loggas användarna in på programmet med ett användar namn och lösen ord första gången de använder den. Efter den första inloggningen skickar Azure AD användar namn och lösen ord till programmet.

Lösenordsbaserad enkel inloggning använder den befintliga autentiseringsprocessen som tillhandahålls av programmet. När du aktiverar enkel inloggning med lösen ord för ett program samlar Azure AD in och lagrar användar namn och lösen ord på ett säkert sätt för programmet. Användarautentiseringsuppgifter lagras i ett krypterat tillstånd i katalogen.

Välj lösenordsbaserad enkel inloggning när:

- Ett program har inte stöd för SAML-protokoll för enkel inloggning.
- Ett program autentiseras med ett användar namn och lösen ord i stället för åtkomst-tokens och-rubriker.

Lösenordsbaserad enkel inloggning stöds för alla molnbaserade program som har en HTML-baserad inloggnings sida. Användaren kan använda någon av följande webbläsare:

- Internet Explorer 11 i Windows 7 eller senare
   > [!NOTE]
   > Internet Explorer har begränsad support och tar inte längre emot nya program uppdateringar. Microsoft Edge är den rekommenderade webbläsaren.

- Microsoft Edge på Windows 10-jubileums version eller senare
- Microsoft Edge för iOS och Android
- Intune Managed Browser
- Chrome på Windows 7 eller senare och på MacOS X eller senare
- Firefox 26,0 eller senare på Windows XP SP2 eller senare och på Mac OS X 10,6 eller senare

Information om hur du konfigurerar ett moln program för lösenordsbaserad enkel inloggning finns i [Konfigurera enkel inloggning för lösen ord](configure-password-single-sign-on-non-gallery-applications.md).

Om du vill konfigurera ett lokalt program för enkel inloggning via Application Proxy, se [lösen ords valv för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Hur autentisering fungerar med lösenordsbaserad SSO

För att autentisera en användare i ett program hämtar Azure AD användarens autentiseringsuppgifter från katalogen och anger dem i programmets inloggnings sida.  Azure AD skickar säkert användarautentiseringsuppgifter via ett webb läsar tillägg eller en mobilapp. Den här processen gör det möjligt för en administratör att hantera användarautentiseringsuppgifter och kräver inte att användarna kommer ihåg sitt lösen ord.

> [!IMPORTANT]
> Autentiseringsuppgifterna är fördunklade från användaren under den automatiserade inloggnings processen. Autentiseringsuppgifterna kan dock identifieras med hjälp av webb fel söknings verktyg. Användare och administratörer måste följa samma säkerhets principer som om autentiseringsuppgifterna angavs direkt av användaren.

### <a name="managing-credentials-for-password-based-sso"></a>Hantera autentiseringsuppgifter för lösenordsbaserad SSO

Lösen ord för varje program kan antingen hanteras av Azure AD-administratören eller av användarna.

När Azure AD-administratören hanterar autentiseringsuppgifterna:  

- Användaren behöver inte återställa eller komma ihåg användar namnet och lösen ordet. Användaren kan komma åt programmet genom att klicka på det på åtkomst panelen eller via en angiven länk.
- Administratören kan utföra hanterings uppgifter för autentiseringsuppgifterna. Administratören kan till exempel uppdatera program åtkomsten enligt användar grupp medlemskap och status för anställda.
- Administratören kan använda administrativa autentiseringsuppgifter för att ge åtkomst till program som delas mellan många användare. Administratören kan till exempel tillåta alla som har åtkomst till ett program att ha åtkomst till ett socialt medium eller ett dokument delnings program.

När slutanvändaren hanterar autentiseringsuppgifterna:

- Användare kan hantera sina lösen ord genom att uppdatera eller ta bort dem efter behov.
- Administratörer kan fortfarande ange nya autentiseringsuppgifter för programmet.

## <a name="linked-sign-on"></a>Länkad inloggning
Länkad inloggning gör det möjligt för Azure AD att tillhandahålla enkel inloggning till ett program som redan har kon figurer ATS för enkel inloggning i en annan tjänst. Det länkade programmet kan visas för slutanvändare i Office 365-portalen eller Azure AD-portalen. En användare kan till exempel starta ett program som är konfigurerat för enkel inloggning i Active Directory Federation Services (AD FS) 2,0 (AD FS) från Office 365-portalen. Ytterligare rapportering är också tillgängligt för länkade program som startas från Office 365-portalen eller Azure AD-portalen. Information om hur du konfigurerar ett program för länkad inloggning finns i [Konfigurera länkad inloggning](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Länkad inloggning för programmigrering

Länkad inloggning kan ge en konsekvent användar upplevelse när du migrerar program under en viss tids period. Om du migrerar program till Azure Active Directory kan du använda länkad inloggning för att snabbt publicera länkar till alla program som du vill migrera.  Användarna kan hitta alla länkar i portalen för mina [appar](../user-help/active-directory-saas-access-panel-introduction.md) eller [Office 365-programmet](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Användare vet inte att de har åtkomst till ett länkat program eller ett migrerat program.  

När en användare har autentiserats med ett länkat program måste en konto post skapas innan slutanvändaren ges åtkomst till enkel inloggning. Etableringen av den här konto posten kan antingen ske automatiskt, eller så kan den utföras manuellt av en administratör.

## <a name="disabled-sso"></a>Inaktive rad SSO

Inaktiverat läge innebär att enkel inloggning inte används för programmet. När enkel inloggning är inaktive rad kan användarna behöva autentisera två gånger. Först autentiserar användare till Azure AD och loggar sedan in i programmet.

Använd inaktiverat läge för enkel inloggning:

- Om du inte är redo att integrera det här programmet med enkel inloggning med Azure AD eller
- Om du testar andra delar av programmet eller
- Som ett säkerhets lager i ett lokalt program som inte kräver att användare autentiseras. Med inaktiverat måste användaren autentisera sig.

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrerad Windows-autentisering (IWA) SSO

[Application Proxy](application-proxy.md) tillhandahåller enkel inloggning (SSO) till program som använder [integrerad Windows-autentisering (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)eller anspråksmedvetna program. Om ditt program använder IWA, autentiserar Application Proxy till programmet med hjälp av Kerberos-begränsad delegering (KCD). För ett anspråks medvetet program som litar på Azure Active Directory fungerar enkel inloggning eftersom användaren redan har autentiserats med hjälp av Azure AD.

Välj läge för integrerad Windows-autentisering för enkel inloggning för att tillhandahålla enkel inloggning till en lokal app som autentiserar med IWA.

Om du vill konfigurera en lokal app för IWA, se [Kerberos-begränsad delegering för enkel inloggning till dina program med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Så här fungerar enkel inloggning med KCD
Det här diagrammet förklarar flödet när en användare får åtkomst till ett lokalt program som använder IWA.

![Flödes diagram för Microsoft Azure AD-autentisering](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Användaren anger URL: en för att komma åt den lokala appen via programproxyn.
1. Programproxyn omdirigerar begäran till Azure AD Authentication Services för att förautentisera sig. I det här fallet tillämpar Azure AD alla tillämpliga autentiserings-och Auktoriseringsprinciper, till exempel multifaktorautentisering. Om användaren verifieras skapar Azure AD en token och skickar den till användaren.
1. Användaren skickar token till Application Proxy.
1. Application Proxy verifierar token och hämtar användarens huvud namn (UPN) från token. Den skickar sedan begäran, UPN och tjänstens huvud namn (SPN) till anslutnings tjänsten via en dubbelriktad autentiserad säker kanal.
1. I anslutnings tjänsten används KCD-förhandling (Kerberos-begränsad delegering) med lokal AD, som personifierar användaren för att hämta en Kerberos-token till programmet.
1. Active Directory skickar Kerberos-token för programmet till anslutningen.
1. Anslutningen skickar den ursprungliga begäran till program servern med hjälp av Kerberos-token som den fick från AD.
1. Programmet skickar svaret till anslutningen, som sedan returneras till Application Proxy-tjänsten och slutligen till användaren.

## <a name="header-based-sso"></a>Rubrik-baserad SSO

Rubrik-baserad enkel inloggning fungerar för program som använder HTTP-huvuden för autentisering. Den här inloggnings metoden använder en tredjeparts autentiseringstjänst som kallas PingAccess. En användare behöver bara autentisera till Azure AD.

Välj sidhuvud-baserad enkel inloggning när programproxyn och PingAccess har kon figurer ATS för programmet.

Om du vill konfigurera en huvud-baserad autentisering, se [rubrikbaserade autentisering för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Vad är PingAccess för Azure AD?

Med PingAccess för Azure AD kan användare komma åt och använda enkel inloggning till program som använder rubriker för autentisering. Application Proxy behandlar dessa program på samma sätt som andra, med hjälp av Azure AD för att autentisera åtkomst och sedan skicka trafik via anslutnings tjänsten. När autentiseringen utförs översätter PingAccess-tjänsten Azure AD-åtkomsttoken till ett huvud format som skickas till programmet.

Användarna märker inte något annorlunda när de loggar in för att använda dina företags program. De kan fortfarande arbeta var som helst på valfri enhet. Application Proxy-anslutningarna dirigerar fjärrtrafik till alla program och fortsätter att belastningsutjämna automatiskt.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Hur gör jag för att skaffa en licens för PingAccess?

Eftersom det här scenariot erbjuds via ett partnerskap mellan Azure AD och PingAccess, behöver du licenser för båda tjänsterna. Azure AD Premium prenumerationer innehåller dock en grundläggande PingAccess-licens som omfattar upp till 20 program. Om du behöver publicera fler än 20 huvudbaserade program kan du skaffa ytterligare en licens från PingAccess.

Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Relaterade artiklar
* [Självstudier för att integrera SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md)
* [Konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
* [Konfigurera lösenordsbaserad enkel inloggning](configure-password-single-sign-on-non-gallery-applications.md)
* [Konfigurera länkad inloggning](configure-linked-sign-on.md)
* [Introduktion till hantering av åtkomst till program](what-is-access-management.md)
* Nedladdnings länk: [distributions plan för enkel inloggning](https://aka.ms/SSODeploymentPlan).
