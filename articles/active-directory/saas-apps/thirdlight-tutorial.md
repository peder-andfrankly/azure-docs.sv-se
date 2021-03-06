---
title: 'Självstudier: Azure Active Directory-integrering med ThirdLight | Microsoft Docs'
description: I de här självstudierna lär du dig att konfigurera enkel inloggning mellan Azure Active Directory och ThirdLight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 448d46cd21a63488c4f567d5555fe6406fc0fa73
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089097"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Självstudier: Azure Active Directory-integrering med ThirdLight

I de här självstudierna lär du dig att integrera ThirdLight med Azure Active Directory (AD Azure). Den här integrationen har följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till ThirdLight.
* Du kan aktivera användarna att logga in automatiskt till ThirdLight (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med ThirdLight, måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En ThirdLight-prenumeration som har enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* ThirdLight stöder SP-initierad SSO.

## <a name="add-thirdlight-from-the-gallery"></a>Lägg till ThirdLight från galleriet

Om du vill konfigurera integrering av ThirdLight i Azure AD, som du behöver lägga till ThirdLight från galleriet i din lista över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företagsprogram** > **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program, Välj **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. I sökrutan anger **ThirdLight**. Välj **ThirdLight** i sökresultatet och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ThirdLight med hjälp av en användare med namnet Britta Simon.
Om du vill aktivera enkel inloggning, måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i ThirdLight.

Om du vill konfigurera och testa Azure AD enkel inloggning med ThirdLight, måste du slutföra de här stegen:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  att aktivera funktionen för dina användare.
2. **[Konfigurera ThirdLight enkel inloggning](#configure-thirdlight-single-sign-on)**  på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Azure AD enkel inloggning för användaren.
5. **[Skapa en testanvändare ThirdLight](#create-a-thirdlight-test-user)**  som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen.

Konfigurera Azure AD enkel inloggning med ThirdLight genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com/), på sidan ThirdLight application integration väljer **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML/WS-Fed** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer den **redigera** ikonen för att öppna den **SAML grundkonfiguration** dialogrutan:

    ![Ikonen Redigera](common/edit-urls.png)

4. I den **SAML grundkonfiguration** dialogrutan rutan, utför följande steg.

    ![Dialogrutan för grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. I den **inloggnings-URL** anger en URL i det här mönstret:
    
          `https://<subdomain>.thirdlight.com/`

    1. I den **identifierare (entitets-ID)** anger en URL i det här mönstret:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Dessa värden är platshållare. Du måste använda faktiska inloggnings-URL och identifierare. Kontakta den [ThirdLight supportteamet](https://www.thirdlight.com/support) att hämta värdena. Du kan också referera till de mönster som visas i den **SAML grundkonfiguration** dialogrutan i Azure-portalen.

5. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer den **hämta** länka bredvid **Federation Metadata-XML** enligt krav och spara filen på datorn:

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

6. I den **konfigurera ThirdLight** avsnittet, kopiera de lämpliga URL: er, baserat på dina krav:

    ![Kopiera URL: er för konfiguration](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **URL för utloggning**.

### <a name="configure-thirdlight-single-sign-on"></a>Konfigurera ThirdLight enkel inloggning

1. I ett nytt webbläsarfönster, loggar du in din ThirdLight företagets webbplats som en administratör.

1. Gå till **Configuration** > **systemadministration** > **SAML2**:

    ![Systemadministration](./media/thirdlight-tutorial/ic805843.png "systemadministration")

1. Vidta följande steg i konfigurationsavsnittet SAML2.
  
    ![Konfigurationsavsnittet för SAML2](./media/thirdlight-tutorial/ic805844.png "SAML2 konfigurationsavsnittet")

    1. Välj **aktivera SAML2 enkel inloggning**.

    1. Under **källa för IDP: N Metadata**väljer **Load IdP Metadata från XML**.

    1. Öppna metadatafilen som du hämtade från Azure-portalen i föregående avsnitt. Kopiera filens innehåll och klistra in den i den **XML-Metadata för IDP: N** box.

    1. Välj **spara SAML2 inställningar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan väljer **användare**, och välj sedan **alla användare**:

    ![Välj alla användare](common/users.png)

2. Välj **ny användare** överst i fönstret:

    ![Välj ny användare](common/new-user.png)

3. I den **användaren** dialogrutan rutan, vidta följande steg.

    ![Användardialogrutan](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **BrittaSimon @\<företagsdomänen >.\< tillägget >** . (Till exempel BrittaSimon@contoso.com.)

    1. Välj **visa lösenord**, och sedan skriva ned det värde som är i den **lösenord** box.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst ThirdLight.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **ThirdLight**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **ThirdLight**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** i listan och klicka sedan på den **Välj** knappen längst ned i fönstret.

6. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka på den **Välj** knappen längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-thirdlight-test-user"></a>Skapa en ThirdLight testanvändare

Om du vill aktivera Azure AD-användare att logga in på ThirdLight som du behöver lägga till dem i ThirdLight. Du måste lägga till dem manuellt.

Skapa ett användarkonto genom att göra följande:

1. Logga in på webbplatsen ThirdLight företag som en administratör.

1. Gå till den **användare** fliken.

1. Välj **användare och grupper**.

1. Välj **Lägg till ny användare**.

1. Ange användarnamnet, ett namn eller beskrivning och e-postadressen till en giltig Azure AD-konto som du vill etablera. Välj en förinställning eller grupp av nya medlemmar.

1. Välj **Skapa**.

> [!NOTE]
> Du kan använda valfri användare verktyg för skapande av konto eller API tillhandahålls av ThirdLight att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu ska du testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen ThirdLight i åtkomstpanelen, bör det vara loggas in automatiskt till ThirdLight-instansen som du ställer in enkel inloggning. Läs mer om åtkomstpanelen [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
