---
title: 'Självstudier: Azure Active Directory-integrering med ITRP | Microsoft Docs'
description: I de här självstudierna lär du dig att konfigurera enkel inloggning mellan Azure Active Directory och ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d44391624e29d2bdd182bb07452e0e8def2d1407
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656696"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Självstudier: Azure Active Directory-integrering med ITRP

I de här självstudierna lär du dig att integrera ITRP med Azure Active Directory (AD Azure).
Den här integrationen har följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till ITRP.
* Du kan aktivera användarna att logga in automatiskt till ITRP (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ITRP, måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En ITRP-prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* ITRP stöder SP-initierad SSO.

## <a name="add-itrp-from-the-gallery"></a>Lägg till ITRP från galleriet

Om du vill konfigurera integrering av ITRP i Azure AD, som du behöver lägga till ITRP från galleriet i din lista över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företagsprogram** > **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program, Välj **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. I sökrutan anger **ITRP**. Välj **ITRP** i sökresultatet och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ITRP med hjälp av en användare med namnet Britta Simon.
Om du vill aktivera enkel inloggning, måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i ITRP.

Om du vill konfigurera och testa Azure AD enkel inloggning med ITRP, måste du slutföra de här stegen:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  att aktivera funktionen för dina användare.
2. **[Konfigurera ITRP enkel inloggning](#configure-itrp-single-sign-on)**  på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Azure AD enkel inloggning för användaren.
5. **[Skapa en testanvändare ITRP](#create-an-itrp-test-user)**  som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen.

Konfigurera Azure AD enkel inloggning med ITRP genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com/), på sidan ITRP application integration väljer **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML/WS-Fed** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer den **redigera** ikonen för att öppna den **SAML grundkonfiguration** dialogrutan:

    ![Ikonen Redigera](common/edit-urls.png)

4. I den **SAML grundkonfiguration** dialogrutan rutan, vidta följande steg.

    ![Dialogrutan för grundläggande SAML-konfiguration](common/sp-identifier.png)

    1. I den **inloggnings-URL** anger en URL i det här mönstret:
    
       `https://<tenant-name>.itrp.com`

    1. I den **identifierare (entitets-ID)** anger en URL i det här mönstret:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Dessa värden är platshållare. Du måste använda faktiska inloggnings-URL och identifierare. Kontakta den [ITRP supportteam](https://www.itrp.com/support) att hämta värdena. Du kan också referera till de mönster som visas i den **SAML grundkonfiguration** dialogrutan i Azure-portalen.

5. I den **SAML-signeringscertifikat** väljer den **redigera** ikonen för att öppna den **SAML-signeringscertifikat** dialogrutan:

    ![Ikonen Redigera](common/edit-certificate.png)

6. I den **SAML-signeringscertifikat** dialogrutan, kopiera den **tumavtryck** värde och spara den:

    ![Kopiera tumavtrycket värdet](common/copy-thumbprint.png)

7. I den **konfigurera ITRP** avsnittet, kopiera de lämpliga URL: er, baserat på dina krav:

    ![Kopiera URL: er för konfiguration](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **URL för utloggning**.

### <a name="configure-itrp-single-sign-on"></a>Konfigurera ITRP enkel inloggning

1. I ett nytt webbläsarfönster, loggar du in din ITRP företagets webbplats som en administratör.

1. Längst ned i fönstret Välj den **inställningar** ikon:

    ![Ikon för inställningar](./media/itrp-tutorial/ic775570.png "inställningsikonen")

1. I den vänstra rutan väljer **enkel inloggning**:

    ![Välj enkel inloggning](./media/itrp-tutorial/ic775571.png "Välj enkel inloggning")

1. I den **enkel inloggning** configuration avsnittet, vidta följande steg.

    ![Enkel inloggning för avsnittet](./media/itrp-tutorial/ic775572.png "enkel inloggning för avsnittet")

    ![Enkel inloggning för avsnittet](./media/itrp-tutorial/ic775573.png "enkel inloggning för avsnittet")

    1. Välj **aktiverat**.

    1. I den **Remote utloggnings-URL** rutan, klistra in den **URL för utloggning** värde som du kopierade från Azure-portalen.

    1. I den **URL för SAML SSO** rutan, klistra in den **inloggnings-URL** värde som du kopierade från Azure-portalen.

    1. I den **certifikat fingeravtryck** rutan, klistra in den **tumavtryck** värdet för certifikatet som du kopierade från Azure-portalen.

    1. Välj **Spara**.

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

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst ITRP.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **ITRP**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **ITRP**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** i listan och klicka sedan på den **Välj** knappen längst ned i fönstret.

6. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka på den **Välj** knappen längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-an-itrp-test-user"></a>Skapa en ITRP testanvändare

Om du vill aktivera Azure AD-användare att logga in på ITRP som du behöver lägga till dem i ITRP. Du måste lägga till dem manuellt.

Skapa ett användarkonto genom att göra följande:

1. Logga in på din ITRP-klient.

1. Längst ned i fönstret Välj den **poster** ikon:

    ![Poster ikonen](./media/itrp-tutorial/ic775575.png "poster ikon")

1. I menyn, Välj **personer**:

    ![Välj personer](./media/itrp-tutorial/ic775587.png "Välj personer")

1. Klicka på plustecknet ( **+** ) att lägga till en ny person:

    ![Klicka på plustecknet](./media/itrp-tutorial/ic775576.png "Klicka på plustecknet")

1. I den **Lägg till ny Person** dialogrutan rutan, vidta följande steg.

    ![Lägg till ny Person dialogrutan](./media/itrp-tutorial/ic775577.png "dialogrutan Lägg till ny Person")

    1. Ange namn och e-post-adressen för en giltig Azure AD-konto som du vill lägga till.

    1. Välj **Spara**.

> [!NOTE]
> Du kan använda valfri användare verktyg för skapande av konto eller API tillhandahålls av ITRP att etablera användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu ska du testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen ITRP i åtkomstpanelen, bör det vara loggas in automatiskt till ITRP-instansen som du ställer in enkel inloggning. Läs mer om åtkomstpanelen [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
