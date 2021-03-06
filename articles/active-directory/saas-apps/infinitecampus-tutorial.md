---
title: 'Självstudier: Azure Active Directory-integrering med oändlig Campus | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och oändlig Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d54769c1f3265e2cee619520044313fca46855a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100357"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Självstudier: Azure Active Directory-integrering med oändlig Campus

I den här självstudien får du lära dig hur du integrerar oändlig Campus med Azure Active Directory (AD Azure).
Integrera oändlig Campus med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till oändlig Campus.
* Du kan aktivera användarna att vara automatiskt inloggad till oändlig Campus (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med oändlig Campus, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Oändlig Campus enkel inloggning aktiverat prenumeration
* Minimum måste du vara administratör för Azure Active Directory och har en säkerhetsroll för Campus produkten av ”Student Information System (SIS)” Slutför konfigurationen.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Oändlig Campus stöder **SP** -initierad SSO

## <a name="adding-infinite-campus-from-the-gallery"></a>Att lägga till oändlig Campus från galleriet

För att konfigurera integrering av oändlig Campus i Azure AD, som du behöver lägga till oändlig Campus från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till oändlig Campus från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Lägg till ett nytt program, klicka på den **nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **oändlig Campus**väljer **oändlig Campus** från panelen resultatet klickar på **Lägg till** för att lägga till programmet.

    ![Oändlig Campus i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med oändlig Campus baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i oändlig Campus upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med oändlig Campus, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera oändlig Campus enkel inloggning](#configure-infinite-campus-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa oändlig Campus testanvändare](#create-infinite-campus-test-user)**  – du har en motsvarighet för Britta Simon i oändlig Campus som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med oändlig Campus:

1. I den [Azure-portalen](https://portal.azure.com/)på den **oändlig Campus** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Utför följande steg i avsnittet grundläggande SAML-konfiguration (Observera att domänen varierar beroende på som är värd för modellen, men **fullständigt-QUALIFIED-DOMAIN** värdet måste matcha din oändlig Campus-installation):

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Oändlig Campus domän och URL: er med enkel inloggning för information](common/sp-identifier-reply.png)

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Konfigurera oändlig Campus enkel inloggning

1. I ett annat webbläsarfönster, loggar du in oändlig Campus som en administratör.

2. På vänster sida av menyn, klickar du på **systemadministration**.

    ![Administratören](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Gå till **användarsäkerhet** > **SAML Management** > **SSO konfiguration av ServiceProvider**.

    ![Saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. På den **SSO konfiguration av ServiceProvider** utför följande steg:

    ![Sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Välj **aktivera SAML enkel inloggning**.

    b. Redigera den **valfritt attributnamnet** innehålla **namn**

    c. På den **Välj ett alternativ för att hämta serverdata identitet Provider (IDP)** väljer du **Metadata_url**, klistra in den **Appfederationsmetadata** värde, som du har kopieras från Azure-portalen i rutan och klicka sedan på **synkronisering**.

    d. När du klickar på **synkronisering** värdena får fylls i **SSO konfiguration av ServiceProvider** sidan. Dessa värden kan verifieras för att matcha de värden som visas i steg 4 ovan.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

> [!NOTE]
> Om du vill att alla dina Azure-användare att ha enkel inloggning för åtkomst till oändlig Campus och förlitar sig på oändlig Campus interna behörigheter system för åtkomstkontroll kan du ange den **användare tilldelning krävs** egenskapen för programmet på Nej och hoppa över följande steg.

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till oändlig Campus.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **oändlig Campus**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **oändlig Campus**.

    ![Oändlig Campus-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-infinite-campus-test-user"></a>Skapa oändlig Campus testanvändare

Oändlig Campus har en arkitektur för demografi centrerad. Kontakta [oändlig Campus-supportteamet](mailto:sales@infinitecampus.com) att lägga till användare i oändlig Campus-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen oändlig Campus i åtkomstpanelen, bör det vara loggas in automatiskt till oändlig Campus som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
