---
title: Sök efter användar namn under inloggning Azure Active Directory | Microsoft Docs
description: Hur meddelanden på skärmen visar användar namns sökning under inloggning i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024258"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Identifiering av start sfär för Azure Active Directory inloggnings sidor

Vi ändrar inloggningen i Azure Active Directory (Azure AD) för att göra rum för nya autentiseringsmetoder och förbättra användningen. Under inloggningen avgör Azure AD var en användare ska autentiseras. Azure AD fattar intelligenta beslut genom att läsa av organisations- och användarinställningar för det användarnamn som angavs på inloggningssidan. Det här är ett steg mot en lösenordsfri framtid som öppnar för andra autentiseringsuppgifter som FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Identifierings beteende för start sfär

Tidigare var identifiering av start sfär styrd av den domän som anges vid inloggning eller av en identifierings princip för start sfär för vissa äldre program. I vårt identifierings beteende kan en Azure Active Directory användare felaktigt ange sitt användar namn, men det kommer fortfarande att hamna på sin organisations skärm för autentiseringsuppgifter samling. Detta inträffar när användaren korrekt tillhandahåller organisationens domän namn "contoso.com". Det här beteendet tillåter inte detaljnivån att anpassa upplevelsen för enskilda användare.

För att ge stöd för ett bredare antal autentiseringsuppgifter och öka användbarheten, kan Azure Active Directory Sök efter användar namns ökningen under inloggnings processen uppdateras nu. Det nya beteendet gör intelligenta beslut genom att läsa inställningar för klient-och användar nivå baserat på användar namnet som angetts på inloggnings sidan. För att göra detta möjligt kontrollerar Azure Active Directory för att se om det användar namn som har angetts på inloggnings sidan finns i den angivna domänen eller omdirigerar användaren att ange sina autentiseringsuppgifter.

En ytterligare fördel med detta arbete är förbättrad fel meddelande tjänst. Här följer några exempel på de förbättrade fel meddelandena vid inloggning till ett program som endast stöder Azure Active Directory användare.

- Användar namnet är felaktigt eller också har användar namnet ännu inte synkroniserats med Azure AD:
  
    ![användar namnet är felaktigt eller har inte hittats](./media/signin-realm-discovery/typo-username.png)
  
- Domän namnet är felskrivet:
  
    ![domän namnet är felaktigt eller kan inte hittas](./media/signin-realm-discovery/typo-domain.png)
  
- Användare försöker logga in med en känd konsument domän:
  
    ![Logga in med en känd konsument domän](./media/signin-realm-discovery/consumer-domain.png)
  
- Lösen ordet har fel typ, men användar namnet är korrekt:  
  
    ![lösen ordet är felskrivet med ett korrekt användar namn](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Den här funktionen kan ha påverkan på federerade domäner som förlitar sig på den gamla identifieringen av start sfären på domän nivå för att tvinga Federation. Uppdateringar på när federerade domän stöd kommer att läggas till finns i [Start sfär identifiering under inloggning för Microsoft 365-tjänster](https://azure.microsoft.com/updates/signin-hrd/). Under tiden har vissa organisationer tränat sina anställda att logga in med ett användar namn som inte finns i Azure Active Directory men som innehåller rätt domän namn, eftersom domän namnen dirigerar användare för närvarande till organisationens domän slut punkt. Det nya inloggnings beteendet tillåter inte detta. Användaren meddelas att korrigera användar namnet och de får inte logga in med ett användar namn som inte finns i Azure Active Directory.
>
> Om du eller din organisation har metoder som är beroende av det gamla beteendet, är det viktigt för organisations administratörer att uppdatera medarbetarnas inloggnings-och autentiserings dokumentation och för att träna anställda att använda sina Azure Active Directory användar namn för att logga in.
  
Om du har problem med det nya beteendet lämnar du dina kommentarer i avsnittet **feedback** i den här artikeln.  

## <a name="next-steps"></a>Nästa steg

[Anpassa din inloggnings anpassning](../fundamentals/add-custom-domain.md)
