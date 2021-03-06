---
title: Distributions planer – Azure Active Directory | Microsoft Docs
description: Fullständig vägledning om hur du distribuerar många Azure Active Directory-funktioner.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17e6708225262349d56c6e261895882e9c31677f
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558532"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-distributionsplaner
Letar du efter slut punkt till slut punkt om hur du distribuerar Azure Active Directory (Azure AD)-funktioner? Distributions planer för Azure AD vägleder dig genom affärs värde, planerings överväganden och operativa procedurer som krävs för att distribuera vanliga funktioner i Azure AD.

Från någon av plan sidorna använder du webbläsarens utskrifts-till-PDF-funktion för att skapa en uppdaterad offline-version av dokumentationen.
## <a name="include-the-right-stakeholders"></a>Ta med rätt intressenter

När du påbörjar distributions planeringen för en ny funktion är det viktigt att inkludera viktiga intressenter i organisationen. Vi rekommenderar att du identifierar och dokumenterar den person eller de personer som uppfyller var och en av följande roller, och arbetar med dem för att fastställa deras medverkan i projektet.  

Roller kan innehålla följande 

|Roll |Beskrivning |
|-|-|
|Slutanvändare|En representativ grupp användare för vilka funktionen ska implementeras. Visar ofta ändringarna i ett pilotprogram.
|IT-support ansvarig|IT-avdelningen stöder organisations representant som kan tillhandahålla insikter om den här förändringen från ett support perspektiv.  
|Identitets arkitekt eller Global Azure-administratör|Identitets hanterings team som är ansvarig för att definiera hur den här ändringen justeras med infrastrukturen för kärn identitets hantering i din organisation.|
|Programmets företags ägare |Den övergripande företags ägaren av de berörda programmen, som kan innehålla hanterings åtkomst.  Kan även ge indata om användar upplevelsen och användbarheten för den här ändringen från slutanvändarens perspektiv.
|Säkerhets ägare|En representant från säkerhets teamet som kan godkänna att planen uppfyller organisationens säkerhets krav.|
|Compliance Manager|Den person i organisationen som ansvarar för att säkerställa efterlevnaden av företags-, bransch-eller myndighets krav.|

**Inblandning nivåer kan vara:**

- **R**-esponsible för implementering av projekt plan och resultat 

- **En**pproval av projekt plan och resultat 

- **C**-ontributor till projekt plan och resultat 

- **I**nformed för projekt plan och resultat


## <a name="best-practices-for-a-pilot"></a>Metod tips för en pilot
Med en pilot kan du testa med en liten grupp innan du aktiverar en funktion för alla. Se till att varje användnings fall i din organisation testas grundligt under testningen. Det är bäst att rikta in sig på en bestämd grupp pilot användare innan du går över till din organisation som helhet.

I din första Wave, rikta in dig på den, användbarhet och andra lämpliga användare som kan testa och ge feedback. Den här feedbacken bör användas för att ytterligare utveckla kommunikationen och anvisningarna som du skickar till användarna och ger insikter om vilka typer av problem som support Personalen kan se. 

Att utöka distributionen till större grupper av användare bör utföras genom att utöka omfånget för de grupper som är riktade till varandra. Detta kan göras via [dynamiskt grupp medlemskap](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)eller genom att manuellt lägga till användare till mål grupperna.


## <a name="deploy-authentication"></a>Distribuera autentisering

