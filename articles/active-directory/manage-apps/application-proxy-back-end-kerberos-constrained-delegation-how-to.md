---
title: Felsöka Kerberos-begränsad delegering – App proxy
description: Felsöka Kerberos-begränsad delegering konfigurationer för Application Proxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5e866f61409960447e17ecb50b035eabd53dc38
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275680"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Felsöka Kerberos-begränsad delegering konfigurationer för Application Proxy

Metoder för att uppnå SSO till publicerade program kan skilja sig från ett program till en annan. Ett alternativ som Azure Active Directory (Azure AD) Application Proxy ger som standard är Kerberos-begränsad delegering (KCD). Du kan konfigurera en anslutning för dina användare att köra begränsad Kerberos-autentisering till backend-program.

Proceduren för att aktivera KCD är enkelt. Det krävs inte fler än en förståelse av de olika komponenter och autentiseringsflödet som har stöd för enkel inloggning. Men ibland KCD SSO fungerar inte som förväntat. Du behöver bra informationskällorna felsökning av dessa scenarier.

Den här artikeln innehåller en enda åtkomstpunkt för referens som hjälper dig att felsöka och reparera några av de vanligaste problemen. Den behandlar också mer komplicerade problem för implementering kan undersöka.

Den här artikeln gör följande antaganden:

