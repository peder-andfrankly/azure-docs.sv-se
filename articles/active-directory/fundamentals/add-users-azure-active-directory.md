---
title: Lägga till eller ta bort användare – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till nya användare eller ta bort befintliga användare med Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073480"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Lägga till eller ta bort användare som använder Azure Active Directory

Lägg till nya användare eller ta bort befintliga användare från din Azure Active Directory (Azure AD)-organisation. Om du vill lägga till eller ta bort användare måste du vara en användar administratör eller global administratör.

## <a name="add-a-new-user"></a>Lägg till en ny användare

Du kan skapa en ny användare med hjälp av Azure Active Directory-portalen.

Följ dessa steg om du vill lägga till en ny användare:

1. Logga in på [Azure Portal](https://portal.azure.com/) som användar administratör för organisationen.

1. Sök efter och välj *Azure Active Directory* från vilken sida som helst.

1. Välj **användare**och välj sedan **ny användare**.

    ![Lägg till en användare via användare – alla användare i Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Ange information för den här användaren på sidan **användare** :

   - **Namn**. Krävs. Första och sista namnet på den nya användaren. Till exempel *Mary Parker*.

   - **Användar namn**. Krävs. Användarnamn för den nya användaren. Till exempel `mary@contoso.com`.

     Domän delen av användar namnet måste antingen använda det initiala standard domän namnet, *\<yourdomainname >. onmicrosoft. com*eller ett anpassat domän namn, till exempel *contoso.com*. Mer information om hur du skapar ett anpassat domän namn finns i [lägga till ditt anpassade domän namn med hjälp av Azure Active Directory portalen](add-custom-domain.md).

   - **Grupper**. Du kan också kan du lägga till användaren till en eller flera befintliga grupper. Du kan också lägga till användaren till grupper vid ett senare tillfälle. Mer information om hur du lägger till användare till grupper finns i [skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Katalog roll**: om du kräver administratörs behörighet för Azure AD för användaren kan du lägga till dem i en Azure AD-roll. Du kan tilldela användaren till en global administratör eller en eller flera av de begränsade administratörs rollerna i Azure AD. Mer information om hur du tilldelar roller finns i [tilldela roller till användare](active-directory-users-assign-role-azure-portal.md).

   - **Jobb information**: du kan lägga till mer information om användaren här eller göra det senare. Läs mer om att lägga till användarinformation [lägga till eller ändra information om användarprofiler](active-directory-users-profile-azure-portal.md).

1. Kopiera det automatiskt genererade lösen ordet som anges i rutan **lösen ord** . Du måste ge det här lösen ordet till användaren för att kunna logga in för första gången.

1. Välj **Skapa**.

Användaren skapas och läggs till i din Azure AD-organisation.

## <a name="add-a-new-guest-user"></a>Lägg till en ny gäst användare

Du kan också bjuda in nya gäst användare att samar beta med din organisation genom att välja **Bjud in användare** från sidan **ny användare** . Om din organisations inställningar för externt samarbete har kon figurer ATS så att du får bjuda in gäster, skickas användaren via e-post till en inbjudan som de måste acceptera för att börja samar beta. Mer information om att bjuda in B2B-samarbets användare finns i [Bjud in B2B-användare till Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Lägg till en konsument användare

Det kan finnas scenarier där du manuellt vill skapa konsument konton i din Azure Active Directory B2C-katalog (Azure AD B2C). Mer information om hur du skapar konsument konton finns [i skapa och ta bort konsument användare i Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Lägg till en ny användare i en hybridmiljö

Om du har en miljö med både Azure Active Directory (moln) och Windows Server Active Directory (lokalt), kan du lägga till nya användare genom att synkronisera befintliga konto användardata. Mer information om hybridmiljöer och användare finns i [integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Ta bort en användare

Du kan ta bort en befintlig användare med hjälp av Azure Active Directory-portalen.

Följ dessa steg om du vill ta bort en användare:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett användar administratörs konto för organisationen.

1. Sök efter och välj *Azure Active Directory* från vilken sida som helst.

1. Sök efter och välj den användare som du vill ta bort från Azure AD-klienten. Till exempel _Mary Parker_.

1. Välj **ta bort användaren**.

    ![Användare - sidan för alla användare med ta bort användaren markerat](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

Användaren tas bort och inte längre visas på den **användare – alla användare** sidan. Användaren visas på den **borttagna användare** för de närmaste 30 dagarna och kan återställas under den tiden. Mer information om hur du återställer en användare finns i [återställa eller ta bort en nyligen borttagen användare med hjälp av Azure Active Directory](active-directory-users-restore.md).

När en användare tas bort görs alla licenser som används av användaren tillgängliga för andra användare.

>[!Note]
>Du måste använda Windows Server Active Directory för att uppdatera identitet, kontakt information eller jobb information för användare vars auktoritets källa är Windows Server Active Directory. När du har slutfört uppdateringen måste du vänta tills nästa synkroniseringscykel ska slutföras innan ändringarna visas.

## <a name="next-steps"></a>Nästa steg

När du har lagt till dina användare kan du göra följande grundläggande processer:

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Skapa en basgrupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md)

- [Arbeta med dynamiska grupper och användare](../users-groups-roles/groups-create-rule.md)

Eller så kan du utföra andra användar hanterings uppgifter, till exempel [lägga till gäst användare från en annan katalog](../b2b/what-is-b2b.md) eller [återställa en borttagen användare](active-directory-users-restore.md). Läs mer om andra tillgängliga åtgärder, [Azure Active Directory management supportdokumentation](../users-groups-roles/index.yml).
