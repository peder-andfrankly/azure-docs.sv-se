---
title: Blockera äldre autentisering – Azure Active Directory
description: Lär dig hur du kan förbättra din säkerhets position genom att blockera äldre autentisering med villkorlig åtkomst i Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a65145fe9752a90e3328c308ce603c8626d8708
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380867"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Gör så här: blockera äldre autentisering till Azure AD med villkorlig åtkomst   

Azure Active Directory (Azure AD) stöder en mängd olika autentiseringsprotokoll, inklusive äldre autentisering, för att ge dina användare enkel åtkomst till dina molnappar. Äldre protokoll stöder dock inte Multi-Factor Authentication (MFA). MFA är i många miljöer ett gemensamt krav för att lösa identitets stölder. 

Om din miljö är redo att blockera äldre autentisering för att förbättra din klients skydd kan du uppnå det här målet med villkorlig åtkomst. Den här artikeln förklarar hur du kan konfigurera principer för villkorlig åtkomst som blockerar äldre autentisering för din klient.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du är bekant med: 

- [Grundläggande begrepp](overview.md) för villkorlig åtkomst i Azure AD 
- [Metod tips](best-practices.md) för att konfigurera principer för villkorlig åtkomst i Azure Portal

## <a name="scenario-description"></a>Scenariobeskrivning

Azure AD stöder flera av de vanligaste autentiseringsprotokollen för autentisering och auktorisering, inklusive äldre autentisering. Äldre autentisering syftar på protokoll som använder grundläggande autentisering. Dessa protokoll kan vanligt vis inte tillämpa någon typ av andra Factor Authentication. Exempel på appar som baseras på äldre autentisering är:

- Äldre Microsoft Office-appar
- Appar som använder e-postprotokoll som POP, IMAP och SMTP

Autentisering med en enda faktor (till exempel användar namn och lösen ord) är inte tillräckligt för dessa dagar. Lösen orden är dåliga eftersom de är lätta att gissa och vi (människa) är felaktiga vid val av bra lösen ord. Lösen ord är också sårbara för olika attacker, t. ex. nätfiske och lösen ords spridning. Ett av de enklaste saker du kan göra för att skydda mot lösen ords hot är att implementera MFA. Med MFA, även om en angripare får till gång till en användares lösen ord, så är lösen ordet inte tillräckligt för att autentisera och komma åt data.

Hur kan du förhindra att appar använder äldre autentisering för att komma åt din klients resurser? Rekommendationen är att bara blockera dem med en princip för villkorlig åtkomst. Om det behövs kan endast vissa användare och specifika nätverks platser använda appar som baseras på äldre autentisering.

Principer för villkorlig åtkomst tillämpas när den första faktorn har slutförts. Därför är villkorlig åtkomst inte avsedd som ett första rad skydd för scenarier som denial-of-service (DoS)-attacker, men kan använda signaler från dessa händelser (till exempel inloggnings risk nivån, platsen för begäran och så vidare) för att fastställa åtkomst.

## <a name="implementation"></a>Implementering

I det här avsnittet beskrivs hur du konfigurerar en princip för villkorlig åtkomst för att blockera äldre autentisering. 

### <a name="identify-legacy-authentication-use"></a>Identifiera användning av äldre autentisering

Innan du kan blockera äldre autentisering i din katalog måste du först förstå om dina användare har appar som använder äldre autentisering och hur de påverkar den övergripande katalogen. Inloggnings loggar för Azure AD kan användas för att förstå om du använder äldre autentisering.

1. Gå till **Azure Portal** > **Azure Active Directory** > **inloggningar**.
1. Lägg till kolumnen klient program om den inte visas genom att klicka på **kolumner** > - **klient program**.
1. **Lägg till filter** > **klient program** > Välj alla alternativ för **andra klienter** och klicka på **Använd**.

Vid filtrering visas bara inloggnings försök som gjorts av äldre autentiseringsprotokoll. Om du klickar på varje enskilt inloggnings försök visas ytterligare information. Fältet **klient app** på fliken **grundläggande information** visar vilket äldre autentiseringsprotokoll som användes.

