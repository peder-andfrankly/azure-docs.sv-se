---
title: Tilldela känslighets etiketter till grupper – Azure AD | Microsoft Docs
description: Så här skapar du medlemskaps regler för automatisk ifyllning av grupper och en regel referens.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/19/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07859299805c5f7be869350adbdbfa675775888c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404811"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Tilldela känslighets etiketter till Office 365-grupper i Azure Active Directory (för hands version)

Azure Active Directory (Azure AD) stöder användning av känslighets etiketter som publiceras av [Microsoft 365 Compliance Center](https://sip.protection.office.com/homepage) till Office 365-grupper. Känslighets etiketter gäller för grupp över tjänster som Outlook, Microsoft Teams och SharePoint. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.

> [!IMPORTANT]
> Om du använder Azure ADs känslighets etiketter för Office 365-grupper krävs en licens för Azure Active Directory Premium P1.

## <a name="group-settings-controlled-by-labels"></a>Grupp inställningar som styrs av etiketter

Det finns två inställningar som kan associeras med en etikett:

- **Sekretess**: administratörer kan associera en sekretess inställning med etiketten för att kontrol lera om en grupp är offentlig eller privat.
- **Gäst åtkomst**: administratörer kan genomdriva gäst principen för alla grupper som har tilldelats etiketten. Den här principen anger om gäster kan läggas till som medlemmar eller inte. Om gäst principen har kon figurer ATS för en etikett kommer alla grupper som du tilldelar etiketten att inte tillåta AllowToAddGuests-inställningen att ändras.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Aktivera stöd för känslighets etikett i PowerShell

Om du vill tillämpa publicerade etiketter på grupper måste du först aktivera funktionen. De här stegen aktiverar funktionen i Azure AD.

1. Öppna ett Windows PowerShell-fönster på datorn. Du kan öppna den utan utökade privilegier.
1. Kör följande kommandon för att förbereda körningen av cmdletarna.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    På sidan **Logga in på ditt konto** anger du ditt administratörs konto och lösen ord för att ansluta dig till tjänsten och väljer sedan **Logga**in.
1. Hämta de aktuella grupp inställningarna för Azure AD-organisationen.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Om inga grupp inställningar har skapats för den här Azure AD-organisationen måste du först skapa inställningarna. Följ stegen i [Azure Active Directory-cmdletar för att konfigurera grupp inställningar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) för att skapa grupp inställningar för den här Azure AD-organisationen.

1. Sedan visar du de aktuella grupp inställningarna.

    ```PowerShell
    $Setting.Values
    ```

1. Aktivera sedan funktionen:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Spara ändringarna och tillämpa inställningarna:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Det var allt. Du har aktiverat funktionen och du kan tillämpa publicerade etiketter på grupper.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Tilldela en etikett till en ny grupp i Azure Portal

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com).
1. Välj **grupper**och välj sedan **ny grupp**.
1. På sidan **ny grupp** väljer du **Office 365**och fyller sedan i nödvändig information för den nya gruppen och väljer en känslighets etikett i listan.

   ![Tilldela en känslighets etikett på sidan nya grupper](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Spara ändringarna och välj **skapa**.

Gruppen skapas och de principer som är associerade med den valda etiketten verkställs sedan automatiskt.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Tilldela en etikett till en befintlig grupp i Azure Portal

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett globalt administratörs-eller grupp administratörs konto eller som grupp ägare.
1. Välj **grupper**.
1. Välj den grupp som du vill märka på sidan **alla grupper** .
1. Välj **Egenskaper** på den valda gruppens sida och välj en känslighets etikett i listan.

   ![Tilldela en känslighets etikett på översikts sidan för en grupp](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Spara ändringarna genom att välja **Spara** .

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Ta bort en etikett från en befintlig grupp i Azure Portal

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett globalt administratörs-eller grupp administratörs konto eller som grupp ägare.
1. Välj **grupper**.
1. På sidan **alla grupper** väljer du den grupp som du vill ta bort etiketten från.
1. På sidan **grupp** väljer du **Egenskaper**.
1. Välj **Ta bort**.
1. Välj **Spara** för att tillämpa ändringarna.

## <a name="office-365-app-support-for-sensitivity-labels"></a>Office 365-App-stöd för känslighets etiketter

Följande Office 365-appar och-tjänster stöder känslighets etiketter i den här förhands granskningen:

- Administrations Center för Azure AD
- Microsoft 365 Compliance Center
- SharePoint
- Outlook på webben
- Arbets
- Administrations Center för SharePoint

Mer information om stöd för Office 365-appar finns i [office 365-stöd för känslighets etiketter](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-new-sensitivity-labels).

## <a name="using-classic-azure-ad-classifications"></a>Använda klassiska Azure AD-klassificeringar

När du har aktiverat den här funktionen stöder Office 365 inte längre de klassiska klassificeringarna för nya grupper. Klassiska klassificeringar är de gamla klassificeringar som du ställer in genom att definiera värden för `ClassificationList` inställningen i Azure AD PowerShell. När den här funktionen är aktive rad kommer dessa klassificeringar inte att tillämpas på grupper.

## <a name="troubleshooting-issues"></a>Fel söknings problem

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Känslighets etiketter är inte tillgängliga för tilldelning i en grupp

Alternativet känslighets etikett visas bara för grupper när följande villkor uppfylls:

1. Etiketter publiceras i Microsoft 365 Compliance Center för den här klienten.
1. Funktionen är aktive rad, EnableMIPLabels har ställts in på True i PowerShell.
1. Gruppen är en Office 365-grupp.
1. Klienten har en aktiv Azure Active Directory Premium P1-licens.
1. Den aktuella inloggade användaren har åtkomst till publicerade etiketter.
1. Den aktuella inloggade användaren har tillräcklig behörighet för att tilldela etiketter. Användaren måste antingen vara global administratör, grupp administratör eller grupp ägare.
1. Den aktuella inloggade användaren har tilldelats en Office 365-licens. Mer information om licens krav finns i [känslighets etiketter i Office-appar](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-office-apps).

Se till att alla villkor är uppfyllda för att tilldela etiketter till en grupp.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Etiketten som jag vill tilldela finns inte i listan

Om etiketten som du söker efter inte finns i listan kan detta vara fallet av någon av följande orsaker:

- Etiketten kanske inte har publicerats i Microsoft 365 Compliance Center. Detta kan också gälla etiketter som inte längre publiceras. Kontakta administratören om du vill ha mer information.
- Etiketten kan vara publicerad, men den är inte tillgänglig för den användare som är inloggad. Kontakta administratören om du vill ha mer information om hur du får åtkomst till etiketten.

### <a name="how-can-i-change-the-label-on-a-group"></a>Hur kan jag ändra etiketten för en grupp?

Etiketter kan bytas när som helst med samma steg som för att tilldela en etikett till en befintlig grupp, enligt följande:

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett globalt konto eller grupp administratörs konto eller som grupp ägare.
1. Välj **grupper**.
1. Välj den grupp som du vill märka på sidan **alla grupper** .
1. Välj **Egenskaper** på den valda gruppens sida och välj en ny känslighets etikett i listan.
1. Välj **Spara**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Grupp inställnings ändringar för publicerade etiketter uppdateras inte i grupperna

Som bästa praxis rekommenderar vi inte att du ändrar grupp inställningarna för en etikett när etiketten har tillämpats på grupper. När du gör ändringar i grupp inställningarna som är associerade med publicerade etiketter i [Microsoft 365 Compliance Center](https://sip.protection.office.com/homepage), tillämpas dessa princip ändringar inte automatiskt på de berörda grupperna.

Om du måste göra en ändring använder du ett [Azure AD PowerShell-skript](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) för att manuellt tillämpa uppdateringar på de berörda grupperna. Den här metoden säkerställer att alla befintliga grupper tillämpar den nya inställningen.

## <a name="next-steps"></a>Nästa steg

- [Använd känslighets etiketter med Microsoft Teams, Office 365-grupper och SharePoint-webbplatser](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Uppdatera grupper efter att etikett policyn ändrats manuellt med Azure AD PowerShell-skriptet](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Redigera dina gruppinställningar](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Hantera grupper med PowerShell-kommandon](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
