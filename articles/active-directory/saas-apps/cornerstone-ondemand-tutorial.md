---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med ett hörn OnDemand | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CornerStone OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 363b5b2dc2891166f779c98c6de1487bc45047a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227638"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-ondemand"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med ett hörn OnDemand

I den här självstudien får du lära dig hur du integrerar en hörn OnDemand med Azure Active Directory (Azure AD). När du integrerar en hörn OnDemand med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ett hörn OnDemand.
* Gör det möjligt för dina användare att vara automatiskt inloggade till att gå till hörn OnDemand med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En prenumeration med enkel inloggning (SSO) för hörn OnDemand.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Cornerstone OnDemand har stöd för **SP**-initierad enkel inloggning
* Cornerstone OnDemand har stöd för [automatisk användaretablering](cornerstone-ondemand-provisioning-tutorial.md)

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Lägga till Cornerstone OnDemand från galleriet

För att konfigurera integrering av Cornerstone OnDemand i Azure AD behöver du lägga till Cornerstone OnDemand från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **hörn OnDemand** i sökrutan.
1. Välj **hörn OnDemand** på resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cornerstone-ondemand"></a>Konfigurera och testa enkel inloggning med Azure AD för en hörn OnDemand

Konfigurera och testa Azure AD SSO med ett hörn OnDemand med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i hörn OnDemand.

Om du vill konfigurera och testa Azure AD SSO med hörn OnDemand, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. Konfigurera den enkla inloggningen i **[hörn OnDemand](#configure-cornerstone-ondemand-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa](#create-cornerstone-ondemand-test-user)** en miljö med en OnDemand-test för att få en motsvarighet till B. Simon i ett hörn försök som är länkat till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/) **går du till** sidan för att visa och väljer **enkel inloggning**på sidan för **hörn OnDemand** -program integration.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company>.csod.com`

    b. I textrutan **Identifierare (entitets-ID)** skriver du en URL med följande mönster: `https://<company>.csod.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Cornerstone OnDemand-klientens supportteam](mailto:moreinfo@csod.com) för att hämta dessa värden. Du kan även se de mönster som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera hörn OnDemand** , kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
    1. I **Namn**-fältet skriver du `B.Simon`.  
    1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
    1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ett hörn OnDemand.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **hörn OnDemand**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-cornerstone-ondemand-sso"></a>Konfigurera hörn OnDemand/SSO

För att kunna konfigurera enkel inloggning på **Cornerstone OnDemand**-sidan måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [Cornerstone OnDemand-supporten](mailto:moreinfo@csod.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-cornerstone-ondemand-test-user"></a>Skapa testanvändare för Cornerstone OnDemand

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i ett hörn OnDemand. Cornerstone OnDemand stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](https://docs.microsoft.com/azure/active-directory/saas-apps/cornerstone-ondemand-provisioning-tutorial) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

Om du vill konfigurera användar etablering skickar du informationen (t. ex.: namn, e-post) om den Azure AD-användare som du vill etablera till [support teamet för hörn OnDemand](mailto:moreinfo@csod.com).

>[!NOTE]
>Du kan använda andra hörn åtgärder för att skapa användar konton eller API: er som tillhandahålls av ett hörn OnDemand för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Cornerstone OnDemand-panelen i åtkomstpanelen bör du automatiskt loggas in på Cornerstone OnDemand som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova hörn OnDemand med Azure AD](https://aad.portal.azure.com)
