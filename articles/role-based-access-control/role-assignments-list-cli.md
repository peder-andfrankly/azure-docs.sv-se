---
title: Lista roll tilldelningar med Azure RBAC och Azure CLI
description: Lär dig hur du avgör vilka resurser användare, grupper, tjänstens huvud namn eller hanterade identiteter har åtkomst till med hjälp av rollbaserad åtkomst kontroll (RBAC) och Azure CLI i Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1a58d2170ec1107222f0e37e432063af23743e42
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710443"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Lista roll tilldelningar med Azure RBAC och Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] den här artikeln beskriver hur du visar roll tilldelningar med hjälp av Azure CLI.

## <a name="prerequisites"></a>Krav

- [Bash i Azure Cloud Shell](/azure/cloud-shell/overview) eller [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

Om du vill visa roll tilldelningarna för en speciell användare använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Som standard visas endast direkta tilldelningar som är begränsade till prenumerationen. Om du vill visa tilldelningar som omfattas av resurs eller grupp använder du `--all` och för att Visa ärvda tilldelningar, använder du `--include-inherited`.

I följande exempel visas roll tilldelningarna som tilldelas direkt till patlong- *\@contoso.com* -användare:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>Visa rolltilldelningar för en resursgrupp

Om du vill visa en lista över roll tilldelningar som finns i ett resurs grupps omfång använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

I följande exempel visas roll tilldelningarna för resurs gruppen *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Lista med rolltilldelningar för en prenumeration

Om du vill visa en lista över alla roll tilldelningar i ett prenumerations omfång använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list). Om du vill hämta prenumerations-ID: t kan du hitta det på bladet **prenumerationer** i Azure Portal eller så kan du använda [konto listan AZ](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Lista roll tilldelningar för en hanterings grupp

Om du vill visa alla roll tilldelningar i ett hanterings grupps omfång använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list). Om du vill hämta ID för hanterings grupp kan du hitta det på bladet **hanterings grupper** i Azure Portal eller så kan du använda [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort roll tilldelningar med Azure RBAC och Azure CLI](role-assignments-cli.md)
