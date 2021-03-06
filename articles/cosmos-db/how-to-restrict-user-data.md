---
title: Begränsa användar åtkomsten till data åtgärder endast med Azure Cosmos DB
description: Lär dig hur du begränsar åtkomsten till data åtgärder med Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980379"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Begränsa användar åtkomsten till endast data åtgärder

I Azure Cosmos DB finns det två sätt att autentisera dina interaktioner med databas tjänsten:
- Använd Azure Active Directory identitet när du interagerar med Azure Portal,
- använda Azure Cosmos DB [nycklar](secure-access-to-data.md#master-keys) eller [resurs-token](secure-access-to-data.md#resource-tokens) vid utfärdande av anrop från API: er och SDK: er.

Varje autentiseringsmetod ger åtkomst till olika uppsättningar med åtgärder, med vissa överlappningar: ![uppdelning av åtgärder per autentiseringstyp](./media/how-to-restrict-user-data/operations.png)

I vissa fall kanske du vill begränsa vissa användare av organisationen för att utföra data åtgärder (som endast är CRUD-begäranden och frågor). Detta är vanligt vis fallet för utvecklare som inte behöver skapa eller ta bort resurser eller ändra det etablerade data flödet för de behållare som de arbetar med.

Du kan begränsa åtkomsten genom att använda följande steg:
1. Skapa en anpassad Azure Active Directory roll för de användare som du vill begränsa åtkomsten till. Den anpassade Active Directorys rollen ska ha detaljerad åtkomst nivå för åtgärder som använder Azure Cosmos DBs [detaljerade åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb).
1. Otillåten körning av icke-data åtgärder med nycklar. Du kan åstadkomma detta genom att begränsa åtgärderna till endast Azure Resource Manager samtal.

Nästa avsnitt i den här artikeln visar hur du utför de här stegen.

> [!NOTE]
> För att kunna köra kommandona i nästa avsnitt måste du installera Azure PowerShell Module 3.0.0 eller senare, samt [rollen Azure-ägare](../role-based-access-control/built-in-roles.md#owner) för den prenumeration som du försöker ändra.

I PowerShell-skripten i nästa avsnitt ersätter du följande plats hållare med värden som är speciella för din miljö:
- `$MySubscriptionId`-det prenumerations-ID som innehåller det Azure Cosmos-konto där du vill begränsa behörigheterna. Till exempel: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName` – resurs gruppen som innehåller Azure Cosmos-kontot. Till exempel: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName` – namnet på ditt Azure Cosmos-konto. Till exempel: `mycosmosdbsaccount`.
- `$MyUserName`-inloggningen (username@domain) för den användare som du vill begränsa åtkomsten för. Till exempel: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Välj din Azure-prenumeration

Azure PowerShell-kommandon kräver att du loggar in och väljer den prenumeration som ska köra kommandona:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Skapa rollen för anpassad Azure Active Directory

Följande skript skapar en Azure Active Directory roll tilldelning med "endast nyckel"-åtkomst för Azure Cosmos-konton. Rollen baseras på [anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md) och [detaljerade åtgärder för Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). De här rollerna och åtgärderna är en del av `Microsoft.DocumentDB` Azure Active Directory-namnområdet.

1. Skapa först ett JSON-dokument med namnet `AzureCosmosKeyOnlyAccess.json` med följande innehåll:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Kör följande kommandon för att skapa roll tilldelningen och tilldela den till användaren:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Tillåt inte körning av åtgärder som inte är data

Följande kommandon tar bort möjligheten att använda nycklar för att:
- skapa, ändra eller ta bort resurser
- uppdatera behållar inställningar (inklusive indexerings principer, data flöde osv.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [Cosmos DB rollbaserad åtkomst kontroll](role-based-access-control.md)
- Få en översikt över [säker åtkomst till data i Cosmos DB](secure-access-to-data.md)
