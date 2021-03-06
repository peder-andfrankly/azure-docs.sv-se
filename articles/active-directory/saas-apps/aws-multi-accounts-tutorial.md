---
title: 'Självstudie: Azure Active Directory integration med Amazon Web Services (AWS) för att ansluta flera konton | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure AD och flera konton med Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb528d71b94449b282947a487e4fc79b343df778
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195909"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Självstudie: Azure Active Directory integration med flera Amazon Web Services-konton (AWS)

I den här självstudien får du lära dig att integrera Azure Active Directory (Azure AD) med flera konton av Amazon Web Services (AWS).

Integreringen av Amazon Web Services (AWS) med Azure AD medför följande fördelar:

- Du kan i Azure AD styra vem som har åtkomst till Amazon Web Services (AWS).
- Du kan låta dina användare automatiskt bli inloggade till Amazon Web Services (AWS) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om SaaS-appens integrering med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Tänk på att ansluta en AWS-app till alla dina AWS-konton är inte den rekommenderade metoden. I stället rekommenderar vi att du använder [den här](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) metoden för att konfigurera flera instanser av AWS-kontot till flera instanser av AWS-appar i Azure AD.

**Observera att vi inte rekommenderar att du använder den här metoden på grund av följande:**

* Du måste använda Graph Explorer-metoden för att uppdatera alla roller i appen. Vi rekommenderar inte att du använder manifest fil metoden.

* Vi har sett kunder som rapporterar att efter att ha lagt till ~ 1200-AppData för en enda AWS-app, och alla åtgärder i appen har börjat visa felen som är relaterade till storlek. Det finns en hård gräns för programobjektets storlek.

* Du måste uppdatera rollen manuellt när rollerna läggs till i något av kontona, vilket är en ersättnings metod och inte heller tillägg. Även om dina konton växer blir detta n x n-relation med konton och roller.

* Alla AWS-konton kommer att använda samma XML-fil för federationsmetadata och vid tidpunkten för certifikat förnyelse måste du köra den här enorma övningen för att uppdatera certifikatet på alla AWS-konton på samma gång

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Amazon Web Services (AWS) behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Amazon Web Services (AWS)-prenumeration med enkel inloggning aktiverat

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Amazon Web Services (AWS) stöder **SP- och IDP**-initierad enkel inloggning

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Lägga till Amazon Web Services (AWS) från galleriet

För att konfigurera integreringen av Amazon Web Services (AWS) med Azure AD måste du lägga till Amazon Web Services (AWS) från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Amazon Web Services (AWS) från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Amazon Web Services (AWS)** , väljer **Amazon Web Services (AWS)** från resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![Amazon Web Services (AWS) i resultatlistan](common/search-new-app.png)

5. När du har lagt till programmet går du till sidan **Egenskaper** och kopierar **objekt-ID: t**.

    ![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Amazon Web Services (AWS) baserat på en test användare som kallas "Britta Simon".

För att enkel inloggning ska fungera måste Azure AD veta vad motsvarande användare i Amazon Web Services (AWS) är till en användare i Azure AD. Med andra ord måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Amazon Web Services (AWS).

I Amazon Web Services (AWS) tilldelar du värdet för **användar namnet** i Azure AD som värdet för **användar** namnet för att upprätta länk relationen.

För att konfigurera och testa enkel inloggning med Azure AD med Amazon Web Services (AWS) behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure Portal och konfigurerar enkel inloggning i ditt Amazon Web Services (AWS)-program.

**Utför följande steg för att konfigurera enkel inloggning för Azure AD med Amazon Web Services (AWS):**

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Amazon Web Services (AWS)** -programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![image](common/preintegrated.png)

5. Amazon Web Services (AWS)-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialog rutan **användarattribut & anspråk** .

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn  | Källattribut  | Namnrymd |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Roll            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | ”ange ett värde mellan 900 sekunder (15 minuter) och 43 200 sekunder (12 timmar)” |  https://aws.amazon.com/SAML/Attributes |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. I textrutan **Namnrymd** anger du det namnrymdsvärde som visas för den raden.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Save** (Spara).

7. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på **Ladda ned** för att ladda ned **XML-metadata för federationen** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Konfigurera enkel inloggning för Amazon Web Services (AWS)

1. I ett annat webbläsarfönster loggar du in på din Amazon Web Services (AWS)-företagsplats som administratör.

2. Klicka på **AWS Home**.

    ![Konfigurera start för enkel inloggning][11]

3. Klicka på **Identitets- och åtkomsthantering**.

    ![Konfigurera identitet för enkel inloggning][12]

4. Klicka på **Identitetsprovidrar** och sedan på **Skapa provider**.

    ![Konfigurera provider för enkel inloggning][13]

5. På dialogsidan **Konfigurera provider** utför du följande steg:

    ![Dialogruta för att konfigurera enkel inloggning][14]

    a. För **Providertyp** väljer du **SAML**.

    b. I text rutan **Providernamn** anger du ett providernamn (till exempel: *WAAD*).

    c. För att ladda upp den nedladdade **metadatafilen** från Azure-portalen klickar du på **Välj fil**.

    d. Klicka på **Nästa steg**.

6. På dialogsidan **Verifiera providerinformation** klickar du på **Skapa**.

    ![Konfigurera verifiering för enkel inloggning][15]

7. Klicka på **Roller** och sedan på **Skapa roll**.

    ![Konfigurera roller för enkel inloggning][16]

8. På sidan **Skapa roll** utför du följande steg:  

    ![Konfigurera förtroende för enkel inloggning][19]

    a. Välj **SAML 2.0-federation** under **Select type of trusted entity** (Välj typ av betrodd entitet).

    b. I **avsnittet Välj en saml 2,0-Provider**väljer du den **SAML-Provider** som du har skapat tidigare (till exempel: *WAAD*)

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).
  
    d. Klicka på **Nästa: behörigheter**.

