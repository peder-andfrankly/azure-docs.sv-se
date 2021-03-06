---
title: Lägg till tjänstens huvud namn i Azure Analysis Services admin-rollen | Microsoft Docs
description: Lär dig hur du lägger till ett Automation-tjänstens huvud namn i rollen Azure Analysis Services Server-administratör
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0a3a86283c8ec9876fbec049a2a1a110eb1a80f3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573621"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Lägg till ett huvud namn för tjänsten i Server administratörs rollen 

 För att automatisera obevakade PowerShell-aktiviteter måste ett huvud namn för tjänsten ha **Server administratörs** behörighet på den Analysis Services server som hanteras. Den här artikeln beskriver hur du lägger till ett huvud namn för tjänsten i rollen Server administratörer på en Azure AS-Server.

## <a name="before-you-begin"></a>Innan du börjar
Innan du slutför den här uppgiften måste du ha ett huvud namn för tjänsten som registrerats i Azure Active Directory.

[Skapa tjänstens huvud namn – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Nödvändiga behörigheter
För att slutföra den här uppgiften måste du ha [Server administratörs](analysis-services-server-admins.md) behörighet på Azure as Server. 

## <a name="add-service-principal-to-server-administrators-role"></a>Lägg till tjänstens huvud namn i rollen Server administratörer

1. I SSMS ansluter du till din Azure AS-Server.
2. I **Server egenskaper** > **säkerhet**klickar du på **Lägg till**.
3. I **Välj en användare eller grupp**söker du efter din registrerade app efter namn, väljer och klickar sedan på **Lägg till**.

    ![Sök efter tjänstens huvud konto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Kontrol lera konto-ID för tjänstens huvud namn och klicka sedan på **OK**.
    
    ![Sök efter tjänstens huvud konto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> För Server åtgärder som använder Azure PowerShell-cmdletar måste tjänstens huvud namn som kör Scheduler också tillhöra **ägar** rollen för resursen i [Azure role-based Access Control (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Relaterad information

* [Ladda ned SQL Server PowerShell-modul](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Ladda ned SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


