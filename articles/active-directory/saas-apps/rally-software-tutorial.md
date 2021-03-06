---
title: 'Självstudier: Azure Active Directory-integrering med Rally programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rally programvara.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: de84d03c3e0e433dbe7bc24c47b1766b32ad7bc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093179"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Självstudier: Azure Active Directory-integrering med Rally programvara

I den här självstudien får du lära dig hur du integrerar Rally programvara med Azure Active Directory (AD Azure).
Integrera Rally programvara med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Rally programvara.
* Du kan aktivera användarna att vara automatiskt inloggad Rally programvara (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Rally programvara, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Rally enkel inloggning aktiverat programvaruprenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Rally programvara stöder **SP** -initierad SSO

## <a name="adding-rally-software-from-the-gallery"></a>Lägga till Rally programvara från galleriet

För att konfigurera integrering av Rally programvara i Azure AD, som du behöver lägga till Rally programvara från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Rally programvara från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Rally programvara**väljer **Rally programvara** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Rally programvara i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Rally programvara baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Rally programvara ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Rally programvara, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Rally programvara enkel inloggning](#configure-rally-software-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Rally programvara testanvändare](#create-rally-software-test-user)**  – du har en motsvarighet för Britta Simon Rally programvara som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Rally programvara:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Rally programvara** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Rally programvara domän och URL: er enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.rally.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.rally.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Rally klientprogrammet supportteamet](https://help.rallydev.com/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. På den **konfigurera Rally programvara** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-rally-software-single-sign-on"></a>Konfigurera Rally programvara enkel inloggning

1. Logga in på din **Rally programvara** klient.

2. I verktygsfältet högst upp, klickar du på **installationsprogrammet**, och välj sedan **prenumeration**.
   
    ![Prenumeration](./media/rally-software-tutorial/ic769531.png "prenumeration")

3. Klicka på den **åtgärd** knappen. Välj **redigera prenumeration** på upp till höger i verktygsfältet.

4. På den **prenumeration** dialogrutan sida, utför följande steg och klicka sedan på **spara och Stäng**:
   
    ![Autentisering](./media/rally-software-tutorial/ic769542.png "Autentisering")
   
    a. Välj **Rally eller SSO autentisering** listrutan för autentisering.

    b. I den **-URL för identitetsprovider** textrutan klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen. 

    c. I den **SSO utloggning** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Rally programvara.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Rally programvara**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Rally programvara**.

    ![Länken Rally programvara i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-rally-software-test-user"></a>Skapa Rally programvara testanvändare

För Azure AD-användare för att kunna logga in, måste de etableras till programmet Rally programvara med hjälp av deras Azure Active Directory-användarnamn.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din Rally programvara-klient.

2. Gå till **installationsprogrammet \> användare**, och klicka sedan på **+ Lägg till ny**.
   
    ![Användare](./media/rally-software-tutorial/ic781039.png "Användare")

3. Skriv namnet i den nya användaren textrutan och klicka sedan på **Lägg till med information om**.

4. I avsnittet **Skapa användare** utför du följande steg:
   
    ![Skapa användare](./media/rally-software-tutorial/ic781040.png "Skapa användare")

    a. I den **användarnamn** textrutan skriver du namnet på användaren som **Brittsimon**.
   
    b. I **e-postadress** textrutan Ange e-postadress för användaren som brittasimon@contoso.com.

    c. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    d. I textrutan **Efternamn** anger du efternamnet på användaren som **Simon**.

    e. Klicka på **spara och Stäng**.

   >[!NOTE]
   >Du kan använda andra Rally användaren-konto skapas programverktyg eller API: er som tillhandahålls av Rally programvara för att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Rally programvara i åtkomstpanelen, bör det vara loggas in automatiskt till Rally programvara som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

