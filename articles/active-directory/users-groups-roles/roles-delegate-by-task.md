---
title: Delegera roller efter administratörs uppgift – Azure Active Directory | Microsoft Docs
description: Roller som ska delegeras för identitets uppgifter i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ccd98553b06ef3fcd2241c8223889c0a86e331
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024659"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Administratörs roller efter administratörs uppgift i Azure Active Directory

I den här artikeln hittar du den information som behövs för att begränsa en användares administratörs behörighet genom att tilldela minst privilegierade roller i Azure Active Directory (Azure AD). Du hittar administratörs uppgifter ordnade efter funktions områden och den minst privilegierade rollen som krävs för att utföra varje aktivitet, tillsammans med ytterligare icke-globala administratörs roller som kan utföra uppgiften.

## <a name="application-proxy"></a>Programproxy

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera Application Proxy-appen | Program administratör | 
Konfigurera egenskaper för kopplings grupp | Program administratör | 
Skapa program registrering när möjligheten är inaktive rad för alla användare | Programutvecklare | Moln program administratör, program administratör
Skapa anslutnings grupp | Program administratör | 
Ta bort anslutnings grupp | Program administratör | 
Inaktivera programproxy | Program administratör | 
Hämta kopplings tjänst | Program administratör | 
Läs all konfiguration | Program administratör | 

## <a name="b2c"></a>B2C

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Skapa Azure AD B2C kataloger | Alla användare som inte är gäst ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Skapa B2C-program | Global administratör | 
Skapa företags program | Moln program administratör | Program administratör
Skapa, läsa, uppdatera och ta bort B2C-principer | Global administratör | 
Skapa, läsa, uppdatera och ta bort identitets leverantörer | Global administratör | 
Skapa, läsa, uppdatera och ta bort användar flöden för lösen ords återställning | Global administratör | 
Skapa, läsa, uppdatera och ta bort profil redigera användar flöden | Global administratör | 
Skapa, läsa, uppdatera och ta bort användar flöden för inloggning | Global administratör | 
Skapa, läsa, uppdatera och ta bort användar flöde för registrering |Global administratör | 
Skapa, läsa, uppdatera och ta bort användarattribut | Global administratör | 
Skapa, läsa, uppdatera och ta bort användare | Global administratör ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Läs all konfiguration | Global administratör | 
Läs gransknings loggar för B2C | Global administratör ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Azure AD B2C globala administratörer har inte samma behörigheter som globala Azure AD-administratörer. Om du har Azure AD B2C global administratörs behörighet kontrollerar du att du är i en Azure AD B2C katalog och inte i Azure AD-katalogen.

## <a name="company-branding"></a>Företags anpassning

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera varumärkesexponering | Global administratör | 
Läs all konfiguration | Katalog läsare | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Företags egenskaper

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera företags egenskaper | Global administratör | 

## <a name="connect"></a>Anslut

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Genom strömnings autentisering | Global administratör | 
Läs all konfiguration | Global administratör | 
Smidig enkel inloggning | Global administratör | 

## <a name="connect-health"></a>Connect Health

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Lägg till eller ta bort tjänster | Ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Tillämpa fel korrigeringar på synkroniseringsfel | Bidrags givare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Konfigurera meddelanden | Bidrags givare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Konfigurera inställningar | Ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Konfigurera sync-meddelanden | Bidrags givare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Läsa ADFS säkerhets rapporter | Säkerhets läsare | Deltagare, ägare
Läs all konfiguration | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Fel vid läsning av synkroniseringsfel | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Läs Sync Services | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Visa mått och aviseringar | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Visa mått och aviseringar | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Visa mått och aviseringar för synkroniseringstjänst | Läsare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare


## <a name="custom-domain-names"></a>Egna domännamn

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Hantera domäner | Global administratör | 
Läs all konfiguration | Katalog läsare | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Domain Services

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Skapa Azure AD Domain Services instans | Global administratör | 
Utföra alla Azure AD Domain Services uppgifter | Gruppen Azure AD DC-administratörer ([Se dokumentationen](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)) | 
Läs all konfiguration | Läsare på Azure-prenumeration som innehåller AD DS-tjänsten | 

## <a name="devices"></a>Enheter

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Inaktivera enhet | Moln enhets administratör | 
Aktivera enhet | Moln enhets administratör | 
Läsa grundläggande konfiguration | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Läs BitLocker-nycklar | Säkerhets läsare | Lösen ords administratör, säkerhets administratör

