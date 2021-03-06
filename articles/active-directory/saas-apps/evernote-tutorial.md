---
title: 'Självstudier: Azure Active Directory enkel inloggning (SSO) med Evernote | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55216e9eb4bd5cda2635564480751459f05e8309
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121631"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Självstudier: Azure Active Directory enkel inloggning (SSO) med Evernote

I den här självstudien får du lära dig hur du integrerar Evernote med Azure Active Directory (Azure AD). När du integrerar Evernote med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Evernote.
* Gör det möjligt för användarna att logga in automatiskt på Evernote med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Aktive rad prenumeration på Evernote-enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Evernote stöder **SP-och IDP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-evernote-from-the-gallery"></a>Lägga till Evernote från galleriet

För att konfigurera integrering av Evernote i Azure AD behöver du lägga till Evernote från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Evernote** i rutan Sök.
1. Välj **Evernote** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Konfigurera och testa enkel inloggning med Azure AD för Evernote

Konfigurera och testa Azure AD SSO med Evernote med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Evernote.

Om du vill konfigurera och testa Azure AD SSO med Evernote slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testuser-för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Evernote SSO](#configure-evernote-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa användare av Evernote-test](#create-evernote-test-user)** – om du vill ha en motsvarighet till B. Simon i Evernote som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Evernote** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    I textrutan **Identifierare** skriver du en URL: `https://www.evernote.com/saml2`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du en URL: `https://www.evernote.com/Login.action`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

7. Om du vill ändra **signerings** alternativen klickar du på knappen **Redigera** för att öppna dialog rutan **SAML-signeringscertifikat** .

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Välj alternativet **signera SAML-svar och assert** för **signering**.

    b. Klicka på **Spara**

1. I avsnittet **Konfigurera Evernote** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Evernote.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Evernote**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-evernote-sso"></a>Konfigurera Evernote SSO

1. Om du vill automatisera konfigurationen i Evernote måste du installera **webb läsar tillägget Mina appar säkra inloggnings webbläsare** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klickar du på **installations programmet** för att dirigera dig till Evernote-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Evernote. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Evernote manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Evernote företags webbplats som administratör och utför följande steg:

4. Gå till **”Administratörskonsolen”**

    ![Administratörskonsolen](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Från **”Administratörskonsolen”** går du till **”Säkerhet”** och väljer **”Enkel inloggning”**

    ![Inställning för enkel inloggning](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Konfigurera följande värden:

    ![Inställning för certifikat](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Aktivera enkel inloggning:** Enkel inloggning är aktiverat som standard (klicka på **Inaktivera enkel inloggning** om du vill ta bort kravet på enkel inloggning)

    b. Klistra in det värde för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **SAML HTTP Request URL** (HTTP-begärande-URL för SAML).

    c. Öppna det certifikat som laddats ned från Azure AD i Anteckningar, kopiera innehållet, inklusive ”BEGIN CERTIFICATE” (början på certifikatet) och ”END CERTIFICATE” (slutet på certifikatet) och klistra in det i textrutan **X.509-certifikat**. 

    d. Klicka på **Spara ändringar**

### <a name="create-evernote-test-user"></a>Skapa Evernote testanvändare

För att Azure AD-användare ska kunna logga in på Evernote måste de tillhandahållas i Evernote.  
När det gäller Evernote är etablering en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din Evernote-företags webbplats som administratör.

2. Klicka på **”Administratörskonsolen”** .

    ![Administratörskonsolen](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Från **”Administratörskonsolen”** går du till **”Lägg till användare”** .

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Lägg till gruppmedlemmar** i textrutan **E-post**, skriv e-postadressen för användarkontot och klicka på **Bjud in.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. När inbjudan har skickats får Azure Active Directory-kontoinnehavaren ett e-postmeddelande för att tacka ja till inbjudan.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Evernote-panelen på åtkomstpanelen bör du automatiskt loggas in i Evernote som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Evernote med Azure AD](https://aad.portal.azure.com/)

