---
title: Felsöka problem med inloggning för Azure-prenumeration
description: Hjälper till att lösa de problem som gör att du inte kan logga in på Azure-portalen eller Azure-kontocentret.
services: azure
author: v-miegge
manager: dcscontentpm
editor: na
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 7b467e42553d992231f8f5d45afcff37f077a9d2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223398"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Felsöka problem med inloggning för Azure-prenumeration

Den här guiden hjälper till att lösa de problem som gör att du inte kan logga in på Azure-portalen eller Azure-kontocentret. 

## <a name="issues"></a>Problem

### <a name="page-hangs-in-the-loading-status"></a>Sidan låser sig under inläsningen

Om sidan i webbläsaren låser sig provar du vart och ett av följande steg tills du kommer till Azure-portalen.

- Uppdatera sidan.
- Använd en annan webbläsare.
- Använd privat surfläge för webbläsaren. För Internet Explorer: Klicka på**Verktyg** > **Säkerhet** > **InPrivate-surfning** och gå till och logga in på[Azure-portalen](https://portal.azure.com/) eller [Azure-kontocentret](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Du loggas automatiskt in som en annan användare

Det här problemet kan uppstå om du använder mer än ett användarkonto i en webbläsare.

Försök att lösa problemet på något av följande sätt:

- Rensa cacheminnet och ta bort Internet-cookies. I Internet Explorer klickar du på **Verktyg** > **Internetalternativ** > **Ta bort**. Kontrollera att kryssrutorna för temporära filer, cookies, lösenord och webbläsarhistorik är markerade och klicka sedan på ta bort.
- Återställ Internet Explorer-inställningarna för att återställa alla personliga inställningar som du har gjort. Klicka på **Verktyg** > **Internetalternativ** > **Avancerat** > välj rutan **Ta bort personliga inställningar** > **Återställ**.
- Använd privat surfläge för webbläsaren. För Internet Explorer:  Klicka på**Verktyg** > **Säkerhet** > **InPrivate-surfning** och gå till och logga in på[Azure-portalen](https://portal.azure.com/) eller [Azure-kontocentret](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Jag kan logga in men det står *Inga prenumerationer hittades*

Det här problemet uppstår om du har valt fel katalog eller om ditt konto inte har tillräcklig behörighet.

**Scenario 1:** Felmeddelande visas i [Azure-portalen](https://portal.azure.com/)

Så här åtgärdar du problemet:

- Se till att rätt Azure-katalog har valts genom att klicka på ditt konto längst uppe till höger.
- Om rätt Azure-katalog har valts men du fortfarande får felmeddelandet ska du [lägga till ditt konto som ägare](billing-add-change-azure-subscription-administrator.md).

**Scenario 2:** Felmeddelande visas i [Azure-kontocentret](https://account.windowsazure.com/Subscriptions)

Kontrollera om det konto som du använde är kontoadministratören. Så här kontrollerar du vem som är kontoadministratör:

1. Logga in i [vyn Prenumerationer i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Välj den prenumeration du vill kontrollera och gå till **Inställningar**.

3. Välj **Egenskaper**. Prenumerationens kontoadministratör visas i rutan **Kontoadministratör**.

## <a name="additional-help-resources"></a>Fler hjälpresurser

Här är några fler felsökningsartiklar som gäller fakturering och prenumerationer i Azure

- [Nekat kort](billing-troubleshoot-declined-card.md)
- [Problem med prenumerationsregistrering](billing-troubleshoot-azure-sign-up.md)
- [Inga prenumerationer hittades](billing-no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-fakturering](index.md)
