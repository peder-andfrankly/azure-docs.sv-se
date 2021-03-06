---
title: Konfigurera hanterade identiteter på virtuell Azure-dator med mall – Azure AD
description: Stegvisa instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av en Azure Resource Manager-mall.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b12cd339aee0e9ae0e1cd6d31e523b9b1457c57
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971068"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av mallar

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln, med hjälp av mallen för Azure Resource Manager distribution, lär du dig att utföra följande hanterade identiteter för Azure-resurser på en virtuell Azure-dator:

## <a name="prerequisites"></a>Krav

- Om du inte känner till hur du använder mallen för Azure Resource Manager distribution kan du läsa [avsnittet Översikt](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Precis som med Azure Portal och skript ger [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) mallar möjlighet att distribuera nya eller ändrade resurser som definieras av en Azure-resurs grupp. Det finns flera alternativ för att redigera och distribuera mallar, både lokalt och Portal-baserat, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), som gör att du kan skapa en mall från grunden eller basera den på en befintlig gemensam eller [snabb starts mall](https://azure.microsoft.com/documentation/templates/).
   - Härleda från en befintlig resurs grupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)eller från det [aktuella läget för distributionen](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
   - Använda en lokal [JSON-redigerare (till exempel vs-kod)](../../azure-resource-manager/resource-manager-create-first-template.md)och ladda upp och distribuera med hjälp av POWERSHELL eller cli.
   - Använd Visual Studio [Azures resurs grupps projekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) för att både skapa och distribuera en mall.  

Oavsett vilket alternativ du väljer, är mallens syntax detsamma vid den första distributionen och omdistributionen. Att aktivera en system-eller användardefinierad hanterad identitet på en ny eller befintlig virtuell dator görs på samma sätt. Som standard gör Azure Resource Manager också en [stegvis uppdatering](../../azure-resource-manager/deployment-modes.md) av distributioner.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet ska du aktivera och inaktivera en systemtilldelad hanterad identitet med hjälp av en Azure Resource Manager mall.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Aktivera systemtilldelad hanterad identitet när en virtuell Azure-dator skapas eller på en befintlig virtuell dator

Om du vill aktivera systemtilldelad hanterad identitet på en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Oavsett om du loggar in på Azure lokalt eller via Azure Portal använder du ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorn.

2. Om du vill aktivera systemtilldelad hanterad identitet läser du in mallen i en redigerare, letar reda på `Microsoft.Compute/virtualMachines` resurs av intresse i avsnittet `resources` och lägger till `"identity"`-egenskapen på samma nivå som `"type": "Microsoft.Compute/virtualMachines"`-egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   },
   ```



3. När du är klar ska följande avsnitt läggas till i avsnittet `resource` i mallen och det bör likna följande:

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },
        
            //The following appears only if you provisioned the optional VM extension (to be deprecated)
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Tilldela en roll den systemtilldelade hanterade identiteten för den virtuella datorn

När du har aktiverat systemtilldelad hanterad identitet på den virtuella datorn kanske du vill ge den en roll som **läsar** åtkomst till resurs gruppen där den skapades.

För att tilldela en roll till den virtuella datorns systemtilldelade identitet behöver ditt konto rollen som [administratör för användar åtkomst](/azure/role-based-access-control/built-in-roles#user-access-administrator) .

1. Oavsett om du loggar in på Azure lokalt eller via Azure Portal använder du ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorn.
 
2. Läs in mallen i ett [redigerings program](#azure-resource-manager-templates) och Lägg till följande information för att ge din VM- **läsare** åtkomst till den resurs grupp där den skapades.  Mallens struktur kan variera beroende på vilken redigerare och vilken distributions modell du väljer.
   
   Lägg till följande under avsnittet `parameters`:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Lägg till följande under avsnittet `variables`:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Lägg till följande under avsnittet `resources`:

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Inaktivera en systemtilldelad hanterad identitet från en virtuell Azure-dator

För att ta bort systemtilldelad hanterad identitet från en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Oavsett om du loggar in på Azure lokalt eller via Azure Portal använder du ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorn.

2. Läs in mallen i ett [redigerings program](#azure-resource-manager-templates) och leta upp `Microsoft.Compute/virtualMachines` resurs av intresse i avsnittet `resources`. Om du har en virtuell dator som bara har systemtilldelad hanterad identitet kan du inaktivera den genom att ändra identitets typen till `None`.  
   
   **Microsoft. Compute/virtualMachines API version 2018-06-01**

   Om den virtuella datorn har både system-och användarspecifika hanterade identiteter tar du bort `SystemAssigned` från identitets typen och behåller `UserAssigned` tillsammans med värdena för `userAssignedIdentities`s ord lista.

   **Microsoft. Compute/virtualMachines API version 2018-06-01**
   
   Om din `apiVersion` är `2017-12-01` och din virtuella dator har både system-och användarspecifika hanterade identiteter, tar du bort `SystemAssigned` från identitets typen och behåller `UserAssigned` tillsammans med `identityIds`-matrisen för de användare som tilldelats hanterade identiteter.  
   
I följande exempel visas hur du tar bort en systemtilldelad hanterad identitet från en virtuell dator utan användare tilldelade hanterade identiteter:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": { 
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet tilldelar du en användardefinierad hanterad identitet till en virtuell Azure-dator med hjälp av Azure Resource Manager mall.

> [!Note]
> Om du vill skapa en användardefinierad hanterad identitet med hjälp av en Azure Resource Manager mall, se [skapa en användardefinierad hanterad identitet](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Tilldela en användardefinierad hanterad identitet till en virtuell Azure-dator

För att tilldela en användardefinierad identitet till en virtuell dator måste ditt konto ha roll tilldelningarna [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitets operatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Under `resources`-elementet lägger du till följande post för att tilldela en användardefinierad hanterad identitet till den virtuella datorn.  Se till att ersätta `<USERASSIGNEDIDENTITY>` med namnet på den användare-tilldelade hanterade identitet som du har skapat.

   **Microsoft. Compute/virtualMachines API version 2018-06-01**

   Om din `apiVersion` är `2018-06-01`lagras dina användares tilldelade hanterade identiteter i `userAssignedIdentities` ord listans format och `<USERASSIGNEDIDENTITYNAME>`-värdet måste lagras i en variabel som definierats i `variables`-avsnittet i mallen.

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```
   
   **Microsoft. Compute/virtualMachines API version 2017-12-01**
    
   Om din `apiVersion` är `2017-12-01`lagras dina användares tilldelade hanterade identiteter i `identityIds` matrisen och `<USERASSIGNEDIDENTITYNAME>`-värdet måste lagras i en variabel som definierats i `variables`-avsnittet i mallen.
    
   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```
       
3. När du är klar ska följande avsnitt läggas till i avsnittet `resource` i mallen och det bör likna följande:
   
   **Microsoft. Compute/virtualMachines API version 2018-06-01**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                  
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]   
   ```
   **Microsoft. Compute/virtualMachines API version 2017-12-01**
   
   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },
                 
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                   
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
       }
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användare som tilldelats en hanterad identitet från en virtuell Azure-dator

