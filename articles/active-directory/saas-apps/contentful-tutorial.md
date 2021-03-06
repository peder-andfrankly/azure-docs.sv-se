---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med contentable | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och innehåll.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f29e1015-d508-4698-a381-5d871c646161
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd218c61114c1e15009ace5a9a9bd7a536996e86
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968649"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med innehåll

I den här självstudien får du lära dig hur du integrerar innehåll med Azure Active Directory (Azure AD). När du integrerar innehåll med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till innehåll.
* Gör det möjligt för användarna att logga in automatiskt till innehåll med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration med contentable Single Sign-on (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Contentable stöder **SP-och IDP** -INITIERAd SSO
* Contentable stöder **just-in-Time** User-etablering

> [!NOTE]
> Identifieraren för det här programmet är ett fast sträng värde. Endast en instans kan konfigureras i en klient.

## <a name="adding-contentful-from-the-gallery"></a>Lägga till innehåll från galleriet

Om du vill konfigurera en integrering av innehåll i Azure AD måste du lägga till innehåll från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **innehåll** i sökrutan.
1. Välj **innehåll** som finns i resultaten och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>Konfigurera och testa enkel inloggning med Azure AD för innehåll

Konfigurera och testa Azure AD SSO med innehåll med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i innehåll.

Om du vill konfigurera och testa Azure AD SSO med contentable slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera contentable SSO](#configure-contentful-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    * **[Skapa contentable test User](#create-contentful-test-user)** -om du vill ha en motsvarighet till B. Simon i innehåll som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för integration av **innehålls** utan program och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    - I text rutan **svars-URL** kopierar du URL: en för ACS (intygad konsument tjänst) från sidan för SSO-installation i contentable. Det kommer att se ut så här: `https://be.contentful.com/sso/<organization_id>/consume`

1. Klicka på **Ange ytterligare URL: er** och utför följande steg om du vill konfigurera programmet i läget **SP** -initierat:

    - I text rutan **inloggnings-URL** kopierar du samma URL för ACS (intygad konsument tjänst). Det kommer att se ut så här: `https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska svars-URL: en och inloggnings-URL: en genom att kopiera ACS-URL: en (intygad konsument tjänst) från sidan för SSO-installation i contentable.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera innehåll** , kopierar du inloggnings-URL: en för att konfigurera contentable SSO.

    ![Kopiera konfigurations-URL:er](media/contentful-tutorial/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till innehåll.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **innehållet**i listan program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på sidan.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på sidan.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-contentful-sso"></a>Konfigurera contentable SSO

Följ de här stegen för att konfigurera enkel inloggning på **innehålls** sidan.

1. Vid [innehålls](https://app.contentful.com)växling navigerar du till sidan SSO-konfiguration i **organisations inställningar**.
1. Klicka på **Konfigurera SSO**.
1. Kopiera och klistra in inloggnings-URL: en från avsnittet **Konfigurera innehåll** i Azure AD.
1. Kopiera och klistra in certifikatet från den base64-certifikat fil som du laddade ned från Azure AD.
1. Konfigurera ett SSO-namn för en SP-initierad inloggning.
1. Klicka på **Aktivera SSO**.

Om detta inte fungerar kan du kontakta det [innehålls lösa support teamet](mailto:support@contentful.com).

### <a name="create-contentful-test-user"></a>Skapa användare med innehålls kontroll

I det här avsnittet skapas en användare som heter B. Simon i contentable. Contentable stöder just-in-Time User-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i ett innehåll, skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen innehålls lös på åtkomst panelen, bör du loggas in automatiskt på det innehåll som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova innehåll med Azure AD](https://aad.portal.azure.com/)
