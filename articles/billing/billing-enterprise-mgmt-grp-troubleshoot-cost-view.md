---
title: Felsöka vyer för Azure-företagskostnader
description: Lär dig hur du löser eventuella problem med vyer för organisationskostnader i Azure-portalen.
author: bandersmsft
manager: amberb
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 8266296bcf77a5889f6a747b990bd47f4d5ff252
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223931"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Felsöka vyer för företagskostnader

I företagsregistreringar finns det flera inställningar som kan göra att användarna i registreringen inte ser kostnader.  De här inställningarna hanteras av registreringsadministratören. Alternativt hanteras inställningarna av partnern om registreringen inte köps direkt via Microsoft.  Den här artikeln förklarar vad inställningarna är och hur de påverkar registreringen. Inställningarna är oberoende av Azure-roller för rollbaserad åtkomstkontroll (RBAC).

## <a name="enable-access-to-costs"></a>Aktivera åtkomst till kostnader

Visas meddelandet Obehörig eller *”Kostnadsvyer är inaktiverade i din registrering.”* när du letar efter kostnadsinformation?
![Skärmbild som visar ”Obehörig” i fältet Aktuell kostnad för prenumerationen.](media/billing-enterprise-mgmt-groups/unauthorized.png)

Det kan bero på någon av följande orsaker:

1. Du har köpt Azure via en företagspartner och partnern har inte publicerat någon prisinformation ännu. Kontakta din partner för att uppdatera prisinställningen i [Enterprise-portalen](https://ea.azure.com).
2. Om du är en direkt EA-kund finns det några möjligheter:
    * Du är kontoägare och registreringsadministratören har inaktiverat inställningen **Visa debiteringar för kontoägare**.  
    * Du är avdelningsadministratör och registreringsadministratören har inaktiverat inställningen **Visa debiteringar för avdelningsadministratörer**.
    * Kontakta registreringsadministratören för att få åtkomst. Registreringsadministratören kan uppdatera inställningarna i [Enterprise-portalen](https://ea.azure.com/manage/enrollment).

      ![Skärmbild som visar inställningarna i Enterprise-portal för visning av avgifter.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Tillgången är inte tillgänglig

Om du får felmeddelandet **Den här tillgången är inte tillgänglig** när du försöker få åtkomst till en prenumeration eller en hanteringsgrupp har du inte rätt roll för att visa det objektet.  

![Skärmbild som visar meddelandet ”Tillgången är inte tillgänglig”.](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Be administratören för din Azure-prenumeration eller hanteringsgrupp om att få åtkomst. Mer information finns i [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg
- Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
