---
title: 'Självstudie: Azure Active Directory integration med lösen ords hanteraren i Keepr & digitalt valv | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Keeper Password Manager & Digital Vault.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fecbe924c1511c247ff846d3b2a39d309ecf0d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159898"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Självstudie: Azure Active Directory integrering med lösen ords hanteraren i Keepr & digitalt valv

I den här självstudien lär du dig att integrera Keeper Password Manager & Digital Vault med Azure Active Directory (AD Azure).
Genom att integrera Keeper Password Manager & Digital Vault med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Keeper Password Manager & Digital Vault.
* Du kan göra så att dina användare automatiskt loggas in på Keeper Password Manager & Digital Vault (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Keeper Password Manager & Digital Vault behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Keeper Password Manager & Digital Vault-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Keeper Password Manager & Digital Vault stöder **IDP**-initierad enkel inloggning

* Keeper Password Manager & Digital Vault stöder **just in time**-användaretablering

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Lägga till Keeper Password Manager & Digital Vault från galleriet

För att konfigurera integreringen av Keeper Password Manager & Digital Vault till Azure AD behöver du lägga till Keeper Password Manager & Digital Vault från galleriet till listan över hanterade SaaS-appar.

**Lägg till Keeper Password Manager & Digital Vault från galleriet genom att utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Keeper Password Manager & Digital Vault**, väljer **Keeper Password Manager & Digital Vault** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Keeper Password Manager & Digital Vault i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med Keeper Password Manager & Digital Vault baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Keeper Password Manager & Digital Vault upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Keeper Password Manager & Digital Vault behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **Konfigurera enkel inloggning för Keeper Password Manager & Digital Vault** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Keeper Password Manager & Digital Vault-testanvändare](#create-keeper-password-manager--digital-vault-test-user)** – för att ha en motsvarighet för Britta Simon i Keeper Password Manager & Digital Vault som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Keeper Password Manager & Digital Vault:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Keeper Password Manager & Digital Vault**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Keeper Password Manager & Digital Vault-domän och information om URL:er för enkel inloggning](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://{SSO CONNECT SERVER}/sso-connect`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Hämta dessa värden genom att kontakta [supportteamet för Keeper Password Manager & Digital Vault-klienten](https://keepersecurity.com/contact.html). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Keeper Password Manager & Digital Vault** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-keeper-password-manager--digital-vault-single-sign-on"></a>Konfigurera enkel inloggning för Keeper Password Manager & Digital Vault

Konfigurera enkel inloggning på sidan om **Keeper Password Manager & Digital Vault-konfiguration** genom att följa anvisningarna i [supportguiden för Keeper](https://keepersecurity.com/assets/pdf/KeeperSSOConnect_v11.pdf).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon\@yourcompanydomain. extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Keeper Password Manager & Digital Vault.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Keeper Password Manager & Digital Vault**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Keeper Password Manager & Digital Vault**.

    ![Keeper Password Manager & Digital Vault-länken i programlistan](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Skapa Keeper Password Manager & Digital Vault-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på Keeper Password Manager & Digital Vault måste de etableras i Keeper Password Manager & Digital Vault. Programmet stöder just-in-time-användaretablering, och efter autentiseringen skapas användare automatiskt i programmet. Du kan kontakta [Keeper-supporten](https://keepersecurity.com/contact.html) om du vill konfigurera användare manuellt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Keeper Password Manager & Digital Vault-panelen i åtkomstpanelen bör du automatiskt loggas in på Keeper Password Manager & Digital Vault som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

