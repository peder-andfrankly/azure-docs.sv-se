---
title: Lösa aviseringar om nätverks säkerhets grupper i Azure AD DS | Microsoft Docs
description: Lär dig hur du felsöker och löser konfigurations aviseringar för nätverks säkerhets grupper för Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257991"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Kända problem: Aviseringar om nätverks konfiguration i Azure Active Directory Domain Services

För att program och tjänster ska kunna kommunicera korrekt med Azure Active Directory Domain Services (Azure AD DS) måste särskilda nätverks portar vara öppna för att tillåta trafik att flöda. I Azure styr du trafik flödet med hjälp av nätverks säkerhets grupper. Hälso status för en Azure AD DS-hanterad domän visar en avisering om de nödvändiga reglerna för nätverks säkerhets grupper inte finns på plats.

Den här artikeln hjälper dig att förstå och lösa vanliga aviseringar för konfigurations problem med nätverks säkerhets grupper.

## <a name="alert-aadds104-network-error"></a>Aviserings AADDS104: Nätverksfel

### <a name="alert-message"></a>Aviserings meddelande

*Microsoft kan inte komma åt domän kontrol Lanterna för den här hanterade domänen. Detta kan inträffa om en nätverks säkerhets grupp (NSG) som kon figurer ATS i ditt virtuella nätverk blockerar åtkomsten till den hanterade domänen. En annan möjlig orsak är om det finns en användardefinierad väg som blockerar inkommande trafik från Internet.*

Ogiltiga regler för nätverks säkerhets grupper är den vanligaste orsaken till nätverks fel för Azure AD DS. Nätverks säkerhets gruppen för det virtuella nätverket måste tillåta åtkomst till vissa portar och protokoll. Om dessa portar blockeras kan Azure-plattformen inte övervaka eller uppdatera den hanterade domänen. Synkroniseringen mellan Azure AD-katalogen och den hanterade domänen i Azure AD DS påverkas också. Se till att du behåller standard portarna öppna för att undvika avbrott i tjänsten.

## <a name="default-security-rules"></a>Standardsäkerhetsregler

Följande standard säkerhets regler för inkommande och utgående trafik tillämpas på nätverks säkerhets gruppen för en hanterad Azure AD DS-domän. De här reglerna skyddar Azure AD DS och ger Azure-plattformen möjlighet att övervaka, hantera och uppdatera den hanterade domänen. Du kan också ha en ytterligare regel som tillåter inkommande trafik om du [konfigurerar säker LDAP][configure-ldaps].

### <a name="inbound-security-rules"></a>Ingående säkerhetsregel

| Priority | Name | Port | Protocol | Källa | Destination | Action |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Any | Allow |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 65000    | AllVnetInBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Any | Allow |
| 65500    | DenyAllInBound | Any | Any | Any | Any | Neka |

### <a name="outbound-security-rules"></a>Utgående säkerhetsregler

| Priority | Name | Port | Protocol | Källa | Destination | Action |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | Any | Any |  Any | Internet | Allow |
| 65500    | DenyAllOutBound | Any | Any | Any | Any | Neka |

>[!NOTE]
> Azure AD DS behöver obegränsad utgående åtkomst från det virtuella nätverket. Vi rekommenderar inte att du skapar några ytterligare regler som begränsar utgående åtkomst för det virtuella nätverket.

## <a name="verify-and-edit-existing-security-rules"></a>Verifiera och redigera befintliga säkerhets regler

För att kontrol lera de befintliga säkerhets reglerna och se till att standard portarna är öppna, utför du följande steg:

1. Sök efter och välj **nätverks säkerhets grupper**i Azure Portal.
1. Välj den nätverks säkerhets grupp som är kopplad till din hanterade domän, t. ex. *AADDS-contoso.com-NSG*.
1. På sidan **Översikt** visas befintliga inkommande och utgående säkerhets regler.

    Granska reglerna för inkommande och utgående trafik och jämför med listan över nödvändiga regler i föregående avsnitt. Om det behövs markerar du och tar bort eventuella anpassade regler som blockerar nödvändig trafik. Om någon av de nödvändiga reglerna saknas lägger du till en regel i nästa avsnitt.

    När du har lagt till eller tagit bort regler för att tillåta den nödvändiga trafiken uppdateras Azure AD DS-hanterad domän hälsa automatiskt inom två timmar och aviseringen tas bort.

### <a name="add-a-security-rule"></a>Lägg till en säkerhets regel

Slutför följande steg för att lägga till en säkerhets regel som saknas:

1. Sök efter och välj **nätverks säkerhets grupper**i Azure Portal.
1. Välj den nätverks säkerhets grupp som är kopplad till din hanterade domän, t. ex. *AADDS-contoso.com-NSG*.
1. Under **Inställningar** i den vänstra panelen klickar du på *inkommande säkerhets regler* eller *utgående säkerhets regler* , beroende på vilken regel du behöver lägga till.
1. Välj **Lägg till**och skapa sedan den nödvändiga regeln baserat på porten, protokollet, riktningen osv. När du är klar väljer du **OK**.

Det tar en stund för säkerhets regeln att läggas till och visas i listan.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