- Distribution av Azure AD-programproxy per [Kom igång med Application Proxy](application-proxy-add-on-premises-application.md) och allmän åtkomst till icke-KCD program fungerar som förväntat.
- Publicerade målprogrammet baseras på Internet Information Services (IIS) och Microsofts implementering av Kerberos.
- Server- och värdarna finnas i en enda Azure Active Directory-domän. Detaljerad information om scenarier mellan domäner och skogar finns i [KCD White Paper](https://aka.ms/KCDPaper).
- Programmet har publicerats i en Azure-klient med förautentisering aktiverat. Användare som förväntas autentisera till Azure via formulärbaserad autentisering. Autentiseringsscenarier för Rich-klient omfattas inte av den här artikeln. De kan läggas vid en viss tidpunkt i framtiden.

## <a name="prerequisites"></a>Krav

Azure AD Application Proxy kan distribueras till många typer av infrastrukturer eller miljöer. Arkitekturerna varierar från olika organisationer. De vanligaste orsakerna till KCD-relaterade problem finns inte i miljöer. Enkel felkonfigurationer eller allmänt fel orsakar de flesta problem.

Därför är det bäst att se till att du uppfyller alla krav för att [använda KCD SSO med programproxyn](application-proxy-configure-single-sign-on-with-kcd.md) innan du påbörjar fel sökningen. Observera avsnittet om hur du konfigurerar Kerberos-begränsad delegering på 2012 R2. Den här processen använder en annan metod för att konfigurera KCD på tidigare versioner av Windows. Dessutom Tänk också på dessa överväganden:

- Det är inte ovanligt att en domänmedlemsserver att öppna en dialogruta för säker kanal med en specifik domänkontrollant (DC). Servern kan sedan flytta till en annan dialogruta vid en given tidpunkt. Connector värdar är därför inte begränsad till kommunikation med specifika lokala platsen domänkontrollanter.
- Domänerna scenarier är beroende av referenser som dirigerar en värd för anslutningen till domänkontrollanter som kan vara utanför den lokala nätverksperimeter. I dessa fall är det lika viktigt att även skicka trafik och uppåt till domänkontrollanter som representerar andra respektive domäner. Om inte delegering misslyckas.
- Om möjligt, Undvik att placera alla aktiva enheter för IP-adresser eller ID: N mellan connector värdar och domänkontrollanter. Dessa enheter är ibland för påträngandea och stör Core RPC-trafik.

Testa delegering i grundläggande scenarier. Flera variabler du introducerar, Ju mer du kan behöva Jämka. Spara tid genom att begränsa testningen till en enda anslutning. Lägga till ytterligare anslutningar när problemet har lösts.

Vissa miljöfaktorer kan också bidra till ett problem. För att undvika dessa faktorer kan minimera arkitektur så mycket som möjligt under testningen. Till exempel är felkonfigurerad intern brandvägg ACL: er vanliga. Om möjligt skicka all trafik från en anslutningen rakt igenom till domänkontrollanter och backend-programmet.

Den bästa platsen att placera kopplingar är så nära som möjligt till sina mål. En brandvägg som är placerad direkt när du testar lägger till onödig komplexitet och kan förlänga dina undersökningar.

Vad visar KCD problem? Det finns flera vanliga uppgifter som KCD SSO misslyckas. De första tecknen på ett problem visas i webbläsaren.

![Exempel: felaktigt konfigurations fel för KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Exempel: Auktoriseringen misslyckades på grund av saknade behörigheter](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Båda bilderna visar samma symptom: SSO-fel. Nekad användaråtkomst till programmet.

## <a name="troubleshooting"></a>Felsökning

Hur du felsöker beror på problemet och du ser symtomen. Innan du går längre, utforska följande artiklar. De ger användbar felsökningsinformation:

- [Felsök problem med programproxy och fel meddelanden](application-proxy-troubleshoot.md)
- [Kerberos-fel och symptom](application-proxy-troubleshoot.md#kerberos-errors)
- [Arbeta med SSO när lokala och moln identiteter inte är identiska](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Om du har fått hittills finns huvudsakliga problemet. Börja genom att avgränsa flow i följande tre steg som du kan felsöka.

### <a name="client-pre-authentication"></a>Förautentisering av klient

Den externa användaren autentiseras till Azure via en webbläsare. Möjlighet att autentisera förväg till Azure krävs för KCD SSO ska fungera. Testa och lösa den här möjligheten om det finns några problem. Förautentisering scenen inte är relaterade till KCD eller det publicerade programmet. Det är enkelt att korrigera skillnader genom att kontrollera förstånd som ämne-konto finns i Azure. Kontrollera också att den inte har inaktiverats eller blockeras. Felsvar i webbläsaren är beskrivande att förklara orsaken. Om du är osäker, kontrollera andra Microsoft-felsökningsartiklar att verifiera.

### <a name="delegation-service"></a>Delegering service

Den Azure Proxy-koppling som hämtar en Kerberos-tjänstbiljett för användare från en Kerberos Key Distribution Center (KCD).

Den externa kommunikationen mellan klienten och Azure klientdelen har inte någon bäring på KCD. Dessa meddelanden bara se till att KCD fungerar. Azure-proxytjänsten tillhandahålls ett giltigt användar-ID som används för att få en Kerberos-biljett. Utan detta ID KCD är inte möjligt och misslyckas.

Som tidigare nämnts innehåller felmeddelanden webbläsare några bra uppfattning om varför saker misslyckas. Se till att anteckna aktivitets-ID och tidsstämpel i svaret. Den här informationen hjälper dig att korrelera beteende på faktiska händelser i händelseloggen Azure Proxy.

![Exempel: felaktigt konfigurations fel för KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Motsvarande poster visas i händelseloggen om du vill visa som händelser 13019 eller 12027. Sök efter anslutnings händelse loggar i **program-och tjänst loggar** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **admin**.

![Händelsen 13019 från händelseloggen för Application Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Händelsen 12027 från händelseloggen för Application Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Använd en **A** -post i din interna DNS för programmets adress, inte en **CNAME**.
1. Bekräfta att connector-värden har beviljats behörighet att delegera till målkontot avsedda-SPN. Bekräfta att **Använd valfritt autentiseringsprotokoll** är markerat. Mer information finns i [artikeln SSO Configuration](application-proxy-configure-single-sign-on-with-kcd.md).
1. Kontrollera att det finns endast en instans av SPN: et i finns i Azure AD. Problem `setspn -x` från en kommando tolk på en domän medlems värd.
1. Kontrol lera att en domän princip tillämpas som begränsar den [maximala storleken på utfärdade Kerberos-token](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Den här principen stoppar anslutningen från att få en token om det visar sig för att bli orimliga.

En nätverksspårning som samlar in utbyten mellan connector-värden och en domän KDC är nästa bästa steg för att hämta mer på låg nivå information om problemen. Mer information finns i [fel söknings bladet för djupet](https://aka.ms/proxytshootpaper).

Om biljetter ser bra ut kan se du en händelse i loggarna som talar om att autentiseringen misslyckades eftersom programmet returnerade ett 401. Den här händelsen indikerar att målprogrammet avvisat din biljett. Gå till nästa steg.

### <a name="target-application"></a>Målprogrammet

Konsumenter av Kerberos-biljetten som tillhandahålls av anslutningen. Förvänta dig anslutningen så att du har skickat en Kerberos-tjänstbiljett till backend-servern i det här skedet. Den här biljetten är en rubrik i den första begäran för programmet.

1. Verifiera att programmet kan nås direkt från webbläsaren på connector-värden med hjälp av interna programmets URL som definierats i portalen. Du kan logga sedan in har. Information finns på sidan **för fel sökning** av anslutning.
1. Kontrollera att autentiseringen mellan webbläsaren och programmet använder Kerberos fortfarande på connector-värden. Gör något av följande åtgärder:
1. Kör DevTools (**F12**) i Internet Explorer eller Använd [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) från anslutnings värden. Gå till programmet genom att använda den interna URL: en. Granska de erbjudna WWW auktorisering rubriker som returneras i svaret från programmet för att se till att antingen förhandla eller Kerberos finns.

   - Nästa Kerberos-blob som returneras i svaret från webbläsaren till programmet börjar med **YII**. Dessa bokstäver berättar att Kerberos körs. Microsoft NT LAN Manager (NTLM), å andra sidan, börjar alltid med **TlRMTVNTUAAB**, som läser NTLM Security Support Provider (NTLMSSP) när den avkodas från base64. Om du ser **TlRMTVNTUAAB** i början av blobben är Kerberos inte tillgängligt. Om du inte ser **TlRMTVNTUAAB**är Kerberos troligt vis tillgängligt.

      > [!NOTE]
      > Om du använder Fiddler kan kräver den här metoden att tillfälligt inaktivera utökat skydd på programmets konfiguration i IIS.

      ![Webbläsarfönster network inspection](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Blobben i den här bilden startar inte med **TIRMTVNTUAAB**. I det här exemplet är Kerberos tillgängligt och Kerberos-blobben börjar inte med **YII**.

1. Ta bort NTLM tillfälligt från listan med providers på IIS-webbplats. Åtkomst till appen direkt från Internet Explorer på värden för anslutningen. NTLM är inte längre i listan med providers. Du kan komma åt programmet genom att endast använda Kerberos. Om programmet inte, kan det finnas ett problem med programmets konfiguration. Kerberos-autentisering fungerar inte.

   - Om Kerberos inte är tillgänglig, kontrollera programmets autentiseringsinställningarna i IIS. Se till att **Negotiate** visas överst, med enbart NTLM under det. Om du **inte ser Negotiate**, **Kerberos eller Negotiate**eller **PKU2U**fortsätter du bara om Kerberos fungerar.

     ![Windows autentiseringsprovidrar](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Med Kerberos och NTLM på plats kan du tillfälligt inaktivera förautentisering för programmet i portalen. Försök att komma åt den från internet med hjälp av den externa URL: en. Du uppmanas att autentisera. Du kan göra det med samma konto som används i föregående steg. Om inte, det finns ett problem med backend-programmet, inte KCD.
   - Återaktivera förautentisering i portalen. Autentisera via Azure genom att försöka ansluta till programmet via den externa URL: en. Om enkel inloggning misslyckas kan se du meddelandet förbjudet fel i webbläsaren och händelsen 13022 i loggen:

     *Microsoft AAD Application Proxy Connector kan inte autentisera användaren eftersom backend-servern svarar på Kerberos-autentiseringsförsök med ett HTTP 401-fel.*

      ![Visar HTTTP 401-otillåtet fel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Kontrollera IIS-program. Kontrollera att den konfigurera programpoolen och SPN har konfigurerats för att använda samma konto i Azure AD. Navigera i IIS som visas i följande bild:

      ![Konfigurationsfönstret för IIS-program](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      När du känner till identiteten, kontrollera att det här kontot har konfigurerats med SPN i fråga. Ett exempel är `setspn –q http/spn.wacketywack.com`. Ange följande text i en kommandotolk:

      ![Visar kommando fönstret SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Kontrollera SPN definieras programinställningar i portalen. Se till att samma SPN konfigurerats mot målet Azure AD-konto används av programpoolen app.

      ![SPN-konfigurationen på Azure-portalen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Gå till IIS och välj alternativet **konfigurations redigerare** för programmet. Gå till **system. webserver/Security/Authentication/windowsAuthentication**. Kontrol lera att värdet **UseAppPoolCredentials** är **Sant**.

      ![IIS-konfigurationspooler app autentiseringsuppgift alternativet](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Ändra värdet till **Sant**. Ta bort alla cachelagrade Kerberos-biljetter från backend-servern genom att köra följande kommando:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Mer information finns i [Rensa Kerberos-klientens biljett-cache för alla sessioner](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Om du lämnar Kernel-läge har aktiverats, förbättrar prestanda för Kerberos-åtgärder. Men det gör även biljett för den begärda tjänsten att dekrypteras med hjälp av datorkontot. Det här kontot kallas även det lokala systemet. Ange värdet **Sant** om du vill bryta KCD när programmet finns på fler än en server i en Server grupp.

- Ytterligare kontroll är att inaktivera **utökat** skydd. I vissa scenarier är **utökat** skydd KCD när det aktiverades i vissa konfigurationer. I sådana fall kan har ett program publicerats som en undermapp på standardwebbplatsen. Det här programmet har konfigurerats för anonym autentisering. Alla dialogrutor är nedtonade, vilket tyder på underordnade objekt inte ärver alla aktiva inställningar. Vi rekommenderar att du testar, men inte glömmer att återställa värdet till **aktive rad**, där det är möjligt.

  Den här extra kontroll ger dig spåra att använda ditt publicerade program. Du kan skapa ytterligare anslutningar som också har konfigurerats för att delegera. Mer information finns i mer djupgående teknisk genom gång, [fel sökning av Azure-AD-programproxy](https://aka.ms/proxytshootpaper).

Om du fortfarande inte göra förloppet kan Microsoft-supporten hjälpa dig. Skapa en supportbiljett direkt i portalen. En supporttekniker kontaktar dig.

## <a name="other-scenarios"></a>Andra scenarier

- Azure Application Proxy begär en Kerberos-biljett innan du skickar begäran till ett program. Vissa program från tredje part liknar denna metod för autentisering. Dessa program förväntar sig mer konventionella förhandlingar ska kunna utföras. Den första begäran är anonyma, vilket gör att programmet att svara med autentiseringstyper som stöds via en 401.
- Multi-hop autentisering används ofta i scenarier där ett program är nivåer, med en backend-server och en klientdel, där båda kräver autentisering, till exempel SQL Server Reporting Services. Information om hur du konfigurerar multi-hop-scenariot finns i Support artikeln [Kerberos-begränsad delegering kan kräva protokoll över gång i scenarier med flera hopp](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Nästa steg

[Konfigurera KCD på en hanterad domän](../../active-directory-domain-services/deploy-kcd.md).