9. I dialogrutan **Attach Permissions Policies** (Koppla behörighetsprinciper) kopplar du lämplig princip enligt din organisation. Klicka på **Nästa: granska**.  

    ![Konfigurera princip för enkel inloggning][33]

10. I dialogrutan **Granskning** utför du följande steg:

    ![Konfigurera granskning för enkel inloggning][34]

    a. I textrutan **Rollnamn** anger du ditt rollnamn.

    b. I textrutan **Rollbeskrivning** anger du beskrivningen.

    c. Klicka på **Skapa roll**.

    d. Skapa så många roller som behövs och mappa dem till identitetsprovidern.

11. Logga ut från det aktuella AWS-kontot och logga in med ett annat konto där du vill konfigurera enkel inloggning med Azure AD.

12. Utför steg-2 till steg-10 för att skapa flera roller som du vill konfigurera för det här kontot. Om du har fler än två konton utför du samma steg för alla konton för att skapa roller för dem.

13. När alla roller har skapats i kontona visas de i listan **roller** för dessa konton.

    ![Roll konfiguration](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Vi måste samla in alla roll ARN och betrodda entiteter för alla roller i alla konton, som vi behöver mappa manuellt med Azure AD-program.

15. Klicka på rollerna för att kopiera **rollens ARN** och **betrodda entitets** värden. Du behöver dessa värden för alla roller som du behöver skapa i Azure AD.

    ![Roll konfiguration](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Utför ovanstående steg för alla roller i alla konton och lagra dem i formatet **roll ARN, betrodda entiteter** i ett anteckningar.

17. Öppna [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

    a. Logga in på Graph Explorer-webbplatsen med hjälp av autentiseringsuppgifterna för global administratör/medadministratör för din klient organisation.

    b. Du måste ha tillräcklig behörighet för att skapa roller. Klicka på **ändra behörigheter** för att hämta de behörigheter som krävs.

    ![Dialog rutan Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Välj följande behörigheter i listan (om du inte redan har dem) och klicka på Ändra behörigheter 

    ![Dialog rutan Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Då uppmanas du att logga in igen och godkänna medgivande. När du har accepterat godkännandet loggas du in i Graph Explorer igen.

    e. Ändra List rutan version till **beta**. Om du vill hämta alla tjänst huvud namn från klienten använder du följande fråga:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger kan du använda följande mönster, som har din primära domän i den `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialog rutan Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Hämta den som du behöver ändra i listan över tjänstens huvud namn. Du kan också använda Ctrl + F för att söka i programmet från alla listade service princip ALS. Du kan använda följande fråga med hjälp av det **objekt-ID** som du har kopierat från sidan Azure AD-egenskaper för att komma till respektive tjänst huvud namn.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialog rutan Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extrahera egenskapen appRoles från objektet tjänstens huvud namn.

    ![Dialog rutan Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Du måste nu generera nya roller för ditt program. 

    i. Under-JSON är ett exempel på appRoles-objekt. Skapa ett liknande objekt för att lägga till de roller som du vill använda för ditt program.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Du kan bara lägga till nya roller efter **msiam_access** för korrigerings åtgärden. Du kan också lägga till så många roller du vill per organisationens behov. Azure AD skickar **värdet** för dessa roller som anspråks värde i SAML-svar.

    j. Gå tillbaka till Graph Explorer och ändra-metoden från **Hämta** till **patch**. Uppdatera tjänstens huvud namns objekt till att ha önskade roller genom att uppdatera appRoles-egenskapen som liknar den som visas ovan i exemplet. Kör korrigerings åtgärden genom att klicka på **Kör fråga** . Ett meddelande om att rollen har skapats bekräftar att rollen för ditt Amazon Web Services-program är klar.

    ![Dialog rutan Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. När tjänstens huvud namn har uppdaterats med fler roller kan du tilldela användare/grupper till respektive roller. Detta kan göras genom att gå till portalen och navigera till Amazon Web Services programmet. Klicka på fliken **användare och grupper** överst.

19. Vi rekommenderar att du skapar nya grupper för varje AWS-roll så att du kan tilldela den specifika rollen i gruppen. Observera att detta är en till en mappning för en grupp till en roll. Du kan sedan lägga till medlemmar som tillhör den gruppen.

20. När grupperna har skapats väljer du gruppen och tilldelar programmet.

    ![Konfigurera enkel inloggning Lägg till](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Kapslade grupper stöds inte när grupper tilldelas grupper.

21. Om du vill tilldela rollen till gruppen väljer du rollen och klickar på knappen **tilldela** längst ned på sidan.

    ![Konfigurera enkel inloggning Lägg till](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Observera att du måste uppdatera sessionen i Azure Portal för att se nya roller.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Amazon Web Services (AWS) i åtkomst panelen bör du hämta Amazon Web Services (AWS)-program sidan med möjlighet att välja rollen.

![Konfigurera enkel inloggning Lägg till](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Du kan också kontrol lera SAML-svaret för att se vilka roller som skickas som anspråk.

![Konfigurera enkel inloggning Lägg till](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Mer information om åtkomst panelen finns i [Introduktion till åtkomst panelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Konfigurera etablering med MS Graph API: er](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
