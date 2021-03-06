---
title: Synkronisera lokala partner konton till molnet som B2B-användare – Azure AD
description: Ge lokalt hanterade externa partners åtkomst till både lokala och molnbaserade resurser med samma autentiseringsuppgifter med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcc8c0538bb3362818a4172dd42905fd72b19812
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272604"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Bevilja lokalt hanterad partner konton åtkomst till moln resurser med Azure AD B2B-samarbete

Innan Azure Active Directory (Azure AD) har organisationer med lokala identitets system traditionellt hanterade partner konton i sin lokala katalog. När du börjar flytta appar till Azure AD i en sådan organisation, vill du se till att dina partner har åtkomst till de resurser som de behöver. Det bör inte vara viktigt om resurserna är lokala eller i molnet. Du vill också att dina partner användare ska kunna använda samma inloggnings uppgifter för både lokala och Azure AD-resurser. 

Om du skapar konton för dina externa partner i din lokala katalog (till exempel skapar du ett konto med inloggnings namnet "wmoran" för en extern användare med namnet Wendy Moran i din partners.contoso.com-domän) kan du nu synkronisera dessa konton till kunde. Mer specifikt kan du använda Azure AD Connect för att synkronisera partner kontona till molnet som Azure AD B2B-användare (det vill säga användare med UserType = gäst). Detta gör det möjligt för dina partner användare att komma åt moln resurser med samma autentiseringsuppgifter som de lokala kontona, utan att ge dem mer åtkomst än de behöver. 

## <a name="identify-unique-attributes-for-usertype"></a>Identifiera unika attribut för UserType

Innan du aktiverar synkronisering av UserType-attributet måste du först bestämma hur UserType-attributet ska härledas från lokala Active Directory. Vilka parametrar i din lokala miljö är unika för dina externa medarbetare? Bestäm en parameter som särskiljer dessa externa medarbetare från medlemmar i din organisation.

Två vanliga metoder för detta är att:

- Ange ett oanvänt lokalt Active Directory-attribut (till exempel extensionAttribute1) som ska användas som källattribut. 
- Du kan också härleda värdet för attributet UserType från andra egenskaper. Till exempel vill du synkronisera alla användare som gäst om deras lokala Active Directory UserPrincipalName-attribut slutar med domän *\@partners.contoso.com*.
 
Detaljerade attribut krav finns i [Aktivera synkronisering av UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurera Azure AD Connect för att synkronisera användare till molnet

När du har identifierat det unika attributet kan du konfigurera Azure AD Connect att synkronisera dessa användare till molnet som Azure AD B2B-användare (det vill säga användare med UserType = gäst). Från en behörighets punkt i vyn kan de här användarna inte särskiljas från B2B-användare som skapats genom inbjudan till Azure AD B2B-samarbete.

Anvisningar för implementering finns i [Aktivera synkronisering av UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory B2B-samarbete för Hybrid organisationer](hybrid-organizations.md)
- [Bevilja B2B-användare i Azure AD åtkomst till dina lokala program](hybrid-cloud-to-on-premises.md)
- En översikt över Azure AD Connect finns i [integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

