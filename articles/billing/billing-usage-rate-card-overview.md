---
title: Hämta Azure-användning med API:er för Azure-fakturering | Microsoft Docs
description: Lär dig mer om API:erna för Azure-fakturering och RateCard och få information om resursförbrukning och trender i Azure.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: a8e189e8070de0fccf9428e26337e1091ba7f7b4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225632"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Använd API:erna för Azure-fakturering för att få programmatisk insyn i din Azure-användning
Använd API:er för Azure-fakturering för att hämta användnings- och resursdata till önskat dataanalysverktyg. Azures API:er för resursanvändning och RateCard kan hjälpa dig att korrekt förutse och hantera dina kostnader. API:erna implementeras som en resursprovider och en del av familjen av API:er som exponeras av Azure Resource Manager.  

## <a name="azure-invoice-download-api-preview"></a>API för nedladdning av Azure-fakturor (förhandsversion)
När [registreringen är klar](billing-manage-access.md#opt-in) kan du ladda ned fakturor med [förhandsversionen av API:et för fakturor](/rest/api/billing). Här följer exempel på några funktioner:

* **Rollbaserad åtkomstkontroll i Azure** – Konfigurera åtkomstprinciper på [Azure-portalen](https://portal.azure.com) eller med [Azure PowerShell-cmdlets](/powershell/azure/overview) för att ange vilka användare eller program som kan komma åt prenumerationens användningsinformation. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till någon av rollerna Faktureringsläsare, Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en specifik Azure-prenumeration.
* **Datumfiltrering** – Använd `$filter`-parametern för att hämta alla fakturor i omvänd kronologisk ordning efter faktureringsperiodens slutdatum.

> [!NOTE]
> Den här funktionen är i den första förhandsversionen och den ändras på sätt som påverkar bakåtkompatibiliteten. För närvarande, är den inte tillgänglig för vissa prenumerationserbjudanden (EA, CSP, AIO stöds inte) och Azure Germany.

## <a name="azure-resource-usage-api-preview"></a>API för Azure-resursanvändning (förhandsversion)
Använd [API:et för Azure-resursanvändning](/previous-versions/azure/reference/mt219003(v=azure.100)) för att hämta information om din beräknade Azure-användning. API:et ger tillgång till:

* **Rollbaserad åtkomstkontroll i Azure** – Konfigurera åtkomstprinciper på [Azure-portalen](https://portal.azure.com) eller med [Azure PowerShell-cmdlets](/powershell/azure/overview) för att ange vilka användare eller program som kan komma åt prenumerationens användningsinformation. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till någon av rollerna Faktureringsläsare, Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en specifik Azure-prenumeration.
* **Aggregeringar per timme eller dag** – Anropare kan ange om de vill samla Azure-användningsdata i containrar för varje timme eller varje dag. Standardvärdet är varje dag.
* **Metadata för instanser (inklusive resurstaggar)** – Få information på instansnivå, t.ex. den fullständigt kvalificerade resurs-URI:n (/Subscriptions/{Subscription-ID}/..), resursgruppsinformation och resurstaggar. Med dessa metadata kan du deterministiskt och programmässigt allokera användning baserat på taggarna, exempelvis för scenarier med tvärdebitering.
* **Metadata för resurser** – Resursinformation som mätarnamn, mätarkategori, underkategori för mätare, enhet och region gör det enklare för anroparen att förstå vad som förbrukats. Vi arbetar också med att anpassa terminologin för resursmetadata mellan Azure-portalen, CSV-filen med Azure-användning, EA-fakturering och andra offentliga gränssnitt, så att du kan korrelera data mellan olika upplevelser.
* **Användning för olika typer av erbjudanden** – Användningsdata är tillgängliga för erbjudandetyper som Betala per användning, MSDN, utgiftsåtagande, penningkredit och EA, förutom [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>RateCard-API för Azure-resurser (förhandsversion)
Använd [RateCard-API:et för Azure-resurser](/previous-versions/azure/reference/mt219005(v=azure.100)) för att hämta en lista över tillgängliga Azure-resurser och information om beräknade priser för var och en. API:et ger tillgång till:

* **Rollbaserad åtkomstkontroll i Azure** – Konfigurera dina åtkomstprinciper på [Azure-portalen](https://portal.azure.com) eller med [Azure PowerShell-cmdlets](/powershell/azure/overview) för att ange vilka användare eller program som kan komma åt RateCard-informationen. Anropare måste använda Azure Active Directory-standardtoken för autentisering. Lägg till anroparen till Rollen Läsare, Ägare eller Deltagare för att få åtkomst till användningsdata för en viss Azure-prenumeration.
* **Stöd för erbjudandetyperna Betala per användning, MSDN, betalningsåtagande och penningkredit (EA och [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) stöds inte)** – Det här API:et tillhandahåller information på nivån för Azure-erbjudanden.  Anroparen för det här API:et måste skicka information om erbjudandet för att få resursinformation och priser. Vi kan för närvarande inte uppge EA-priser eftersom EA-erbjudanden har anpassade priser per registrering.

## <a name="scenarios"></a>Scenarier
Här är exempel på några av de scenarier som är möjliga med kombinationen av användnings- och RateCard-API:erna:

* **Azure-utgifter under månaden** – Använd användnings-API:et i kombination med RateCard-API:et för att få bättre inblick i dina molnkostnader under månaden. Du kan analysera den beräknade användningen och de beräknade kostnaderna efter timme eller dag.
* **Konfigurera aviseringar** – Använd användnings- och RateCard-API:erna för att visa den beräknade molnanvändningen och de beräknade molnkostnaderna och konfigurera resurs- eller penningbaserade aviseringar.
* **Förutse kostnaden** – Visa den beräknade molnanvändningen och de beräknade molnkostnaderna och använd maskininlärningsalgoritmer för att förutsäga vad kostnaden skulle bli i slutet av faktureringsperioden.
* **Kostnadsanalys före användning** – Använd RateCard-API:et för att förutse kostnaden för din förväntade användning när du flyttar dina arbetsbelastningar till Azure. Om du har befintliga arbetsbelastningar i andra moln eller privata moln kan du även mappa din användning med Azures priser för att få en bättre kostnadsuppskattning för Azure. Den här kostnadsuppskattningen ger dig möjlighet att pivotera erbjudandet och jämföra med andra erbjudanden än Betala per användning, till exempel Betalningsåtagande och Penningkredit. Med det här API:et kan du även se kostnadsskillnader per region och göra en konsekvensanalys som hjälper dig att fatta distributionsbeslut.
* **Konsekvensanalys** -

  * Du kan avgöra om det är mer kostnadseffektivt att köra arbetsbelastningar i en annan region eller med en annan konfiguration för Azure-resursen. Resurskostnaderna för Azure kan variera beroende på vilken Azure-region du använder.
  * Du kan också se om priset för en Azure-resurs är bättre med en annan typ av Azure-erbjudande.


## <a name="next-steps"></a>Nästa steg
* Kolla in kodexemplen på GitHub:
  * [Kodexempel för API för faktura](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Kodexempel för API för användning](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Kodexempel för API för RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Mer information om Azure Resource Manager finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
