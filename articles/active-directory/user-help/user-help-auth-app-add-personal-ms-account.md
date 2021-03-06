---
title: Lägg till en personlig Microsoft-konto i Microsoft Authenticator-appen – Azure AD
description: Lägg till personliga Microsoft-konton, till exempel för Outlook.com eller Xbox LIVE i Microsoft Authenticator-appen för att verifiera din identitet när du använder tvåstegsverifiering.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28fd29c1f3f393c37c9e9a8d0995766258c2eb45
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278906"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Lägga till personliga Microsoft-konton i Microsoft Authenticator-appen

Lägg till dina personliga Microsoft-konton, till exempel för Outlook.com och Xbox LIVE i Microsoft Authenticator-appen för både standard processen med två faktorer och lösen ords lös inloggnings metod.

- **Standard verifierings metod med två faktorer.** Ange ditt användar namn och lösen ord i den enhet som du loggar in på och välj sedan om Microsoft Authenticator appen skickar ett meddelande eller om du hellre vill kopiera den tillhör ande verifierings koden från skärmen **konton** i Microsoft Authenticator-app.

- **Lösen ords registrerings metod.** Ange ditt användar namn i enheten som du loggar in på för din personliga Microsoft-konto och Använd sedan din mobila enhet för att kontrol lera att det är du genom att använda finger avtryck, ansikte eller PIN-kod. För den här metoden behöver du inte ange ditt lösenord.

>[!Important]
>Innan du kan lägga till ditt konto måste du ladda ned och installera Microsoft Authenticator-appen. Om du inte har gjort det än följer du stegen i artikeln [Hämta och installera appen](user-help-auth-app-download-install.md) .

## <a name="add-your-personal-microsoft-account"></a>Lägg till din personliga Microsoft-konto

Du kan lägga till din personliga Microsoft-konto genom att först aktivera tvåstegsverifiering och sedan lägga till kontot i appen.

>[!Note]
>Om du endast planerar att använda lösen ords lös inloggning för din personliga Microsoft-konto behöver du inte aktivera tvåstegsverifiering. För ytterligare konto säkerhet rekommenderar vi dock att du aktiverar tvåstegsverifiering.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåstegsverifiering

1. På din dator går du till sidan [grundläggande om säkerhet](https://account.microsoft.com/security) och loggar in med din personliga Microsoft-konto. Till exempel alain@outlook.com.

2. Längst ned på sidan **grundläggande säkerhets** inställningar väljer du länken **fler säkerhets alternativ** .

    ![Sidan grundläggande säkerhet med länken "fler säkerhets alternativ" markerad](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Gå till avsnittet **tvåstegsverifiering** och välj att **aktivera funktionen.** Du kan också inaktivera det här om du inte längre vill använda det med ditt personliga konto.

### <a name="add-your-microsoft-account-to-the-app"></a>Lägg till din Microsoft-konto i appen

1. Öppna appen Microsoft Authenticator på den mobila enheten.

2. Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger.

    ![Sidan konton, där ikonen anpassa och kontroll är markerad](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. På sidan **Lägg till konto** väljer du **personligt konto**.

4. Logga in på ditt personliga konto med lämplig e-postadress (till exempel alain@outlook.com) och välj sedan **Nästa**.

    >[!Note]
    >Om du inte har någon personlig Microsoft-konto kan du skapa en här.

5. Ange ditt lösen ord och välj sedan **Logga**in.

    Ditt personliga konto läggs till i Microsoft Authenticator-appen.

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med Authenticator-appen på din enhet. Mer information finns i [Logga in med appen](user-help-auth-app-sign-in.md).

- Om du har problem med att hämta din verifierings kod för din personliga Microsoft-konto kan du läsa avsnittet **fel sökning av verifierings kod problem** i artikeln [Microsoft-konto säkerhets information & verifierings koder](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- För enheter som kör iOS kan du också säkerhetskopiera dina kontoautentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton, i molnet. Mer information finns i [säkerhets kopiering och återställning med Microsoft Authenticator app](user-help-auth-app-backup-recovery.md).
