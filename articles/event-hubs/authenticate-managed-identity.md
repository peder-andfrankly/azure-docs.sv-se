---
title: Autentisera en hanterad identitet med Azure Active Directory
description: Den här artikeln innehåller information om hur du autentiserar en hanterad identitet med Azure Active Directory för åtkomst till Azure Event Hubs-resurser
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cbd7de7d526e1954aaad60f7d71e5cdf202f6a29
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992840"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser
Azure Event Hubs stöder Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). Hanterade identiteter för Azure-resurser kan ge åtkomst till Event Hubs resurser med hjälp av Azure AD-autentiseringsuppgifter från program som körs i Azure Virtual Machines (VM), Function-appar, Virtual Machine Scale Sets och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.

Den här artikeln visar hur du auktoriserar åtkomst till en Event Hub genom att använda en hanterad identitet från en virtuell Azure-dator.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator
Innan du kan använda hanterade identiteter för Azure-resurser för att auktorisera Event Hubs resurser från din virtuella dator måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klient bibliotek](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Bevilja behörighet till en hanterad identitet i Azure AD
Om du vill auktorisera en begäran om att Event Hubs tjänst från en hanterad identitet i ditt program måste du först konfigurera inställningarna för rollbaserad åtkomst kontroll (RBAC) för den hanterade identiteten. Azure Event Hubs definierar RBAC-roller som omfattar behörigheter för att skicka och läsa från Event Hubs. När RBAC-rollen tilldelas till en hanterad identitet beviljas den hanterade identiteten åtkomst till Event Hubs data i lämplig omfattning.

Mer information om hur du tilldelar RBAC-roller finns i [autentisera med Azure Active Directory för åtkomst till Event Hubs resurser](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Använda Event Hubs med hanterade identiteter
Om du vill använda Event Hubs med hanterade identiteter måste du tilldela identiteten rollen och lämplig omfattning. I proceduren i det här avsnittet används ett enkelt program som körs under en hanterad identitet och som har åtkomst till Event Hubs resurser.

Här ska vi använda ett exempel på ett webb program som finns i [Azure App Service](https://azure.microsoft.com/services/app-service/). Stegvisa instruktioner för hur du skapar ett webb program finns i [skapa en ASP.net Core webbapp i Azure](../app-service/app-service-web-get-started-dotnet.md)

Följ dessa steg när programmet har skapats: 

1. Gå till **Inställningar** och välj **identitet**. 
1. Välj den **status** som skaanvändas. 
1. Spara inställningen genom att välja **Spara**. 

    ![Hanterad identitet för en webbapp](./media/authenticate-managed-identity/identity-web-app.png)

När du har aktiverat den här inställningen skapas en ny tjänst identitet i din Azure Active Directory (Azure AD) och konfigureras i App Service-värden.

Tilldela nu den här tjänst identiteten till en roll i det begärda omfånget i Event Hubs-resurser.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Tilldela RBAC-roller med hjälp av Azure Portal
Om du vill tilldela en roll till Event Hubs resurser navigerar du till resursen i Azure Portal. Visa inställningarna för Access Control (IAM) för resursen och följ de här anvisningarna för att hantera roll tilldelningar:

> [!NOTE]
> Följande steg tilldelar en tjänst identitets roll till dina Event Hubs namnrum. Du kan följa samma steg för att tilldela en roll begränsad till en Event Hubs resurs. 

1. I Azure Portal navigerar du till Event Hubs namn området och visar **översikten** för namn området. 
1. Välj **Access Control (IAM)** på den vänstra menyn för att Visa inställningar för åtkomst kontroll för händelsehubben.
1.  Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar.
3.  Välj **Lägg** till för att lägga till en ny roll.
4.  På sidan **Lägg till roll tilldelning** väljer du de Event Hubs roller som du vill tilldela. Sök sedan efter den tjänst identitet som du har registrerat för att tilldela rollen.
    
    ![Sidan Lägg till roll tilldelning](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Välj **Spara**. Identiteten som du har tilldelat rollen visas i listan under den rollen. Följande bild visar till exempel att tjänst identiteten har Event Hubs data ägare.
    
    ![Identitet som har tilldelats en roll](./media/authenticate-managed-identity/role-assigned.png)

När du har tilldelat rollen får webb programmet åtkomst till Event Hubs resurser under det definierade omfånget. 

### <a name="test-the-web-application"></a>Testa webb programmet
Nu kan du starta ditt webb program och peka webbläsaren på exempel-aspx-sidan. Du kan hitta det exempel webb program som skickar och tar emot data från Event Hubs resurser i [GitHub-lagrings platsen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Installera det senaste paketet från [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)och börja skicka till och ta emot data från Event Hub med hjälp av EventHubClient som visas i följande kod: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```

## <a name="next-steps"></a>Nästa steg
- Hämta [exemplet](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp) från GitHub.
- I följande artikel finns information om hanterade identiteter för Azure-resurser: [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)
- Se följande relaterade artiklar:
    - [Autentisera begär anden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
    - [Autentisera begär anden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
    - [Ge åtkomst till Event Hubs resurser med Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Ge åtkomst till Event Hubs resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)