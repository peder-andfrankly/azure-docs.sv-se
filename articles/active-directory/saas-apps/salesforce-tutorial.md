---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Salesforce | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d137a96a967e017c67da7dfabf25502de32ba7a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970065"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Salesforce

I den här självstudien får du lära dig hur du integrerar Salesforce med Azure Active Directory (Azure AD). När du integrerar Salesforce med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Salesforce.
* Gör det möjligt för användarna att logga in automatiskt till Salesforce med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration på Salesforce-aktiverad enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Salesforce har stöd för **SP**-initierad enkel inloggning

* Salesforce stöder [ **automatiserad** användar etablering och avetablering](salesforce-provisioning-tutorial.md) (rekommenderas)

* Salesforce stöder **just-in-time**-användaretablering

* Salesforce-mobilappen kan nu konfigureras med Azure AD för att aktivera SSO. I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

## <a name="adding-salesforce-from-the-gallery"></a>Lägga till Salesforce från galleriet

För att kunna konfigurera integreringen av Salesforce i Azure AD måste du lägga till Salesforce från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. Skriv **Salesforce** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **Salesforce** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Konfigurera och testa enkel inloggning med Azure AD för Salesforce

Konfigurera och testa Azure AD SSO med Salesforce med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Salesforce.

Om du vill konfigurera och testa Azure AD SSO med Salesforce slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera Salesforce-SSO](#configure-salesforce-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Salesforce-testanvändare](#create-salesforce-test-user)** – för att få en motsvarighet till B. Simon i Salesforce som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Gör följande för att konfigurera enkel inloggning med Azure AD med Salesforce:

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **Salesforce** -programintegration i [Azure Portal](https://portal.azure.com/)letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du värdet med följande mönster:

    Enterprise-konto: `https://<subdomain>.my.salesforce.com`

    Developer-konto: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. I textrutan **Identifierare** anger du värdet med följande mönster:

    Enterprise-konto: `https://<subdomain>.my.salesforce.com`

    Developer-konto: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren. Kontakta [Salesforce-kundsupporten](https://help.salesforce.com/support) för att få dessa värden.

1. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Salesforce** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.
   
    > [!NOTE]
    > Salesforce-användarattribut är Skift läges känsliga för SAML-verifiering.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Salesforce.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Salesforce** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-salesforce-sso"></a>Konfigurera Salesforce SSO

1. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-administratörskonto.

2. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/configure1.png)

3. Rulla ned till **INSTÄLLNINGAR** i navigeringsfönstret och klicka på **Identitet** för att expandera det relaterade avsnittet. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-admin-sso.png)

4. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Om du inte kan aktivera inställningar för enkel inloggning för ditt Salesforce-konto kanske du måste kontakta [Salesforce-kundsupporten](https://help.salesforce.com/support).

5. Välj **SAML-aktiverat** och klicka på **Spara**.

      ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-enable-saml.png)

6. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Klicka på **Välj fil** för att ladda upp XML-filen med metadata som du har laddat ned från Azure-portalen och klicka på **Skapa**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/xmlchoose.png)

8. På sidan med **inställningar för enkel inloggning med SAML** fylls fälten i automatiskt. Klicka på Spara.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/salesforcexml.png)

9. I det vänstra navigeringsfönstret i Salesforce klickar du på **Företagsinställningar** för att expandera relaterat avsnitt och klickar sedan på **Min domän**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-my-domain.png)

10. Rulla ned till avsnittet **Autentiseringskonfiguration** och klicka på knappen **Redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. I avsnittet **Autentiseringskonfiguration** markerar du **AzureSSO** vid **Autentiseringstjänst** för din SAML SSO-konfiguration och klickar sedan på **Spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Om mer än en autentiseringstjänst har valts uppmanas användarna att välja vilken autentiseringstjänst de vill logga in med när de initierar enkel inloggning i din Salesforce-miljö. Om du inte vill att det ska hända ska du **låta alla andra autentiseringstjänster vara avmarkerade**.

### <a name="create-salesforce-test-user"></a>Skapa Salesforce-testanvändare

I det här avsnittet skapas en användare som heter B. Simon i Salesforce. Salesforce stöder just-in-time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Salesforce skapas en ny när du försöker komma åt Salesforce. Salesforce stöder även automatisk användaretablering. Mer information finns [här](salesforce-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Salesforce-panelen i åtkomstpanelen bör du automatiskt loggas in på Salesforce som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="test-sso-for-salesforce-mobile"></a>Testa SSO för Salesforce (mobil)

1. Öppna Salesforce-mobilapp. På inloggnings sidan klickar du på **Använd anpassad domän**.

    ![Salesforce-mobilapp](media/salesforce-tutorial/mobile-app1.png)

1. I text rutan **anpassad domän** anger du ditt registrerade anpassade domän namn och klickar på **Fortsätt**.

    ![Salesforce-mobilapp](media/salesforce-tutorial/mobile-app2.png)

1. Ange dina autentiseringsuppgifter för Azure AD för att logga in i Salesforce-programmet och klicka på **Nästa**.

    ![Salesforce-mobilapp](media/salesforce-tutorial/mobile-app3.png)

1. På sidan **Tillåt åtkomst** som visas nedan klickar du på **Tillåt** för att ge åtkomst till Salesforce-programmet.

    ![Salesforce-mobilapp](media/salesforce-tutorial/mobile-app4.png)

1. När du har loggat in igen visas programmets start sida.

    ![Salesforce-mobilapp](media/salesforce-tutorial/mobile-app5.png) ![Salesforce-mobilapp](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](salesforce-provisioning-tutorial.md)

- [Prova Salesforce med Azure AD](https://aad.portal.azure.com)
