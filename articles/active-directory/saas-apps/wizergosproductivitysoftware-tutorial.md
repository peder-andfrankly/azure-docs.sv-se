---
title: 'Självstudier: Azure Active Directory-integrering med Wizergos produktivitetsprogram | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wizergos produktivitetsprogram.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: b6491013cb35f2473eff6c2019fe2a80dd9e9b08
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086985"
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Självstudier: Azure Active Directory-integrering med Wizergos produktivitetsprogram

I den här självstudien får du lära dig hur du integrerar Wizergos produktivitetsprogram med Azure Active Directory (AD Azure).
Integrera Wizergos produktivitetsprogram med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Wizergos produktivitetsprogram.
* Du kan aktivera användarna att vara automatiskt inloggad till Wizergos produktivitetsprogram (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Wizergos produktivitetsprogram, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Wizergos produktivitetsprogram enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Wizergos produktivitetsprogramvara **IDP** -initierad SSO

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Att lägga till Wizergos produktivitetsprogram från galleriet

För att konfigurera integrering av Wizergos produktivitetsprogram i Azure AD, som du behöver lägga till Wizergos produktivitetsprogram från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Wizergos produktivitetsprogram från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Wizergos produktivitetsprogramvara**väljer **Wizergos produktivitetsprogramvara** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Wizergos produktivitetsprogram i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Wizergos produktivitetsprogram baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Wizergos produktivitetsprogram upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Wizergos produktivitetsprogram, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Wizergos produktivitet programvara enkel inloggning](#configure-wizergos-productivity-software-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Wizergos produktivitetsprogram testanvändare](#create-wizergos-productivity-software-test-user)**  – du har en motsvarighet för Britta Simon i Wizergos produktivitetsprogram som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Wizergos produktivitetsprogram:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Wizergos produktivitetsprogramvara** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Wizergos produktivitet programvara domän och URL: er med enkel inloggning för information](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://www.wizergos.net`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. På den **konfigurera Wizergos produktivitetsprogram** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-wizergos-productivity-software-single-sign-on"></a>Konfigurera Wizergos produktivitet programvara enkel inloggning

1. I ett annat webbläsarfönster inloggning till Wizergos produktivitetsprogram-klienten som administratör.

2. Från hamburger-menyn, Välj **Admin**.

    ![Konfigurera enkel inloggning på appsidan](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

3. I Administrationssida på den vänstra menyn väljer du **AUTENTISERING** och klicka på **Azure AD**.

    ![Konfigurera enkel inloggning på appsidan](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

4. Utför följande steg på **AUTENTISERING** avsnittet.

    ![Konfigurera enkel inloggning på appsidan](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. Klicka på **överför** knappen för att ladda upp det nedladdade certifikatet från Azure AD.
    
    b. I den **utfärdar-URL** textrutan klistra in den **Azure AD-identifierare** värde som du har kopierat från Azure-portalen.
    
    c. I den **URL för enkel inloggning** textrutan klistra in den **inloggnings-URL** värde som du har kopierat från Azure-portalen.
    
    d. I den **URL för enkel utloggning** textrutan klistra in den **URL för utloggning** värde som du har kopierat från Azure-portalen.
    
    e. Klicka på **spara** knappen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Wizergos produktivitetsprogram.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Wizergos produktivitetsprogramvara**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Wizergos produktivitetsprogramvara**.

    ![Länken Wizergos produktivitetsprogram i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-wizergos-productivity-software-test-user"></a>Skapa Wizergos produktivitetsprogram testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Wizergos produktivitetsprogram. Arbeta med [Wizergos produktivitetsprogram supportteamet](mailTo:support@wizergos.com) att lägga till användare i Wizergos produktivitetsprogram-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Wizergos produktivitetsprogram i åtkomstpanelen, bör det vara loggas in automatiskt till Wizergos produktivitetsprogram som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

