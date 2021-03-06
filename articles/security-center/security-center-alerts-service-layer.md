---
title: Hot identifiering i Azure Service Layer – Azure Security Center
description: I det här avsnittet presenteras Azure Service Layer-aviseringar som är tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 7db9f50b4fb1a9309737f05db13a914f414372ed
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186508"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Hot identifiering för Azure Service Layer i Azure Security Center

I det här avsnittet presenteras Azure Security Centers aviseringar som är tillgängliga när du övervakar följande Azure Service-lager:

* [Azure nätverks lager](#network-layer)
* [Azure Management Layer (Azure Resource Manager) (för hands version)](#management-layer)
* [Azure Key Vault](#azure-keyvault)

>[!NOTE]
>Följande analys gäller för alla resurs typer. De använder Telemetrin som Security Center tillhandahåller genom att trycka på interna Azure-flöden.

## Azure nätverks lager<a name="network-layer"></a>

Security Center Network Layer Analytics baseras på [IPFIX data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), som är paket rubriker som samlas in av Azure Core-routrar. Baserat på denna datafeed kan Security Center Machine Learning-modeller identifiera och flagga skadliga trafik aktiviteter. Security Center använder Microsoft Threat Intelligence-databasen för att utöka IP-adresser.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Misstänkt utgående RDP-nätverks aktivitet**|Analys av exempel på nätverks trafik upptäckte avvikande RDP-kommunikation (utgående Remote Desktop Protocol) som härstammar från en resurs i distributionen. Den här aktiviteten betraktas som onormal för den här miljön. Det kan betyda att din resurs har komprometterats och att den nu används för att bryta Force-angrepp till en extern RDP-slutpunkt. Den här typen av aktivitet kan göra att din IP-adress flaggas som skadlig av externa entiteter.|
|**Misstänkt utgående RDP-nätverks aktivitet till flera destinationer**|Analys av exempel på nätverks trafik identifierade avvikande RDP-kommunikation som härstammar från en resurs i distributionen till flera mål. Den här aktiviteten betraktas som onormal för den här miljön. Det kan betyda att din resurs har komprometterats och att den nu används för att bryta Force-angrepp av externa RDP-slutpunkter. Den här typen av aktivitet kan göra att din IP-adress flaggas som skadlig av externa entiteter.|
|**Misstänkt utgående SSH-nätverks aktivitet**|Analys av exempel på nätverks trafik upptäckte avvikande SSH-kommunikation (utgående Secure Shell) som härstammar från en resurs i distributionen. Den här aktiviteten betraktas som onormal för den här miljön. Det kan betyda att din resurs har komprometterats och att den nu används för att bryta Force-angrepp till en extern SSH-slutpunkt. Den här typen av aktivitet kan göra att din IP-adress flaggas som skadlig av externa entiteter.|
|**Misstänkt utgående SSH-nätverks aktivitet till flera mål**|Analys av exempel på nätverks trafik upptäckte avvikande utgående SSH-kommunikation som härstammar från en resurs i distributionen till flera mål. Den här aktiviteten betraktas som onormal för den här miljön. Det kan betyda att din resurs har komprometterats och att den nu används för att bryta Force-attackens externa SSH-slutpunkter. Den här typen av aktivitet kan göra att din IP-adress flaggas som skadlig av externa entiteter.|
|**Misstänkt inkommande SSH-nätverks aktivitet från flera källor**|Analys av exempel på nätverks trafik upptäckte avvikande inkommande SSH-kommunikation från flera källor till en resurs i distributionen. Olika unika IP-adresser som ansluter till din resurs betraktas som onormala för den här miljön. Den här aktiviteten kan indikera ett försök att orsaka en brute force-attack till SSH-gränssnittet från flera värdar (botnät).|
|**Misstänkt inkommande SSH-nätverks aktivitet**|Analys av exempel på nätverks trafik upptäckte avvikande inkommande SSH-kommunikation till en resurs i distributionen. Ett relativt högt antal inkommande anslutningar till resursen anses vara onormalt för den här miljön. Den här aktiviteten kan indikera ett försök att orsaka en brute force-attack till SSH-gränssnittet.
|**Misstänkt inkommande RDP-nätverks aktivitet från flera källor**|Analys av exempel på nätverks trafik upptäckte avvikande inkommande RDP-kommunikation från flera källor till en resurs i distributionen. Olika unika IP-adresser som ansluter till din resurs betraktas som onormala för den här miljön. Den här aktiviteten kan indikera ett försök att orsaka en brute force-attack till RDP-gränssnittet från flera värdar (botnät).|
|**Misstänkt inkommande RDP-nätverks aktivitet**|Analys av exempel på nätverks trafik upptäckte avvikande inkommande RDP-kommunikation till en resurs i distributionen. Ett relativt högt antal inkommande anslutningar till resursen anses vara onormalt för den här miljön. Den här aktiviteten kan indikera ett försök att orsaka en brute force-attack till SSH-gränssnittet.|
|**Nätverkskommunikationen med en skadlig adress har upptäckts**|Analys av exempel på nätverks trafik har identifierat kommunikation från en resurs i distributionen med en möjlig kommando-och kontroll (C & C)-Server. Den här typen av aktivitet kan orsaka att din IP-adress flaggas som skadlig av externa entiteter.|

Information om hur Security Center kan använda nätverksrelaterade signaler för att använda hot skydd finns i [heuristisk DNS-identifieringar i Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Aviseringar om hot identifiering i Azure Network Layer, i Azure Security Center, genereras bara på virtuella datorer som har tilldelats samma IP-adress för hela timmen och som en misstänkt kommunikation har ägt rum. Detta gäller för virtuella datorer, samt virtuella datorer som skapas i kundens prenumeration som en del av en hanterad tjänst (t. ex. AKS, Databricks).

## Azure Management Layer (Azure Resource Manager) (för hands version)<a name ="management-layer"></a>

>[!NOTE]
>Security Center skydds lager baserat på Azure Resource Manager är för närvarande en för hands version.

Security Center erbjuder ett extra skydds lager genom att använda Azure Resource Manager händelser, som anses vara kontroll planet för Azure. Genom att analysera Azure Resource Manager poster Security Center identifiera ovanliga eller potentiellt skadliga åtgärder i Azure-prenumerationens miljö.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Körning av mikroburst-verktyget**|En känd rekognosering Toolkit-körning i moln miljö har upptäckts i din miljö. Verktyget [mikroburst](https://github.com/NetSPI/MicroBurst) kan användas av en angripare (eller utträngande testare) för att mappa dina prenumerations resurser, identifiera oskyddade konfigurationer och avslöja konfidentiell information.|
|**Azurite Toolkit-körning**|En känd rekognosering Toolkit-körning i moln miljö har upptäckts i din miljö. Verktyget [Azurite](https://github.com/mwrlabs/Azurite) kan användas av en angripare (eller utträngande testare) för att mappa dina prenumerations resurser och identifiera oskyddade konfigurationer.|
|**Misstänkt hanterings-session med ett inaktivt konto**|Analysen av prenumerations aktivitets loggar har identifierat ett misstänkt beteende. Ett huvud konto som inte används under en lång tids period utför nu åtgärder som kan skydda beständigheten för en angripare.|
|**Misstänkt hanterings-session med PowerShell**|Analysen av prenumerations aktivitets loggar har identifierat ett misstänkt beteende. Ett huvud konto som inte regelbundet använder PowerShell för att hantera prenumerations miljön är nu att använda PowerShell, och utföra åtgärder som kan skydda beständigheten för en angripare.|
|**Användning av avancerade metoder för Azure-persistence**|Analysen av prenumerations aktivitets loggar har identifierat ett misstänkt beteende. Anpassade roller har fått legitimized identitets enheter. Detta kan leda till att angriparen får persistency i en Azure-kundmiljö.|
|**Aktivitet från ovanligt land**|Aktivitet från en plats som inte nyligen har ägt rum eller någonsin varit besökt av någon användare i organisationen har inträffat.<br/>Den här identifieringen tar hänsyn till de senaste aktivitet platser att fastställa nya och ovanliga platser. Avvikelseidentifieringsmotorn lagrar information om tidigare platser som används av användarna i organisationen. 
|**Aktivitet från anonyma IP-adresser**|Användares aktivitet från en IP-adress som har identifierats som en anonym proxy-IP-adress har identifierats. <br/>Dessa proxyservrar används av personer som vill dölja deras enhets IP-adress och som kan användas för skadliga avsikter. Den här identifieringen använder en Machine Learning-algoritm som minskar falska positiva identifieringar, till exempel felmärkta IP-adresser som används ofta av användare i organisationen.|
|**Omöjlig resa upptäcktes**|Två användar aktiviteter (i en eller flera sessioner) har inträffat och härstammar från geografiskt avlägsna platser. Detta inträffar inom en tids period som är kortare än den tid det skulle ha tagit användaren att resa från den första platsen till den andra. Detta indikerar att en annan användare använder samma autentiseringsuppgifter. <br/>Den här identifieringen använder en Machine Learning-algoritm som ignorerar uppenbara falska positiva identifieringar som bidrar till omöjliga rese villkor, t. ex. VPN och platser som regelbundet används av andra användare i organisationen. Identifieringen har en inledande inlärnings period på sju dagar, under vilken den lär sig en ny användares aktivitets mönster.|

>[!NOTE]
> Flera av föregående analyser drivs av Microsoft Cloud App Security. Om du vill dra nytta av dessa analyser måste du aktivera en Cloud App Security-licens. Om du har en Cloud App Security licens är de här aviseringarna aktiverade som standard. Så här inaktiverar du dem:
>
> 1. På bladet **Security Center** väljer du **säkerhets princip**. Välj **Redigera inställningar**för den prenumeration som du vill ändra.
> 2. Välj **hot identifiering**.
> 3. Under **Aktivera integreringar**, rensa **Tillåt Microsoft Cloud App Security åtkomst till mina data**och välj **Spara**.

>[!NOTE]
>Security Center lagrar säkerhetsrelaterad kund information i samma geo som dess resurs. Om Microsoft ännu inte har distribuerat Security Center i resursens geografiska område, lagrar data i USA. När Cloud App Security är aktive rad lagras informationen i enlighet med reglerna för geo-platsen för Cloud App Security. Mer information finns i [data lagring för icke-regionala tjänster](https://azuredatacentermap.azurewebsites.net/).

## Azure Key Vault<a name="azure-keyvault"></a>

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. 

Azure Security Center innehåller Azure-inbyggt Avancerat skydd för Azure Key Vault, vilket ger ytterligare ett lager av säkerhets information. Security Center identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. Det här skydds lagret gör att du kan åtgärda hot utan att vara säkerhets expert och utan att behöva hantera säkerhets övervaknings system från tredje part.  

När avvikande aktiviteter inträffar kan Security Center Visa aviseringar och skicka dem via e-post till prenumerations administratörer. Dessa aviseringar innehåller information om den misstänkta aktiviteten och rekommendationer om hur du undersöker och åtgärdar hot. 

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i moln regioner i Azure myndigheter och suveräna.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Åtkomst från en TOR-stängningsmodul till en Key Vault**|Den Key Vault har öppnats av någon som använder TOR IP-anonymisering för att dölja deras plats. Skadliga aktörer försöker ofta dölja sin plats vid försök att få obehörig åtkomst till Internet-anslutna resurser.|
|**Misstänkt princip ändring och hemlig fråga i en Key Vault**|En Key Vault princip ändring har gjorts och åtgärder för att lista och/eller hämta hemligheter har inträffat. Dessutom utförs detta åtgärds mönster normalt inte av användaren i det här valvet. Detta är starkt känsligt att Key Vault komprometteras och hemligheterna i har blivit stulna av en obehörig aktör.|
|**Misstänkt hemlig lista och fråga i en Key Vault**|En åtgärd för en hemlig lista följdes av många hemliga get-åtgärder. Detta åtgärds mönster utförs vanligt vis inte av användaren i det här valvet. Det innebär att någon kan dumpa hemligheter som lagras i Key Vault för potentiellt skadliga syfte.|
|**Ovanliga User-Application-par har åtkomst till ett Key Vault**|Key Vault har öppnats av ett användar kopplings par som normalt inte har åtkomst till den. Detta kan vara ett legitimt åtkomst försök (till exempel efter en infrastruktur eller kod uppdatering). Detta är också en möjlig indikation på att din infrastruktur har komprometterats och att en obehörig aktör försöker få åtkomst till din Key Vault.|
|**Ovanligt program har åtkomst till ett Key Vault**|Key Vault har öppnats av ett program som normalt inte har åtkomst till det. Detta kan vara ett legitimt åtkomst försök (till exempel efter en infrastruktur eller kod uppdatering). Detta är också en möjlig indikation på att din infrastruktur har komprometterats och att en obehörig aktör försöker få åtkomst till din Key Vault.|
|**Ovanliga användare har till gång till ett Key Vault**|Den Key Vault har varit tillgänglig av en användare som normalt inte har åtkomst till den. Detta kan vara ett legitimt åtkomst försök (till exempel att en ny användare som behöver åtkomst har anslutit till organisationen). Detta är också en möjlig indikation på att din infrastruktur har komprometterats och att en obehörig aktör försöker få åtkomst till din Key Vault.|
|**Ovanligt åtgärds mönster i en Key Vault**|En ovanlig uppsättning Key Vault åtgärder har utförts jämfört med historiska data. Key Vault aktivitet är vanligt vis samma över tid. Detta kan vara en legitim förändring i aktiviteten. Alternativt kan din infrastruktur bli komprometterad och ytterligare undersökningar krävs.|
|**Hög mängd åtgärder i en Key Vault**|En större mängd Key Vault åtgärder har utförts jämfört med historiska data. Key Vault aktivitet är vanligt vis samma över tid. Detta kan vara en legitim förändring i aktiviteten. Alternativt kan din infrastruktur bli komprometterad och ytterligare undersökningar krävs.|
|**Användaren har åtkomst till hög volym med nyckel valv**|Antalet valv som användare eller program har åtkomst till har ändrats jämfört med historiska data. Key Vault aktivitet är vanligt vis samma över tid. Detta kan vara en legitim förändring i aktiviteten. Alternativt kan din infrastruktur bli komprometterad och ytterligare undersökningar krävs.|