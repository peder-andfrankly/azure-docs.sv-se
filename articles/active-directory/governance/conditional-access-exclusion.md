---
title: Använd åtkomst granskningar för att hantera användare som uteslutits från principer för villkorlig åtkomst – Azure Active Directory | Microsoft Docs
description: Lär dig hur du använder Azure Active Directory (Azure AD) åtkomst granskningar för att hantera användare som har uteslutits från principer för villkorlig åtkomst
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 246503fec6436bf49dcd5fb89c2dc0ed345a43ca
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499921"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Använd åtkomst granskningar i Azure AD för att hantera användare som uteslutits från principer för villkorlig åtkomst

I en idealisk värld följer alla användare åtkomst principerna för att skydda åtkomsten till organisationens resurser. Ibland finns det dock affärs fall där du behöver göra undantag. I den här artikeln beskrivs några exempel där undantag kan krävas och hur du, som IT-administratör, kan hantera den här uppgiften, Undvik överblick av princip undantag och tillhandahålla granskare med bevis på att dessa undantag granskas regelbundet med hjälp av Azure Åtkomst granskningar för Active Directory (Azure AD).

> [!NOTE]
> En giltig Azure AD Premium P2, Enterprise Mobility + Security E5 betald eller utvärderings licens krävs för att använda åtkomst granskningar för Azure AD. Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Varför skulle du undanta användare från principer?

Som IT-administratör kan du använda [villkorlig åtkomst för Azure AD](../conditional-access/overview.md) för att kräva att användare autentiserar med Multi-Factor Authentication (MFA) eller loggar in från ett betrott nätverk eller en enhet. Under distributions planeringen får du reda på att vissa av dessa krav inte uppfylls av alla användare. Det finns till exempel användare som arbetar från ett fjärran slutet kontor som inte ingår i det interna nätverket eller så är det en chef som använder en gammal telefon som inte stöds. Företaget kräver att dessa användare får logga in och göra sitt jobb, vilket innebär att de är undantagna från principerna för villkorlig åtkomst.

Ett annat exempel är om du använder [namngivna platser](../conditional-access/location-condition.md) i villkorlig åtkomst för att konfigurera en uppsättning regioner och regioner som du inte vill att användarna ska ha åtkomst till av klienten.

![Namngivna platser i villkorlig åtkomst](./media/conditional-access-exclusion/named-locations.png)

Men i vissa fall kan användarna ha en legitim anledning att logga in från dessa blockerade länder/regioner. Till exempel kanske användarna reser i arbetet eller personliga skäl. I det här exemplet kan den villkorliga åtkomst principen för att blockera dessa länder/regioner ha en särskild moln säkerhets grupp för de användare som är exkluderade från principen. Användare som behöver åtkomst under resor kan lägga till sig själva i gruppen med hjälp av [Azure AD-](../users-groups-roles/groups-self-service-management.md)självbetjänings grupp hantering.

