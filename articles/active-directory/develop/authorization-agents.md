---
title: Auktoriserings agenter och hur du aktiverar dem | Azure
description: Lär dig mer om de olika auktoriserings agenter som Microsoft Authentication Library (MSAL) tillåter att din Android-app använder och hur du aktiverar dem.
services: active-directory
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a0e93c87c50fbc63cddad349ec9b5fbf45d91f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843911"
---
# <a name="authorization-agents-android"></a>Auktoriseringsagenter (Android)

I den här artikeln beskrivs de olika auktoriserings agenter som Microsoft Authentication Library (MSAL) tillåter att din app använder och hur de aktive ras.

Att välja en speciell strategi för auktoriserings agenter är valfritt och representerar ytterligare funktioner som kan anpassas. De flesta appar använder MSAL-standardvärdena (se [förstå konfigurations filen för Android-MSAL](msal-configuration.md) för att se de olika standardvärdena).

MSAL stöder auktorisering med hjälp av en `WebView`eller systemets webbläsare.  Bilden nedan visar hur den ser ut med hjälp av `WebView`, eller system läsaren med CustomTabs eller utan CustomTabs:

![Exempel på MSAL-inloggning](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Konsekvenser för enkel inloggning

Som standard använder program som är integrerade med MSAL de anpassade flikarna i system webbläsare för att godkänna. Till skillnad från webbvyer delar de anpassade flikarna med standard system webbläsaren och gör färre inloggningar med webb-eller andra inbyggda appar som är integrerade med anpassade flikar.

Om programmet använder en `WebView` strategi utan att integrera Microsoft Authenticator eller Företagsportal stöd i appen, kommer användarna inte att ha någon enkel inloggnings upplevelse på enheten eller mellan inbyggda appar och webbappar.

Om programmet använder MSAL med Microsoft Authenticator-eller Företagsportal-stöd kan användarna ha en SSO-upplevelse över program om användaren har en aktiv inloggning med en av apparna.

## <a name="webview"></a>WebView

Om du vill använda webbvy i appen, Lägg följande rad i JSON-appens konfiguration som skickas till MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

När du använder `WebView`i appen loggar användaren in direkt till appen. Token behålls inuti appens Sand låda och är inte tillgängliga utanför appens jar-jar. Det innebär att användaren inte kan ha en SSO-upplevelse över program om inte apparna integreras med autentiseraren eller Företagsportal.

`WebView` ger dock möjlighet att anpassa utseendet och känslan för inloggnings gränssnittet. Mer information om hur du gör denna anpassning finns i avsnittet om [Android-webbvyer](https://developer.android.com/reference/android/webkit/WebView) .

## <a name="default-browser-plus-custom-tabs"></a>Standard webbläsare plus anpassade flikar

Som standard använder MSAL webbläsaren och en strategi för [anpassade flikar](https://developer.chrome.com/multidevice/android/customtabs) . Du kan uttryckligen ange den här strategin för att förhindra ändringar i framtida versioner till `DEFAULT` genom att använda följande JSON-konfiguration i den anpassade konfigurations filen:

```json
"authorization_user_agent" : "BROWSER"
```

Använd den här metoden för att tillhandahålla enkel inloggning via enhetens webbläsare. MSAL använder en gemensam cookie-jar, som gör att andra inbyggda appar eller webbappar kan nå SSO på enheten med hjälp av den sparade sessions-cookien som anges av MSAL.

## <a name="browser-selection-heuristic"></a>Heuristiska val av webbläsare

Eftersom det är omöjligt för MSAL att ange det exakta webb läsar paket som ska användas på var och en av de breda Android-telefoner, implementerar MSAL en heuristik för val av webbläsare som försöker tillhandahålla det bästa SSO-värdet mellan enheter.

MSAL hämtar den fullständiga listan över webbläsare som är installerade på enheten för att välja vilken webbläsare som ska användas. Listan är i den ordning som returneras av paket hanteraren, som indirekt återspeglar användarens inställningar. Till exempel är standard webbläsaren, om den är inställd, den första posten i listan. Den _första_ webbläsaren i listan kommer att väljas oavsett om den stöder anpassade flikar eller inte. Om webbläsaren har stöd för anpassade flikar, kommer MSAL att starta den anpassade fliken. anpassade flikar har en känsla som är närmare för en app `WebView` och ger grundläggande anpassning av gränssnittet. Mer information finns i [anpassade flikar i Android](https://developer.chrome.com/multidevice/android/customtabs) .

Om det inte finns några webb läsar paket på enheten, använder MSAL `WebView`i appen.

Ordningen på webbläsare i webbläsarens lista bestäms av operativ systemet. Den är i ordning från mest prioriterad till minst. Om enhetens standardinställning inte ändras ska samma webbläsare startas för varje inloggning för att säkerställa en SSO-upplevelse.

> [!NOTE]
> MSAL föredrar inte längre krom om en annan webbläsare är inställd som standard. Till exempel, på en enhet som har både Chrome och en annan webbläsare förinstallerad, kommer MSAL att använda webbläsaren som användaren har angett som standard.

### <a name="tested-browsers"></a>Testade webbläsare

Följande webbläsare har testats för att se om de har omdirigerats korrekt till `"redirect_uri"` som anges i konfigurations filen:

| | Inbyggd webbläsare | Chrome | Opera  | Microsoft Edge | UC-webbläsare | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | Pass | Pass |ej tillämpligt |ej tillämpligt |ej tillämpligt |ej tillämpligt |
| Samsung S7 (API 25) | pass | Pass | Pass | Pass | misslyckas |Pass |
| Huawei (API 26) |pass * * | Pass | misslyckas | Pass | Pass |Pass |
| Vivo (API 26) |Pass|Pass|Pass|Pass|Pass|misslyckas|
| Pixel 2 (API 26) |Pass | Pass | Pass | Pass | misslyckas |Pass |
| OPPO | Pass | inte tillämpligt * * * |ej tillämpligt  |ej tillämpligt |ej tillämpligt | ej tillämpligt|
| OnePlus (API 25) |Pass | Pass | Pass | Pass | misslyckas |Pass |
| Nexus (API 28) |Pass | Pass | Pass | Pass | misslyckas |Pass |
|MI | Pass | Pass | Pass | Pass | misslyckas |Pass |

\* Samsung: s inbyggda webbläsare är Samsung Internet.  
\* * Huawei-inbyggda webbläsare är Huawei webbläsare.  
Det går inte att ändra standard webbläsaren i enhets inställningen OPPO.
