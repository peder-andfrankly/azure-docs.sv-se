---
title: Faser i en skissdistribution
description: Läs om säkerhets-och artefakt relaterade steg som Azure Blueprints tjänsterna går igenom när du skapar en skiss tilldelning.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: 4c1d0cd47e0f43b73e3178e18a4ba5d705048a72
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463563"
---
# <a name="stages-of-a-blueprint-deployment"></a>Faser i en skissdistribution

När en skiss distribueras vidtas en serie åtgärder av tjänsten Azure-ritningar för att distribuera de resurser som definierats i skissen. Den här artikeln innehåller information om vad varje steg omfattar.

Skiss distribution utlöses genom att tilldela en skiss till en prenumeration eller [Uppdatera en befintlig tilldelning](../how-to/update-existing-assignments.md). Under distributionen utför ritningar följande övergripande steg:

> [!div class="checklist"]
> - Skisser som beviljats ägar rättigheter
> - Skiss tilldelnings objekt skapas
> - Valfria-ritningar skapar **systemtilldelad** hanterad identitet
> - Den hanterade identiteten distribuerar skiss artefakter
> - Skiss tjänst och **systemtilldelade** hanterade identitets rättigheter återkallas

## <a name="blueprints-granted-owner-rights"></a>Skisser som beviljats ägar rättigheter

Tjänstens huvud namn för Azure-ritningar beviljas ägar rättigheter till den tilldelade prenumerationen eller prenumerationen när en [systemtilldelad hanterad identitets](../../../active-directory/managed-identities-azure-resources/overview.md) hanterad identitet används. Den beviljade rollen gör det möjligt för skisser att skapa och senare återkalla den **systemtilldelade** hanterade identiteten. Om du använder en **användardefinierad** hanterad identitet, så får inte tjänstens huvud namn för Azure-ritningar och saknar ägar rättigheter för prenumerationen.

Rättigheterna beviljas automatiskt om tilldelningen görs via portalen. Men om tilldelningen görs via REST API måste beviljandet av rättigheterna göras med ett separat API-anrop. Azure Blueprint AppId är `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, men tjänstens huvud namn varierar beroende på klient. Använd [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) och rest Endpoint [service princip ALS](/graph/api/resources/serviceprincipal) för att hämta tjänstens huvud namn. Sedan tilldelar du Azure-resurserna _ägar_ rollen via [portalen](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md)eller en [Resource Manager-mall](../../../role-based-access-control/role-assignments-template.md).

Tjänsten skisser distribuerar inte resurserna direkt.

## <a name="the-blueprint-assignment-object-is-created"></a>Skiss tilldelnings objekt skapas

En användare, grupp eller tjänstens huvud namn tilldelar en skiss till en prenumeration. Tilldelnings objekt finns på den prenumerations nivå där skissen tilldelades. Resurser som skapas av distributionen sker inte i samband med distributionen av entiteten.

När du skapar skiss tilldelningen väljs typen av [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md) . Standardvärdet är en hanterad identitet som **tilldelats av systemet** . Du kan välja en **användare som tilldelats** en hanterad identitet. När du använder en **användardefinierad** hanterad identitet måste den definieras och beviljas behörighet innan skiss tilldelningen skapas. Både den inbyggda rollen [ägare](../../../role-based-access-control/built-in-roles.md#owner) och [skiss](../../../role-based-access-control/built-in-roles.md#blueprint-operator) har nödvändig `blueprintAssignment/write` behörighet att skapa en tilldelning som använder en **användardefinierad** hanterad identitet.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Valfria-ritningar skapar systemtilldelad hanterad identitet

När [systemtilldelad hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md) väljs under tilldelningen skapar ritningar identiteten och beviljar den hanterade identiteten [ägar](../../../role-based-access-control/built-in-roles.md#owner) rollen. Om en [befintlig tilldelning uppgraderas](../how-to/update-existing-assignments.md)använder ritningar den tidigare skapade hanterade identiteten.

Den hanterade identiteten som är relaterad till skiss tilldelningen används för att distribuera eller distribuera om de resurser som definierats i skissen. Den här designen förhindrar tilldelningar oavsiktligt störa varandra.
Den här designen stöder även [resurs låsnings](./resource-locking.md) funktionen genom att kontrol lera säkerheten för varje distribuerad resurs från skissen.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Den hanterade identiteten distribuerar skiss artefakter

Den hanterade identiteten utlöser sedan Resource Manager-distributionerna av artefakterna i skissen i den definierade [ordningsföljds ordningen](./sequencing-order.md). Ordningen kan justeras så att artefakter som är beroende av andra artefakter distribueras i rätt ordning.

Ett åtkomst problem av en distribution är ofta resultatet av åtkomst nivån som beviljats till den hanterade identiteten. Tjänsten ritningar hanterar säkerhets livs cykeln för den **systemtilldelade** hanterade identiteten. Användaren är dock ansvarig för att hantera rättigheterna och livs cykeln för en **användardefinierad** hanterad identitet.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Skiss tjänst och systemtilldelade hanterade identitets rättigheter återkallas

När distributionerna har slutförts återkallar-ritningar rättigheterna för den **systemtilldelade** hanterade identiteten från prenumerationen. Sedan återkallar tjänsten ritningar sina rättigheter från prenumerationen. Rights Removal förhindrar att ritningar blir permanenta ägare till en prenumeration.

## <a name="next-steps"></a>Nästa steg

- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).