Ett annat exempel är att du har en princip för villkorlig åtkomst som [blockerar äldre autentisering för de flesta användare](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Microsoft rekommenderar starkt att du blockerar användningen av äldre protokoll i din klient organisation för att förbättra din säkerhets position. Men om du har vissa användare som absolut behöver använda äldre autentiseringsmetoder för att komma åt dina resurser via Office 2010 eller IMAP/SMTP/POP-baserade klienter, kan du undanta dessa användare från principen som blockerar äldre autentiseringsmetoder.

## <a name="why-are-exclusions-challenging"></a>Varför är undantagen utmanande?

I Azure AD kan du begränsa en princip för villkorlig åtkomst till en uppsättning användare. Du kan också utesluta vissa av dessa användare genom att välja Azure AD-roller, enskilda användare eller användare av gäster. Det är viktigt att komma ihåg att när dessa undantag har kon figurer ATS kan inte princip avsikten tillämpas för dessa användare. Om dessa undantag har kon figurer ATS som en lista över enskilda användare eller via en äldre lokal säkerhets grupp, begränsar den synligheten för den här undantags listan (användarna kanske inte vet om det finns) och IT-administratörens kontroll över den (användarna kan ansluta till säkerhets grupp för genom strömning av principen). Dessutom kanske användare som har kvalificerat sig för undantag på en gång inte längre behöver den eller vara berättigad till det.

I början av ett undantag finns en kort lista med användare som kringgår principen. Över tid utesluts fler och fler användare och listan växer. Nu behöver du gå igenom listan och bekräfta att var och en av dessa användare fortfarande ska uteslutas. Att hantera listan från en teknisk vy, kan vara relativt enkelt, men som fattar affärs beslut och hur ser du till att det är alla gransknings bara?

Men om du konfigurerar uteslutningen till principen för villkorlig åtkomst med hjälp av en Azure AD-grupp, kan du använda åtkomst granskningar som en kompensations kontroll, för att öka synlighet och minska antalet användare som har ett undantag.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Så här skapar du en undantags grupp i en princip för villkorlig åtkomst

Följ dessa steg om du vill skapa en ny Azure AD-grupp och en princip för villkorlig åtkomst som inte gäller för den gruppen.

### <a name="create-an-exclusion-group"></a>Skapa en undantags grupp

1. Logga in på Azure Portal.

1. Klicka på **Azure Active Directory** i det vänstra navigerings fönstret och klicka sedan på **grupper**.

1. Öppna grupp fönstret genom att klicka på **ny grupp** på den översta menyn.

1. Välj **säkerhet**i listan **typ av grupp** . Ange ett namn och en beskrivning.

1. Se till att ange **medlemskaps** typen som tilldelad.

1. Välj de användare som ska ingå i den här undantags gruppen och klicka sedan på **skapa**.

    ![Fönstret ny grupp i Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Skapa en princip för villkorlig åtkomst som utesluter gruppen

Nu kan du skapa en princip för villkorlig åtkomst som använder den här undantags gruppen.

1. Klicka på **Azure Active Directory** i det vänstra navigerings fönstret och klicka sedan på **villkorlig åtkomst** för att öppna bladet **principer** .

1. Klicka på **ny princip** för att öppna det **nya** fönstret.

1. Ange ett namn.

1. Under tilldelningar klickar **du på användare och grupper**.

1. På fliken **Inkludera** väljer du **alla användare**.

1. På fliken **exkludera** lägger du till en bock till **användare och grupper** och klickar sedan på **Välj exkluderade användare**.

1. Välj den undantags grupp som du skapade.

    > [!NOTE]
    > Som bästa praxis rekommenderar vi att du undantar minst ett administratörs konto från principen när du testar för att se till att du inte är utelåst från din klient.

1. Fortsätt med att konfigurera principen för villkorlig åtkomst baserat på organisationens krav.

    ![Välj rutan undantagna användare i villkorlig åtkomst](./media/conditional-access-exclusion/select-excluded-users.png)

Vi ska gå igenom två exempel där du kan använda åtkomst granskningar för att hantera undantag i principer för villkorlig åtkomst.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exempel 1: Åtkomst granskning för användare som kommer åt från blockerade länder/regioner

Anta att du har en princip för villkorlig åtkomst som blockerar åtkomst från vissa länder/regioner. Den innehåller en grupp som undantas från principen. Här är en rekommenderad åtkomst granskning där medlemmarna i gruppen granskas.

> [!NOTE]
> En global administratör eller användar administratörs roll krävs för att skapa åtkomst granskningar.

1. Granskningen görs om varje vecka.

2. Upphör aldrig att gälla för att se till att du behåller den här undantags gruppen mest uppdaterad.

3. Alla medlemmar i den här gruppen kommer att omfattas av granskningen.

4. Varje användare måste själv bekräfta att de fortfarande behöver ha åtkomst från dessa blockerade länder/regioner, och de behöver därför fortfarande vara medlem i gruppen.

5. Om användaren inte svarar på gransknings förfrågan tas de bort automatiskt från gruppen och kan därför inte längre komma åt klienten när de reser till dessa länder/regioner.

6. Aktivera e-postaviseringar så att användarna meddelas om hur åtkomst granskningen startar och slutförs.

    ![Skapa ett åtkomst gransknings fönster till exempel 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exempel 2: Åtkomst granskning för användare som har åtkomst till med äldre autentisering

Anta att du har en princip för villkorlig åtkomst som blockerar åtkomsten för användare med äldre autentisering och äldre klient versioner. Den innehåller en grupp som undantas från principen. Här är en rekommenderad åtkomst granskning där medlemmarna i gruppen granskas.

1. Den här granskningen måste vara en återkommande granskning.

2. Alla i gruppen måste granskas.

3. Det kan konfigureras för att visa en lista över affär senhets ägare som de valda granskarna.

4. Tillämpa resultaten automatiskt och ta bort användare som inte har godkänts för att fortsätta använda äldre autentiseringsmetoder.

5. Det kan vara bra att aktivera rekommendationer så att granskare av stora grupper enkelt kan fatta sina beslut.

6. Aktivera e-postaviseringar så att användarna meddelas om hur åtkomst granskningen startar och slutförs.

    ![Skapa ett åtkomst gransknings fönster till exempel 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro-tips**: Om du har många undantags grupper och därför behöver skapa flera åtkomst granskningar, har vi nu ett API i Microsoft Graph beta-slutpunkten där du kan skapa och hantera dem program mässigt. Information om hur du kommer igång finns i [API-referens för Azure AD Access Reviews](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) och [exempel på hur du hämtar åtkomst granskningar för azure AD via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Åtkomst gransknings resultat och gransknings loggar

Nu när du har allt på plats, grupp, princip för villkorlig åtkomst och åtkomst granskningar, är det dags att övervaka och spåra resultatet av dessa granskningar.

1. Öppna bladet **åtkomst granskningar** i Azure Portal.

1. Öppna den kontroll och det program som du har skapat för att hantera undantags gruppen.

1. Klicka på **resultat** för att se vem som har godkänts för att stanna kvar i listan och vem som har tagits bort.

    ![Åtkomst gransknings resultat Visa vem som godkänts](./media/conditional-access-exclusion/access-reviews-results.png)

1. Klicka sedan på **gransknings loggar** för att se de åtgärder som vidtogs under granskningen.

    ![Åtkomst granskningar gransknings loggar List åtgärder](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Som IT-administratör vet du att hanteringen av undantags grupper i principerna ibland är oundviklig. Men om du behåller dessa grupper kan du granska dem regelbundet av företags ägaren eller själva användarna, och granska ändringarna kan göras enklare med åtkomst granskningar för Azure AD.

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomst granskning av grupper eller program](create-access-review.md)
- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)
