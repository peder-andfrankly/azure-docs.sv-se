---
title: Lista neka tilldelningar för Azure-resurser med hjälp av REST-API – Azure | Microsoft Docs
description: Lär dig hur du listar neka tilldelningar för användare, grupper och program med hjälp av rollbaserad åtkomstkontroll (RBAC) för Azure-resurser och REST API.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0bc49456f5965846a2de542b4a063bab2d1838bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118290"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Lista neka tilldelningar för Azure-resurser med hjälp av REST-API

[Neka tilldelningar](deny-assignments.md) blockera användare från att utföra åtgärder för specifika Azure-resurs, även om en rolltilldelning ger dem åtkomst. Den här artikeln beskriver hur du listar neka tilldelningar med hjälp av REST-API.

> [!NOTE]
> Du kan inte direkt skapa en egen neka tilldelningar. Information om hur neka tilldelningar har skapats, finns i [neka tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill ha information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read` behörigheter, som ingår i de flesta [inbyggda roller för Azure-resurser](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Lista över en enda neka tilldelning

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. I URI: N, Ersätt *{omfång}* med den omfattning som du vill ta med neka-tilldelningar.

    | Scope | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Ersätt *{neka-tilldelning-id}* med neka tilldelning identifierare som du vill hämta.

## <a name="list-multiple-deny-assignments"></a>Lista över flera neka tilldelningar

1. Börja med någon av följande begäranden:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Med valfria parametrar:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. I URI: N, Ersätt *{omfång}* med den omfattning som du vill ta med neka-tilldelningar.

    | Scope | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Ersätt *{filter}* med villkor som du vill använda för att filtrera listan över nekade tilldelningen.

    | Filter | Beskrivning |
    | --- | --- |
    | (inget filter) | Lista över alla neka tilldelningar på, över och under det angivna omfånget. |
    | `$filter=atScope()` | Lista neka tilldelningar för endast det angivna omfånget och senare. Inkluderar inte neka tilldelningar på subscopes. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Lista neka tilldelningar med det angivna namnet. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lista neka tilldelningar vid rotscopet (/)

1. Utöka din behörighet enligt beskrivningen i [utöka behörighet för Global administratör i Azure Active Directory](elevate-access-global-admin.md).

1. Använd följande begäran:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Ersätt *{filter}* med villkor som du vill använda för att filtrera listan över nekade tilldelningen. Ett filter måste anges.

    | Filter | Beskrivning |
    | --- | --- |
    | `$filter=atScope()` | Lista neka tilldelningar för endast rotscopet. Inkluderar inte neka tilldelningar på subscopes. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Lista neka tilldelningar med det angivna namnet. |

1. Ta bort utökad åtkomst.

## <a name="next-steps"></a>Nästa steg

- [Förstå neka tilldelningar för Azure-resurser](deny-assignments.md)
- [Utöka behörighet för global administratör i Azure Active Directory](elevate-access-global-admin.md)
- [Azure REST API-referens](/rest/api/azure/)
