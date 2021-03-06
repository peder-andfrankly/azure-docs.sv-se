---
title: Planera hybrid Azure Active Directory Join – Azure Active Directory
description: Lär dig att konfigurera anslutna Azure Active Directory-hybridenheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a4f85aeaf2fb263ba2df8f34a51f9e25c212aff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379324"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Gör så här: planera din hybrid Azure Active Directory delta-implementering

På ett liknande sätt som en användare är en enhet en annan kärn identitet som du vill skydda och använda den för att skydda dina resurser när som helst och var som helst. Du kan uppnå det här målet genom att placera och hantera enhets identiteter i Azure AD med någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

När du börjar använda dina enheter med Azure Active Directory maximerar du användarnas produktivitet med enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. På samma gång kan du skydda åtkomsten till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md).

Om du har en lokal Active Directory (AD)-miljö och vill ansluta till dina AD-domänanslutna datorer till Azure AD kan du göra detta genom att göra en hybrid Azure AD-anslutning. Den här artikeln innehåller relaterade steg för att implementera en hybrid Azure AD-anslutning i din miljö. 

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du är bekant med [introduktionen till enhets identitets hantering i Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Den minsta nödvändiga domänkontrollanten för Windows 10 hybrid Azure AD Join är Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planera implementeringen

För att planera din hybrid Azure AD-implementering bör du bekanta dig med:

|   |   |
| --- | --- |
| ![Markera][1] | Granska enheter som stöds |
| ![Markera][1] | Granska saker du bör känna till |
| ![Markera][1] | Granska kontrollerad validering av hybrid Azure AD-anslutning |
| ![Markera][1] | Välj ditt scenario baserat på din identitets infrastruktur |
| ![Markera][1] | Granska lokala AD UPN-stöd för Hybrid Azure AD-anslutning |

## <a name="review-supported-devices"></a>Granska enheter som stöds

Hybrid Azure AD-anslutning har stöd för ett brett utbud av Windows-enheter. Eftersom konfigurationen för enheter som kör äldre versioner av Windows kräver ytterligare eller olika steg, grupperas de enheter som stöds i två kategorier:

### <a name="windows-current-devices"></a>Aktuella Windows-enheter

- Windows 10
- Windows Server 2016
- Windows Server 2019

För enheter som kör operativ systemet Windows-skrivbordet visas den version som stöds i den här artikeln [information om Windows 10-utgåvor](https://docs.microsoft.com/windows/release-information/). Som bästa praxis rekommenderar Microsoft att du uppgraderar till den senaste versionen av Windows 10.

### <a name="windows-down-level-devices"></a>Windows-enheter på nivån

- Windows 8.1
- Windows 7. Information om support för Windows 7 finns i [Support för Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Information om support för Windows Server 2008 och 2008 R2 finns i [förbereda för Windows server 2008-slut för support](https://www.microsoft.com/cloud-platform/windows-server-2008).

Som första planerings steg bör du granska din miljö och avgöra om du behöver stöd för Windows-enheter på äldre nivå.

## <a name="review-things-you-should-know"></a>Granska saker du bör känna till

Hybrid Azure AD Join stöds inte för närvarande om din miljö består av en enda AD-skog som synkroniserar identitets data till mer än en Azure AD-klient.

Om din miljö använder VDI (Virtual Desktop Infrastructure), se [enhets identitet och skriv bords virtualisering](https://docs.microsoft.com/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

Hybrid Azure AD-anslutning stöds för FIPS-kompatibla TPM 2,0 och stöds inte för TPM 1,2. Om dina enheter har FIPS-kompatibel TPM 1,2 måste du inaktivera dem innan du fortsätter med hybrid Azure AD-anslutning. Microsoft tillhandahåller inga verktyg för att inaktivera FIPS-läge för TPM eftersom det är beroende av TPM-tillverkaren. Kontakta maskin varans OEM om du vill ha hjälp. Från och med WIndows 10 1903-versionen används inte TPM 1,2 för Hybrid Azure AD-anslutning och enheter med de här TPM: erna kommer att anses som om de inte har någon TPM.

Hybrid Azure AD Join stöds inte för Windows Server som kör rollen domänkontrollant (DC).

Hybrid Azure AD-anslutning stöds inte på Windows-enheter med äldre versioner vid användning av centrala autentiseringsuppgifter eller centrala profiler för användar profiler.

Om du förlitar dig på system förberedelse verktyget (Sysprep) och om du använder en avbildning med **tidigare versioner än Windows 10 1809** för installation kontrollerar du att avbildningen inte är från en enhet som redan har registrerats med Azure AD som en hybrid Azure AD-anslutning.

Om du förlitar dig på en ögonblicks bild av en virtuell dator (VM) för att skapa ytterligare virtuella datorer, se till att ögonblicks bilden inte är från en virtuell dator som redan är registrerad i Azure AD som en hybrid Azure AD-anslutning.

Om dina Windows 10-domänanslutna enheter är [registrerade i Azure AD](overview.md#getting-devices-in-azure-ad) till din klient organisation, kan det leda till ett dubbelt tillstånd med hybrid Azure AD-anslutna och en registrerad Azure AD-enhet. Vi rekommenderar att du uppgraderar till Windows 10 1803 (med KB4489894 installerat) eller senare för att automatiskt hantera det här scenariot. I pre-1803-versioner måste du ta bort Azure AD-registrerat tillstånd manuellt innan du aktiverar hybrid Azure AD Join. I 1803 och senare versioner har följande ändringar gjorts för att undvika detta dubbla tillstånd:

- Alla befintliga Azure AD-registrerade tillstånd tas bort automatiskt <i>när enheten är hybrid Azure AD-ansluten</i>.
- Du kan förhindra att din domänanslutna enhet är registrerad i Azure AD genom att lägga till register nyckeln-HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = DWORD: 00000001.
- Om du har konfigurerat Windows Hello för företag i Windows 10 1803 måste användaren konfigurera Windows Hello för företag igen när dubbelt tillstånd rensas. Det här problemet har åtgärd ATS med KB4512509

> [!NOTE]
> Den registrerade Azure AD-enheten tas inte bort automatiskt om den hanteras av Intune.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Granska kontrollerad validering av hybrid Azure AD-anslutning

När alla krav är på plats registreras Windows-enheter automatiskt som enheter i din Azure AD-klient. Status för dessa enhets identiteter i Azure AD kallas för Hybrid Azure AD-anslutning. Mer information om de begrepp som beskrivs i den här artikeln finns i artikeln [Introduktion till enhets identitets hantering i Azure Active Directory](overview.md).

Organisationer kan vilja göra en kontrollerad validering av hybrid Azure AD-anslutning innan den aktive ras i hela organisationen samtidigt. Läs igenom artikeln [kontrollerad verifiering av hybrid Azure AD-anslutning](hybrid-azuread-join-control.md) för att förstå hur du utför den.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Välj ditt scenario baserat på din identitets infrastruktur

Hybrid Azure AD Join fungerar med både hanterade och federerade miljöer beroende på om UPN är dirigerbart eller inte. Se slutet på sidan för tabell i scenarier som stöds.  

### <a name="managed-environment"></a>Hanterad miljö

En hanterad miljö kan distribueras antingen via [PHS (Password hash Sync)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) eller [genom strömning (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) med [sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

De här scenarierna kräver inte att du konfigurerar en Federations Server för autentisering.

### <a name="federated-environment"></a>Federerad miljö

En federerad miljö bör ha en identitetsprovider som uppfyller följande krav. Om du har en federerad miljö som använder Active Directory Federation Services (AD FS) (AD FS) stöds redan nedanstående krav.

- **WIAORMULTIAUTHN-anspråk:** Detta anspråk krävs för att göra en hybrid Azure AD-anslutning för Windows-enheter på hög nivå.
- **WS-Trust-protokoll:** Det här protokollet krävs för att autentisera Windows aktuella hybrid Azure AD-anslutna enheter med Azure AD. När du använder AD FS måste du aktivera följande WS-Trust-slutpunkter: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Både **ADFS/tjänster/Trust/2005/windowstransport** eller **adfs/services/trust/13/windowstransport** ska aktive ras som enbart intranät riktade slut punkter och får inte visas som extra näts slut punkter via webbprogramproxy. Läs mer om hur du inaktiverar Windows-slutpunkter för WS-Trust i [inaktivera WS-Trust Windows-slutpunkter på proxyn](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Du kan se vilka slutpunkter som är aktiverade via AD FS-hanteringskonsolen under **Tjänst** > **Slutpunkter**.

> [!NOTE]
> Azure AD har inte stöd för smartkort eller certifikat i hanterade domäner.

Från och med version 1.1.819.0 tillhandahåller Azure AD Connect en guide för att konfigurera Hybrid Azure AD-koppling. Med guiden kan du förenkla konfigurationsprocessen avsevärt. Om du installerar den nödvändiga versionen av Azure AD Connect inte är ett alternativ för dig, kan du läsa mer i [Konfigurera enhets registrering manuellt](hybrid-azuread-join-manual.md). 

Baserat på scenariot som matchar din identitets infrastruktur, se:

- [Konfigurera hybrid Azure Active Directory anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
- [Konfigurera hybrid Azure Active Directorys anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Granska lokala AD UPN-stöd för Hybrid Azure AD-anslutning

Ibland kan dina lokala AD-UPN skilja sig från dina Azure AD-UPN. I sådana fall ger Windows 10 hybrid Azure AD Join begränsat stöd för lokala AD-UPN: er baserat på [autentiseringsmetoden](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), domän typen och Windows 10-versionen. Det finns två typer av lokala AD-UPN: er som kan finnas i din miljö:

- Dirigerbart UPN: ett dirigerbart UPN har en giltig verifierad domän som är registrerad hos en domän registrator. Om contoso.com till exempel är den primära domänen i Azure AD, är contoso.org den primära domänen i den lokala AD som ägs av Contoso och [verifierats i Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- Icke-dirigerbart UPN: ett icke-dirigerbart UPN har ingen verifierad domän. Den kan bara användas inom din organisations privata nätverk. Om contoso.com till exempel är den primära domänen i Azure AD, är contoso. local den primära domänen i den lokala AD-domänen, men är inte en verifierbar domän på Internet och används endast i Contosos nätverk.

Tabellen nedan innehåller information om stöd för dessa lokala AD-UPN i Windows 10 hybrid Azure AD Join

| Typ av lokalt AD-UPN | Domäntyp | Windows 10 version | Beskrivning |
| ----- | ----- | ----- | ----- |
| Dirigera | Externt | Från 1703-version | Allmänt tillgänglig |
| Ej dirigerbart | Externt | Från 1803-version | Allmänt tillgänglig |
| Dirigera | Hanterad | Från 1803-version | Azure AD-SSPR på Windows-låsskärm som är allmänt tillgängligt stöds inte |
| Ej dirigerbart | Hanterad | Stöds inte | |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera hybrid Azure Active Directory anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
> [Konfigurera hybrid Azure Active Directory anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
