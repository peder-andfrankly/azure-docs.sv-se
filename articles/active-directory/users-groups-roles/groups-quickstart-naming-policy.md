---
title: Snabb start för grupp namngivnings princip – Azure Active Directory | Microsoft Docs
description: Beskriver hur du lägger till nya användare eller tar bort befintliga användare i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e419ac5c3d292b7e630d1ebb3d3b9f59ef7b8a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026937"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Snabbstart: Namnprincip för grupper i Azure Active Directory

I den här snabbstarten konfigurerar du en namnprincip i din Azure Active Directory-klientorganisationen (Azure AD) för användarskapade Office 365-grupper för att hjälpa dig att sortera och söka i klientorganisationens grupper. Du kan till exempel använda namnprincipen till att:

* Kommunicera funktionen för en grupp, ett medlemskap, en geografisk region eller vem som har skapat gruppen.
* Kategorisera grupper i adressboken.
* Blockera specifika ord från att användas i gruppnamn och alias.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal"></a>Konfigurera en grupp namngivnings princip för en klient med hjälp av Azure Portal

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett användar administratörs konto.
1. Välj **grupper**och välj sedan **namngivnings princip** för att öppna sidan namngivnings princip.

    ![Öppna sidan namngivnings princip i administrations centret](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Visar eller redigerar prefix-suffixets namngivnings princip

1. På sidan **namngivnings princip** väljer du **grupp namngivnings princip**.
1. Du kan visa eller redigera nuvarande prefix eller suffix för namngivning individuellt genom att välja de attribut eller strängar som du vill tillämpa som en del av namngivnings principen.
1. Om du vill ta bort ett prefix eller suffix från listan väljer du prefixet eller suffixet och väljer sedan **ta bort**. Flera objekt kan tas bort samtidigt.
1. Välj **Spara** för att ändringarna i principen ska börja gälla.

### <a name="view-or-edit-the-custom-blocked-words"></a>Visar eller redigerar anpassade blockerade ord

1. På sidan **namngivnings princip** väljer du **blockerade ord**.

    ![Redigera och ladda upp blockerade ord lista för namngivnings princip](./media/groups-naming-policy/blockedwords.png)

1. Visa eller redigera den aktuella listan med anpassade blockerade ord genom att välja **Hämta**.
1. Ladda upp den nya listan med anpassade blockerade ord genom att välja fil ikonen.
1. Välj **Spara** för att ändringarna i principen ska börja gälla.

Klart! Du har angett en namnprincip och lagt till dina anpassade blockerade ord.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="remove-the-naming-policy-using-azure-portal"></a>Ta bort namngivnings principen med Azure Portal

1. På sidan **namngivnings princip** väljer du **ta bort princip**.
1. När du har bekräftat borttagningen tas namngivnings principen bort, inklusive alla namngivnings principer för prefix och alla anpassade blockerade ord.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ställer in namngivnings principen för din Azure AD-organisation via Azure Portal.

Fortsätt till nästa artikel om du vill ha mer information, inklusive PowerShell-cmdletar för namngivnings princip, tekniska begränsningar, lägga till en lista med anpassade blockerade ord och slutanvändarens upplevelse i Office 365-appar.
> [!div class="nextstepaction"]
> [Namnger princip PowerShell](groups-naming-policy.md)