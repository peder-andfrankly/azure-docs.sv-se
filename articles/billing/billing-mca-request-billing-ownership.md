---
title: Få faktureringsägarskap för Azure-prenumerationer
description: Lär dig hur du begär faktureringsägarskap för Azure-prenumerationer från andra användare.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 2ac2d552aed5b11eaf5a8e4708b2cabc33ba1bb3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223813"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Få faktureringsägarskap för Azure-prenumerationer från andra konton

Du vill kanske ta över ägarskapet för Azure-prenumerationer om den befintliga faktureringsägaren lämnar din organisation eller om du vill betala för prenumerationerna via ditt faktureringskonto. Om du tar över ägarskapet överförs faktureringsansvar för prenumerationer till ditt konto.

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-for-access).

För att kunna begära faktureringsägarskap måste du vara **fakturaavsnittsägare** eller **fakturaavsnittsdeltagare**. Mer information finns i [uppgifter för roller i fakturaavsnitt](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Begära faktureringsägarskap

1. Logga in på [Azure-portalen](https://portal.azure.com) som fakturaavsnittsägare eller -deltagare för ett faktureringskonto för Microsoft-kundavtal.

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar sökning i Azure-portalen efter kostnadshantering och fakturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Välj **Fakturaavsnitt** på den vänstra sidan. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en faktureringsprofil. Från faktureringskontot eller -profilen väljer du **Fakturaavsnitt**.
   
   ![Skärmbild som visar val av fakturaavsnitt](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Välj ett fakturaavsnitt i listan. När du tar över ägarskapet för prenumerationerna faktureras de till det här fakturaavsnittet.

5. Välj **Överföringsbegäranden** på den nedre vänstra sidan och välj sedan **Lägg till**.
 
   ![Skärmbild som visar val av överföringsbegäranden](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Ange e-postadressen till den användare som du begär faktureringsägarskap från. Användaren måste vara kontoadministratör på ett faktureringskonto för Microsoft Online Service-program eller kontoägare för ett Enterprise-avtal. Mer information finns i [visa dina faktureringskonton i Azure-portalen](billing-view-all-accounts.md). Välj **Skicka överföringsbegäran**.

   ![Skärmbild som visar sändning av en överföringsbegäran](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. Användaren får ett e-postmeddelande med instruktioner för att granska din överföringsförfrågan.

   ![Skärmbild som visar granskning av e-postmeddelande med överföringsbegäran](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. För att godkänna överföringsbegäran väljer användaren länken i e-postmeddelandet och följer anvisningarna.

    ![Skärmbild som visar granskning av e-postmeddelande med överföringsbegäran](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Kontrollera statusen för överföringsbegäran

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar sökning i Azure-portalen efter kostnadshantering och fakturering](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Välj **Fakturaavsnitt** på den vänstra sidan. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en faktureringsprofil. Från faktureringskontot eller -profilen väljer du **Fakturaavsnitt**.
   
   ![Skärmbild som visar val av fakturaavsnitt](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Välj det fakturaavsnitt i listan som du skickade överföringsbegäran för.

5. Välj **Överföringsbegäranden** på den nedre vänstra sidan. På sidan Överföringsbegäranden visas följande information:

    ![Skärmbild som visar lista med överföringsbegäranden](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Kolumn|Definition|
   |---------|---------|
   |Datum för begäran|Det datum då överföringsbegäran skickades|
   |Mottagare|E-postadressen för den användare till vilken du skickade begäran om att överföra faktureringsägarskapet|
   |Förfallodatum|Det datum då begäran upphör att gälla|
   |Status|Status för överföringsbegäran|

    Överföringsbegäran kan ha en av följande statusar:

   |Status|Definition|
   |---------|---------|
   |Pågår|Användaren har inte accepterat överföringsbegäran|
   |Bearbetar|Användaren har accepterat överföringsbegäran. Fakturering för prenumerationer som användaren valde överförs till ditt fakturaavsnitt|
   |Slutfört| Fakturering för prenumerationer som användaren valde har överförts till ditt fakturaavsnitt|
   |Slutförd med fel|Begäran slutfördes men faktureringen för vissa prenumerationer som användaren valde kunde inte överföras|
   |Upphörd|Användaren accepterade inte begäran i tid, och den upphörde|
   |Avbrutna|Någon med åtkomst till överföringsbegäran avbröt begäran|
   |Nekad|Användaren nekade överföringsbegäran|

7. Välj en överföringsbegäran för att visa information. På sidan för överföringsbegäran visas följande information:
   
   ![Skärmbild som visar en lista med överförda prenumerationer](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Kolumn  |Definition|
   |---------|---------|
   |ID för överföringsbegäran|Unikt ID för din överföringsbegäran. Om du skickar en supportbegäran kan du uppge ID:t för Azure-supporten för att påskynda supportbegäran|
   |Överföringen begärdes den|Det datum då överföringsbegäran skickades|
   |Överföringen begärdes av|E-postadressen för den användare som skickade överföringsbegäran|
   |Överföringsbegäran upphör den| Det datum då överföringsbegäran upphör|
   |Mottagarens e-postadress|E-postadressen för den användare till vilken du skickade begäran om att överföra faktureringsägarskapet|
   |Överföringslänk som skickats till mottagaren|Den URL som skickades till användaren för granskning av överföringsbegäran|

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Du kan begära faktureringsägarskap för de prenumerationstyper som anges nedan.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure i öppen licensiering](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Sponsring av Azure-pass](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure-plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure-sponsrat erbjudande](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise-prenumeranter (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise-prenumeranter (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Eventuell kredit som är tillgänglig i prenumerationen kommer inte att vara tillgänglig i det nya kontot efter överföringen.

\*\* Stöds endast för prenumerationer i konton som skapas vid registrering på Azure-webbplatsen.


## <a name="additional-information"></a>Ytterligare information

Följande avsnitt innehåller ytterligare information om överföring av prenumerationer.

### <a name="no-service-downtime"></a>Inget avbrott i tjänsten

Azure-tjänster i prenumerationen fortsätter att köras utan avbrott. Vi överför endast faktureringsrelationen för de Azure-prenumerationer som användaren väljer att överföra.

### <a name="disabled-subscriptions"></a>Inaktiverade prenumerationer

Inaktiverade prenumerationer kan inte överföras. Prenumerationer måste vara i aktivt tillstånd för att deras faktureringsägarskap ska överföras.

### <a name="azure-resources-transfer"></a>Överföring av Azure-resurser

Alla resurser från prenumerationer såsom virtuella datorer, diskar och webbplatser överförs.

### <a name="azure-marketplace-products-transfer"></a>Överföring av Azure Marketplace-produkter

Azure Marketplace-produkter överförs tillsammans med sina respektive prenumerationer.

### <a name="azure-reservations-transfer"></a>Överföring av Azure-reservationer

Azure-reservationer flyttas inte automatiskt med prenumerationer. [Kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att flytta reservationer.

### <a name="access-to-azure-services"></a>Åtkomst till Azure-tjänster

Åtkomst för befintliga användare, grupper eller tjänsthuvudnamn som tilldelades med hjälp av (Azure RBAC (rollbaserad åtkomstkontroll))[../role-based-access-control/overview.md] påverkas inte under övergången.

### <a name="azure-support-plan"></a>Azure-supportplan

Azure-support överförs inte med prenumerationerna. Om användaren överför alla Azure-prenumerationer ber du den att säga upp sin supportplan.

### <a name="charges-for-transferred-subscription"></a>Avgifter för överförd prenumeration

Den ursprungliga faktureringsägaren för prenumerationerna ansvarar för eventuella avgifter som rapporterades fram till den tidpunkt då överföringen slutfördes. Ditt fakturaavsnitt ansvarar för avgifter som rapporteras från och med tiden för överföring. Det kan finnas en vissa avgifter som ägde rum före överföringen men som rapporterades efteråt. De här avgifterna visas i ditt fakturaavsnitt.

### <a name="cancel-a-transfer-request"></a>Avbryta en överföringsbegäran

Du kan avbryta överföringsbegäran tills begäran har godkänts eller nekats. Om du vill avbryta överföringsbegäran går du till [sidan med överföringsinformation](#check-the-transfer-request-status) och väljer Avbryt längst ned på sidan.

### <a name="software-as-a-service-saas-transfer"></a>Överföring av SaaS (Software as a Service)

SaaS-produkter överförs inte med prenumerationerna. Be användaren att [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att överföra faktureringsägarskap för SaaS-produkter. Utöver faktureringsägarskapet kan användaren även överföra resursägarskapet. Med resursägarskap kan du utföra hanteringsåtgärder som att ta bort och visa information om produkten. Användaren måste vara resursägare för SaaS-produkten för att överföra resursägarskap.

## <a name="check-for-access"></a>Kontrollera åtkomst
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- Faktureringsägarskapet för Azure-prenumerationerna överförs till ditt fakturaavsnitt. Håll koll på avgifterna för dessa prenumerationer i [Azure-portalen](https://portal.azure.com).
- Ge andra behörigheter att visa och hantera fakturering för dessa prenumerationer. Mer information finns i [Roller och uppgifter för fakturaavsnitt](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
