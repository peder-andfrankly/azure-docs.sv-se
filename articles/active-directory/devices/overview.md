---
title: Vad är enhets identitet i Azure Active Directory?
description: Lär dig hur enhets identitets hantering kan hjälpa dig att hantera enheter som har åtkomst till resurser i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b56319579bc8766bbd8467296daf41e25fc31862
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420508"
---
# <a name="what-is-a-device-identity"></a>Vad är en enhetsidentitet?

Med spridning av enheter av alla former och storlekar och BYOD-konceptet (ta med din egen enhet) är IT-proffsen riktade mot två lite motsatta mål:

- Tillåt slutanvändare att vara produktiva var och när
- Skydda organisationens till gångar

För att skydda dessa till gångar måste IT-personalen först hantera enhetens identiteter. IT-personalen kan bygga på enhets identiteten med verktyg som Microsoft Intune för att säkerställa att standarder för säkerhet och efterlevnad är uppfyllda. Azure Active Directory (Azure AD) möjliggör enkel inloggning till enheter, appar och tjänster från var som helst via dessa enheter.

- Dina användare får till gång till din organisations till gångar som de behöver. 
- IT-personalen får de kontroller de behöver för att skydda din organisation.

Enhets identitets hantering är grunden för [enhets-baserad villkorlig åtkomst](../conditional-access/require-managed-devices.md). Med enhets principer för villkorlig åtkomst kan du se till att åtkomsten till resurser i din miljö bara är möjlig med hanterade enheter.

## <a name="getting-devices-in-azure-ad"></a>Hämta enheter i Azure AD

För att få en enhet i Azure AD har du flera alternativ:

- **Azure AD har registrerats**
   - Enheter som är registrerade i Azure AD är vanligt vis personligt ägda eller mobila enheter och är inloggade på med en personlig Microsoft-konto eller ett annat lokalt konto.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Azure AD-ansluten**
   - Enheter som är Azure AD-anslutna ägs av en organisation och är inloggade på med ett Azure AD-konto som tillhör den organisationen. De finns bara i molnet.
      - Windows 10 
- **Hybrid Azure AD-ansluten**
   - Enheter som är hybrid Azure AD-anslutna ägs av en organisation och är inloggade på med ett Azure AD-konto som tillhör den organisationen. De finns i molnet och lokalt.
      - Windows 7, 8,1 eller 10
      - Windows Server 2008 eller senare

![Enheter som visas på bladet Azure AD-enheter](./media/overview/azure-active-directory-devices-all-devices.png)

## <a name="device-management"></a>Enhetshantering

Enheter i Azure AD kan hanteras med verktyg för hantering av mobila enheter (MDM) som Microsoft Intune, System Center Configuration Manager, grupprincip (hybrid Azure AD Join), verktyg för hantering av mobila program (MAM) eller andra verktyg från tredje part.

## <a name="resource-access"></a>Resurs åtkomst

Genom att registrera och ansluta får användarna sömlös inloggning (SSO) till moln resurser och administratörer möjlighet att tillämpa principer för villkorlig åtkomst för dessa resurser. 

Enheter som är Azure AD-anslutna eller hybrid Azure AD-anslutna fördelar från SSO till din organisations lokala resurser samt moln resurser. Mer information finns i artikeln [hur SSO till lokala resurser fungerar på Azure AD-anslutna enheter](azuread-join-sso.md).

## <a name="device-security"></a>Enhetssäkerhet

- **Registrerade Azure AD-enheter** använder ett konto som hanteras av slutanvändaren. det här kontot är antingen en Microsoft-konto eller en annan lokalt hanterad autentiseringsuppgift som skyddas med ett eller flera av följande.
   - Lösenord
   - PUK
   - Mönster
   - Windows Hello
- **Azure AD-anslutna eller hybrid Azure AD-anslutna enheter** använder ett organisations konto i Azure AD som skyddas med en eller flera av följande.
   - Lösenord
   - Windows Hello för företag

## <a name="provisioning"></a>Etablering

Att hämta enheter i till Azure AD kan göras i ett självbetjänings sätt eller en kontrollerad etablerings process av administratörer.

## <a name="summary"></a>Sammanfattning

Med enhets identitets hantering i Azure AD kan du:

- Förenkla processen att sätta och hantera enheter i Azure AD
- Ge användarna enkel åtkomst till organisationens molnbaserade resurser

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- Läs mer om [registrerade Azure AD-enheter](concept-azure-ad-register.md)
- Läs mer om [Azure AD-anslutna enheter](concept-azure-ad-join.md)
- Läs mer om [Azure AD-anslutna enheter](concept-azure-ad-join-hybrid.md)
- För att få en översikt över hur du hanterar enhets identiteter i Azure Portal, se [Hantera enhets identiteter med hjälp av Azure Portal](device-management-azure-portal.md).
- Mer information om enhets villkorliga åtkomst finns i [Konfigurera Azure Active Directory enhet-baserade principer för villkorlig åtkomst](../conditional-access/require-managed-devices.md).
