---
title: Skydda appar med en enda sida med ett implicit flöde för Microsoft Identity Platform | Azure
description: Skapa webb program med hjälp av Microsoft Identity Platform-implementering av det implicita flödet för appar på en sida.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8afae9535c190c05bca3153dfbe5279cd4c47968
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919230"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity Platform och implicit beviljande flöde

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Med Microsoft Identity Platform-slutpunkten kan du logga användare i appar med en sida med både personliga konton och arbets-eller skol konton från Microsoft. En enkel sida och andra JavaScript-appar som körs främst i en webbläsare och har några intressanta utmaningar när de kommer till autentisering:

* Säkerhetsegenskaperna för dessa appar skiljer sig avsevärt från traditionella serverbaserade webb program.
* Många auktoriseringsregler och identitets leverantörer stöder inte CORS-begäranden.
* Full sid webbläsare omdirigeras bort från appen, vilket är särskilt invasivt i användar upplevelsen.

För dessa program (AngularJS, maskininlärning. js, reagerar. js osv.) stöder Microsoft Identity Platform det OAuth-för implicit beviljande av OAuth-2,0. Det implicita flödet beskrivs i [OAuth 2,0-specifikationen](https://tools.ietf.org/html/rfc6749#section-4.2). Den främsta fördelen är att den tillåter att appen hämtar token från Microsoft Identity Platform utan att utföra ett utbyte av autentiseringsuppgifter för backend-servern. Detta gör att appen kan logga in användaren, underhålla sessionen och hämta token till andra webb-API: er i klientens JavaScript-kod. Det finns några viktiga säkerhets aspekter att ta med i beräkningen när du använder det implicita flödet specifikt kring [klient](https://tools.ietf.org/html/rfc6749#section-10.3) -och [användarautentisering](https://tools.ietf.org/html/rfc6749#section-10.3).

Den här artikeln beskriver hur du programmerar direkt mot protokollet i ditt program.  När det är möjligt rekommenderar vi att du använder MSAL (Microsoft Authentication Libraries) i stället för att [Hämta tokens och anropa säkra webb-API: er](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Ta också en titt på de [exempel appar som använder MSAL](sample-v2-code.md).

Om du däremot hellre inte vill använda ett bibliotek i en app med en enda sida och skicka protokoll meddelanden själv, följer du de allmänna stegen nedan.

> [!NOTE]
> Inte alla Azure Active Directory (Azure AD)-scenarier och-funktioner stöds av Microsoft Identity Platform-slutpunkten. För att avgöra om du ska använda Microsoft Identity Platform-slutpunkten läser du om [begränsningar för Microsoft Identity Platform](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Protokoll diagram

Följande diagram visar hur hela det implicita inloggnings flödet ser ut och de avsnitt som följer beskriver varje steg i detalj.

![Diagram som visar det implicita inloggnings flödet](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Skicka inloggnings förfrågan

För att först logga in användaren i din app kan du skicka en [OpenID Connect](v2-protocols-oidc.md) -autentiseringsbegäran och få en `id_token` från slut punkten för Microsoft Identity Platform.

> [!IMPORTANT]
> För att kunna begära en ID-token och/eller en åtkomsttoken måste registrerings flödet på sidan [Azure Portal-Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) ha motsvarande implicit tilldelnings flöde aktiverat, genom att välja **ID-token** och. **eller åtkomsttoken** i avsnittet **implicit beviljande** . Om den inte är aktive rad returneras ett `unsupported_response` fel: **det angivna värdet för indataparametern response_type tillåts inte för den här klienten. Förväntat värde är ' Code '**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Om du vill testa att logga in med det implicita flödet klickar du på <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> När du har loggat in bör webbläsaren omdirigeras till `https://localhost/myapp/` med ett `id_token` i adress fältet.
>

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `tenant` | obligatorisk |`{tenant}`-värdet i sökvägen till begäran kan användas för att kontrol lera vem som kan logga in på programmet. De tillåtna värdena är `common`, `organizations`, `consumers`och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obligatorisk | Det program (klient)-ID som den [Azure Portal-Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan har tilldelats till din app. |
| `response_type` | obligatorisk |Måste innehålla `id_token` för OpenID Connect-inloggning. Den kan även innehålla response_type `token`. Med hjälp av `token` här kan din app ta emot en åtkomsttoken omedelbart från den auktoriserade slut punkten utan att behöva göra en andra begäran till auktoriserad slut punkt. Om du använder `token` response_type måste parametern `scope` innehålla ett omfång som anger vilken resurs som ska utfärda token för (till exempel User. read på Microsoft Graph).  |
| `redirect_uri` | rekommenderas |Appens redirect_uri, där autentiserings svar kan skickas och tas emot av din app. Det måste exakt matcha ett av de redirect_uris som du registrerade i portalen, förutom att det måste vara URL-kodat. |
| `scope` | obligatorisk |En blankstegsavgränsad lista över [omfång](v2-permissions-and-consent.md). För OpenID Connect (id_tokens) måste den innehålla omfånget `openid`, vilket översätts till behörigheten "logga in dig" i medgivande gränssnittet. Du kanske också vill inkludera `email` och `profile` omfattningar för att få åtkomst till ytterligare användar data. Du kan också inkludera andra omfattningar i denna begäran om att begära medgivande till olika resurser, om en åtkomsttoken begärs. |
| `response_mode` | valfri |Anger den metod som ska användas för att skicka den resulterande token tillbaka till din app. Standardvärdet söker efter en åtkomsttoken, men fragment om begäran innehåller en id_token. |
| `state` | rekommenderas |Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med innehåll som du vill. Ett slumpmässigt genererat unikt värde används vanligt vis för [att förhindra förfalsknings attacker på begäran](https://tools.ietf.org/html/rfc6749#section-10.12)från en annan plats. Statusen används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
| `nonce` | obligatorisk |Ett värde som ingår i begäran, som genereras av appen, som kommer att ingå i den resulterande id_token som ett anspråk. Appen kan sedan verifiera det här värdet för att minimera omuppspelning av token. Värdet är vanligt vis en slumpmässig, unik sträng som kan användas för att identifiera ursprunget för begäran. Krävs endast när en id_token begärs. |
| `prompt` | valfri |Anger vilken typ av användar interaktion som krävs. De enda giltiga värdena för tillfället är ' inloggning "', ' none ', ' select_account ' och ' medgivande '. `prompt=login` tvingar användaren att ange sina autentiseringsuppgifter för den begäran, vilket negerar enkel inloggning. `prompt=none` är motsatt, ser du till att användaren inte visas med någon interaktiv prompt alls. Om begäran inte kan slutföras i bakgrunden via enkel inloggning, returnerar slut punkten för Microsoft Identity Platform ett fel. `prompt=select_account` skickar användaren till en konto väljare där alla konton som är lagrade i sessionen visas. `prompt=consent` utlöser dialog rutan OAuth-medgivande när användaren loggar in och ber användaren att bevilja behörighet till appen. |
| `login_hint`  |valfri |Kan användas för att fylla i fältet användar namn/e-postadress på inloggnings sidan för användaren, om du känner till användar namnet i förväg. Appar använder ofta den här parametern under omautentiseringen och har redan extraherat användar namnet från en tidigare inloggning med `preferred_username`-anspråket.|
| `domain_hint` | valfri |Om den är inkluderad hoppar den e-postbaserad identifierings processen som användaren går igenom på inloggnings sidan, vilket leder till en något mer strömlinjeformad användar upplevelse. Detta används ofta för branschspecifika appar som fungerar i en enda klient, där de kommer att tillhandahålla ett domän namn inom en viss klient.  Detta vidarebefordrar användaren till Federations leverantören för den klienten.  Observera att detta hindrar gästerna från att logga in på det här programmet.  |

Användaren uppmanas att ange sina autentiseringsuppgifter och slutföra autentiseringen. Slut punkten för Microsoft Identity Platform ser också till att användaren har samtyckt till de behörigheter som anges i parametern `scope` fråga. Om användaren har samtyckt till **ingen** av dessa behörigheter uppmanas användaren att godkänna de behörigheter som krävs. Mer information finns i [behörigheter, medgivande och appar för flera klient organisationer](v2-permissions-and-consent.md).

När användaren autentiserar och godkänner godkännandet, returnerar Microsoft Identity Platform-slutpunkten ett svar till din app vid den angivna `redirect_uri`med hjälp av metoden som anges i parametern `response_mode`.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar med `response_mode=fragment` och `response_type=id_token+token` ser ut ungefär så här (med rad brytningar för läsbarhet):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beskrivning |
| --- | --- |
| `access_token` |Inkludera om `response_type` innehåller `token`. Den åtkomsttoken som appen begärde. Åtkomsttoken bör inte avkodas eller på annat sätt kontrol leras. den bör behandlas som en ogenomskinlig sträng. |
| `token_type` |Inkludera om `response_type` innehåller `token`. Kommer alltid att vara `Bearer`. |
| `expires_in`|Inkludera om `response_type` innehåller `token`. Anger antalet sekunder som token är giltig för cachelagring. |
| `scope` |Inkludera om `response_type` innehåller `token`. Anger omfånget som access_token ska gälla för. Får inte innehålla alla begärda omfattningar, om de inte gällde för användaren (endast för Azure AD-scope som begärs när ett personligt konto används för att logga in). |
| `id_token` | En signerad JSON Web Token (JWT). Appen kan avkoda segmenten i denna token för att begära information om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men de bör inte förlita sig på några begränsningar för auktorisering eller säkerhet. Mer information om id_tokens finns i [`id_token reference`](id-tokens.md). <br> **Obs:** Anges endast om `openid` omfattning begärdes. |
| `state` |Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

#### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till `redirect_uri` så att appen kan hantera dem på rätt sätt:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivning |
| --- | --- |
| `error` |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` |Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Hämta åtkomsttoken tyst i bakgrunden

Nu när du har loggat in användaren i en app med en enda sida kan du hämta åtkomsttoken för att anropa webb-API: er som skyddas av Microsoft Identity Platform, t. ex. [Microsoft Graph](https://developer.microsoft.com/graph). Även om du redan har tagit emot en token med `token` response_type kan du använda den här metoden för att hämta tokens till ytterligare resurser utan att behöva omdirigera användaren för att logga in igen.

I det normala OpenID Connect/OAuth-flödet gör du detta genom att göra en begäran till Microsoft Identity Platform `/token`-slutpunkten. Slut punkten för Microsoft Identity Platform stöder dock inte CORS-begäranden, så gör AJAX-anrop för att hämta och uppdatera token utanför frågan. I stället kan du använda det implicita flödet i en dold iframe för att hämta nya token för andra webb-API: er: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Information om frågeparametrar i URL: en finns i [Skicka inloggnings förfrågan](#send-the-sign-in-request).

> [!TIP]
> Försök kopiera & att klistra in nedanstående begäran i en webbläsare-flik! (Glöm inte att ersätta `login_hint` värden med rätt värde för din användare)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Tack vare `prompt=none`-parametern kommer den här begäran antingen att lyckas eller Miss lyckas omedelbart och återgå till ditt program. Ett lyckat svar skickas till din app vid den angivna `redirect_uri`med hjälp av den metod som anges i parametern `response_mode`.

#### <a name="successful-response"></a>Lyckat svar

Ett lyckat svar med `response_mode=fragment` ser ut så här:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parameter | Beskrivning |
| --- | --- |
| `access_token` |Inkludera om `response_type` innehåller `token`. Den åtkomsttoken som appen begärde, i det här fallet för Microsoft Graph. Åtkomsttoken bör inte avkodas eller på annat sätt kontrol leras. den bör behandlas som en ogenomskinlig sträng. |
| `token_type` | Kommer alltid att vara `Bearer`. |
| `expires_in` | Anger antalet sekunder som token är giltig för cachelagring. |
| `scope` | Anger omfånget som access_token ska gälla för. Får inte innehålla alla begärda omfattningar, om de inte gällde för användaren (endast för Azure AD-scope som begärs när ett personligt konto används för att logga in). |
| `id_token` | En signerad JSON Web Token (JWT). Inkludera om `response_type` innehåller `id_token`. Appen kan avkoda segmenten i denna token för att begära information om den användare som har loggat in. Appen kan cachelagra värdena och visa dem, men de bör inte förlita sig på några begränsningar för auktorisering eller säkerhet. Mer information om id_tokens finns i [`id_token` referens](id-tokens.md). <br> **Obs:** Anges endast om `openid` omfattning begärdes. |
| `state` |Om en tillstånds parameter ingår i begäran ska samma värde visas i svaret. Appen bör kontrol lera att tillstånds värden i begäran och svaret är identiska. |

#### <a name="error-response"></a>Fel svar

Fel svar kan också skickas till `redirect_uri` så att appen kan hantera dem på rätt sätt. Om `prompt=none`visas ett förväntat fel:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivning |
| --- | --- |
| `error` |En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` |Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. |

Om du får det här felet i iframe-begäran måste användaren interaktivt logga in igen för att hämta en ny token. Du kan välja att hantera det här fallet på det sätt som passar ditt program.

## <a name="refreshing-tokens"></a>Uppdatera token

Den implicita beviljandet tillhandahåller inte uppdaterade tokens. Både `id_token`s och `access_token`s upphör att gälla efter en kort tids period, så att din app måste vara för beredd för att uppdatera dessa token regelbundet. Om du vill uppdatera någon av typerna av token kan du utföra samma dolda iframe-begäran från ovan med hjälp av parametern `prompt=none` för att styra identitets plattformens beteende. Om du vill ta emot en ny `id_token`måste du använda `id_token` i `response_type` och `scope=openid`, samt en `nonce`-parameter.

## <a name="send-a-sign-out-request"></a>Skicka en begäran om utloggning

OpenID Connect `end_session_endpoint` gör att din app kan skicka en begäran till Microsoft Identity Platform-slutpunkten för att avsluta en användares session och Rensa cookies som anges av Microsoft Identity Platform-slutpunkten. För att fullständigt signera en användare från ett webb program bör din app avsluta sin egen session med användaren (vanligt vis genom att rensa en token-cache eller släppa cookies) och sedan omdirigera webbläsaren till:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parameter |  | Beskrivning |
| --- | --- | --- |
| `tenant` |obligatorisk |`{tenant}`-värdet i sökvägen till begäran kan användas för att kontrol lera vem som kan logga in på programmet. De tillåtna värdena är `common`, `organizations`, `consumers`och klient-ID: n. Mer information finns i [grunderna om protokoll](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | rekommenderas | URL: en som användaren ska returneras till när utloggning har slutförts. Värdet måste matcha en av de omdirigerings-URI: er som registrerats för programmet. Om detta inte anges visas ett allmänt meddelande för användaren av Microsoft Identity Platform-slutpunkten. |

## <a name="next-steps"></a>Nästa steg

* Gå över [MSAL JS-exemplen](sample-v2-code.md) för att komma igång med kodning.
