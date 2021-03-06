---
title: Jämför Active Directory-baserade tjänster i Azure | Microsoft Docs
description: I den här översikten jämför du de olika identitets erbjudandena för Active Directory Domain Services, Azure Active Directory och Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 04a1f19ddf894467a9129e8a16c951298a6af529
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474708"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Jämför självhanterade Active Directory Domain Services, Azure Active Directory och hanterade Azure Active Directory Domain Services

För att tillhandahålla program, tjänster eller enheter åtkomst till en central identitet finns det tre vanliga sätt att använda Active Directory-baserade tjänster i Azure. Det här valet i identitets lösningar ger dig flexibilitet att använda den lämpligaste katalogen för organisationens behov. Om du till exempel främst hanterar molnbaserade användare som kör mobila enheter, kan det vara klokt att skapa och köra din egen Active Directory Domain Services (AD DS)-identitets lösning. I stället kan du bara använda Azure Active Directory.

Även om de tre Active Directory-baserade identitets lösningarna delar ett gemensamt namn och teknik, är de utformade för att tillhandahålla tjänster som uppfyller olika kund krav. På hög nivå är dessa identitets lösningar och funktions uppsättningar:

* **Active Directory Domain Services (AD DS)** – Enterprise-Ready Lightweight Directory Access Protocol (LDAP)-server som tillhandahåller viktiga funktioner som identitet och autentisering, hantering av dator objekt, grup princip och förtroenden.
    * AD DS är en central komponent i många organisationer med en lokal IT-miljö och ger grundläggande autentisering av användar konton och dator hanterings funktioner.
* **Azure Active Directory (Azure AD)** – molnbaserad identitet och hantering av mobila enheter som tillhandahåller användar konto-och autentiseringstjänster för resurser som Office 365, Azure Portal eller SaaS-program.
    * Azure AD kan synkroniseras med en lokal AD DS-miljö för att tillhandahålla en enskild identitet för användare som fungerar internt i molnet.
* **Azure Active Directory Domain Services (Azure AD DS)** – tillhandahåller hanterade domän tjänster med en del av fullständigt kompatibla, vanliga AD DS-funktioner som domän anslutning, grup princip, LDAP och KERBEROS/NTLM-autentisering.
    * Azure AD DS integreras med Azure AD, som i sin tur kan synkroniseras med en lokal AD DS-miljö, för att utöka centrala identitets användnings fall till traditionella webb program som körs i Azure som en del av en lyft-och-Shift-strategi.

I den här översikts artikeln jämförs och kontrasterar vi hur dessa identitets lösningar kan fungera tillsammans eller används oberoende av varandra, beroende på organisationens behov.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS och själv hanterad AD DS

Om du har program och tjänster som behöver åtkomst till traditionella autentiseringsmekanismer, till exempel Kerberos eller NTLM, finns det två sätt att tillhandahålla Active Directory Domain Services i molnet:

* En *hanterad* domän som du skapar med hjälp av Azure Active Directory Domain Services (Azure AD DS). Microsoft skapar och hanterar de resurser som krävs.
* En *egen hanterad* domän som du skapar och konfigurerar med hjälp av traditionella resurser, till exempel virtuella datorer, Windows Server gäst operativ system och Active Directory Domain Services (AD DS). Sedan fortsätter du att administrera resurserna.

Med Azure AD DS distribueras och underhålls kärn tjänst komponenterna av Microsoft som en *hanterad* domän upplevelse. Du distribuerar, hanterar, korrigerar och skyddar AD DS-infrastrukturen för komponenter som de virtuella datorerna, Windows Server-OPERATIVSYSTEMen eller domän kontrol Lanterna (DCs).

Azure AD DS innehåller en mindre delmängd av funktioner för traditionell självhanterad AD DS-miljö, vilket minskar risken för design och hantering. Det finns till exempel inga AD-skogar, domäner, platser och länkar för att utforma och underhålla. För program och tjänster som körs i molnet och som behöver åtkomst till traditionella autentiseringsmekanismer som Kerberos eller NTLM ger Azure AD DS en hanterad domän upplevelse med minsta möjliga administrativa kostnader.

När du distribuerar och kör en självhanterad AD DS-miljö måste du upprätthålla alla associerade infrastruktur-och katalog komponenter. Det finns ytterligare underhålls kostnader med en självhanterad AD DS-miljö, men du kan sedan utföra ytterligare åtgärder, till exempel utöka schemat eller skapa skogs förtroenden.

Vanliga distributions modeller för en självhanterad AD DS-miljö som tillhandahåller identitet för program och tjänster i molnet omfattar följande:

* **Fristående endast MOLNBASERAD AD DS** – virtuella Azure-datorer konfigureras som domänkontrollanter och en separat MOLNBASERAD AD DS-miljö skapas. Den här AD DS-miljön integreras inte med en lokal AD DS-miljö. En annan uppsättning autentiseringsuppgifter används för att logga in på och administrera virtuella datorer i molnet.
* **Distribution av resurs skog** – virtuella Azure-datorer konfigureras som domänkontrollanter och en AD DS-domän som en del av en befintlig skog skapas. En förtroende relation konfigureras sedan till en lokal AD DS-miljö. Andra virtuella Azure-datorer kan domän anslutning till den här resurs skogen i molnet. Användarautentisering körs via en VPN/ExpressRoute-anslutning till den lokala AD DS-miljön.
* **Utöka den lokala domänen till Azure** – ett virtuellt Azure-nätverk ansluter till ett lokalt nätverk med en VPN/ExpressRoute-anslutning. Virtuella Azure-datorer ansluter till det här virtuella Azure-nätverket, vilket gör det möjligt för dem att ansluta till den lokala AD DS-miljön.
    * Ett alternativ är att skapa virtuella Azure-datorer och befordra dem som replik domän kontroller från den lokala AD DS-domänen. Dessa domänkontrollanter replikeras via en VPN/ExpressRoute-anslutning till den lokala AD DS-miljön. Den lokala AD DS-domänen utökas effektivt till Azure.

