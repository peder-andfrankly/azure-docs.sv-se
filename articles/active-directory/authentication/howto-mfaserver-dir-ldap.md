---
title: LDAP-autentisering och Azure MFA Server – Azure Active Directory
description: Distribuera LDAP-autentisering och Azure Multi-Factor Authentication-server.
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
ms.openlocfilehash: 6e24442abd707706e035bc1f9dc0a46951e0711a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848076"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-autentisering och Azure Multi-Factor Authentication-server

Som standard konfigureras Azure Multi-Factor Authentication Server att importera eller synkronisera användare från Active Directory. Tjänsten kan dock konfigureras att binda till olika LDAP-kataloger, till exempel en ADAM-katalog, eller en särskild Active Directory-domänkontrollant. När du är ansluten till en katalog via LDAP kan Azure-Multi-Factor Authentication-server fungera som en LDAP-proxy för att utföra autentiseringar. Den stöder också användningen av LDAP-bindningar som ett RADIUS-mål, förautentisering av användare med IIS-autentisering, eller för primär autentisering i Azure MFA-användarportalen.

Om du vill använda Azure Multi-Factor Authentication som en LDAP-proxy sätter du in Azure-Multi-Factor Authentication-server mellan LDAP-klienten (till exempel VPN-installation, program) och LDAP-katalogserver. Azure Multi-Factor Authentication-servern måste vara konfigurerad att kommunicera med både klientservrarna och LDAP-katalogen. I den här konfigurationen accepterar Azure Multi-Factor Authentication Server LDAP-förfrågningar från klientservrar och klientprogram och vidarebefordrar dem till mål-LDAP-katalogservern för verifiering av de primära autentiseringsuppgifterna. Om LDAP-katalogen validerar de primära autentiseringsuppgifterna utför Azure Multi-Factor Authentication en andra identitets verifiering och skickar tillbaka ett svar till LDAP-klienten. Hela autentisering fungerar endast om både LDAP-serverautentiseringen och det andra stegets verifiering lyckas.

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication från sina användare bör använda molnbaserad Azure-Multi-Factor Authentication. Befintliga kunder som har aktiverat MFA Server tidigare än 1 juli kommer att kunna ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

## <a name="configure-ldap-authentication"></a>Konfigurera LDAP-autentisering

Om du vill konfigurera LDAP-autentisering installerar du Azure Multi-Factor Authentication-servern på en Windows-server. Följ dessa steg:

### <a name="add-an-ldap-client"></a>Lägg till en LDAP-klient

