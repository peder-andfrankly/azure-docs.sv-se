---
title: Så här använder du rollbaserad Access Control i Azure API Management | Microsoft Docs
description: Lär dig hur du använder de inbyggda rollerna och skapa anpassade roller i Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: e33d981429f0e79accbe47ea0edea5f3c7a2157b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072215"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Använda rollbaserad Access Control i Azure API Management

Azure API Management förlitar sig på Azure rollbaserad Access Control (RBAC) för att möjliggöra detaljerad åtkomst hantering för API Management tjänster och entiteter (t. ex. API: er och principer). Den här artikeln ger en översikt över de inbyggda och anpassade rollerna i API Management. Mer information om åtkomst hantering i Azure Portal finns i [Kom igång med åtkomst hantering i Azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Inbyggda roller

API Management tillhandahåller för närvarande tre inbyggda roller och lägger till ytterligare två roller inom en snar framtid. Dessa roller kan tilldelas i olika omfång, inklusive prenumeration, resurs grupp och individ API Management instans. Om du till exempel tilldelar rollen "Azure API Management Service Reader" till en användare på resurs grupps nivå, har användaren Läs behörighet till alla API Management instanser i resurs gruppen. 

Följande tabell innehåller korta beskrivningar av de inbyggda rollerna. Du kan tilldela dessa roller med hjälp av Azure Portal eller andra verktyg, inklusive Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)och [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Mer information om hur du tilldelar inbyggda roller finns i [använda roll tilldelningar för att hantera åtkomst till dina Azure-prenumerations resurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Role          | Läs behörighet<sup>[1]</sup> | Skriv åtkomst<sup>[2]</sup> | Skapa, ta bort, skala, VPN och anpassad domän konfiguration för tjänsten | Åtkomst till den äldre Publisher-portalen | Beskrivning
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Azure API Management Service Contributor | ✓ | ✓ | ✓ | ✓ | Super User. Har fullständig CRUD åtkomst till API Management tjänster och entiteter (till exempel API: er och principer). Har åtkomst till den äldre Publisher-portalen. |
| Azure API Management Service Reader | ✓ | | || Har skrivskyddad åtkomst till API Management tjänster och entiteter. |
| Azure API Management tjänst operatör | ✓ | | ✓ | | Kan hantera API Management tjänster, men inte entiteter.|
| Azure API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Kan hantera API Management entiteter, men inte tjänster.|
| Innehålls hanterare för Azure API Management<sup>*</sup> | ✓ | | | ✓ | Kan hantera Developer-portalen. Skrivskyddad åtkomst till tjänster och entiteter.|

<sup>[1] Läs åtkomst till API Management tjänster och entiteter (till exempel API: er och principer).</sup>

<sup>[2] skriv åtkomst till API Management tjänster och entiteter, förutom följande åtgärder: skapa, ta bort och skala för instanser. VPN-konfiguration; och anpassad domän konfiguration.</sup>

<sup>\*Rollen tjänst redigerare är tillgänglig när vi har migrerat alla administratörs gränssnitt från den befintliga Publisher-portalen till Azure Portal. Rollen innehålls hanterare blir tillgänglig när utgivar portalen har omarbetats för att endast innehålla funktioner relaterade till hantering av Developer-portalen.</sup>  

## <a name="custom-roles"></a>Anpassade roller

Om ingen av de inbyggda rollerna uppfyller dina speciella behov kan anpassade roller skapas för att ge mer detaljerad åtkomst hantering för API Management entiteter. Du kan till exempel skapa en anpassad roll som har skrivskyddad åtkomst till en API Management-tjänst, men bara har Skriv behörighet till ett speciellt API. Mer information om anpassade roller finns i [anpassade roller i Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> För att kunna se en API Management instans i Azure Portal måste en anpassad roll innehålla ```Microsoft.ApiManagement/service/read``` åtgärden.

När du skapar en anpassad roll är det enklare att starta med en av de inbyggda rollerna. Redigera attributen för att lägga till **åtgärder**, **NotActions**eller **AssignableScopes**och spara sedan ändringarna som en ny roll. Följande exempel börjar med rollen "Azure API Management Service Reader" och skapar en anpassad roll med namnet "kalkylator-API-redigerare". Du kan tilldela den anpassade rollen till ett angivet API. Den här rollen har därför bara åtkomst till detta API. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Artikeln [Azure Resource Manager Resource Provider Operations](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) innehåller en lista över behörigheter som kan beviljas på API Management nivå.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Nästa steg

Mer information om rollbaserad Access Control i Azure finns i följande artiklar:
  * [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
  * [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md)
  * [Anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Åtgärder för Azure Resource Manager Resource Provider](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)