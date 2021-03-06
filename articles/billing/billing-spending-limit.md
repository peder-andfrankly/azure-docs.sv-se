---
title: Utgiftsgräns för Azure | Microsoft Docs
description: I den här artikeln beskrivs hur en utgiftsgräns för Azure fungerar samt hur du tar bort den.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 0983e21e5ab2e895b2c12fe7cc45c5b9c9f0a405
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223578"
---
# <a name="azure-spending-limit"></a>Utgiftsgräns i Azure

Utgiftsgränsen i Azure gör att du inte kan spendera mer än ditt kreditbelopp. Alla nya kunder som registrerar sig för ett kostnadsfritt Azure-konto eller prenumerationer som innefattar krediter under flera månader har utgiftsgränsen aktiverad som standard. Utgiftsgränsen är lika med kreditbeloppet och kan inte ändras. Om du till exempel har registrerat dig för ett kostnadsfritt Azure-konto är din utgiftsgräns 200 USD och du kan inte ändra den till 500 USD. Dock kan du när som helst ta bort utgiftsgränsen. Så du har antingen ingen gräns eller så har du en gräns som motsvarar kreditbeloppet. Det här spärrar dig från de flesta typer av utgifter. Utgiftsgränsen är inte tillgänglig för prenumerationer med åtagandeplaner eller prissättning enligt Betala per användning. Mer information finns i den [fullständiga listan med typer av Azure-prenumerationer och tillgänglighet för utgiftsgränsen](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Uppnå en utgiftsgräns

När din användning leder till avgifter som överstiger utgiftsgränsen så inaktiveras de tjänster du har distribuerat under resten av faktureringsperioden.

När du till exempel spenderar all kredit som ingår i ett kostnadsfritt Azure-konto tas de Azure-resurser du har distribuerat bort från produktion och dina virtuella Azure-datorer stoppas och avallokeras. Data i dina lagringskonton är tillgängliga som skrivskyddade.

Om din prenumerationstyp omfattar krediter under flera månader aktiveras prenumerationen igen automatiskt i början av nästa faktureringsperiod. Sedan kan du distribuera om dina Azure-resurser och få fullständig åtkomst till dina lagringskonton och databaser.

Azure skickar e-postaviseringar när du når utgiftsgränsen. Logga in i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) för att se aviseringar om prenumerationer som har nått utgiftsgränsen.

Om du har ett kostnadsfritt Azure-konto och når utgiftsgränsen kan du uppgradera till att [betala per användning](billing-upgrade-azure-subscription.md) för att ta bort utgiftsgränsen och automatiskt aktivera prenumerationen på nytt.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-azure-portal"></a>Ta bort utgiftsgränsen i Azure-portalen

Du kan ta bort utgiftsgränsen när som helst så länge det finns en giltig betalningsmetod som är associerad med din Azure-prenumeration. För prenumerationstyper som har kredit under flera månader, som Visual Studio Enterprise och Visual Studio Professional, kan du också aktivera utgiftsgränsen i början av nästa faktureringsperiod.

Om du vill ta bort utgiftsgränsen följer du dessa anvisningar:

1. Logga in på [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj en prenumeration. Om du når utgiftsgränsen för prenumerationen inaktiveras den.
1. Välj **Ta bort utgiftsgräns** längst upp på sidan.
1. Välj ett alternativ som passar dig.

![Välja ett alternativ för att ta bort utgiftsgränsen](./media/billing-spending-limit/remove-spending-limit.PNG)

| Alternativ | Verkan |
| --- | --- |
| Ta bort utgiftsgränsen på obestämd tid | Tar bort utgiftsgränsen utan att den aktiveras automatiskt vid början av nästa faktureringsperiod. |
| Ta bort utgiftsgränsen för den aktuella faktureringsperioden | Tar bort utgiftsgränsen så att den återaktiveras automatiskt vid början av nästa faktureringsperiod. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Därför bör du kanske ta bort utgiftsgränsen

Utgiftsgränsen kan hindra dig från att distribuera eller använda vissa tjänster från tredje part och Microsoft-tjänster. Här följer några situationer där du bör ta bort utgiftsgränsen för din prenumeration.

-  Du planerar att distribuera avbildningar från första part som Oracle, eller tjänster som Azure DevOps Services. Den här situationen gör att du når upp till utgiftsgränsen nästan omedelbart och din prenumeration inaktiveras.
- Du har tjänster som du inte vill ska störas. När du når upp till utgiftsgränsen tas de Azure-resurser du har distribuerat bort från produktionen och dina virtuella Azure-datorer stoppas och avallokeras. Om du har tjänster där du vill undvika avbrott måste du ta bort utgiftsgränsen.
- Du har tjänster och resurser med inställningar, till exempel virtuella IP-adresser, som du inte vill förlora. Dessa inställningar förloras när du når upp till utgiftsgränsen och tjänsterna och resurserna avallokeras.

## <a name="turn-on-the-spending-limit-after-removing"></a>Aktivera utgiftsgränsen efter borttagning

Den här funktionen är bara tillgänglig när du har tagit bort utgiftsgränsen tills vidare för prenumerationstyper med krediter under flera månader. Du kan använda den här funktionen till att aktivera utgiftsgränsen automatiskt i början av nästa faktureringsperiod.

1. Logga in på [Kontocenter](https://account.windowsazure.com/Subscriptions).
1. Klicka på den gula banderollen för att ändra alternativet för utgiftsgräns.
1. Välj **Aktivera utgiftsgränsen under nästa faktureringsperiod \<startdatum för faktureringsperioden\>**

## <a name="custom-spending-limit"></a>Anpassad utgiftsgräns

Anpassade utgiftsgränser är inte tillgängliga.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>En utgiftsgräns förhindrar inte alla typer av utgifter

[Vissa externa tjänster som publicerats på Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) kan inte användas med dina prenumerationskrediter, och kan medföra separata avgifter även när din utgiftsgräns har angetts. Exempel är Visual Studio-licenser, Azure Active Directory Premium, supportavtal och de flesta varumärkestjänster från tredje part. När du etablerar en ny extern tjänst visas en varning om att tjänsterna faktureras separat:

![Marketplace-inköpsvarning](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Uppgradera till en plan med [Betala per användning-priser](billing-upgrade-azure-subscription.md).
