---
title: 'Självstudier: Azure Active Directory integrering med SecureW2 JoinNow-anslutning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SecureW2 JoinNow-anslutning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2445b3af-f827-40de-9097-6f5c933d0f53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dbb21bcea978f566082e5edb8831ac044c95fd6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880121"
---
# <a name="tutorial-integrate-securew2-joinnow-connector-with-azure-active-directory"></a>Självstudier: Integrera SecureW2 JoinNow-anslutning med Azure Active Directory

I den här självstudien får du lära dig att integrera SecureW2 JoinNow-anslutningsprogram med Azure Active Directory (Azure AD). När du integrerar SecureW2 JoinNow Connector med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SecureW2 JoinNow-anslutningsprogrammet.
* Gör det möjligt för användarna att logga in automatiskt till SecureW2 JoinNow Connector med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SecureW2 JoinNow Connector Single Sign-on (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SecureW2 JoinNow-anslutning stöder **SP** -INITIERAd SSO





## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>Lägga till SecureW2 JoinNow Connector från galleriet

Om du vill konfigurera integreringen av SecureW2 JoinNow-anslutningen i Azure AD måste du lägga till SecureW2 JoinNow Connector från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SecureW2 JoinNow Connector** i sökrutan.
1. Välj **SecureW2 JoinNow Connector** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med SecureW2 JoinNow Connector med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SecureW2 JoinNow-anslutningen.

Om du vill konfigurera och testa Azure AD SSO med SecureW2 JoinNow Connector slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SecureW2 JoinNow Connector SSO](#configure-securew2-joinnow-connector-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testuser-för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa SecureW2 JoinNow Connector test User](#create-securew2-joinnow-connector-test-user)** – om du vill ha en motsvarighet till B. Simon i SecureW2 JoinNow-anslutning som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **SecureW2 JoinNow Connector** Application Integration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [SecureW2 JoinNow Connector client support team](mailto:support@securew2.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **metadata-XML** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

1. I avsnittet **Konfigurera SecureW2 JoinNow-anslutning** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-securew2-joinnow-connector-sso"></a>Konfigurera SecureW2 JoinNow Connector SSO

Om du vill konfigurera enkel inloggning på **SecureW2 JoinNow Connector** -sidan måste du skicka den hämtade **metadata-XML-** filen och lämpliga kopierade url: er från Azure Portal till [SecureW2 JoinNow Connector support team](mailto:support@securew2.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.
### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SecureW2 JoinNow Connector.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **SecureW2 JoinNow Connector**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-securew2-joinnow-connector-test-user"></a>Skapa SecureW2 JoinNow Connector test User

I det här avsnittet skapar du en användare med namnet Britta Simon i SecureW2 JoinNow Connector. Arbeta med [SecureW2 JoinNow Connector support team](mailto:support@securew2.com) för att lägga till användare i SecureW2 JoinNow Connector-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på SecureW2 JoinNow Connector-panelen på åtkomst panelen, bör du loggas in automatiskt till SecureW2 JoinNow-anslutaren som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

