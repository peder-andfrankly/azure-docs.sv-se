---
title: 'Självstudier: Azure Active Directory-integrering med OneTrust sekretess hanteringsprogramvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och programvaran för hantering av OneTrust sekretess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 5fbec8b6a1a21826896f8e2499b1e8b7237d0ff9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095887"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Självstudier: Azure Active Directory-integrering med hanteringsprogramvara för OneTrust sekretess

Lär dig hur du integrerar OneTrust sekretess hanteringsprogramvara med Azure Active Directory (AD Azure) i den här självstudien.
Integrera OneTrust sekretess hanteringsprogramvara med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har tillgång till programvaran för hantering av OneTrust sekretess.
* Du kan aktivera användarna att vara automatiskt inloggad till OneTrust sekretess hanteringsprogramvara (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med OneTrust hanteringsprogramvara för sekretess, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Programvaran för hantering av OneTrust sekretess enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för OneTrust sekretess hanteringsprogramvara **SP** och **IDP** -initierad SSO

* Har stöd för OneTrust sekretess hanteringsprogramvara **Just In Time** etableringen av användare

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Att lägga till OneTrust sekretess hanteringsprogramvara från galleriet

För att konfigurera integrering av hanteringsprogramvara för OneTrust sekretess i Azure AD, som du behöver lägga till OneTrust sekretess hanteringsprogramvara från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till OneTrust sekretess hanteringsprogramvara från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **OneTrust sekretess hanteringsprogramvara**väljer **OneTrust sekretess hanteringsprogramvara** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

     ![OneTrust sekretess hanteringsprogramvara i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med OneTrust sekretess hanteringsprogramvara baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i OneTrust sekretess hanteringsprogramvara upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med OneTrust hanteringsprogramvara för sekretess, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera OneTrust sekretess Management programvara enkel inloggning](#configure-onetrust-privacy-management-software-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa OneTrust sekretess hanteringsprogramvara testanvändare](#create-onetrust-privacy-management-software-test-user)**  – du har en motsvarighet för Britta Simon i hanteringsprogramvara för OneTrust sekretess som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med hanteringsprogramvara för OneTrust sekretess:

1. I den [Azure-portalen](https://portal.azure.com/)på den **OneTrust sekretess hanteringsprogramvara** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![OneTrust sekretess Hanteringsdomän programvara och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du in en URL: `https://www.onetrust.com/saml2`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![OneTrust sekretess Hanteringsdomän programvara och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en. Kontakta [OneTrust sekretess Management-klientprogrammet supportteamet](mailto:support@onetrust.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

7. På den **konfigurera OneTrust sekretess hanteringsprogramvara** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-onetrust-privacy-management-software-single-sign-on"></a>Konfigurera OneTrust sekretess Management programvara enkel inloggning

Att konfigurera enkel inloggning på **OneTrust sekretess hanteringsprogramvara** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopieras URL: er från Azure portal för att [ Programvaran för hantering av OneTrust sekretess supportteamet](mailto:support@onetrust.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till programvaran för hantering av OneTrust sekretess.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **OneTrust sekretess hanteringsprogramvara**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **OneTrust sekretess hanteringsprogramvara**.

    ![Länken hanteringsprogramvara för OneTrust sekretess i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Skapa OneTrust sekretess hanteringsprogramvara testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i hanteringsprogramvara för OneTrust sekretess. Programvaran för hantering av OneTrust sekretess stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i OneTrust hanteringsprogramvara för sekretess, skapas en ny efter autentisering.

>[!Note]
>Om du vill skapa en användare manuellt, kontakta [OneTrust sekretess hanteringsprogramvara supportteamet](mailto:support@onetrust.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen hanteringsprogramvara för OneTrust sekretess i åtkomstpanelen bör det vara loggas in automatiskt till hanteringsprogramvara för OneTrust sekretess som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

