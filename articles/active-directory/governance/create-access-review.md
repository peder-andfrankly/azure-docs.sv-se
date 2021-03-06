---
title: Skapa en åtkomst granskning av grupper eller program – Azure Active Directory | Microsoft Docs
description: Lär dig hur du skapar en åtkomst granskning av grupp medlemmar eller program åtkomst i Azure Active Directory åtkomst granskningar.
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
ms.openlocfilehash: 230fb40c8e3a100d2fdfa0af6b40c93c3e5b47d2
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499730"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Skapa en åtkomst granskning av grupper eller program i åtkomst granskningar för Azure AD

Åtkomst till grupper och program för medarbetare och gäster förändras över tid. För att minska risken som är kopplad till inaktuella åtkomst tilldelningar kan administratörer använda Azure Active Directory (Azure AD) för att skapa åtkomst granskningar för grupp medlemmar eller program åtkomst. Om du behöver granska åtkomst rutinmässigt kan du också skapa återkommande åtkomst granskningar. Mer information om dessa scenarier finns i [hantera användar åtkomst](manage-user-access-with-access-reviews.md) och [Hantera gäst åtkomst](manage-guest-access-with-access-reviews.md).

Den här artikeln beskriver hur du skapar en eller flera åtkomst granskningar för grupp medlemmar eller program åtkomst.

## <a name="prerequisites"></a>Förutsättningar

- Azure AD Premium P2
- Global administratör eller användar administratör

