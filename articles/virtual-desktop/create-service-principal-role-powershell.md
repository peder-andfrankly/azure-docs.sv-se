---
title: Roll tilldelning för tjänstens huvud namn för Windows Virtual Desktop – Azure
description: Hur du skapar tjänstens huvud namn och tilldelar roller med PowerShell i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: helohr
ms.openlocfilehash: 1141731697c9f649a4a8d4052cd550605049b52e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606943"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Självstudie: skapa tjänstens huvud namn och roll tilldelningar med hjälp av PowerShell

Tjänstens huvud namn är identiteter som du kan skapa i Azure Active Directory för att tilldela roller och behörigheter för ett specifikt syfte. I Windows Virtual Desktop kan du skapa ett huvud namn för tjänsten för att:

- Automatisera vissa hanterings uppgifter för virtuella Windows-datorer.
- Använd som autentiseringsuppgifter i stället för MFA-obligatoriska användare när du kör en Azure Resource Manager mall för Windows Virtual Desktop.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett huvud namn för tjänsten i Azure Active Directory.
> * Skapa en roll tilldelning i Windows Virtual Desktop.
> * Logga in på Windows Virtual Desktop genom att använda tjänstens huvud namn.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kan skapa tjänstens huvud namn och roll tilldelningar måste du göra tre saker:

1. Installera AzureAD-modulen. Installera modulen genom att köra PowerShell som administratör och köra följande cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Hämta och importera Windows Virtual Desktop PowerShell-modulen](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview).

3. Följ alla anvisningar i den här artikeln i samma PowerShell-session. Processen kanske inte fungerar om du avbryter PowerShell-sessionen genom att stänga fönstret och öppna den igen senare.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Skapa ett huvudnamn för tjänsten i Azure Active Directory

När du har uppfyllt kraven i PowerShell-sessionen kör du följande PowerShell-cmdletar för att skapa ett huvud namn för flera innehavare i Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Visa dina autentiseringsuppgifter i PowerShell

Innan du skapar roll tilldelningen för tjänstens huvud namn kan du Visa dina autentiseringsuppgifter och skriva ned dem för framtida bruk. Lösen ordet är särskilt viktigt eftersom du inte kan hämta det när du har stängt PowerShell-sessionen.

Här följer de tre autentiseringsuppgifterna som du bör skriva ned och vilka cmdlets du måste köra för att hämta dem:

- Ords

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Klient-ID:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Program-ID:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Skapa en roll tilldelning i för hands versionen av Windows Virtual Desktop

Därefter måste du skapa en roll tilldelning så att tjänstens huvud namn kan logga in på det virtuella Windows-skrivbordet. Var noga med att logga in med ett konto som har behörighet att skapa roll tilldelningar.

Börja med att [Hämta och importera Windows Virtual Desktop PowerShell-modulen](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) som ska användas i PowerShell-sessionen om du inte redan gjort det.

Kör följande PowerShell-cmdlets för att ansluta till det virtuella Windows-skrivbordet och Visa dina klienter.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

När du hittar klient namnet för den klient som du vill skapa en roll tilldelning för, använder du namnet i följande cmdlet:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Logga in med tjänstens huvud namn

När du har skapat en roll tilldelning för tjänstens huvud namn kontrollerar du att tjänstens huvud namn kan logga in på Windows Virtual Desktop genom att köra följande cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

När du har loggat in ser du till att allt fungerar genom att testa några Windows PowerShell-cmdletar för virtuella datorer med tjänstens huvud namn.

## <a name="next-steps"></a>Nästa steg

När du har skapat tjänstens huvud namn och tilldelat rollen som en roll i din Windows-klient för virtuella datorer kan du använda den för att skapa en adresspool. Om du vill veta mer om värdar för pooler fortsätter du till självstudien för att skapa en adresspool i Windows Virtual Desktop.

 > [!div class="nextstepaction"]
 > [Självstudie för Windows virtuell Skriv bords värd](./create-host-pools-azure-marketplace.md)
