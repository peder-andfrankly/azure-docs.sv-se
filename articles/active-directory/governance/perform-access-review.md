---
title: Granska åtkomst till grupper eller program i åtkomst granskningar – Azure Active Directory | Microsoft Docs
description: Lär dig hur du granskar åtkomsten till grupp medlemmar eller program åtkomst i Azure Active Directory åtkomst granskningar.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e51ad8faecd935ea999b1287e542e4b14d55290a
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948246"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Granska åtkomst till grupper eller program i åtkomst granskningar för Azure AD

Azure Active Directory (Azure AD) gör det enklare för företag att hantera åtkomst till grupper och program i Azure AD och andra Microsoft Online Services med en funktion som kallas Azure AD-åtkomst granskningar.

Den här artikeln beskriver hur en angiven granskare utför en åtkomst granskning för medlemmar i en grupp eller användare som har åtkomst till ett program.

## <a name="open-the-access-review"></a>Öppna åtkomst granskningen

Det första steget för att utföra en åtkomst granskning är att hitta och öppna åtkomst granskningen.

1. Sök efter ett e-postmeddelande från Microsoft som ber dig att granska åtkomst. Här är ett exempel på ett e-postmeddelande för att granska åtkomsten för en grupp.

    ![Exempel på e-post från Microsoft för att kontrol lera åtkomsten till en grupp](./media/perform-access-review/access-review-email.png)

1. Klicka på länken **Starta granskning** för att öppna åtkomst granskningen.

Om du inte har e-postmeddelandet kan du hitta dina väntande åtkomst granskningar genom att följa dessa steg.

1. Logga in på portalen för att [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Appar för portalen som visar appar som du har behörighet att](./media/perform-access-review/myapps-access-panel.png)

1. I det övre högra hörnet på sidan klickar du på användarsymbolen, som visar ditt namn och standardorganisationen. Om mer än en organisation visas väljer du den organisation som begärde en åtkomstgranskning.

1. Klicka på panelen **åtkomst granskningar** om du vill se en lista över väntande åtkomst granskningar.

    Om panelen inte är synlig finns det inga åtkomstgranskningar att utföra för den organisationen, och ingen åtgärd krävs just nu.

    ![Lista över väntande åtkomst granskningar för appar och grupper](./media/perform-access-review/access-reviews-list.png)

1. Klicka på länken **Starta granskning** för den åtkomst granskning som du vill utföra.

## <a name="perform-the-access-review"></a>Utför åtkomst granskningen

När du har öppnat åtkomst granskningen visas namnen på de användare som behöver granskas.

Om begäran är att granska din egen åtkomst kommer sidan att se annorlunda ut. Mer information finns i [Granska åtkomst för dig själv till grupper eller program](review-your-access.md).

![Öppna åtkomst granskning visar en lista över de användare som behöver granskas](./media/perform-access-review/perform-access-review.png)

Du kan godkänna eller neka åtkomst på två sätt:

- Du kan godkänna eller neka åtkomst för en eller flera användare, eller
- Du kan acceptera system rekommendationer, vilket är det enklaste och snabbaste sättet.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Godkänn eller neka åtkomst för en eller flera användare

1. Granska listan med användare och bestäm om du vill godkänna eller neka fortsatt åtkomst.

1. Om du vill godkänna eller neka åtkomst för en enskild användare klickar du på raden för att öppna ett fönster för att ange vilken åtgärd som ska vidtas. Om du vill godkänna eller neka åtkomst för flera användare, så Lägg till kryss markeringar bredvid användarna och klicka sedan på knappen **Granska X-användare** för att öppna ett fönster för att ange vilken åtgärd som ska vidtas.

1. Klicka på **Godkänn** eller **neka**. Om du är osäker kan du klicka på **vet inte**. Detta leder till att användaren behåller åtkomsten, men att valet visas i gransknings loggarna.

    ![Åtgärds fönster som innehåller alternativen Godkänn, neka och vet inte](./media/perform-access-review/approve-deny.png)

1. Ange en orsak i rutan **orsak** vid behov.

    Administratören för åtkomst granskningen kan kräva att du anger en orsak till godkännande av fortsatt åtkomst eller grupp medlemskap.

1. När du har angett vilken åtgärd som ska utföras klickar du på **Spara**.

    Om du vill ändra ditt svar väljer du raden och uppdaterar svaret. Du kan till exempel godkänna en tidigare nekad användare eller neka en tidigare godkänd användare. Du kan ändra ditt svar när du vill tills åtkomst granskningen har slutförts.

    Om det finns flera granskare registreras det senast skickade svaret. Överväg ett exempel där en administratör betecknar två granskare – Alice och Bob. Alice öppnar först åtkomst granskningen och godkänner åtkomst. Innan granskningen avslutas öppnar Bob åtkomst granskningen och nekar åtkomst. Det senaste neka-svaret är det som registreras.

    > [!NOTE]
    > Om en användare nekas åtkomst tas de inte bort direkt. De tas bort när granskningen har slutförts eller när en administratör stoppar granskningen.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Godkänn eller neka åtkomst baserat på rekommendationer

För att få åtkomst granskningar enklare och snabbare ger vi även rekommendationer som du kan acceptera med ett enda klick. Rekommendationerna genereras baserat på användarens inloggnings aktivitet.

1. Klicka på **Godkänn rekommendationer**i det blå fältet längst ned på sidan.

    ![Öppna listan med åtkomst granskningar som visar knappen Godkänn rekommendationer](./media/perform-access-review/accept-recommendations.png)

    Du ser en sammanfattning av de rekommenderade åtgärderna.

    ![Fönster som visar en sammanfattning av de rekommenderade åtgärderna](./media/perform-access-review/accept-recommendations-summary.png)

1. Klicka på **OK** för att godkänna rekommendationerna.

## <a name="next-steps"></a>Nästa steg

- [Slutför en åtkomst granskning av grupper eller program](complete-access-review.md)
