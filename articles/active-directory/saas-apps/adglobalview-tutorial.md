---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med ADP-Globalview | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ADP Globalview.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1184129783b448cb2d6dc22e488ffe38401a1218
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264124"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp-globalview"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med ADP-Globalview

I den här självstudien får du lära dig hur du integrerar ADP-Globalview med Azure Active Directory (Azure AD). När du integrerar ADP-Globalview med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ADP-Globalview.
* Gör det möjligt för användarna att logga in automatiskt till ADP-Globalview med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* ADP Globalview-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* ADP Globalview stöder **IDP**-initierad enkel inloggning

## <a name="adding-adp-globalview-from-the-gallery"></a>Lägga till ADP Globalview från galleriet

För att konfigurera integreringen av ADP Globalview i Azure AD måste du lägga till ADP Globalview från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **ADP Globalview** i sökrutan.
1. Välj **ADP-Globalview** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp-globalview"></a>Konfigurera och testa enkel inloggning med Azure AD för ADP-Globalview

Konfigurera och testa Azure AD SSO med ADP-Globalview med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ADP-Globalview.

Om du vill konfigurera och testa Azure AD SSO med ADP Globalview slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ADP-GLOBALVIEW SSO](#configure-adp-globalview-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa ADP Globalview test User](#create-adp-globalview-test-user)** – om du vill ha en motsvarighet till B. Simon i ADP-Globalview som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan ADP- **Globalview** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    
    I textrutan **Identifierare** skriver du en URL med följande mönster:

    | |
    |--|
    | `https://<subdomain>.globalview.adp.com/federate`|
    | `https://<subdomain>.globalview.adp.com/federate2`|
    | |

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska identifieraren. Kontakta [kundsupporten för ADP Globalview](https://www.adp.com/contact-us/overview.aspx) och be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera ADP-Globalview** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ADP-Globalview.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **ADP Globalview**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-adp-globalview-sso"></a>Konfigurera ADP-Globalview SSO

För att konfigurera enkel inloggning på **ADP Globalview**-sidan behöver du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för ADP Globalview](https://www.adp.com/contact-us/overview.aspx). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-adp-globalview-test-user"></a>Skapa ADP Globalview-testanvändare

I det här avsnittet skapar du en användare som heter B. Simon i ADP-Globalview. Kontakta [supportteamet för ADP Globalview](https://www.adp.com/contact-us/overview.aspx) och lägg till användarna i ADP Globalview-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på ADP Globalview-panelen i åtkomstpanelen bör du automatiskt loggas in på ADP Globalview som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa ADP-Globalview med Azure AD](https://aad.portal.azure.com/)

