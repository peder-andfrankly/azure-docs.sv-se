---
title: Aktivera mina Azure AD-roller i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du aktiverar Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6007762c897337170dec69c3486302aa62723480
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756289"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktivera mina Azure AD-roller i PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gör det enklare för företag att hantera privilegie rad åtkomst till resurser i Azure AD och andra Microsoft-onlinetjänster som Office 365 eller Microsoft Intune.  

Om du har gjorts berättigad till en administrativ roll innebär det att du kan aktivera rollen när du behöver utföra privilegierade åtgärder. Om du till exempel ibland hanterar Office 365-funktioner kanske organisationens privilegierade roll administratörer inte gör en permanent global administratör, eftersom rollen även påverkar andra tjänster. De gör i stället att du är berättigade till Azure AD-roller som Exchange Online-administratör. Du kan begära att aktivera rollen när du behöver dess behörigheter och sedan har du administratörs behörighet för en fördefinierad tids period.

Den här artikeln är för administratörer som behöver aktivera sin Azure AD-roll i Privileged Identity Management.

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver utföra en Azure AD-roll kan du begära aktivering genom att använda navigerings alternativet **Mina roller** i Privileged Identity Management.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**. Information om hur du lägger till panelen Privileged Identity Management på instrument panelen finns i [börja använda Privileged Identity Management](pim-getting-started.md).

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina roller** om du vill se en lista över dina berättigade Azure AD-roller.

    ![Azure AD-roller – mina roller visar listan med berättigade eller aktiva roller](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Hitta en roll som du vill aktivera.

    ![Azure AD-roller – listan Mina berättigade roller visar aktiverings länken](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Klicka på **Aktivera** för att öppna fönstret roll aktiverings information.

1. Om din roll kräver Multi-Factor Authentication (MFA), klickar **du på verifiera din identitet innan du fortsätter**. Du behöver bara autentisera en gång per session.

    ![Verifiera mitt identitets fönster med MFA innan roll aktivering](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Klicka på **verifiera min identitet** och följ instruktionerna för att tillhandahålla ytterligare säkerhets verifiering.

    ![Sidan ytterligare säkerhets verifiering där du tillfrågas om hur du kontaktar dig](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Klicka på **Aktivera** för att öppna aktiverings fönstret.

    ![Aktiverings fönstret för att ange start tid, varaktighet, biljett och orsak](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Vid behov kan du ange en start tid för anpassad aktivering.

1. Ange varaktighet för aktivering.

1. I rutan **aktiverings orsak** anger du orsaken till aktiverings förfrågan. Vissa roller kräver att du anger ett fel biljett nummer.

    ![Slutfört aktiverings fönster med anpassad start tid, varaktighet, biljett och orsak](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Klicka på **Aktivera**.

    Om rollen inte kräver godkännande visas fönstret **aktiverings status** som visar status för aktiveringen.

    ![Sidan aktiverings status med de tre aktiverings stegen](./media/pim-how-to-activate-role/activation-status.png)

    När alla steg har slutförts klickar du på länken **Logga ut** för att logga ut från Azure Portal. När du loggar in på portalen igen kan du nu använda rollen.

    Om [rollen kräver godkännande](./azure-ad-pim-approval-workflow.md) för att aktive ras visas ett meddelande i det övre högra hörnet i webbläsaren som informerar dig om att begäran väntar på godkännande.

    ![Begäran om aktivering väntar på godkännande](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Visa status för dina begär Anden

Du kan visa statusen för väntande begär Anden att aktivera.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina förfrågningar** om du vill se en lista över dina begär Anden.

    ![Azure AD-roller – listan Mina begär Anden](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Inaktivera en roll

När en roll har Aktiver ATS inaktive ras den automatiskt när tids gränsen (den kvalificerade varaktigheten) har nåtts.

Om du slutför dina administratörs aktiviteter tidigt kan du också inaktivera en roll manuellt i Azure AD Privileged Identity Management.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina roller**.

1. Klicka på **aktiva roller** om du vill se en lista över aktiva roller.

1. Hitta den roll du är klar med och klicka sedan på **inaktivera**.

## <a name="cancel-a-pending-request"></a>Avbryt en väntande begäran

Om du inte behöver aktivera en roll som kräver godkännande kan du när som helst avbryta en väntande begäran.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Mina förfrågningar**.

1. Klicka på knappen **Avbryt** för den roll som du vill avbryta.

    När du klickar på Avbryt avbryts begäran. Om du vill aktivera rollen igen måste du skicka in en ny begäran om aktivering.

   ![Listan Mina begär Anden med knappen Avbryt markerad](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Felsökning

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Behörigheter beviljas inte efter att en roll har aktiverats

När du aktiverar en roll i Privileged Identity Management kan aktiveringen inte genast spridas till alla portaler som kräver den privilegierade rollen. Ibland kan webbcachelagringen i en portal resultera i att ändringen inte börjar gälla omedelbart, även om ändringen har spridits. Om aktiveringen fördröjs, är det vad du ska göra.

1. Logga ut från Azure-portalen och logga sedan in igen.

    När du aktiverar en Azure AD-roll visas stegen i aktiveringen. När alla steg har slutförts visas länken **Logga ut**. Du kan använda den här länken för att logga ut. Detta kommer att lösa de flesta fall för aktiverings fördröjning.

1. I Privileged Identity Management kontrollerar du att du är listad som medlem i rollen.

## <a name="next-steps"></a>Nästa steg

- [Aktivera mina Azure-resurs roller i Privileged Identity Management](pim-resource-roles-activate-your-roles.md)
