---
title: Aktivera fjärråtkomst till SharePoint – Azure AD-programproxy
description: Beskriver grunderna för hur du integrerar en lokal SharePoint-server med Azure AD-programproxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d8f1bbd79e6dcbbd75e1ea1b98bd211d77ed1a9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275453"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Aktivera fjärråtkomst till SharePoint med Azure AD-programproxy

Den här steg-för-steg-guiden beskriver hur du integrerar en lokal SharePoint-grupp med Azure Active Directory (Azure AD) Application Proxy.

## <a name="prerequisites"></a>Krav

För att utföra konfigurationen behöver du följande resurser:
- En SharePoint 2013-Server grupp eller senare.
- En Azure AD-klient med en plan som inkluderar programproxy. Lär dig mer om [Azure AD-planer och priser](https://azure.microsoft.com/pricing/details/active-directory/).
- En [anpassad, verifierad domän](../fundamentals/add-custom-domain.md) i Azure AD-klienten.
- Lokala Active Directory synkroniseras med Azure AD Connect, via vilka användare kan [Logga in på Azure](../hybrid/plan-connect-user-signin.md).
- En Application Proxy-koppling installeras och körs på en dator i företags domänen.

Att konfigurera SharePoint med Application Proxy kräver två URL: er:
- En extern URL som är synlig för slutanvändare och som fastställs i Azure AD. Den här URL: en kan använda en anpassad domän. Lär dig mer om att [arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md).
- En intern URL, som endast är känd inom företags domänen och som aldrig har använts direkt.

> [!IMPORTANT]
> Följ dessa rekommendationer för den interna URL: en för att se till att länkarna mappas korrekt:
> - Använd HTTPS.
> - Använd inte anpassade portar.
> - I företags Domain Name System (DNS) skapar du en värd (A) som pekar på SharePoint WFE (eller belastningsutjämnaren) och inte ett alias (CName).

I den här artikeln används följande värden:
- Intern URL: `https://sharepoint`
- Extern URL: `https://spsites-demo1984.msappproxy.net/`
- Konto för programpool för SharePoint-webbprogram: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Steg 1: Konfigurera ett program i Azure AD som använder Application Proxy

I det här steget skapar du ett program i Azure Active Directory-klienten som använder programproxy. Du anger den externa URL: en och anger den interna URL: en som används senare i SharePoint.

1. Skapa appen enligt beskrivningen i följande inställningar. Stegvisa instruktioner finns i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Intern URL**: SharePoint-intern URL som kommer att anges senare i SharePoint, till exempel `https://sharepoint`.
   * **Förautentisering**: Azure Active Directory
   * **Översätt URL: er i huvuden**: Nej
   * **Översätt URL: er i program texten**: Nej

   ![Publicera SharePoint som programmet](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. När din app har publicerats följer du de här stegen för att konfigurera inställningarna för enkel inloggning:

   1. På sidan program i portalen väljer du **enkel inloggning**.
   1. För **läge för enkel inloggning**väljer du **integrerad Windows-autentisering**.
   1. Ange det **interna programmets SPN** till det värde som du angav tidigare. I det här exemplet är värdet `HTTP/sharepoint`.
   1. Under **delegerad inloggnings identitet**väljer du det lämpligaste alternativet för din Active Directory skogs konfiguration. Om du till exempel har en enda Active Directory domän i skogen väljer du **lokalt SAM-kontonamn** (som visas i följande skärm bild). Men om dina användare inte finns i samma domän som SharePoint och Application Proxy Connector-servrarna, väljer du **lokala User Principal Name** (visas inte i skärm bilden).

   ![Konfigurera integrerad Windows-autentisering för enkel inloggning](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Slutför konfigurationen av programmet genom att gå till avsnittet **användare och grupper** och tilldela användare åtkomst till det här programmet. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Steg 2: konfigurera SharePoint-webbprogrammet

SharePoint-webbprogrammet måste konfigureras med Kerberos och lämpliga alternativa åtkomst mappningar fungerar korrekt med Azure AD-programproxy. Det finns två möjliga alternativ:

- Skapa ett nytt webb program och Använd bara standard zonen. Detta är det bästa alternativet eftersom det erbjuder den bästa upplevelsen med SharePoint (till exempel länkar i e-postaviseringar som genereras av SharePoint alltid till standard zonen).
- Utöka ett befintligt webb program för att konfigurera Kerberos i en zon som inte är standard.

> [!IMPORTANT]
> Oavsett vilken zon som används måste programpoolens konto för SharePoint-webbappen vara ett domän konto för att Kerberos ska fungera korrekt.

### <a name="provision-the-sharepoint-web-application"></a>Etablera SharePoint-webbprogrammet

- Om du skapar ett nytt webb program och bara använder standard zonen (önskat alternativ):

    1. Starta **SharePoint Management Shell** och kör följande skript:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Öppna den **centrala administrations** webbplatsen för SharePoint.
    1. Under **Systeminställningar**väljer du **Konfigurera alternativa åtkomst mappningar**. Rutan för **insamling av alternativa åtkomsts mappningar** öppnas.
    1. Filtrera visningen med det nya webb programmet och bekräfta att du ser något som liknar detta:

       ![Alternativa åtkomst mappningar för webb program](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Om du utökar ett befintligt webb program till en ny zon (om du inte kan använda standard zonen):

    1. Starta SharePoint Management Shell och kör följande skript:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Öppna den **centrala administrations** webbplatsen för SharePoint.
    1. Under **Systeminställningar**väljer du **Konfigurera alternativa åtkomst mappningar**. Rutan för **insamling av alternativa åtkomsts mappningar** öppnas.
    1. Filtrera visningen med det webb program som har utökats och bekräfta att du ser något som liknar detta:

        ![Alternativa åtkomst mappningar för utökade program](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Kontrol lera att SharePoint-webbappen körs under ett domän konto

Följ dessa steg om du vill identifiera det konto som kör programpoolen för SharePoint-webbprogrammet och se till att det är ett domän konto:

1. Öppna den **centrala administrations** webbplatsen för SharePoint.
1. Gå till **säkerhet** och välj **Konfigurera tjänst konton**.
1. Välj **Web Application pool-YourWebApplicationName**.

   ![Alternativ för att konfigurera ett tjänstkonto](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Bekräfta att **Välj ett konto för den här komponenten** returnerar ett domän konto och kom ihåg det, eftersom det kommer att behövas i nästa steg.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Kontrol lera att ett HTTPS-certifikat har kon figurer ATS för IIS-platsen i extra nät zonen

Eftersom den interna URL: en använder HTTPS-protokoll (`https://SharePoint/`), måste ett certifikat anges på Internet Information Services (IIS)-platsen.

1. Öppna Windows PowerShell-konsolen.
1. Kör följande skript för att skapa ett självsignerat certifikat och Lägg till det i datorns MY Store:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Självsignerade certifikat är bara lämpliga i test syfte. I produktions miljöer rekommenderar vi starkt att du använder certifikat som utfärdats av en certifikat utfärdare i stället.

1. Öppna konsolen för Internet Information Services Manager.
1. Expandera servern i trädvyn, expandera **platser**, Välj webbplatsen **SharePoint-AAD-proxy** och välj **bindningar**.
1. Välj **https-bindning** och välj sedan **Redigera**.
1. I fältet SSL-certifikat väljer du **SharePoint** -certifikat och väljer sedan **OK**.

Nu kan du få åtkomst till SharePoint-webbplatsen externt via Azure AD-programproxy.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Steg 3: Konfigurera Kerberos-begränsad delegering

Användare autentiseras inlednings vis i Azure AD och sedan till SharePoint med hjälp av Kerberos via Azure AD-proxy-anslutaren. Om du vill tillåta att anslutningen får en Kerberos-token åt Azure AD-användaren, måste du konfigurera Kerberos-begränsad delegering (KCD) med protokoll över gång. Mer information om KCD finns i [Översikt över Kerberos-begränsad delegering](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Ange SPN för SharePoint-tjänstkontot

I den här artikeln är den interna URL: en `https://sharepoint`, vilket innebär att tjänstens huvud namn (SPN) är `HTTP/sharepoint`. Du måste ersätta dessa värden med de värden som motsvarar din miljö.
Om du vill registrera SPN-`HTTP/sharepoint` för konto `Contoso\spapppool`för SharePoint-programpoolen kör du följande kommando från en kommando tolk som administratör för domänen:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Kommandot `Setspn` söker efter SPN innan det läggs till. Om SPN redan finns visas ett **dubbelt SPN-värde** . I så fall bör du överväga att ta bort det befintliga SPN om det inte anges under rätt konto för programpool. Du kan kontrol lera att SPN har lagts till genom att köra kommandot `Setspn` med alternativet-L. Mer information om det här kommandot finns i [setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Kontrol lera att anslutningen är betrodd för delegering till SPN som har lagts till i SharePoint-programpoolens konto

Konfigurera KCD så att Azure AD-programproxy-tjänsten kan delegera användar identiteter till kontot för SharePoint-programpoolen. Konfigurera KCD genom att aktivera programproxy-kopplingen att hämta Kerberos-biljetter för de användare som har verifierats i Azure AD. Sedan skickar servern kontexten till mål programmet (SharePoint i det här fallet).

Om du vill konfigurera KCD följer du de här stegen för varje kopplings dator:

1. Logga in på en domänkontrollant som domän administratör och öppna sedan Active Directory användare och datorer.
1. Hitta datorn som kör Azure AD proxy-anslutningsprogrammet. I det här exemplet är det själva SharePoint-servern.
1. Dubbelklicka på datorn och välj sedan fliken **delegering** .
1. Kontrol lera att Delegerings alternativen är inställda på att **lita på den här datorn för delegering endast till de angivna tjänsterna**. Välj sedan **Använd valfritt autentiseringsprotokoll**.
1. Välj knappen **Lägg till** , Välj **användare eller datorer**och leta upp kontot för SharePoint-programpoolen. Till exempel: `Contoso\spapppool`.
1. I listan över SPN-namn, väljer du det konto som du skapade tidigare för tjänstkontot.
1. Välj **OK** och välj sedan **OK** igen för att spara ändringarna.
  
   ![Delegeringsinställningarna](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Du är nu redo att logga in på SharePoint genom att använda den externa URL: en och för att autentisera med Azure.

## <a name="troubleshoot-sign-in-errors"></a>Felsöka inloggnings fel

Om inloggningen till platsen inte fungerar kan du få mer information om problemet i anslutnings loggarna: från den dator som kör-anslutningen öppnar du logg boken, går till **program-och tjänst loggar** > **Microsoft** > **AadApplicationProxy** > **Connector**och kontrollerar **Administratörs** loggen.

## <a name="next-steps"></a>Nästa steg

* [Arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md)
* [Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