Mer information finns i [vilka användare måste ha licenser?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-one-or-more-access-reviews"></a>Skapa en eller flera åtkomst granskningar

1. Logga in på Azure Portal och öppna [sidan identitets styrning](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Klicka på **åtkomst granskningar**på den vänstra menyn.

1. Klicka på **ny åtkomst granskning** för att skapa en ny åtkomst granskning.

    ![Fönstret åtkomst granskningar i identitets styrning](./media/create-access-review/access-reviews.png)

1. Ge åtkomst granskningen namnet. Du kan också ange en beskrivning av granskningen. Namnet och beskrivningen visas för granskarna.

    ![Skapa en åtkomst granskning – granska namn och beskrivning](./media/create-access-review/name-description.png)

1. Ange **start datum**. Som standard sker en åtkomst granskning en gång, startas samma gång som den skapas och den avslutas om en månad. Du kan ändra start-och slutdatumen så att en åtkomst granskning börjar i framtiden och det senaste antalet dagar som du vill.

    ![Skapa en åtkomst granskning – start-och slutdatum](./media/create-access-review/start-end-dates.png)

1. Om du vill göra åtkomst granskningen återkommande ändrar du **frekvens** inställningen från **en gång** till **varje vecka**, **varje månad**, varje **kvartal** eller **varje år**. Använd skjutreglaget **varaktighet** eller text rutan för att definiera hur många dagar varje granskning av den återkommande serien ska vara öppen för inmatade i granskare. Den längsta tid som du kan ange för en månatlig granskning är till exempel 27 dagar för att undvika överlappande granskningar.

1. Använd **End** -inställningen för att ange hur du ska avsluta gransknings serien för återkommande åtkomst. Serien kan sluta på tre sätt: den körs kontinuerligt för att starta recensioner på obestämd tid, fram till ett visst datum eller efter att ett definierat antal förekomster har slutförts. En annan användar administratör eller en annan global administratör kan stoppa serien när den har skapats genom att ändra datumet i **Inställningar**, så att det slutar på det datumet.

1. I avsnittet **användare** anger du de användare som åtkomst granskningen gäller för. Åtkomst granskningar kan göras för medlemmar i en grupp eller för användare som har tilldelats till ett program. Du kan ytterligare ange omfånget för åtkomst granskningen för att granska de gäst användare som är medlemmar (eller tilldelade till programmet), i stället för att granska alla användare som är medlemmar eller som har åtkomst till programmet.

    ![Skapa en åtkomst granskning – användare](./media/create-access-review/users.png)

1. I avsnittet **grupp** väljer du en eller flera grupper som du vill granska medlemskap i.

    > [!NOTE]
    > Om du markerar fler än en grupp skapas flera åtkomst granskningar. Om du till exempel väljer fem grupper skapas fem olika åtkomst granskningar.
    
    ![Skapa en åtkomst granskning – Välj grupp](./media/create-access-review/select-group.png)

1. I avsnittet **program** (om du har valt **tilldelad till ett program** i steg 8) väljer du de program som du vill granska åtkomst till.

    > [!NOTE]
    > Om du väljer mer än ett program skapas flera åtkomst granskningar. Om du till exempel väljer fem program skapas fem olika åtkomst granskningar.
    
    ![Skapa en åtkomst granskning – Välj program](./media/create-access-review/select-application.png)

1. I avsnittet  granskare väljer du antingen en eller flera personer för att granska alla användare i omfånget. Alternativt kan du välja att låta medlemmarna granska sin egen åtkomst. Om resursen är en grupp kan du be grupp ägarna att granska. Du kan också kräva att granskarna anger en anledning när de godkänner åtkomsten.

    ![Skapa en åtkomst granskning – granskare](./media/create-access-review/reviewers.png)

1. I avsnittet **program** väljer du det program som du vill använda. **Standard programmet** är alltid tillgängligt.

    ![Skapa en åtkomst granskning – program](./media/create-access-review/programs.png)

    Du kan förenkla hur du spårar och samlar in åtkomst granskningar i olika syfte genom att organisera dem i program. Varje åtkomst granskning kan länkas till ett program. När du förbereder rapporter för en granskare kan du fokusera på åtkomst granskningarna i omfattningen för ett visst initiativ. Program och åtkomst gransknings resultat visas för användare i rollen global administratör, användar administratör, säkerhets administratör eller säkerhets läsare.

    Om du vill se en lista över program går du till sidan åtkomst granskningar och väljer **program**. Om du har rollen som global administratör eller användar administratör kan du skapa ytterligare program. Du kan till exempel välja att ha ett program för varje initiativ för regelefterlevnad eller affärs mål. Om du inte längre behöver ett program och det inte finns några kontroller som är länkade till det kan du ta bort det.

### <a name="upon-completion-settings"></a>Vid slutförande-inställningar

1. Om du vill ange vad som händer när en granskning har slutförts expanderar du avsnittet **när inställningarna slutförs** .

    ![Skapa en åtkomst granskning-när inställningarna slutförs](./media/create-access-review/upon-completion-settings.png)

1. Om du vill ta bort åtkomst automatiskt för användare som har nekats, ställer du in **automatiskt tillämpa resultat på resursen** för att **Aktivera**. Om du vill tillämpa resultaten manuellt när granskningen är klar ställer du in växeln på **inaktivera**.

1. Använd listan **ska inte svara** på listan om du vill ange vad som händer för användare som inte granskas av granskaren under gransknings perioden. Den här inställningen påverkar inte användare som har granskats manuellt av granskarna. Om den sista granskaren av beslutet är neka tas användarens åtkomst bort.

    - **Ingen ändring** – lämna användarens åtkomst oförändrad
    - **Ta bort åtkomst** – ta bort användares åtkomst
    - **Godkänn åtkomst** – Godkänn användarens åtkomst
    - **Ta rekommendationer** – beakta systemets rekommendation om att neka eller godkänna användarens fortsatta åtkomst

### <a name="advanced-settings"></a>Avancerade inställningar

1. Om du vill ange ytterligare inställningar expanderar du avsnittet **Avancerade inställningar** .

    ![Skapa en åtkomst granskning – avancerade inställningar](./media/create-access-review/advanced-settings.png)

1. Ange **Visa rekommendationer** för att **Aktivera** för att Visa granskarna system rekommendationer baserat på användarens åtkomst information.

1. Ange **Kräv orsak för godkännande** för att **göra det möjligt** för granskaren att ange en orsak till godkännande.

1. Ange **e-** postaviseringar för att **tillåta** att Azure AD skickar e-postmeddelanden till granskare när en åtkomst granskning startar och till administratörer när en granskning har slutförts.

1. Ställ  in påminnelser för att **tillåta** att Azure AD skickar påminnelser om åtkomst granskningar till granskare som inte har slutfört sin granskning.

    Som standard skickar Azure AD automatiskt en påminnelse när halva tiden före slutdatumet har gått till granskarna som ännu inte har svarat.

## <a name="start-the-access-review"></a>Starta åtkomst granskningen

När du har angett inställningarna för åtkomst granskning klickar du på **Start**. Åtkomst granskningen visas i listan med en indikator för dess status.

![Lista över åtkomst granskningar och deras status](./media/create-access-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter att granskningen startar. Om du väljer att inte låta Azure AD skicka e-postmeddelandet måste du meddela granskarna att en åtkomst granskning väntar på att de ska slutföras. Du kan visa dem i anvisningarna för hur du [granskar åtkomst till grupper eller program](perform-access-review.md). Om granskningen är till för gäster för att granska sin egen åtkomst, visar du dem i anvisningarna för att [Granska åtkomsten för dig själv till grupper eller program](review-your-access.md).

Om du har tilldelat gäster som granskare och de inte har accepterat inbjudan får han eller hon ingen e-post från åtkomst granskningar eftersom de först måste godkänna inbjudan innan de kan granskas.

## <a name="create-reviews-via-apis"></a>Skapa recensioner via API: er

Du kan också skapa åtkomst granskningar med hjälp av API: er. Vad du gör för att hantera åtkomst granskningar av grupper och program användare i Azure Portal kan också göras med hjälp av Microsoft Graph-API: er. Mer information finns i API- [referens för Azure AD Access-granskning](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Ett kod exempel finns i [exempel på hämtning av åtkomst granskningar för Azure AD via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till grupper eller program](perform-access-review.md)
- [Granska åtkomst för dig själv till grupper eller program](review-your-access.md)
- [Slutför en åtkomst granskning av grupper eller program](complete-access-review.md)