För att ta bort en tilldelad identitet från en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Oavsett om du loggar in på Azure lokalt eller via Azure Portal använder du ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorn.

2. Läs in mallen i ett [redigerings program](#azure-resource-manager-templates) och leta upp `Microsoft.Compute/virtualMachines` resurs av intresse i avsnittet `resources`. Om du har en virtuell dator som bara har användardefinierad hanterad identitet kan du inaktivera den genom att ändra identitets typen till `None`.
 
   I följande exempel visas hur du tar bort alla användare som tilldelats hanterade identiteter från en virtuell dator utan systemtilldelade hanterade identiteter:
   
   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
          },
    }
   ```
   
   **Microsoft. Compute/virtualMachines API version 2018-06-01**
    
   Om du vill ta bort en enskild användare som tilldelats en hanterad identitet från en virtuell dator tar du bort den från ord listan `useraAssignedIdentities`.

   Om du har en systemtilldelad hanterad identitet behåller du den i `type` värdet under `identity` svärdet.
 
   **Microsoft. Compute/virtualMachines API version 2017-12-01**

   Om du vill ta bort en enskild användare som tilldelats en hanterad identitet från en virtuell dator tar du bort den från `identityIds` matrisen.

   Om du har en systemtilldelad hanterad identitet behåller du den i `type` värdet under `identity` svärdet.
   
## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md).
