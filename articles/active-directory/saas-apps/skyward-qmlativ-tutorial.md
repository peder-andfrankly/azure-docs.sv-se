---
title: 'Självstudie: Azure Active Directory integrering med skyward Qmlativ | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Skyward Qmlativ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f51ad077-b5d2-49b3-8d24-2e0c8708394a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f28a09e12ae1f980515c7e76069edde661cddcf9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161098"
---
# <a name="tutorial-azure-active-directory-integration-with-skyward-qmlativ"></a>Självstudie: Azure Active Directory integrering med skyward Qmlativ

I den här självstudien får du lära dig hur du integrerar Skyward Qmlativ med Azure Active Directory (Azure AD).
Integreringen av Skyward Qmlativ med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Skyward Qmlativ från Azure AD.
* Du kan göra så att dina användare automatiskt loggas in i Skyward Qmlativ (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Du behöver följande för att konfigurera Azure AD-integrering med Skyward Qmlativ:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Skyward Qmlativ-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Skyward Qmlativ stöder **SP**-initierad enkel inloggning

## <a name="adding-skyward-qmlativ-from-the-gallery"></a>Lägga till Skyward Qmlativ från galleriet

För att konfigurera integreringen av Skyward Qmlativ i Azure AD måste du lägga till Skyward Qmlativ från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Skyward Qmlativ från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Skyward Qmlativ**, väljer **Skyward Qmlativ** i resultatrutan och klickar på **Lägg till** för att lägga till programmet.

     ![Skyward Qmlativ i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning för Azure AD med Skyward Qmlativ baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Skyward Qmlativ upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Skyward Qmlativ måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Skyward Qmlativ](#configure-skyward-qmlativ-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en Skyward Qmlativ-testanvändare](#create-skyward-qmlativ-test-user)** – för att skapa en motsvarighet till Britta Simon i Skyward Qmlativ som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Skyward Qmlativ:

1. Välj **Enkel inloggning** på sidan för programintegration med **Skyward Qmlativ** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om domäner och URL:er för enkel inloggning med Skyward Qmlativ](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.skyward.com/<CUSTOMERIDENTIFIERSTS>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<BASEURL>/customeridentifierSTS`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Skyward Qmlativ-kundsupporten](mailto:steveb@skyward.com) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på kopieringsknappen i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** om du vill kopiera **URL:en för federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-skyward-qmlativ-single-sign-on"></a>Konfigurera enkel inloggning för Skyward Qmlativ

För att konfigurera enkel inloggning på **Skyward Qmlativ**-sidan måste du skicka **URL:en för appfederationsmetadata** till [Skyward Qmlativ-supportteamet](mailto:steveb@skyward.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Skyward Qmlativ.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Skyward Qmlativ**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Skyward Qmlativ**.

    ![Skyward Qmlativ-länken i listan med program](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-skyward-qmlativ-test-user"></a>Skapa en Skyward Qmlativ-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Skyward Qmlativ. Arbeta med [Skyward Qmlativ-supportteamet](mailto:steveb@skyward.com) när du lägger till användare till Skyward Qmlativ-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Skyward Qmlativ på åtkomstpanelen bör du loggas in automatiskt i det Skyward Qmlativ-program som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

