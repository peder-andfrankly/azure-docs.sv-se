---
title: Distribuera hanterings verktyget – Azure
description: Så här installerar du ett användar gränssnitts verktyg för att hantera Windows-resurser för virtuella skriv bord.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 11/09/2019
ms.author: helohr
ms.openlocfilehash: ad0c67cea6a5a9b487cd47aa7c10d10da1438050
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384288"
---
# <a name="tutorial-deploy-a-management-tool"></a>Självstudie: Distribuera ett hanterings verktyg

Hanterings verktyget tillhandahåller ett användar gränssnitt för hantering av Microsoft-resurser för virtuella skriv bord. I den här självstudien får du lära dig hur du distribuerar och ansluter till hanterings verktyget.

>[!NOTE]
>De här anvisningarna gäller för en Windows-konfiguration med en virtuell dator som kan användas med din organisations befintliga processer.

## <a name="important-considerations"></a>Viktiga överväganden

Eftersom appen kräver medgivande för att interagera med Windows Virtual Desktop, stöder det här verktyget inte Business-to-Business-scenarier (B2B). Varje Azure Active Directory (AAD)-klient organisations prenumeration måste ha en egen separat distribution av hanterings verktyget.

Det här hanterings verktyget är ett exempel. Microsoft kommer att tillhandahålla viktiga säkerhets-och kvalitets uppdateringar. [Käll koden finns i GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Kunder och partner uppmanas att anpassa verktyget så att det passar deras affärs behov.

Följande webbläsare är kompatibla med hanterings verktyget:
- Google Chrome 68 eller senare
- Microsoft Edge 40,15063 eller senare
- Mozilla Firefox 52,0 eller senare
- Safari 10 eller senare (endast macOS)

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Vad du behöver för att köra Azure Resource Manager-mallen

Innan du distribuerar Azure Resource Manager-mallen behöver du en Azure Active Directory användare för att distribuera hanterings gränssnittet. Den här användaren måste:

- Ha Azure Multi-Factor Authentication (MFA) inaktiverat
- Har behörighet att skapa resurser i din Azure-prenumeration
- Har behörighet att skapa ett Azure AD-program. Följ de här stegen för att kontrol lera om användaren har de [behörigheter som krävs](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

När du har distribuerat Azure Resource Manager-mallen vill du starta hanterings gränssnittet för att verifiera. Den här användaren måste:
- Ha en roll tilldelning för att visa eller redigera din Windows-klient för virtuella skriv bord

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Kör Azure Resource Manager-mallen för att etablera hanterings gränssnittet

Innan du börjar bör du kontrol lera att servern och klientens appar har medgivande genom att besöka [sidan för godkännande av Windows-dator](https://rdweb.wvd.microsoft.com) för den Azure Active Directory (AAD) som visas.

Följ de här anvisningarna för att distribuera Azure-resurs hanterings mal len:

1. Gå till [sidan GitHub Azure RDS – templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Distribuera mallen till Azure.
    - Om du distribuerar i en Enterprise-prenumeration kan du rulla nedåt och välja **distribuera till Azure**. Se [rikt linjer för mallparametrar](#guidance-for-template-parameters).
    - Om du distribuerar i en prenumeration på Cloud Solution Provider följer du de här anvisningarna för att distribuera till Azure:
        1. Rulla nedåt och högerklicka på **distribuera till Azure**och välj sedan **Kopiera länk plats**.
        2. Öppna en text redigerare som anteckningar och klistra in länken där.
        3. Direkt efter <https://portal.azure.com/> och innan hashtagg (#), anger du ett @-tecken följt av klient domän namnet. Här är ett exempel på formatet: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Logga in på Azure Portal som en användare med administratörs-/deltagar behörighet för Cloud Solution Provider-prenumerationen.
        5. Klistra in länken som du kopierade till text redigeraren i adress fältet.

### <a name="guidance-for-template-parameters"></a>Vägledning för mallparametrar
Så här anger du parametrar för att konfigurera verktyget:

- För parametern **isServicePrincipal** väljer du **falskt**.
- Ange dina Azure Active Directory autentiseringsuppgifter med Multi-Factor Authentication inaktiverat för autentiseringsuppgifterna. De här autentiseringsuppgifterna är de som du använder för att logga in på Azure och skapa Azure AD-programmet och Azure-webbappens resurser. Mer information finns i [vad du behöver för att köra Azure Resource Manager-mallen](#what-you-need-to-run-the-azure-resource-manager-template).
- För **applicationName**använder du ett unikt namn för din app som ska registreras i din Azure Active Directory. Det här namnet används också för webbappens webb adress. Du kan till exempel använda ett namn som "Apr3UX".

## <a name="provide-consent-for-the-management-tool"></a>Tillhandahåll medgivande för hanterings verktyget

När GitHub Azure Resource Manager-mallen har slutförts hittar du en resurs grupp som innehåller två app Services tillsammans med en app service-plan i Azure Portal.

Innan du loggar in och använder hanterings verktyget måste du ange ett medgivande för det nya Azure Active Directorys programmet som är associerat med hanterings verktyget. Genom att tillhandahålla medgivande kan du låta hanterings verktyget göra Windows-hantering för virtuella skriv bord åt den användare som är inloggad i verktyget.

![En skärm bild som visar de behörigheter som tillhandahålls när du godkänner användar gränssnitts hanterings verktyget.](media/management-ui-delegated-permissions.png)

För att avgöra vilken användare du kan använda för att logga in på verktyget går du till [sidan Azure Active Directory användar inställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) och noterar värdet för **användarna kan godkänna att appar får åtkomst till företags data för deras räkning**.

![En skärm bild som visar om användarna kan ge tillåtelse till program enbart för användare.](media/management-ui-user-consent-allowed.png)

- Om värdet är inställt på **Ja**kan du logga in med ett användar konto i Azure Active Directory och bara ge användaren tillåtelse. Men om du loggar in på hanterings verktyget med en annan användare senare, måste du utföra samma medgivande igen.
- Om värdet är inställt på **Nej**, måste du logga in som global administratör i Azure Active Directory och ge administratörs tillåtelse för alla användare i katalogen. Inga andra användare får ett medgivande.


När du har bestämt vilken användare du ska använda för att ge sitt medgivande följer du de här anvisningarna för att ge ditt medgivande till verktyget:

1. Gå till dina Azure-resurser, Välj resursen för Azure App tjänster med det namn som du angav i mallen (till exempel Apr3UX) och navigera till den URL som är kopplad till den. till exempel <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Logga in med lämpligt Azure Active Directory användar konto.
3. Om du har autentiserats med en global administratör kan du nu Markera kryss rutan för **din organisations räkning**. Välj **acceptera** för att ge medgivande.
   
   ![En skärm bild som visar sidan för fullständigt godkännande som användaren eller administratören ser.](media/management-ui-consent-page.png)

Nu ska du gå till hanterings verktyget.

## <a name="use-the-management-tool"></a>Använd hanterings verktyget

När du har beviljat godkännande för organisationen eller för en viss användare kan du när som helst komma åt hanterings verktyget.

Följ dessa instruktioner för att starta verktyget:

1. Välj resursen Azure App tjänster med det namn som du angav i mallen (till exempel Apr3UX) och navigera till den URL som är kopplad till den. till exempel <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Logga in med dina Windows-autentiseringsuppgifter för virtuella skriv bord.
3. När du uppmanas att välja en klient grupp väljer du **standard grupp för klient organisation** i list rutan.
4. När du väljer standard klient grupp visas en meny på höger sida av fönstret. På den här menyn söker du efter namnet på din klient grupp och väljer den.

> [!NOTE]
> Om du har en anpassad klient grupp anger du namnet manuellt i stället för att välja i list rutan.

## <a name="report-issues"></a>Rapportera problem

Om du stöter på problem med hanterings verktyget eller andra Windows-verktyg för virtuella skriv bord, följer du anvisningarna i [arm-mallar för Fjärrskrivbordstjänster](https://github.com/Azure/RDS-Templates/blob/master/README.md) för att rapportera dem på GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar och ansluter till hanterings verktyget kan du lära dig hur du använder Azure Service Health för att övervaka tjänst problem och hälso rekommendationer.

> [!div class="nextstepaction"]
> [Själv studie kurs om att konfigurera service varningar](./set-up-service-alerts.md)
