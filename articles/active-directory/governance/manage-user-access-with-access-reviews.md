---
title: Hantera användar åtkomst med åtkomst granskningar – Azure Active Directory | Microsoft Docs
description: Lär dig att hantera användarnas åtkomst som medlemskap i en grupp eller tilldelning till ett program med Azure AD-åtkomstgranskningar
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
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70b6d925772548e6703b57c265ebacf6b7f3f4c9
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499643"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Hantera användare med Azure AD-åtkomstgranskningar

Med Azure Active Directory (Azure AD) kan du enkelt se till att användarna har lämplig åtkomst. Du kan be användarna själva, eller en beslutsfattare, att delta i en åtkomstgranskning och certifiera om (eller intyga) användarnas åtkomst. Granskarna kan ge sin syn på varje användares behov för kontinuerlig åtkomst baserat på förslag från Azure AD. När en åtkomstgranskning är klar kan du göra ändringar och ta bort åtkomst för användare som inte längre behöver den.

> [!NOTE]
> Om du enbart vill granska gästanvändares åtkomst och inte alla typer av användaråtkomster kan du läsa [Manage guest user access with access reviews](manage-guest-access-with-access-reviews.md) (Hantera gästanvändares åtkomst med åtkomstgranskningar). Om du vill granska användarens medlemskap i administrativa roller, som global administratör, läser du [Start an access review in Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md) (Starta en åtkomstgranskning i Azure AD Privileged Identity Management).

## <a name="prerequisites"></a>Förutsättningar

- Azure AD Premium P2

Mer information finns i [vilka användare måste ha licenser?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-and-perform-an-access-review"></a>Skapa och utför du en åtkomstgranskning

Du kan ha en eller flera användare som granskare i en åtkomstgranskning.  

1. Välj en grupp i Azure AD som har en eller flera medlemmar. Alternativt kan du välja ett program har anslutit till Azure AD som har en eller flera användare tilldelade till sig. 

2. Bestäm om varje användare ska granska sin egen åtkomst eller om en eller flera användare ska granska allas åtkomst.

3. Som global administratör eller användar administratör går du till [sidan identitets styrning](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Skapa åtkomst granskning. Mer information finns i [skapa en åtkomst granskning av grupper eller program](create-access-review.md).

5. När åtkomst granskningen startar kan du be granskarna att ange information. Som standard får de ett e-postmeddelande från Azure AD med en länk till åtkomst panelen där de [granskar åtkomst till grupper eller program](perform-access-review.md).

6. Om granskarna inte har framfört några åsikter kan du be att Azure AD skickar en påminnelse. Som standard skickar Azure AD automatiskt en påminnelse när halva tiden före slutdatumet har gått till granskarna som ännu inte har svarat.

7. När granskarna har framfört sina åsikter avslutar du åtkomstgranskningen och tillämpar ändringarna. Mer information finns i [fullständig åtkomst granskning av grupper eller program](complete-access-review.md).


## <a name="next-steps"></a>Nästa steg

[Skapa en åtkomst granskning av grupper eller program](create-access-review.md)




