---
title: Microsoft Identity Platform-autentiserings scenarier | Azure
description: 'Lär dig mer om autentiserings flöden och program scenarier för Microsoft Identity Platform. Lär dig mer om de olika typerna av program som kan autentisera identiteter, Hämta token och anropa skyddade API: er.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969c7b6c86e09ec73ccf1ffa5cc4dc9703a3d25c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963808"
---
# <a name="authentication-flows-and-application-scenarios"></a>Autentiserings flöden och program scenarier

Slut punkten för Microsoft Identity Platform (v 2.0) stöder autentisering för olika typer av moderna program arkitekturer. Alla arkitekturer baseras på bransch standard protokollen [OAuth 2,0 och OpenID Connect](active-directory-v2-protocols.md).  Med hjälp av [autentiseringsscheman](reference-v2-libraries.md)kan program autentisera identiteter och hämta token för att få åtkomst till skyddade API: er.

I den här artikeln beskrivs olika autentiserings flöden och de program scenarier som de används i. Den här artikeln innehåller också listor över:
- [Program scenarier och stödda autentiserings flöden](#scenarios-and-supported-authentication-flows).
- [Program scenarier och plattformar och språk som stöds](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Program kategorier

Du kan hämta token från flera olika typer av program, inklusive:

- Webbappar
- Mobilappar
- Skrivbordsappar
- Webb-API:er

De kan också hämtas från appar som körs på enheter som inte har någon webbläsare eller som körs på IoT.

Program kan kategoriseras som i följande lista:

- [Skyddade resurser jämfört med klient program](#protected-resources-vs-client-applications): vissa scenarier är om att skydda resurser som Web Apps eller webb-API: er. Andra scenarier är att förvärva en säkerhetstoken för att anropa ett skyddat webb-API.
- [Med användare eller utan användare](#with-users-or-without-users): vissa scenarier innefattar en inloggad användare, men andra som bakgrunds scenarier omfattar inte en användare.
- [Enkels Ides, offentlig klient och konfidentiella klient program](#single-page-public-client-and-confidential-client-applications): dessa är tre stora kategorier av program typer. Varje används med olika bibliotek och objekt.
- [Inloggnings mål](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): de tillgängliga autentiserings flödena varierar beroende på inloggnings mål gruppen. Vissa flöden är bara tillgängliga för arbets-eller skol konton. Och några är tillgängliga både för arbets-eller skol konton och för personliga Microsoft-konton. Den tillåtna mål gruppen beror på autentiserings flöden.
- [OAuth 2,0-flöden som stöds](#scenarios-and-supported-authentication-flows): autentiserings flöden används för att implementera program scenarier som begär tokens. Det finns inte en en-till-en-mappning mellan program scenarier och autentiserings flöden.
- [Plattformar som stöds](#scenarios-and-supported-platforms-and-languages): det är inte alla program scenarier som är tillgängliga för varje plattform.

### <a name="protected-resources-vs-client-applications"></a>Skyddade resurser kontra klient program

Autentiserings scenarier omfattar två aktiviteter:

- Hämta säkerhetstoken **för ett skyddat webb-API**: Microsoft rekommenderar att du använder [autentiseringspaket](reference-v2-libraries.md#microsoft-supported-client-libraries) för att hämta tokens, i synnerhet Microsoft Authentication Library (MSAL)-serien.
- **Skydda ett webb-API eller en webbapp**: en utmaning att skydda ett webb-API eller en webbapp är att verifiera säkerhetstoken. På vissa plattformar erbjuder Microsoft [bibliotek för mellanprogram](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Med användare eller utan användare

De flesta autentiseringar hämtar token för inloggade användares räkning.

![Scenarier med användare](media/scenarios/scenarios-with-users.svg)

Det finns dock även scenarier för daemon-appar, där program hämtar token för sig själva utan någon användare.

![Scenarier med daemon-appar](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Enkels Ides, offentlig klient och konfidentiella klient program

Säkerhetstoken kan hämtas från flera olika typer av program. Dessa program är ofta indelade i tre kategorier:

- **Program med en enda sida**: även kallat SPAs, dessa är webbappar där tokens hämtas från en Java Script-eller typescript-app som körs i webbläsaren. Många moderna appar har ett program med en enda sida som främst är skrivet i Java Script. Programmet använder ofta ett ramverk som vinkel, reagera eller Vue. MSAL. js är det enda Microsoft Authentication Library som stöder program med en enda sida.

- **Offentliga klient program**: de här programmen loggar alltid in användare:
  - Skrivbordsappar som anropar webb-API: er å den inloggade användarens vägnar
  - Mobilappar
  - Appar som körs på enheter som inte har någon webbläsare, som de som körs på iOT

  Dessa appar representeras av MSAL [PublicClientApplication](msal-client-applications.md) -klassen.

- **Konfidentiella klient program**:
  - Webb program som anropar ett webb-API
  - Webb-API: er som anropar ett webb-API
  - Daemon-appar, även när de implementeras som en konsol tjänst som en Linux-daemon eller en Windows-tjänst

  Dessa typer av appar använder klassen [ConfidentialClientApplication](msal-client-applications.md) .

## <a name="application-scenarios"></a>Programscenarier

Slut punkten för Microsoft Identity Platform stöder autentisering för olika typer av app-arkitekturer:

- Enkelsidiga appar
- Webbappar
- Webb-API:er
- Mobilappar
- Inbyggda appar
- Daemon-appar
- Appar på Server Sidan

Programmen använder olika autentiserings flöden för att logga in användare och hämta token för att anropa skyddade API: er.

### <a name="a-single-page-application"></a>Ett program med en enda sida

Många moderna webbappar är skapade för program på klient sidan som skrivits med hjälp av Java Script eller ett SPA-ramverk som till exempel vinkel, Vue. js och reagerar. js. Dessa program körs i en webbläsare. Deras egenskaper för autentisering skiljer sig från de flesta av traditionella webb program på Server sidan. Med hjälp av Microsoft Identity Platform kan program med en enda sida logga in användare och hämta token för att få åtkomst till backend-tjänster eller webb-API: er.

![Ett program med en enda sida](media/scenarios/spa-app.svg)

Mer information finns i [program med en enda sida](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>En webbapp som loggar in en användare

![En webbapp som loggar in en användare](media/scenarios/scenario-webapp-signs-in-users.svg)

Så här skyddar du en webbapp som loggar in en användare:

- Om du utvecklar i .NET använder du ASP.NET eller ASP.NET Core med ASP.NET Open ID Connect mellan. Att skydda en resurs innebär att verifiera säkerhetstoken, som görs av IdentityModel- [tilläggen för .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) -bibliotek och inte MSAL-bibliotek.

- Om du utvecklar i Node. js använder du Passport. js.

Mer information finns i [webbapp som loggar in användare](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>En webbapp som loggar in en användare och anropar ett webb-API för användarens räkning

![En webbapp som anropar webb-API: er](media/scenarios/web-app.svg)

För att anropa ett webb-API från en webbapp för en användares räkning, använder du MSAL **ConfidentialClientApplication** -klassen. Du använder flödes kods flödet och lagrar de hämtade token i token-cachen. Vid behov uppdaterar MSAL token och styrenheten hämtar token tyst från cachen.

Mer information finns i [en webbapp som anropar webb-API: er](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>En stationär app som anropar ett webb-API för en inloggad användare

Om du vill att en app ska anropa ett webb-API som loggar in användare använder du den interaktiva token-förvärvs metoden i MSAL **PublicClientApplication** -klassen. Med dessa interaktiva metoder kan du styra inloggnings GRÄNSSNITTs upplevelsen. MSAL använder en webbläsare för den här interaktionen.

![En stationär app som anropar ett webb-API](media/scenarios/desktop-app.svg)

Det finns en annan möjlighet för Windows-värdbaserade program på datorer som är anslutna till en Windows-domän eller av Azure Active Directory (Azure AD). Dessa program kan hämta en token tyst med hjälp av [integrerad Windows-autentisering](https://aka.ms/msal-net-iwa).

Program som körs på en enhet utan webbläsare kan fortfarande anropa ett API för en användares räkning. Användaren måste logga in på en annan enhet som har en webbläsare för att kunna autentisera. Det här scenariot kräver att du använder [enhets kod flödet](https://aka.ms/msal-net-device-code-flow).

![Enhets kod flöde](media/scenarios/device-code-flow-app.svg)

Även om vi inte rekommenderar att du använder den, är [användar namnet/lösen ordet Flow](https://aka.ms/msal-net-up) tillgängligt i offentliga klient program. Det här flödet behövs fortfarande i vissa scenarier som DevOps.

Men att använda det här flödet medför begränsningar i dina program. Till exempel kan program som använder det här flödet inte logga in en användare som behöver utföra Multi-Factor Authentication eller villkorlig åtkomst. Dina program har inte heller nytta av enkel inloggning.

Autentisering med användar namn/lösen ord-flödet går mot principerna för modern autentisering och tillhandahålls endast av äldre skäl.

Om du vill att token cache ska vara beständig i skrivbordsappar, bör du [Anpassa cachelagringen av token](https://aka.ms/msal-net-token-cache-serialization). Genom att implementera [serialisering av dubbla token-cache](https://aka.ms/msal-net-dual-cache-serialization)kan du använda bakåtkompatibla token-och Forward-kompatibla token-cacheminnen med tidigare generationer av autentiseringspaket. Särskilda bibliotek innehåller Azure AD Authentication Library för .NET (ADAL.NET) version 3 och version 4.

Mer information finns i [Skriv bords program som anropar webb-API: er](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>En mobilapp som anropar ett webb-API åt en interaktiv användare

På samma sätt som en stationär app, anropar en mobilapp de interaktiva metoderna för att hämta en MSAL **PublicClientApplication** -klass för att hämta en token för att anropa ett webb-API.

![En mobilapp som anropar ett webb-API](media/scenarios/mobile-app.svg)

MSAL iOS och MSAL Android använder system webbläsare som standard. Du kan dock dirigera dem att använda den inbäddade webbvy i stället. Det finns information som är beroende av den mobila plattformen: Universell Windows-plattform (UWP), iOS eller Android.

Vissa scenarier, t. ex. sådana som inbegriper villkorlig åtkomst som rör ett enhets-ID eller en enhets registrering, kräver att en [Service Broker](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) installeras på enheten. Exempel på utjämnare är Microsoft Företagsportal på Android och Microsoft Authenticator på Android och iOS. Dessutom kan MSAL nu interagera med mäklare.

> [!NOTE]
> Mobilappen som använder MSAL. iOS, MSAL. Android eller MSAL.NET på Xamarin kan ha skydds principer för appar som tillämpas på den. Till exempel kan principerna hindra en användare från att kopiera skyddad text. Mobilappen [hanteras av Intune](https://docs.microsoft.com/intune/app-sdk) och identifieras av Intune som en hanterad app. [Intune App SDK](https://docs.microsoft.com/intune/app-sdk-get-started) är separat från MSAL-bibliotek och interagerar med Azure AD på egen hand.

Mer information finns i [mobilapp som anropar webb-API: er](scenario-mobile-overview.md).

### <a name="a-protected-web-api"></a>Ett skyddat webb-API

Du kan använda Microsoft Identity Platform-slutpunkten för att skydda webb tjänster som appens RESTful-webb-API. Ett skyddat webb-API anropas med en åtkomsttoken för att skydda API: ns data och för att autentisera inkommande begär Anden. Anroparen för ett webb-API lägger till en åtkomsttoken i Authorization-huvudet för en HTTP-begäran.

Om du vill skydda ditt ASP.NET-eller ASP.NET Core-webb-API måste du verifiera åtkomst-token. För den här verifieringen använder du ASP.NET JWT mellanprogram. Verifieringen görs av IdentityModel- [tilläggen för .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) -biblioteket och inte av MSAL.net.

Mer information finns i [Protected Web API](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Ett webb-API som anropar ett annat webb-API för en användares räkning

För ditt ASP.NET-eller ASP.NET Core-skyddat webb-API för att anropa ett annat webb-API för en användares räkning måste appen Hämta en token för det underordnade webb-API: et. Det gör det genom att anropa **ConfidentialClientApplication** -klassens [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) -metod. Sådana anrop kallas även tjänst-till-tjänst-anrop. Webb-API: er som anropar andra webb-API: er behöver tillhandahålla anpassad cachelagring av cache.

  ![Ett webb-API som anropar ett annat webb-API](media/scenarios/web-api.svg)

Mer information finns i [webb-API som anropar webb-API: er](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>En daemon-app som anropar ett webb-API i daemon-namnet

Appar som har långvariga processer eller som fungerar utan användar interaktion behöver också ett sätt att komma åt säkra webb-API: er. En sådan app kan autentisera och hämta token genom att använda appens identitet i stället för en användares delegerade identitet. Appen visar sin identitet med hjälp av en klient hemlighet eller ett certifikat.

Du kan skriva sådana daemon-appar som hämtar en token för den anropande appen genom att använda MSAL **ConfidentialClientApplication** -klassens [förvärvs metoder för klientautentiseringsuppgifter.](https://aka.ms/msal-net-client-credentials) Dessa metoder kräver att den anropande appen har registrerat en hemlighet med Azure AD. Appen delar sedan hemligheten med den heta daemonen. Exempel på sådana hemligheter är program lösen ord, certifikat kontroll eller klient kontroll.

![En daemon-app som anropas av andra appar och API: er](media/scenarios/daemon-app.svg)

Mer information finns i [daemon-program som anropar webb-API: er](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenarier och stödda autentiserings flöden

Scenarier som innefattar att förvärva token mappas också till OAuth 2,0-autentiserings flöden, enligt beskrivningen i [protokollen för Microsoft Identity Platform](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scenario</th> <th>Detaljerad genom gång av scenario</th> <th>OAuth 2,0-flöde och-beviljande</th> <th>Målgrupp</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Enkelsidig app</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Uttrycklig</a></td>
   <td>Arbets-eller skol konton, personliga konton och Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">En webbapp som loggar in användare</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Auktoriseringskod</a></td>
   <td>Arbets-eller skol konton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">En webbapp som anropar webb-API: er</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Auktoriseringskod</a></td>
   <td>Arbets-eller skol konton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">En stationär app som anropar webb-API: er</a></td>
   <td>Interaktivt med hjälp av <a href="v2-oauth2-auth-code-flow.md">auktoriseringskod</a> med PKCE</td>
   <td>Arbets-eller skol konton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td>Integrerad Windows-autentisering</td>
   <td>Arbets- eller skolkonton</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Resurs ägar lösen ord</a></td>
   <td>Arbets-eller skol konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Enhets kod</a></td>
   <td>Arbets- eller skolkonton</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">En mobilapp som anropar webb-API: er</a></td>
   <td>Interaktivt med hjälp av <a href="v2-oauth2-auth-code-flow.md">auktoriseringskod</a> med PKCE</td>
   <td>Arbets-eller skol konton, personliga konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Resurs ägar lösen ord</a></td>
   <td>Arbets-eller skol konton och Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">En daemon-app som anropar webb-API: er</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Klientautentiseringsuppgifter</a></td>
   <td>Endast app-behörigheter utan användare och enbart används i Azure AD-organisationer</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Ett webb-API som anropar webb-API: er</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">På uppdrag av</a></td>
   <td>Arbets-eller skol konton och personliga konton</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenarier och plattformar och språk som stöds

Microsoft Authentication libraries stöder flera plattformar:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- Ursprungligt iOS
- macOS
- Ursprunglig Android
- Java
- Python

Du kan också använda olika språk för att bygga dina program. Observera att vissa program typer inte är tillgängliga på alla plattformar.

I Windows-kolumnen i följande tabell beskrivs varje tid av .NET Core .NET Framework också. Den sistnämnda filen utelämnas för att undvika att tabellen blir rörig.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Enkelsidig app](scenario-spa-overview.md) <br/>[![en app med en sida](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md) <br/>[![-webbapp som loggar in användare](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Webbapp som anropar webb-API: er](scenario-web-app-call-api-overview.md) <br/> <br/>[![-webbapp som anropar webb-API: er](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolv + MSAL python
| [Datorprogram som anropar webb-API:er](scenario-desktop-overview.md) <br/> <br/>[![Desktop-appen som anropar webb-API: er](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![enhets kod flödet](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python <br/> ![iOS/mål C eller Swift](media/sample-v2-code/small_logo_iOS.png) MSAL. objc |
| [Mobilapp som anropar webb-API: er](scenario-mobile-overview.md) <br/> [![mobilapp som anropar webb-API: er](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/mål C eller Swift](media/sample-v2-code/small_logo_iOS.png) MSAL. objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon-app](scenario-daemon-overview.md) <br/> [![daemon-app](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python
| [Webb-API som anropar webb-API: er](scenario-web-api-call-api-overview.md) <br/><br/> [![webb-API som anropar webb-API: er](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python

Se även [Microsoft-bibliotek som stöds av OS/språk](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [grundläggande autentisering](authentication-scenarios.md) och [åtkomst-token](access-tokens.md).
