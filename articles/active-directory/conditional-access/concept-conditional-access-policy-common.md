---
title: Vanliga principer för villkorlig åtkomst – Azure Active Directory
description: Ofta använda principer för villkorlig åtkomst för organisationer
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3d85850fb18b80490bba44b293ece7765124133
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846121"
---
# <a name="common-conditional-access-policies"></a>Vanliga principer för villkorlig åtkomst

Principer för bas linje skydd är bra men många organisationer behöver större flexibilitet än de erbjuder. Många organisationer behöver till exempel möjlighet att utesluta vissa konton, t. ex. deras nödfalls åtkomst eller administrations konton för brytar-glas från principer för villkorlig åtkomst som kräver Multi-Factor Authentication. För dessa organisationer kan de gemensamma principer som hänvisas till i den här artikeln användas.

![Principer för villkorlig åtkomst i Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Konton för nöd åtkomst

Mer information om konton för nöd åtkomst och varför de är viktiga finns i följande artiklar: 

* [Hantera konton för nöd åtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Skapa en elastisk strategi för hantering av åtkomst kontroll med Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Vanliga principer som distribueras av organisationer

* [Kräv MFA för administratörer](howto-conditional-access-policy-admin-mfa.md)
* [Kräv MFA för Azure-hantering](howto-conditional-access-policy-azure-management.md)
* [Kräv MFA för alla användare](howto-conditional-access-policy-all-users-mfa.md)
* [Blockera äldre autentisering](howto-conditional-access-policy-block-legacy.md)
* [Riskfylld villkorlig åtkomst (kräver Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Kräv betrodd plats för MFA-registrering](howto-conditional-access-policy-registration.md)
* [Blockera åtkomst efter plats](howto-conditional-access-policy-location.md)
* [Kräv kompatibel enhet](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>Nästa steg

- [Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst.](troubleshoot-conditional-access-what-if.md)
- [Använd endast rapport läge för villkorlig åtkomst för att fastställa effekten av nya princip beslut.](concept-conditional-access-report-only.md)
