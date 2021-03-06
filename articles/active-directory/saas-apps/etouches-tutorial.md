---
title: 'Självstudie: Azure Active Directory integrering med etouches | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och etouches.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fb9bdfad1480e47eba919d6884f2042f11a2b9c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158238"
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Självstudie: Azure Active Directory integrering med etouches

I den här självstudien lär du dig att integrera etouches med Azure Active Directory (AD Azure).
Integreringen av etouches med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till etouches.
* Du kan göra så att dina användare automatiskt loggas in på etouches (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Du behöver följande för att konfigurera Azure AD-integrering med etouches:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* etouches-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* etouches har stöd för **SP**-initierad enkel inloggning

## <a name="adding-etouches-from-the-gallery"></a>Lägga till etouches från galleriet

För att konfigurera integrering av etouches i Azure AD behöver du lägga till etouches från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till etouches från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **etouches**, väljer **etouches** från resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![etouches i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med etouches baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i etouches upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med etouches behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för etouches](#configure-etouches-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa etouches-testanvändare](#create-etouches-test-user)** – för att ha en motsvarighet till Britta Simon i etouches som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med etouches:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **etouches** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med etouches-domän och -URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Du uppdaterar värdet med faktisk inloggnings-URL och identifierare. Detta förklaras senare i självstudien.
    > 

5. Ditt etouches-program förväntar sig SAML-försäkringar i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i SAML-tokenattributkonfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att lägga till attributen.

    ![mallar](common/edit-attribute.png)

6. Utöver ovanstående förväntar sig etouches-programmet att några fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | ------------------- | -------------------- |
    | E-post | user.mail | 

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![mallar](common/new-save-attribute.png)

    ![mallar](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Save** (Spara).

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **XML-federationsmetadata** från de angivna alternativen enligt dina behov och sparar dem på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. I avsnittet **Konfigurera etouches** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-etouches-single-sign-on"></a>Konfigurera enkel inloggning för etouches

1. Konfigurera enkel inloggning för ditt program genom att utföra följande steg i etouches-programmet: 

    ![etouches-konfiguration](./media/etouches-tutorial/tutorial_etouches_06.png) 

    a. Logga in på **etouches**-programmet med hjälp av administratörsrättigheter.
   
    b. Gå till **SAML**-konfiguration.

    c. I avsnittet **Allmänna inställningar** öppnar du det certifikat som laddats ned från Azure-portalen i Anteckningar, kopierar innehållet och klistrar in det i textrutan för IDP-metadata. 

    d. Klicka på knappen **Save & Stay** (Spara och stanna kvar).
  
    e. Klicka på knappen **Update Metadata** (Uppdatera metadata) i avsnittet SAML-metadata. 

    f. Då öppnas sidan, och enkel inloggning utförs. När enkel inloggning fungerar kan du konfigurera användarnamnet.

    g. I fältet Användarnamn väljer du **e-postadress** enligt bilden nedan. 

    h. Kopiera värdet för **SP-entitets-ID** och klistra in det i textrutan **Identifierare**, som finns i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    i. Kopiera värdet för **URL för enkel inloggning/ACS** och klistra in det i textrutan **Inloggnings-URL**, som finns i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.
   
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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till etouches.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **etouches**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **etouches**.

    ![Länken för etouches i programlistan](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-etouches-test-user"></a>Skapa etouches-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i etouches. Kontakta [kundsupporten för etouches](https://www.etouches.com/event-software/support/customer-support/) och lägg till användarna på etouches-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på etouches-panelen på åtkomstpanelen bör du automatiskt loggas in i etouches som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