I dessa loggar anges vilka användare som fortfarande är beroende av tidigare autentisering och vilka program som använder äldre protokoll för att göra autentiseringsbegäranden. För användare som inte visas i dessa loggar och som bekräftas att inte använda äldre autentisering, implementera endast en princip för villkorlig åtkomst för dessa användare.

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering 

I en princip för villkorlig åtkomst kan du ange ett villkor som är kopplat till de klient program som används för att komma åt dina resurser. Med villkoret klient program kan du begränsa omfattningen till appar med hjälp av äldre autentisering genom att välja **andra klienter** för **mobilappar och skriv bords klienter**.

![Övriga klienter](./media/block-legacy-authentication/01.png)

Om du vill blockera åtkomst för de här apparna måste du välja **blockera åtkomst**.

![Blockera åtkomst](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Välj användare och molnappar

Om du vill blockera äldre autentisering för din organisation tror du förmodligen att du kan åstadkomma detta genom att välja:

- Alla användare
- Alla molnappar
- Blockera åtkomst

![Tilldelningar](./media/block-legacy-authentication/03.png)

Azure har en säkerhetsfunktion som hindrar dig från att skapa en princip som detta eftersom den här konfigurationen strider mot [bästa praxis](best-practices.md) för principer för villkorlig åtkomst.
 
![Princip konfigurationen stöds inte](./media/block-legacy-authentication/04.png)

Säkerhets funktionen är nödvändig eftersom *Blockera alla användare och alla molnappar* har möjlighet att blockera hela organisationen från att logga in på din klient organisation. Du måste undanta minst en användare för att uppfylla kraven för minsta bästa praxis. Du kan också utesluta en katalog roll.

![Princip konfigurationen stöds inte](./media/block-legacy-authentication/05.png)

Du kan utföra den här säkerhetsfunktionen genom att utesluta en användare från principen. Vi rekommenderar att du definierar några [administrativa konton för nöd administration i Azure AD](../users-groups-roles/directory-emergency-access.md) och exkluderar dem från principen.

## <a name="policy-deployment"></a>Princip distribution

Innan du försätter din princip i produktion bör du ta hand om följande:
 
- **Tjänst konton** – identifiera användar konton som används som tjänst konton eller enheter, t. ex. konferens rums telefoner. Kontrol lera att dessa konton har starka lösen ord och Lägg till dem i en exkluderad grupp.
- **Inloggnings rapporter** – granska inloggnings rapporten och leta efter **annan klient** trafik. Identifiera högsta användning och undersök varför den används. Normalt genereras trafiken av äldre Office-klienter som inte använder modern autentisering eller vissa e-postappar från tredje part. Gör en plan för att flytta användning bort från dessa appar, eller om påverkan är låg, meddela användarna att de inte kan använda dessa appar längre.
 
Mer information finns i [så här distribuerar du en ny princip?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Det här bör du känna till

Att blockera åtkomst med **andra klienter** blockerar också Exchange Online PowerShell och Dynamics 365 med Basic auth.

Att konfigurera en princip för **andra klienter** blockerar hela organisationen från vissa klienter som SPConnect. Det här blocket inträffar eftersom äldre klienter autentiseras på oväntade sätt. Problemet gäller inte för större Office-program som äldre Office-klienter.

Det kan ta upp till 24 timmar innan principen börjar gälla.

Du kan välja alla tillgängliga beviljade kontroller för villkoret **andra klienter** . slut användar upplevelsen är dock alltid samma blockerad åtkomst.

Om du blockerar äldre autentisering med hjälp av **andra klient** villkor kan du även ange enhetens plattform och plats villkor. Om du till exempel bara vill blockera äldre autentisering för mobila enheter anger du villkor för **enhets plattformar** genom att välja:

- Android
- iOS
- Windows Phone

![Princip konfigurationen stöds inte](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Nästa steg

- Om du inte är bekant med att konfigurera principer för villkorlig åtkomst ännu kan du läsa [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md) för ett exempel.
- Mer information om stöd för modern autentisering finns i [så här fungerar modern autentisering för office 2013 och office 2016-klient program](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
