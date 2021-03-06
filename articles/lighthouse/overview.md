---
title: Vad är Azure Lighthouse?
description: Med Azure Lighthouse kan tjänst leverantörer leverera hanterade tjänster för sina kunder med högre automatisering och effektivitet i stor skala.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 0551bbcbc9609809b0b28eb56a0a3b848debcaae
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132188"
---
# <a name="what-is-azure-lighthouse"></a>Vad är Azure Lighthouse?

Azure Lighthouse erbjuder tjänste leverantörer ett enda kontroll plan för att visa och hantera Azure i alla sina kunder med högre automatisering, skalbarhet och förbättrad styrning. Med Azure Lighthouse kan tjänst leverantörer leverera hanterade tjänster med hjälp av omfattande och robusta hanterings verktyg som är inbyggda i Azure-plattformen. Det här erbjudandet kan också dra nytta av företagets IT-organisationer och hantera resurser över flera klienter.

![Översikts diagram över Azure-Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Fördelar

Azure Lighthouse hjälper dig att effektivt skapa och leverera hanterade tjänster för dina kunder. Fördelarna är:

- **Hantering i skala**: kund engagemang och livs cykel åtgärder för att hantera kund resurser är enklare och mer skalbar.
- **Bättre synlighet och precision för kunder**: kunder vars resurser du hanterar har större insyn i dina åtgärder och exakt kontroll över omfattningen som de delegerar för hantering, medan IP-adressen bevaras.
- **Omfattande och enhetlig plattforms verktyg**: vår verktygs upplevelse behandlar scenarier för nyckel tjänst leverantörer, inklusive flera licensierings modeller som EA, CSP och betala per användning. De nya funktionerna fungerar med befintliga verktyg och API: er, licensierings modeller och partner program som [Cloud Solution Provider-program (CSP)](https://docs.microsoft.com/partner-center/csp-overview). De Azure Lighthouse-alternativ som du väljer kan integreras i dina befintliga arbets flöden och program, och du kan spåra din påverkan på kund engagemang genom [att länka ditt partner-ID](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

Det finns inga ytterligare kostnader för att använda Azure-Lighthouse för att hantera kundernas Azure-resurser.

## <a name="capabilities"></a>Funktioner

Azure Lighthouse innehåller flera sätt att effektivisera kund engagemang och-hantering:

- **Azure-delegerad resurs hantering**: hantera dina kunders Azure-resurser på ett säkert sätt i din klient, utan att behöva byta kontext-och kontroll plan. Mer information finns i [Azure delegerad resurs hantering](./concepts/azure-delegated-resource-management.md).
- **Nya Azure Portal upplevelser**: Visa information om flera innehavare på sidan nya **mina kunder** i [Azure Portal](https://portal.azure.com). På ett motsvarande **Service Provider-** blad kan dina kunder se och hantera åtkomst till tjänst leverantören. Mer information finns i [Visa och hantera kunder](./how-to/view-manage-customers.md) och [Visa och hantera tjänst leverantörer](./how-to/view-manage-service-providers.md).
- **Azure Resource Manager mallar**: utför hanterings uppgifter enklare, inklusive onboarding-kunder för Azure-delegerad resurs hantering. Mer information finns i våra [exempel lagrings platsen](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates) och [publicera en kund till Azure-delegerad resurs hantering](how-to/onboard-customer.md).
- **Hanterade tjänster erbjuder på Azure Marketplace**: erbjud dina tjänster till kunder via privata eller offentliga erbjudanden och låt dem automatiskt registreras i Azure delegerad resurs hantering, som ett alternativ till att registrera sig med hjälp av Azure Resource Manager mallar. Mer information finns [i erbjudanden om hanterade tjänster på Azure Marketplace](./concepts/managed-services-offers.md).
- **Azure-hanterade program**: paketera och leverera program som är enkla för dina kunder att distribuera och använda i sina egna prenumerationer. Programmet distribueras till en resurs grupp som du kommer åt från din klient organisation, så att du kan hantera tjänsten som en del av den övergripande Azure Lighthouse-upplevelsen. Mer information finns i [Översikt över Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurshantering](concepts/azure-delegated-resource-management.md).
- Lär dig mer om [hanterings upplevelser mellan flera innehavare](concepts/cross-tenant-management-experience.md).