| Kapacitet | Beskrivning|
| -| -|
| [Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)| Azure Multi-Factor Authentication (MFA) är Microsofts verifieringslösning i två steg. Med hjälp av godkända autentiseringsmetoder bidrar Azure MFA till att skydda åtkomsten till dina data och program samtidigt som du kan uppfylla behovet av en enkel inloggnings process. |
| [Villkorlig åtkomst](https://aka.ms/deploymentplans/ca)| Med villkorlig åtkomst kan du implementera automatiska åtkomst kontroll beslut för vilka som kan komma åt dina molnappar, baserat på villkor. |
| [Självåterställning av lösenord](https://aka.ms/deploymentplans/sspr)| Lösen ords återställning via självbetjäning hjälper dina användare att återställa sina lösen ord utan att administratören behöver göra det, när och var de behöver. |
| [Lösen ords lös](https://aka.ms/deploymentplans/passwordless) | Implementera lösenordsbaserad autentisering med hjälp av Microsoft Authenticator-appen eller FIDO2 säkerhets nycklar i din organisation |

## <a name="deploy-application-management"></a>Distribuera program hantering

| Kapacitet | Beskrivning|
| -| - |
| [Enkel inloggning](https://aka.ms/deploymentplans/sso)| Enkel inloggning hjälper dina användare att komma åt de appar och resurser som de behöver för att göra affärer medan de bara loggar in en gång. När de har loggat in kan de gå från Microsoft Office till SalesForce till Box till interna program utan att behöva ange autentiseringsuppgifterna en andra gång. |
| [Åtkomst panel](https://aka.ms/deploymentplans/accesspanel)| Erbjud dina användare en enkel hubb för att upptäcka och komma åt alla sina program. Gör det möjligt för dem att bli mer produktiva med självbetjänings funktioner, t. ex. att begära åtkomst till appar och grupper eller att hantera åtkomst till resurser för andras räkning. |


## <a name="deploy-hybrid-scenarios"></a>Distribuera hybrid scenarier

| Kapacitet | Beskrivning|
| -| -|
| [ADFS till hash-synkronisering av lösenord](https://aka.ms/deploymentplans/adfs2phs)| Med hash-synkronisering av lösen ord synkroniseras hashar av användar lösen ord från lokala Active Directory till Azure AD, vilket gör att Azure AD kan autentisera användare utan interaktion med den lokala Active Directory |
| [ADFS till Pass Through Authentication](https://aka.ms/deploymentplans/adfs2pta) (Passeringsautentisering)| Med Azure AD-direktautentisering kan användarna logga in i både lokala och molnbaserade program med samma lösen ord. Den här funktionen ger användarna en bättre upplevelse – ett mindre lösen ord att komma ihåg – och minskar kostnaderna för IT-supportavdelningen eftersom det är mindre troligt att de glömmer att logga in. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory. |
| [Azure AD-programproxy](https://aka.ms/deploymentplans/appproxy)| I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De behöver åtkomst till SaaS-appar i molnet och i företags program lokalt. Azure AD Application Proxy ger denna robust åtkomst utan kostsamma och komplexa virtuella privata nätverk (VPN) eller demilitariserad zoner (DMZs). |
| [Sömlös enkel inloggning](../hybrid/how-to-connect-sso-quick-start.md)| Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. Med den här funktionen behöver användarna inte ange sina lösen ord för att logga in på Azure AD och behöver vanligt vis inte ange sina användar namn. Den här funktionen ger auktoriserade användare enkel åtkomst till dina molnbaserade program utan att behöva ytterligare lokala komponenter. |

## <a name="deploy-user-provisioning"></a>Distribuera användar etablering

| Kapacitet | Beskrivning|
| -| -|
| [Användaretablering](https://aka.ms/deploymentplans/userprovisioning)| Azure AD hjälper dig att automatisera skapande, underhåll och borttagning av användaridentiteter i molnprogram (SaaS), till exempel Dropbox, Salesforce, ServiceNow och mer. |
| [Molnets användar etablering för HR](https://aka.ms/deploymentplans/cloudhr)| Cloud HR User etablering till Active Directory skapar en grund för pågående identitets styrning och förbättrar kvaliteten på affärs processer som förlitar sig på auktoritativa identitets data. Med hjälp av den här funktionen med din moln produkt i molnet, t. ex. workday eller SuccessFactors, kan du sömlöst hantera identitets livs cykeln för anställda och eventualtillgångar genom att konfigurera regler som mappar antecknings processer – överskrivna processer (till exempel ny anställning, avsluta, Överföring) till etablerings åtgärder (till exempel skapa, aktivera, inaktivera) |

## <a name="deploy-governance-and-reporting"></a>Distribuera styrning och rapportering

| Kapacitet | Beskrivning|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) hjälper dig att hantera privilegierade administrativa roller i Azure AD, Azure-resurser och andra Microsoft Online Services. PIM innehåller lösningar som just-in-Time-åtkomst, begär godkännande arbets flöden och fullständigt integrerade åtkomst granskningar, så att du kan identifiera, upptäcka och förhindra illvilliga aktiviteter för privilegierade roller i real tid. |
| [Rapportering och övervakning](https://aka.ms/deploymentplans/reporting)| Utformningen av din Azure AD-rapportering och övervaknings lösning är beroende av dina juridiska, säkerhets-och drift krav samt din befintliga miljö och befintliga processer. Den här artikeln visar de olika design alternativen och vägleder dig till rätt distributions strategi. |
