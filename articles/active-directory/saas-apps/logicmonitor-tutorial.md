---
title: 'Självstudie: Azure Active Directory integrering med LogicMonitor | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 463a8981689614d96100e03965117c9344aa5d50
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159516"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Självstudie: Azure Active Directory integrering med LogicMonitor

I den här självstudien får du lära dig hur du integrerar LogicMonitor med Azure Active Directory (Azure AD).
Genom att integrera LogicMonitor med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LogicMonitor.
* Du kan göra det möjligt för användarna att logga in automatiskt till LogicMonitor (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med LogicMonitor behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* LogicMonitor-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LogicMonitor stöder **SP** -INITIERAd SSO

## <a name="adding-logicmonitor-from-the-gallery"></a>Lägga till LogicMonitor från galleriet

Om du vill konfigurera integreringen av LogicMonitor i Azure AD måste du lägga till LogicMonitor från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till LogicMonitor från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **LogicMonitor**, väljer **LogicMonitor** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![LogicMonitor i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med LogicMonitor baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i LogicMonitor upprättas.

Om du vill konfigurera och testa enkel inloggning med LogicMonitor i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LogicMonitor-enkel inloggning](#configure-logicmonitor-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa LogicMonitor test User](#create-logicmonitor-test-user)** – om du vill ha en motsvarighet till Britta Simon i LogicMonitor som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med LogicMonitor i Azure AD:

1. Välj **enkel inloggning**på sidan **LogicMonitor** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för LogicMonitor-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.logicmonitor.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.logicmonitor.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [LogicMonitor client support team](https://www.logicmonitor.com/contact/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera LogicMonitor** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-logicmonitor-single-sign-on"></a>Konfigurera LogicMonitor enkel inloggning

1. Logga in på din **LogicMonitor** -företags webbplats som administratör.

2. På menyn längst upp klickar du på **Inställningar**.

    ![Inställningar](./media/logicmonitor-tutorial/ic790052.png "Inställningar")

3. I navigerings-bat till vänster klickar du på **enkel inloggning**

    ![Enkel inloggning](./media/logicmonitor-tutorial/ic790053.png "för Aha!")

4. I avsnittet **Inställningar för enkel inloggning (SSO)** , utför följande steg:

    ![Inställningar för enkel inloggning](./media/logicmonitor-tutorial/ic790054.png "Inställningar för enkel inloggning")

    a. Välj **aktivera enkel inloggning**.

    b. Som **standard roll tilldelning**väljer du **skrivskyddad**.

    c. Öppna den hämtade metadata-filen i anteckningar och klistra sedan in innehållet i filen i text rutan för **identitetsprovider** .

    d. Klicka på **Spara ändringar**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LogicMonitor.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **LogicMonitor**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **LogicMonitor**.

    ![LogicMonitor-länken i program listan](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-logicmonitor-test-user"></a>Skapa LogicMonitor test användare

För att Azure AD-användare ska kunna logga in måste de vara etablerade i LogicMonitor-programmet med sina Azure Active Directory användar namn.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din LogicMonitor-företags webbplats som administratör.

2. I menyn högst upp klickar du på **Inställningar**och sedan på **roller och användare**.

    ![Roller och användare](./media/logicmonitor-tutorial/ic790056.png "Roller och användare")

3. Klicka på **Lägg till**.

4. I avsnittet **Lägg till ett konto** utför du följande steg:

    ![Lägga till ett konto](./media/logicmonitor-tutorial/ic790057.png "Lägga till ett konto")

    a. Ange **användar namn**, **e-post**, **lösen ord**och **lösen ords** värden för den Azure Active Directory användare som du vill etablera i de relaterade text rutorna.

    b. Välj **roller**, **Visa behörigheter**och **status**.

    c. Klicka på **Skicka**.

> [!NOTE]
> Du kan använda andra verktyg för LogicMonitor av användar konton eller API: er som tillhandahålls av LogicMonitor för att etablera Azure Active Directory användar konton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen LogicMonitor på åtkomst panelen, bör du loggas in automatiskt på den LogicMonitor som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