## <a name="enterprise-applications"></a>Företagsprogram

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Medgivande till alla delegerade behörigheter | Moln program administratör | Program administratör
Medgivande till program behörigheter som inte inkluderar Microsoft Graph eller Azure AD Graph | Moln program administratör | Program administratör
Medgivande till program behörigheter till Microsoft Graph eller Azure AD Graph | Global administratör | 
Medgivande till program som har åtkomst till egna data | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Skapa företags program | Moln program administratör | Program administratör
Hantera programproxy | Program administratör | 
Hantera användar inställningar | Global administratör | 
Läs åtkomst granskning av en grupp eller en app | Säkerhets läsare | Säkerhets administratör, användar administratör
Läs all konfiguration | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Uppdatera företags program tilldelningar | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör
Uppdatera företags program ägare | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör
Uppdatera egenskaper för företags program | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör
Uppdatera företags applikations etablering | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör
Uppdatera Self-service för företags program | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör
Uppdatera egenskaper för enkel inloggning | Företags program ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Moln program administratör, program administratör

## <a name="entitlement-management"></a>Berättigandehantering
Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Lägga till resurser i en katalog | Användar administratör | Med rättighets hantering kan du delegera uppgiften till katalog ägaren ([Se dokumentationen](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
Lägg till SharePoint Online-webbplatser i katalogen | Global administratör


## <a name="groups"></a>Grupper

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Tilldela licens | Användar administratör | 
Skapa grupp | Användar administratör | 
Skapa, uppdatera eller ta bort åtkomst granskning för en grupp eller en app | Användar administratör | 
Hantera grupp förfallo datum | Användar administratör | 
Hantera gruppinställningar | Global administratör | 
Läs all konfiguration (förutom dolt medlemskap) | Katalog läsare | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Läs dolt medlemskap | Grupp medlem | Grupp ägare, lösen ords administratör, Exchange-administratör, SharePoint-administratör, team administratör, användar administratör
Läsa medlemskap i grupper med dolt medlemskap | Support administratör | Användar administratör, team administratör
Återkalla licens | Licens administratör | Användar administratör
Uppdatera grupp medlemskap | Grupp ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användar administratör
Uppdatera grupp ägare | Grupp ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användar administratör
Uppdatera grupp egenskaper | Grupp ägare ([Se dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användar administratör

## <a name="identity-protection"></a>Identity Protection

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera aviserings meddelanden| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera MFA-principen| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera inloggnings risk princip| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera användar risk princip | Säkerhetsadministratör | 
Konfigurera vecko sammandrag | Säkerhetsadministratör| 
Ignorera alla risk identifieringar | Säkerhetsadministratör | 
Åtgärda eller ignorera sårbarhet | Säkerhetsadministratör | 
Läs all konfiguration | Säkerhets läsare | 
Läs alla risk identifieringar | Säkerhets läsare | 
Läs sårbarheter | Säkerhets läsare | 

## <a name="licenses"></a>Licenser

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Tilldela licens | Licens administratör | Användar administratör
Läs all konfiguration | Katalog läsare | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Återkalla licens | Licens administratör | Användar administratör
Prova eller köp prenumerationen | Faktureringsadministratör | 


## <a name="monitoring---audit-logs"></a>Övervakning – gransknings loggar

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Läs gransknings loggar | Rapport läsare | Säkerhets läsare, säkerhets administratör

## <a name="monitoring---sign-ins"></a>Övervakning-inloggnings program

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Läs inloggnings loggar | Rapport läsare | Säkerhets läsare, säkerhets administratör

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Ta bort alla befintliga applösenord som har genererats av de valda användarna | Global administratör | 
Inaktivera MFA | Global administratör | 
Aktivera MFA | Global administratör | 
Hantera inställningar för MFA-tjänsten | Global administratör | 
Kräv att valda användare ska tillhandahålla kontakt metoder igen | Administratör för autentisering | 
Återställa Multi-Factor Authentication på alla sparade enheter  | Administratör för autentisering | 

## <a name="mfa-server"></a>MFA-server

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Blockera/avblockera användare | Global administratör | 
Konfigurera konto utelåsning | Global administratör | 
Konfigurera regler för cachelagring | Global administratör | 
Konfigurera bedrägeri avisering | Global administratör
Konfigurera meddelanden | Global administratör | 
Konfigurera en kringgåd körning | Global administratör | 
Konfigurera inställningar för telefonsamtal | Global administratör | 
Konfigurera providers | Global administratör | 
Konfigurera Server inställningar | Global administratör | 
Läs aktivitets rapport | Global administratör | 
Läs all konfiguration | Global administratör | 
Läs Server status | Global administratör |  

## <a name="organizational-relationships"></a>Organisations relationer

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Hantera identitets leverantörer | Global administratör | 
Hantera inställningar | Global administratör | 
Hantera användnings villkor | Global administratör | 
Läs all konfiguration | Global administratör | 

## <a name="password-reset"></a>Lösenordsåterställning

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera autentiseringsmetoder | Global administratör |
Konfigurera anpassning | Global administratör |
Konfigurera meddelande | Global administratör |
Konfigurera lokal integrering | Global administratör |
Konfigurera egenskaper för lösen ords återställning | Användar administratör | Global administratör
Konfigurera registrering | Global administratör |
Läs all konfiguration | Säkerhetsadministratör | Användar administratör |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Tilldela användare till roller | Privilegie rad roll administratör | 
Konfigurera rollinställningar | Privilegie rad roll administratör | 
Visa gransknings aktivitet | Säkerhetsläsare | 
Visa roll medlemskap | Säkerhetsläsare | 

## <a name="roles-and-administrators"></a>Roller och administratörer

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Hantera rolltilldelningar | Privilegie rad roll administratör | 
Läs åtkomst granskning av en Azure AD-roll  | Säkerhets läsare | Säkerhets administratör, privilegie rad roll administratör
Läs all konfiguration | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Metoder för säkerhet – autentisering

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera autentiseringsmetoder | Global administratör | 
Läs all konfiguration | Global administratör | 

## <a name="security---conditional-access"></a>Säkerhet-villkorlig åtkomst

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera MFA-betrodda IP-adresser | Administratör för villkorlig åtkomst | 
Skapa anpassade kontroller | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Skapa namngivna platser | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Skapa principer | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Skapa användnings villkor | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Skapa VPN-anslutnings certifikat | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Ta bort klassisk princip | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Ta bort användnings villkor | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Ta bort VPN-anslutningens certifikat | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Inaktivera klassisk princip | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Hantera anpassade kontroller | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Hantera namngivna platser | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Hantera användnings villkor | Administratör för villkorlig åtkomst | Säkerhetsadministratör
Läs all konfiguration | Säkerhetsläsare | Säkerhetsadministratör
Läs namngivna platser | Säkerhetsläsare | Administratör för villkorlig åtkomst, säkerhets administratör

## <a name="security---identity-security-score"></a>Säkerhets resultat för säkerhet och identitet

Aktivitet | Minst privilegie rad roll | Ytterligare roller | 
---- | --------------------- | ----------------
Läs all konfiguration | Säkerhetsläsare | Säkerhetsadministratör
Läs säkerhets Poäng | Säkerhetsläsare | Säkerhetsadministratör
Uppdatera händelse status | Säkerhetsadministratör | 

## <a name="security---risky-sign-ins"></a>Säkerhet – riskfyllda inloggningar

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Läs all konfiguration | Säkerhets läsare | 
Läs riskfyllda inloggningar | Säkerhets läsare | 

## <a name="security---users-flagged-for-risk"></a>Säkerhet – användare som har flaggats för risk

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Ignorera alla händelser | Säkerhetsadministratör | 
Läs all konfiguration | Säkerhets läsare | 
Läs användare som har flaggats för risk | Säkerhets läsare | 

## <a name="users"></a>Användare

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Lägg till rollen användare i katalog | Privilegie rad roll administratör | 
Lägg till användare i grupp | Användar administratör | 
Tilldela licens | Licens administratör | Användar administratör
Skapa gäst användare | Gäst deltagare | Användar administratör
Skapa användare | Användar administratör | 
Ta bort användare | Användar administratör | 
Invalidera uppdateringstoken för begränsade administratörer (se dokumentationen) | Användar administratör | 
Ogiltig verifiering av uppdateringstoken för icke-administratörer (se dokumentationen) | Lösen ords administratör | Användar administratör
Ogiltig verifiering av uppdateringstoken för privilegierade administratörer (se dokumentationen) | Global administratör | 
Läsa grundläggande konfiguration | Standard användar roll ([se dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Återställ lösen ord för begränsade administratörer (se dokumentationen) | Användar administratör | 
Återställ lösen ord för icke-administratörer (se dokumentationen) | Lösen ords administratör | Användar administratör
Återställ lösen ord för privilegierade administratörer | Global administratör | 
Återkalla licens | Licens administratör | Användar administratör
Uppdatera alla egenskaper utom användarens huvud namn | Användar administratör | 
Uppdatera användarens huvud namn för begränsade administratörer (se dokumentationen) | Användar administratör | 
Uppdatera egenskapen för användarens huvud namn i privilegierade administratörer (se dokumentationen) | Global administratör | 
Uppdatera användar inställningar | Global administratör | 


## <a name="support"></a>Support

Aktivitet | Minst privilegie rad roll | Ytterligare roller
---- | --------------------- | ----------------
Skicka support ärende | Tjänstadministratör | Program administratör, Azure Information Protection administratör, fakturerings administratör, moln program administratör, kompatibilitets administratör, Dynamics 365-administratör, Desktop Analytics-administratör, Exchange-administratör, lösen ord Administratör, Intune-administratör, Skype för företag-administratör, Power BI administratör, Privileged Authentication Administrator, SharePoint-administratör, teams kommunikations administratör, team administratör, användar administratör, Administratör för arbets plats analys

## <a name="next-steps"></a>Nästa steg

* [Tilldela eller ta bort administratörs roller för Azure AD](directory-manage-roles-portal.md)
* [Roll referens för Azure AD-administratörer](directory-assign-admin-roles.md)