I följande tabell beskrivs några av de funktioner som du kan behöva för din organisation och skillnaderna mellan en hanterad Azure AD DS-domän eller en självhanterad AD DS-domän:

| **Funktion** | **Azure AD DS** | **Själv hanterad AD DS** |
| ----------- |:---------------:|:----------------------:|
| **Hanterad tjänst**                               | **&#x2713;** | **&#x2715;** |
| **Säkra distributioner**                            | **&#x2713;** | Administratören skyddar distributionen |
| **DNS-Server**                                    | **&#x2713;** (hanterad tjänst) |**&#x2713;** |
| **Domän-eller företags administratörs behörighet** | **&#x2715;** | **&#x2713;** |
| **Domän anslutning**                                   | **&#x2713;** | **&#x2713;** |
| **Domänautentisering med NTLM och Kerberos** | **&#x2713;** | **&#x2713;** |
| **Kerberos-begränsad delegering**               | Resurs baserad | Resursbaserade & Account-based|
| **Anpassad OU-struktur**                           | **&#x2713;** | **&#x2713;** |
| **grupprincip**                                  | **&#x2713;** | **&#x2713;** |
| **Schema utökningar**                             | **&#x2715;** | **&#x2713;** |
| **Förtroenden för AD-domän/skog**                     | **&#x2715;** | **&#x2713;** |
| **Säkert LDAP (LDAPs)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP-läsning**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP-skrivning**                                    | **&#x2713;** (inom den hanterade domänen) | **&#x2713;** |
| **Geo-distribuerade distributioner**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS och Azure AD

Med Azure AD kan du hantera identiteten för enheter som används av organisationen och kontrol lera åtkomsten till företags resurser från dessa enheter. Användare kan även registrera sin personliga enhet (en egen eller BYOD modell) med Azure AD, som tillhandahåller enheten med en identitet. Azure AD autentiserar sedan enheten när en användare loggar in på Azure AD och använder enheten för att komma åt skyddade resurser. Enheten kan hanteras med MDM-programvara (Mobile Device Management) som Microsoft Intune. Med den här hanterings möjligheten kan du begränsa åtkomsten till känsliga resurser till hanterade och princip kompatibla enheter.

Traditionella datorer och bärbara datorer kan även ansluta till Azure AD. Den här mekanismen ger samma fördelar med att registrera en personlig enhet med Azure AD, till exempel för att tillåta att användare loggar in på enheten med sina företags uppgifter.

Azure AD-anslutna enheter ger dig följande fördelar:

* Enkel inloggning (SSO) till program som skyddas av Azure AD.
* Företags princip – kompatibel roaming av användar inställningar på enheter.
* Åtkomst till Windows Store för företag med företagets autentiseringsuppgifter.
* Windows Hello för företag.
* Begränsad åtkomst till appar och resurser från enheter som är kompatibla med företags principen.

Enheter kan anslutas till Azure AD med eller utan en hybrid distribution som innehåller en lokal AD DS-miljö. I följande tabell beskrivs vanliga modeller för enhets ägarskap och hur de normalt skulle vara anslutna till en domän:

| **Typ av enhet**                                        | **Enhets plattformar**             | **Tillhandahållande**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Personliga enheter                                          | Windows 10, iOS, Android, Mac OS | Azure AD har registrerats    |
| Organisationen ägd enhet är inte ansluten till en lokal AD DS | Windows 10                       | Azure AD-ansluten        |
| Organisationens ägda enhet är ansluten till en lokal AD DS  | Windows 10                       | Hybrid Azure AD-ansluten |

På en Azure AD-ansluten eller registrerad enhet sker användarautentisering med moderna OAuth/OpenID Connect-baserade protokoll. Dessa protokoll är utformade för att fungera via Internet, så det är bra för mobila scenarier där användare kommer åt företags resurser från valfri plats.

Med Azure AD DS-anslutna enheter kan program använda Kerberos-och NTLM-protokoll för autentisering, så att äldre program kan användas för att köras på virtuella Azure-datorer som en del av en lyft-och-Shift-strategi. I följande tabell beskrivs skillnaderna i hur enheterna representeras och kan autentisera sig mot katalogen:

| **16:10**                      | **Azure AD-ansluten**                                 | **Azure AD DS-ansluten**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Enheten styrs av            | Azure AD                                            | Azure AD DS-hanterad domän                                                |
| Representation i katalogen | Enhets objekt i Azure AD-katalogen            | Dator objekt i den hanterade Azure AD DS-domänen                        |
| Autentisering                  | OAuth/OpenID Connect-baserade protokoll              | Kerberos-och NTLM-protokoll                                               |
| Hantering                      | Hanterings program för mobil enheter (MDM) som Intune | grupprincip                                                              |
| Nätverk                      | Fungerar via Internet                             | Måste vara ansluten till, eller peer-with, det virtuella nätverk där den hanterade domänen är distribuerad |
| Perfekt för...                    | Mobila eller Station ära enheter för slutanvändare                  | Virtuella server-datorer som distribuerats i Azure                                              |

## <a name="next-steps"></a>Nästa steg

Kom igång med att använda Azure AD DS genom att [skapa en Azure AD DS-hanterad domän med hjälp av Azure Portal][tutorial-create].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
