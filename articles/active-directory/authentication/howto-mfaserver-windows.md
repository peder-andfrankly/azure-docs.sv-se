---
title: Windows-autentisering och Azure MFA Server – Azure Active Directory
description: Distribuera Windows-autentisering och Azure Multi-Factor Authentication-server.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: faab28a714b1a62e1e34de5b07119aa3018db24e
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847977"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows-autentisering och Azure Multi-Factor Authentication Server

Använd avsnittet Windows-autentisering för Azure Multi-Factor Authentication Server för att aktivera och konfigurera Windows-autentisering för program. Ta följande lista i beaktande innan du konfigurerar Windows-autentisering:

* Efter konfigurationen måste du starta om Azure Multi-Factor Authentication för att Terminal Services ska börja gälla.
* Om ”Kräv Azure Multi-Factor Authentication-användarmatchning” är markerat och du inte finns med i användarlistan kan du inte logga in på datorn efter omstarten.
* Användningen av tillförlitliga IP-adresser beror på om programmet kan tillhandahålla klient-IP-adressen med autentiseringen. För närvarande stöds endast Terminal Services.  

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication från sina användare bör använda molnbaserad Azure-Multi-Factor Authentication. Befintliga kunder som har aktiverat MFA Server tidigare än 1 juli kommer att kunna ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

> [!NOTE]
> Den här funktionen stöds inte om du vill skydda Terminal Services i Windows Server 2012 R2.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Använd följande procedur för att skydda ett program med Windows-autentisering

1. Klicka på ikonen för Windows-autentisering i Azure Multi-Factor Authentication Server.
   ![Windows-autentisering i MFA Server](./media/howto-mfaserver-windows/windowsauth.png)
2. Markera kryssrutan **Aktivera Windows-autentisering**. Den här rutan är avmarkerad som standard.
3. På fliken Program kan administratören konfigurera ett eller flera program för Windows-autentisering.
4. Välj en server eller ett program – ange om servern/programmet är aktiverat. Klicka på **OK**
5. Klicka på **Lägg till...**
6. På fliken Tillförlitliga IP-adresser kan du välja att hoppa över Azure Multi-Factor Authentication för Windows-sessioner som kommer från specifika IP-adresser. Om anställda till exempel använder programmet från kontoret och hemifrån kan du välja att du inte vill att deras telefoner ska ringa för Azure Multi-Factor Authentication när de är på kontoret. För att göra det anger du kontorets undernät som en tillförlitlig IP-adress.
7. Klicka på **Lägg till...**
8. Välj **Enkel IP** om du vill hoppa över en enstaka IP-adress.
9. Välj **IP-intervall** om du vill hoppa över ett helt IP-adressintervall. Exempel: 10.63.193.1-10.63.193.100.
10. Välj **Undernät** om du vill ange ett intervall med IP-adresser med hjälp av undernätsnotation. Ange undernätets start-IP och välj lämplig nätmask i listrutan.
11. Klicka på **OK**

## <a name="next-steps"></a>Nästa steg

- [Konfigurera VPN-installationer från tredje part för Azure MFA Server](howto-mfaserver-nps-vpn.md)

- [Utöka din befintliga infrastruktur för autentisering med NPS-tillägget för Azure MFA](howto-mfa-nps-extension.md)
