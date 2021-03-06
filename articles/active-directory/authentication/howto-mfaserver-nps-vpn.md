---
title: Azure MFA Server och VPN från tredje part – Azure Active Directory
description: Steg-för-steg-konfigurations guider för Azure MFA Server för integrering med Cisco, Citrix och Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdb24bcd79f1766a52f290fd6fe0e6e5bf17e7c9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847957"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Avancerade scenarier med Azure MFA Server och VPN-lösningar från tredje part

Azure Multi-Factor Authentication-server (Azure MFA Server) kan användas för att sömlöst ansluta till olika VPN-lösningar från tredje part. Den här artikeln fokuserar på Cisco® ASA VPN-installation, Citrix NetScaler SSL VPN-installation och Juniper-nätverk säker åtkomst/puls säker anslutning säker SSL VPN-installation. Vi har skapat konfigurations guider för att hantera dessa tre vanliga apparater. Azure MFA Server kan också integreras med de flesta andra system som använder RADIUS, LDAP, IIS eller anspråksbaserad autentisering för att AD FS. Du hittar mer information i [Azure MFA Server-konfigurationer](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication från sina användare bör använda molnbaserad Azure-Multi-Factor Authentication. Befintliga kunder som har aktiverat MFA Server tidigare än 1 juli kommer att kunna ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN-installation och Azure MFA Server
Azure MFA Server integreras med din Cisco® ASA VPN-installation för att ge ytterligare säkerhet för Cisco AnyConnect® VPN-inloggningar och Portal åtkomst.  Du kan antingen använda LDAP-eller RADIUS-protokollet.  Välj något av följande för att ladda ned detaljerade steg-för-steg-konfigurations guider.

| Konfigurations guide | Beskrivning |
| --- | --- |
| [Cisco ASA med AnyConnect VPN och Azure MFA-konfiguration för LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrera din Cisco ASA VPN-enhet med Azure MFA med LDAP |
| [Cisco ASA med AnyConnect VPN och Azure MFA-konfiguration för RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrera din Cisco ASA VPN-enhet med Azure MFA med RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN och Azure MFA Server
Azure MFA Server integreras med Citrix NetScaler SSL VPN-installationen för att ge ytterligare säkerhet för Citrix NetScaler SSL VPN-inloggningar och Portal åtkomst.  Du kan antingen använda LDAP-eller RADIUS-protokollet.  Välj något av följande för att ladda ned detaljerade steg-för-steg-konfigurations guider.

| Konfigurations guide | Beskrivning |
| --- | --- |
| [Citrix NetScaler SSL VPN och Azure MFA-konfiguration för LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrera din Citrix netscaleer SSL VPN med Azure MFA-installation med hjälp av LDAP |
| [Citrix NetScaler SSL VPN och Azure MFA-konfiguration för RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrera din Citrix NetScaler SSL VPN-installation med Azure MFA med RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL VPN-installation och Azure MFA Server
Azure MFA Server integreras med din Juniper/Pulse Secure SSL VPN-installation för att ge ytterligare säkerhet för Juniper/Pulse Secure SSL VPN-inloggningar och Portal åtkomst.  Du kan antingen använda LDAP-eller RADIUS-protokollet.  Välj något av följande för att ladda ned detaljerade steg-för-steg-konfigurations guider.

| Konfigurations guide | Beskrivning |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN och Azure MFA-konfiguration för LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrera din Juniper/Pulse Secure SSL VPN med Azure MFA-installation med hjälp av LDAP |
| [Juniper/Pulse Secure SSL VPN och Azure MFA-konfiguration för RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrera din Juniper/Pulse Secure SSL VPN-installation med Azure MFA med RADIUS |

## <a name="next-steps"></a>Nästa steg

- [Utöka din befintliga infrastruktur för autentisering med NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Konfigurera inställningar för Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
