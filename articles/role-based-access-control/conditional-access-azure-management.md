---
title: Hantera åtkomst till Azure-hantering med villkorlig åtkomst i Azure Active Directory
description: Lär dig mer om att använda villkorlig åtkomst i Azure AD för att hantera åtkomst till Azure-hantering.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: e4b7ce0701349cae3ef501213d0f822ee8d150d0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142835"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Hantera åtkomst till Azure-hantering med villkorlig åtkomst

> [!CAUTION]
> Se till att du förstår hur villkorlig åtkomst fungerar innan du konfigurerar en princip för hantering av åtkomst till Azure-hantering. Se till att du inte skapar villkor som kan blockera din egen åtkomst till portalen.

Villkorlig åtkomst i Azure Active Directory (Azure AD) styr åtkomsten till molnappar baserat på särskilda villkor som du anger. För att tillåta åtkomst skapar du principer för villkorlig åtkomst som tillåter eller blockerar åtkomst baserat på om kraven i principen är uppfyllda. 

Normalt använder du villkorlig åtkomst för att kontrol lera åtkomsten till dina molnappar. Du kan också konfigurera principer för att styra åtkomsten till Azure-hantering baserat på vissa villkor (till exempel inloggnings risk, plats eller enhet) och för att genomdriva krav som Multi-Factor Authentication.

Om du vill skapa en princip för Azure-hantering väljer du **Microsoft Azure hantering** under **molnappar** när du väljer den app som principen ska tillämpas på.

![Villkorlig åtkomst för hantering av Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Principen som du skapar gäller för alla slut punkter för Azure-hantering, inklusive följande:

- Azure Portal
- Azure Resource Manager Provider
- Klassiska tjänst hanterings-API: er
- Azure PowerShell
- Administratörs Portal för Visual Studio-prenumerationer
- Azure DevOps
- Azure Data Factory Portal

Observera att principen gäller för Azure PowerShell, som anropar Azure Resource Manager API. Den gäller inte för [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), som anropar Microsoft Graph.


Mer information om hur du konfigurerar och använder villkorlig åtkomst finns i [villkorlig åtkomst i Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).