1. I Azure-Multi-Factor Authentication-server väljer du ikonen LDAP-autentisering på den vänstra menyn.
2. Markera kryssrutan **Aktivera LDAP-autentisering**.

   ![LDAP-autentisering i MFA Server](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. På fliken Klienter ändrar du TCP- och SSL-porten om Azure Multi-Factor Authentication LDAP-tjänsten ska binda till icke-standardportar för att lyssna efter LDAP-begäranden.
4. Om du planerar att använda LDAPs från klienten till Azure-Multi-Factor Authentication-server måste ett SSL-certifikat installeras på samma server som MFA Server. Klicka på **Bläddra** bredvid rutan SSL-certifikat och välj ett certifikat som ska användas för den säkra anslutningen.
5. Klicka på **Lägg till**.
6. I dialog rutan Lägg till LDAP-klient anger du IP-adressen för enheten, servern eller programmet som autentiserar till servern och ett program namn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
7. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importer ATS till servern än, eller om de är undantagna från tvåstegsverifiering, lämnar du rutan avmarkerad. Mer information om den här funktionen finns i Hjälp filen för MFA-servern.

Upprepa de här stegen för att lägga till ytterligare LDAP-klienter.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurera LDAP-kataloganslutningen

När Azure Multi-Factor Authentication har konfigurerats att ta emot LDAP-autentiseringar måste dessa autentiseringar skickas via en proxyanslutning till LDAP-katalogen. Därför visar fliken Mål endast ett nedtonat alternativ för att använda ett LDAP-mål.

> [!NOTE]
> Katalog integrering garanterar inte att fungera med andra kataloger än Active Directory Domain Services.

1. För att konfigurera anslutningen till LDAP-katalogen, klickar du på ikonen **Katalogintegrering**.
2. På fliken Inställningar väljer du alternativknappen **Använd specifik LDAP-konfiguration**.
3. Välj **Redigera...**
4. I dialogrutan Redigera LDAP-konfiguration fyller du i fälten med den information som krävs för att ansluta till LDAP-katalogen. Fältbeskrivningar finns inkluderade i hjälpfilen för Azure Multi-Factor Authentication-server.

    ![Katalog integrering LDAP-konfiguration](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Testa LDAP-anslutningen genom att klicka på knappen **Testa**.
6. Klicka på **OK** om LDAP-anslutningstestet lyckas.
7. Klicka på fliken **filter** . Servern är förkonfigurerad att läsa in behållare, säkerhets grupper och användare från Active Directory. Om du binder till en annan LDAP-katalog, behöver du förmodligen redigera filtren som visas. Klicka på **Hjälp**-länken för mer information om filter.
8. Klicka på fliken **attribut** . Servern är förkonfigurerad att mappa attribut från Active Directory.
9. Om du binder till en annan LDAP-katalog eller för att ändra de förkonfigurerade attributmappningarna, klickar du på **Redigera…**
10. I dialogrutan Redigera attribut ändrar du LDAP-attributmappningarna för din katalog. Attributnamn kan skrivas in eller väljas genom att klicka på **…** bredvid respektive fält. Klicka på **Hjälp**-länken för mer information om attribut.
11. Klicka på **OK**.
12. Klicka på ikonen **Företagsinställningar** och välj fliken **Matcha användarnamn**.
13. Om du ansluter till Active Directory från en domänansluten server, lämnar du alternativknappen **Använd Windows-säkerhetsidentifierare (SID:er) för matchning av användarnamn** markerad. Annars väljer du alternativknappen **Använd LDAP-unika identifierarattribut för matchning av användarnamn**.

När alternativknappen **Använd LDAP-unikt identifierarattribut för matchning av användarnamn** markerats, försöker Azure Multi-Factor Authentication-servern matcha varje användarnamn till en unik identifierare i LDAP-katalogen. En LDAP-sökning utförs på attributen för användar namn som definierats på fliken Katalog integrering – > attribut. När en användare autentiseras matchas användar namnet med den unika identifieraren i LDAP-katalogen. Den unika identifieraren används för att matcha användaren i Azure Multi-Factor Authentication data-filen. Detta möjliggör Skift läges okänsliga jämförelser och långa och korta användar namns format.

När du har slutfört de här stegen lyssnar MFA-servern på de konfigurerade portarna för LDAP-begäranden från de konfigurerade klienterna och fungerar som proxy för dessa begär anden till LDAP-katalogen för autentisering.

## <a name="configure-ldap-client"></a>Konfigurera LDAP-klient

Följ dessa riktlinjer när du konfigurerar LDAP-klienten:

* Konfigurera din enhet, server eller program för att autentisera via LDAP till Azure Multi-Factor Authentication-servern som om det vore din LDAP-katalog. Använd samma inställningar som du använder normalt för att ansluta direkt till LDAP-katalogen, förutom servernamnet eller IP-adressen som ska vara samma som för Azure Multi-Factor Authentication-servern.
* Konfigurera LDAP-timeout till 30-60 sekunder så att det finns tid att verifiera användarens autentiseringsuppgifter med LDAP-katalogen, utföra det andra steget och ta emot sitt svar och svara på LDAP-åtkomstbegäran.
* Om du använder LDAPS måste enheten eller servern som kör LDAP-frågorna lita på SSL-certifikatet som är installerat på Azure Multi-Factor Authentication-servern.
