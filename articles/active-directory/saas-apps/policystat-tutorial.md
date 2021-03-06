---
title: 'Självstudie: Azure Active Directory integrering med PolicyStat | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 327e470d60235e6bf400293e80e3aec5f6144ff4
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "68943447"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Självstudie: Azure Active Directory integrering med PolicyStat

I den här självstudien får du lära dig hur du integrerar PolicyStat med Azure Active Directory (Azure AD).
Genom att integrera PolicyStat med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till PolicyStat.
* Du kan göra det möjligt för användarna att logga in automatiskt till PolicyStat (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med PolicyStat behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PolicyStat-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* PolicyStat stöder **SP** -INITIERAd SSO

* PolicyStat stöder **just-in-Time** User-etablering

## <a name="adding-policystat-from-the-gallery"></a>Lägga till PolicyStat från galleriet

Om du vill konfigurera integreringen av PolicyStat i Azure AD måste du lägga till PolicyStat från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till PolicyStat från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **PolicyStat**, väljer **PolicyStat** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![PolicyStat i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med PolicyStat baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i PolicyStat upprättas.

Om du vill konfigurera och testa enkel inloggning med PolicyStat i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera PolicyStat-enkel inloggning](#configure-policystat-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa PolicyStat test User](#create-policystat-test-user)** – om du vill ha en motsvarighet till Britta Simon i PolicyStat som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med PolicyStat i Azure AD:

1. Välj **enkel inloggning**på sidan **PolicyStat** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för PolicyStat-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.policystat.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren. Kontakta [PolicyStat client support team](http://www.policystat.com/support/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

5. Ditt PolicyStat-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![mallar](common/edit-attribute.png)

6. Utöver ovan förväntar sig PolicyStat-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix ([mail]) |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.
    
    ![mallar](common/new-save-attribute.png)

    ![mallar](./media/policystat-tutorial/attribute01.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj källa som **omvandling**.

    e. Skriv det attributvärde som visas för raden i listan **omvandling** .
    
    f. I listan **parameter 1** skriver du det attributvärde som visas för raden.

    g. Klicka på **Save** (Spara).

7. I avsnittet **Konfigurera PolicyStat** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-policystat-single-sign-on"></a>Konfigurera PolicyStat enkel inloggning

1. Logga in på din PolicyStat-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på fliken **admin** och klicka sedan på **konfiguration för enkel inloggning** i det vänstra navigerings fönstret.
   
    ![Menyn administratör](./media/policystat-tutorial/ic808633.png "Menyn administratör")

3. I avsnittet **installation** väljer du **Aktivera integrering av enkel inloggning**.
   
    ![Konfiguration av enkel inloggning](./media/policystat-tutorial/ic808634.png "Konfiguration av enkel inloggning")

4. Klicka på **Konfigurera attribut**och utför sedan följande steg i avsnittet **Konfigurera attribut** :
   
    ![Konfiguration av enkel inloggning](./media/policystat-tutorial/ic808635.png "Konfiguration av enkel inloggning")
   
    a. I text rutan **attribut för användar namn** skriver du **UID**.

    b. I text rutan för **första** namnattribut skriver du **FirstName** för User **Britta**.

    c. I text rutan för det **sista namnattributet** skriver du **LastName** för User **Simon**.

    d. Skriv **EmailAddress** för användar `BrittaSimon@contoso.com` i text rutan för **e-postattribut** .

    e. Klicka på **Spara ändringar**.

5. Klicka på **IDP metadata**och utför sedan följande steg i avsnittet **IDP metadata** :
   
    ![Konfiguration av enkel inloggning](./media/policystat-tutorial/ic808636.png "Konfiguration av enkel inloggning")
   
    a. Öppna den hämtade metadatafilen, kopiera innehållet och klistra in den i text rutan för **din identitetsprovider** .

    b. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** anger du brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PolicyStat.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **PolicyStat**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **PolicyStat**.

    ![PolicyStat-länken i program listan](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-policystat-test-user"></a>Skapa PolicyStat test användare

I det här avsnittet skapas en användare som kallas Britta Simon i PolicyStat. PolicyStat stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i PolicyStat skapas en ny efter autentiseringen.

>[!NOTE]
>Du kan använda andra verktyg för PolicyStat av användar konton eller API: er som tillhandahålls av PolicyStat för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen PolicyStat på åtkomst panelen, bör du loggas in automatiskt på den PolicyStat som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

