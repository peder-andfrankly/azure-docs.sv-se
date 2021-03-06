---
title: 'Självstudier: Azure Active Directory-integrering med Organisationsschema nu | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Organisationsschema nu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b96606b5558e0fbb81733b2f548a89bfb38d5f99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095433"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Självstudier: Azure Active Directory-integrering med Organisationsschema nu

I den här självstudien får du lära dig hur du integrerar Organisationsschema nu med Azure Active Directory (AD Azure).
Integrera Organisationsschema nu med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till organisationsschema nu.
* Du kan aktivera användarna att vara automatiskt inloggad Organisationsschema nu (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Organisationsschema nu behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Organisationsschema nu enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Organisationsschema nu stöder **SP** och **IDP** -initierad SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>Att lägga till organisationsschema nu från galleriet

För att konfigurera integrering av organisationsschema nu i Azure AD, som du behöver lägga till organisationsschema nu från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till organisationsschema nu från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Organisationsschema nu**väljer **Organisationsschema nu** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Organisationsschema nu i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Organisationsschema nu baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Organisationsschema nu upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Organisationsschema nu, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Organisationsschema nu enkel inloggning](#configure-orgchart-now-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Organisationsschema nu testanvändare](#create-orgchart-now-test-user)**  – du har en motsvarighet för Britta Simon Organisationsschema nu som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Organisationsschema nu:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Organisationsschema nu** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Organisationsschema nu domän och URL: er med enkel inloggning för information](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://sso2.orgchartnow.com`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` är den **Azure AD-identifierare** kopieras från den **konfigurera Organisationsschema nu** avsnittet beskrivs senare i självstudien.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

7. På den **konfigurera Organisationsschema nu** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-orgchart-now-single-sign-on"></a>Konfigurera Organisationsschema nu enkel inloggning

Att konfigurera enkel inloggning på **Organisationsschema nu** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopierade URL: er från Azure portal för att [Organisationsschema nu support-teamet ](mailto:ocnsupport@officeworksoftware.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till organisationsschema nu.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Organisationsschema nu**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Organisationsschema nu**.

    ![Organisationsschema nu länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-orgchart-now-test-user"></a>Skapa Organisationsschema nu testanvändare

Om du vill aktivera Azure AD-användare att logga in på organisationsschema nu etableras de i Organisationsschema nu. 

1. Organisationsschema nu stöder just-in-time-etablering, vilket är som standard aktiverat. En ny användare har skapats under ett försök att komma åt Organisationsschema nu om det inte finns ännu. Just-in-time-funktionen för användaretablering kommer bara att skapa en **skrivskyddad** användare när en SSO-förfrågan kommer från en känd IDP: N och e-post i SAML-försäkran kan inte hittas i användarlistan. För den här Automatisk etablering funktionen måste du skapa en åtkomstgrupp benämnt **Allmänt** Organisationsschema nu. Följ de stegen nedan för att skapa en åtkomstgrupp:

    a. Gå till den **hantera grupper** alternativet när du klickar på den **kugghjulsikonen** i det övre högra hörnet av Användargränssnittet.

    ![Organisationsschema nu grupper](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Välj den **Lägg till** ikon och namn på gruppen **Allmänt** klickar **OK**. 

    ![Organisationsschema nu lägga till](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Välj mapparna du vill Allmänt eller skrivskyddad användare ska kunna komma åt:

    ![Organisationsschema nu mappar](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Lås** mappar så att endast administratörer kan ändra dem. Tryck på **OK**.

    ![Låser Organisationsschema nu](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Skapa **Admin** användare och **Läs/Skriv** användare, måste du manuellt skapa en användare för att få åtkomst till sina Privilegienivå via enkel inloggning. Gör följande för att etablera ett användarkonto:

    a. Logga in på organisationsschema nu som en administratör.

    b.  Klicka på **inställningar** i övre högra hörnet och gå sedan till **hantera användare**.

    ![Organisationsschema nu inställningar](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klicka på **Lägg till** och utför följande steg:

    ![Organisationsschema nu hantera](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * I den **användar-ID** textrutan Ange användar-ID som **brittasimon\@contoso.com**.

    * I **e-postadress** text, ange den e-postadressen för användaren som **brittasimon\@contoso.com**.

    * Klicka på **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Organisationsschema nu i åtkomstpanelen, bör det vara loggas in automatiskt till organisationsschema nu som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

