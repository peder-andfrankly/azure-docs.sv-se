---
title: 'Självstudier: Azure Active Directory-integrering med Predictix pris Reporting | Microsoft Docs'
description: I de här självstudierna lär du dig att konfigurera enkel inloggning mellan Azure Active Directory och rapportering om Predictix pris.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 691d0c43-3aa1-4220-9e46-e7a88db234ad
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 808b2d964bb39af6b410a84563717102ebece454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094112"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Självstudier: Azure Active Directory-integrering med Predictix pris-rapportering

I de här självstudierna lär du dig att integrera Predictix pris rapportering med Azure Active Directory (AD Azure).

Den här integrationen har följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till Predictix pris Reporting.
* Du kan aktivera användarna att logga in automatiskt till Predictix pris rapportering (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har någon Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Predictix pris rapportering, behöver du:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du registrera dig för en [utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/) prenumeration.
* En Predictix pris Reporting prenumeration som har enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Predictix pris Reporting stöder SP-initierad SSO.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Att lägga till Predictix pris Reporting från galleriet

Om du vill konfigurera integrering av Predictix pris Reporting i Azure AD, som du behöver lägga till Predictix pris Reporting från galleriet i din lista över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företagsprogram** > **alla program**:

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett program, Välj **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. I sökrutan anger **Predictix pris Reporting**. Välj **Predictix pris Reporting** i sökresultatet och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Predictix pris rapportering med hjälp av en användare med namnet Britta Simon.
Om du vill aktivera enkel inloggning, måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Predictix pris Reporting.

Om du vill konfigurera och testa Azure AD enkel inloggning med Predictix pris rapportering, måste du slutföra de här stegen:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  att aktivera funktionen för dina användare.
2. **[Konfigurera Predictix pris Reporting enkel inloggning](#configure-predictix-price-reporting-single-sign-on)**  på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Azure AD enkel inloggning för användaren.
5. **[Skapa en testanvändare Predictix pris Reporting](#create-a-predictix-price-reporting-test-user)**  som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med Predictix pris rapportering, göra följande:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Predictix pris Reporting** application integration markerar **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML/WS-Fed** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer den **redigera** ikonen för att öppna den **SAML grundkonfiguration** dialogrutan:

    ![Ikonen Redigera](common/edit-urls.png)

4. I den **SAML grundkonfiguration** dialogrutan rutan, utför följande steg.

    ![Dialogrutan för grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. I den **inloggnings-URL** anger en URL i det här mönstret:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. I den **identifierare (entitets-ID)** anger en URL i det här mönstret:

        | |
        |--|
        | `https://<companyname-pricing>.predictix.com` |
        | `https://<companyname-pricing>.dev.predictix.com` |
        | |

    > [!NOTE]
    > Dessa värden är platshållare. Du måste använda faktiska inloggnings-URL och identifierare. Kontakta den [Predictix pris Reporting supportteamet](https://www.infor.com/company/customer-center/) att hämta värdena. Du kan också referera till de mönster som visas i den **SAML grundkonfiguration** dialogrutan i Azure-portalen.

5. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer den **hämta** länka bredvid **certifikat (Base64)** enligt krav och spara certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

6. I den **konfigurera Predictix pris Reporting** avsnittet, kopiera de lämpliga URL: er, baserat på dina krav.

    ![Kopiera URL: er för konfiguration](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **URL för utloggning**.

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Konfigurera Predictix pris Reporting enkel inloggning

Om du vill konfigurera enkel inloggning på Predictix pris Reporting-sida, du behöver skicka det certifikat som du hämtade och URL: er som du kopierade från Azure portal för att den [Predictix pris Reporting supportteamet](https://www.infor.com/company/customer-center/). Det här laget säkerställer SAML SSO-anslutningen är korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan väljer **användare**, och välj sedan **alla användare**:

    ![Välj alla användare](common/users.png)

2. Välj **ny användare** överst på skärmen:

    ![Välj ny användare](common/new-user.png)

3. I den **användaren** dialogrutan rutan, vidta följande steg.

    ![Användardialogrutan](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **BrittaSimon @\<företagsdomänen >.\< tillägget >** . (Till exempel BrittaSimon@contoso.com.)

    1. Välj **visa lösenord**, och sedan skriva ned det värde som är i den **lösenord** box.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure AD enkel inloggning ger användarens företagsidentitet åtkomst Predictix pris Reporting.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **Predictix pris Reporting**.

    ![Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Predictix pris Reporting**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** i listan och klicka sedan på den **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka på den **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-predictix-price-reporting-test-user"></a>Skapa en testanvändare Predictix pris Reporting

Därefter måste du skapa en användare med namnet Britta Simon i Predictix pris Reporting. Arbeta med den [Predictix pris Reporting supportteamet](https://www.infor.com/company/customer-center/) att lägga till användare. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu ska du testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Predictix pris Reporting i åtkomstpanelen, bör det vara loggas in automatiskt till Predictix pris Reporting-instansen som du ställer in enkel inloggning. Mer information finns i [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)