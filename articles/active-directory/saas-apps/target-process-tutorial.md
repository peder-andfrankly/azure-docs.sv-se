---
title: 'Självstudie: Azure Active Directory-integrering med TargetProcess | Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 60cd043c34ba2aee365c59feb38cc9dd02d45746
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089048"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Självstudie: Azure Active Directory-integrering med TargetProcess

I den här självstudien får du lära dig att integrera TargetProcess med Azure Active Directory (Azure AD).
Om du integrerar TargetProcess med Azure AD får du följande fördelar:

* Du kan använda Azure AD till att styra vilka som ska ha åtkomst till TargetProcess.
* Du kan låta dina användare loggas in automatiskt på TargetProcess (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med TargetProcess, måste du ha följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TargetProcess-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TargetProcess har stöd för **SP**-initierad enkel inloggning
* TargetProcess stöder användarförsörjning med **Just-in-time**

## <a name="adding-targetprocess-from-the-gallery"></a>Lägga till TargetProcess från galleriet

Om du vill konfigurera integreringen av TargetProcess i Azure AD, måste du lägga till TargetProcess från galleriet i din lista med hanterade SaaS-appar.

**Utför följande steg för att lägga till TargetProcess från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **TargetProcess** och väljer **TargetProcess** i resultatpanelen. Klicka sedan på knappen **Lägg till** för att lägga till programmet.

     ![TargetProcess i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med TargetProcess, baserat testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i TargetProcess upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med TargetProcess, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera TargetProcess Single Sign-On](#configure-targetprocess-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare för TargetProcess](#create-targetprocess-test-user)**  – för att få en motsvarighet till Britta Simon i TargetProcess som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure ADmed TargetProcess:

1. I [Azure Portal](https://portal.azure.com/) går du till sidan för **TargetProcess**-programintegration och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om TargetProcess-domän och URL:er med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.tpondemand.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.tpondemand.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [TargetProcess-klientens supportteam](mailto:support@targetprocess.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera TargetProcess** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-targetprocess-single-sign-on"></a>Konfigurera TargetProcess Single Sign-On

1. Om du vill automatisera konfigurationen i **TargetProcess**, måste du installera **webbläsartillägget Enkel inloggning för mina appar** genom att klicka på **Installera tillägget**.

    ![image](./media/target-process-tutorial/install_extension.png)

2. När du har lagt till tillägg i webbläsaren klickar du på **Konfigurera TargetProcess**. Du omdirigeras då till TargetProcess-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på TargetProcess. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    **Om du vill konfigurera programmet manuellt utför du följande steg:**

3. Logga in på TargetProcess-programmet som administratör.

4. I menyn längst upp klickar du på **Konfiguration**.

    ![Konfiguration](./media/target-process-tutorial/tutorial_target_process_05.png)

5. Klicka på **inställningar** fliken.

    ![Inställningar](./media/target-process-tutorial/tutorial_target_process_06.png)

6. Klicka på **enkel inloggning** fliken.

    ![klicka på Enkel inloggning](./media/target-process-tutorial/tutorial_target_process_07.png)

7. Utför följande steg i inställningsdialogrutan för enkel inloggning:

    ![Konfigurera enkel inloggning](./media/target-process-tutorial/tutorial_target_process_08.png)

    a. Klicka på **Aktivera enkel inloggning**.

    b. I **inloggnings-URL** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. Öppna ditt nedladdade certifikatet i Anteckningar, kopiera innehållet och klistra sedan in det i textrutan **Certifikat**.

    d. klicka på **Aktivera JIT-etablering**.

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
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ser du till att Britta Simon ska kunna använda enkel inloggning i Azure genom att bevilja åtkomst till TargetProcess.

1. I Azure Portal väljer du **Företagsprogram**, **Alla program** och sedan **TargetProcess**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **TargetProcess**.

    ![Länken TargetProcess i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-targetprocess-test-user"></a>Skapa TargetProcess-testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i TargetProcess. TargetProcess stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i TargetProcess, skapas en ny efter autentisering.

> [!Note]
> Om du vill skapa en användare manuellt, kontaktar du  [TargetProcess-supportteamet](mailto:support@targetprocess.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på TargetProcess i åtkomstpanelen bör du bli automatiskt inloggad på den TargetProcess som du konfigurerade enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)