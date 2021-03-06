---
title: Säkerhet för behållar instanser
description: Rekommendationer för att skydda bilder och hemligheter för Azure Container Instances och allmänna säkerhets överväganden för alla behållar plattformar
ms.topic: article
ms.date: 04/29/2019
ms.custom: ''
ms.openlocfilehash: b25cb4178ba211ff819ba512c9820165e0efbbf1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481704"
---
# <a name="security-considerations-for-azure-container-instances"></a>Säkerhets överväganden för Azure Container Instances

Den här artikeln beskriver säkerhets överväganden för att använda Azure Container Instances för att köra behållar appar. Ämnena omfattar:

> [!div class="checklist"]
> * **Säkerhets rekommendationer** för att hantera avbildningar och hemligheter för Azure Container instances
> * **Överväganden för containerns eko system** under hela livs cykelns livs cykel, för alla behållar plattformar

## <a name="security-recommendations-for-azure-container-instances"></a>Säkerhets rekommendationer för Azure Container Instances

### <a name="use-a-private-registry"></a>Använd ett privat register

Container skapas från avbildningar som lagras i en eller flera databaser. Dessa databaser kan tillhöra ett offentligt register, som [Docker Hub](https://hub.docker.com)eller till ett privat register. Ett exempel på ett privat register är [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), som kan installeras lokalt eller i ett virtuellt privat moln. Du kan också använda molnbaserad privata behållar register tjänster, inklusive [Azure Container Registry](../container-registry/container-registry-intro.md). 

En offentligt tillgänglig behållar avbildning garanterar inte säkerheten. Behållar avbildningar består av flera program varu lager och varje program varu lager kan ha sårbarheter. För att minska risken för attacker bör du lagra och hämta avbildningar från ett privat register, till exempel Azure Container Registry eller Docker Trusted Registry. Förutom att tillhandahålla ett hanterat privat register, Azure Container Registry stödja [tjänstens huvudbaserad autentisering](../container-registry/container-registry-authentication.md) via Azure Active Directory för grundläggande autentiserings flöden. Den här autentiseringen innehåller rollbaserad åtkomst för Skriv-, skriv-(push) och ägar behörigheter.

### <a name="monitor-and-scan-container-images"></a>Övervaka och skanna behållar avbildningar

Säkerhetsövervaknings-och skannings lösningar som [twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) och [turkos säkerhet](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) är tillgängliga via Azure Marketplace. Du kan använda dem för att genomsöka behållar avbildningar i ett privat register och identifiera potentiella sårbarheter. Det är viktigt att förstå djupet vid genomsökning av de olika lösningarna. 

### <a name="protect-credentials"></a>Skydda autentiseringsuppgifter

Behållare kan spridas över flera kluster och Azure-regioner. Därför måste du skydda autentiseringsuppgifter som krävs för inloggningar eller API-åtkomst, till exempel lösen ord eller tokens. Se till att endast privilegierade användare kan komma åt dessa behållare under överföring och i vila. Inventera alla hemligheter för autentiseringsuppgifter och Kräv sedan att utvecklare använder nya hemligheter – hanterings verktyg som är utformade för behållar plattformar.  Se till att lösningen innehåller krypterade databaser, TLS-kryptering för hemligheterade data i överföring och [rollbaserad åtkomst kontroll](../role-based-access-control/overview.md)med minst behörighet. [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) är en moln tjänst som skyddar krypterings nycklar och hemligheter (till exempel certifikat, anslutnings strängar och lösen ord) för program i behållare. Eftersom dessa data är känsliga och affärs kritiska kan du skydda åtkomsten till dina nyckel valv så att endast auktoriserade program och användare kan komma åt dem.

## <a name="considerations-for-the-container-ecosystem"></a>Överväganden för containerns eko system

Följande säkerhets åtgärder, som implementeras väl och hanteras effektivt, kan hjälpa dig att skydda och skydda ditt eko system. Dessa åtgärder gäller under hela livs cykelns livs cykel, från utveckling till produktions distribution och till ett antal behållar dirigeringar, värdar och plattformar. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Använd sårbarhets hantering som en del av livs cykeln för behållar utveckling 

Genom att använda effektiv sårbarhets hantering i livs cykeln för container utveckling förbättrar du strider som du identifierar och löser säkerhets problem innan de blir allvarligare. 

### <a name="scan-for-vulnerabilities"></a>Sök efter sårbarheter 

Nya sårbarheter upptäcks hela tiden, så genomsökning av och identifiera sårbarheter är en kontinuerlig process. Införliva säkerhets genomsökning under hela livs cykeln för behållare:

* Som en slutgiltig kontroll i din utvecklings pipeline bör du utföra en sårbarhets sökning på behållare innan du skickar avbildningarna till ett offentligt eller privat register. 
* Fortsätt att skanna behållar avbildningar i registret för att identifiera eventuella fel som skulle ha missats under utvecklingen och för att åtgärda eventuella nyligen identifierade säkerhets risker som kan finnas i koden som används i behållar avbildningarna.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Avbilda avbildnings sårbarheter för att köra behållare 

Du behöver ett sätt att mappa sårbarheter som identifieras i behållar avbildningar till att köra behållare, så säkerhets problem kan åtgärdas eller lösas.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Se till att endast godkända avbildningar används i din miljö 

Det finns tillräckligt med ändrings-och flyktiga i ett eko system för behållare utan att tillåta okända behållare. Tillåt endast godkända behållar avbildningar. Ha verktyg och processer på plats för att övervaka och förhindra användning av icke-godkända behållar avbildningar. 

Ett effektivt sätt att minska risken för attacker och förhindra utvecklare från att fatta kritiska säkerhets fel är att styra flödet av behållar avbildningar till utvecklings miljön. Du kan till exempel sanktionera en enskild Linux-distribution som en bas avbildning, helst en som är Lean (Alpine eller core i stället för Ubuntu), för att minimera ytan för potentiella attacker. 

Bild signering eller finger avtryck kan ge en kedja av vårdnad som gör det möjligt att verifiera behållarens integritet. Azure Container Registry har till exempel stöd för Docker-modellen för [innehålls förtroende](https://docs.docker.com/engine/security/trust/content_trust) , som tillåter avbildnings utgivare att signera bilder som skickas till ett register och avbildnings konsumenter för att bara Hämta signerade avbildningar.

### <a name="permit-only-approved-registries"></a>Tillåt endast godkända register 

Ett tillägg för att säkerställa att miljön endast använder godkända avbildningar är att endast tillåta användning av godkända behållar register. Genom att kräva att godkända behållar register används minskar exponeringen för risker genom att begränsa möjligheten att införa okända sårbarheter eller säkerhets problem. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Säkerställa integriteten för avbildningar under hela livs cykeln 

En del av att hantera säkerhet i livs cykeln för behållare är att säkerställa att behållar avbildningens integritet i registret och att de ändras eller distribueras till produktion. 

* Avbildningar med sårbarheter, även mindre, bör inte tillåtas att köras i produktions miljöer. Vi rekommenderar att alla avbildningar som distribueras i produktion sparas i ett privat register som är tillgängligt för ett urval. Behåll antalet produktions bilder små för att säkerställa att de kan hanteras effektivt.

* Eftersom det är svårt att hitta ursprunget för program varan från en offentligt tillgänglig behållar avbildning skapar du avbildningar från källan för att säkerställa att lagrets ursprung. När en säkerhetsrisk identifieras i en egenutvecklad containeravbildning är det lättare för kunderna att snabbt komma fram till en lösning. Med en offentlig avbildning måste kunderna hitta roten till en offentlig avbildning för att kunna åtgärda den eller få en annan säker avbildning från utgivaren. 

* En väl skannad avbildning som distribueras i produktion är inte garanterat uppdaterad under programmets livs längd. Säkerhetsproblem kan rapporteras för lager i avbildningen som inte tidigare var kända eller som uppstått efter distributionen i produktionsmiljön. 

  Granska regelbundet bilder som distribuerats i produktion för att identifiera avbildningar som är inaktuella eller som inte har uppdaterats på ett tag. Du kan använda blå och gröna distributions metoder och metoder för löpande uppgradering för att uppdatera behållar avbildningar utan drift stopp. Du kan skanna bilder med hjälp av verktygen som beskrivs i föregående avsnitt. 

* Använd en pipeline för kontinuerlig integrering (CI) med integrerad säkerhets genomsökning för att bygga säkra avbildningar och skicka dem till ditt privata register. Den inbyggda säkerhetsgenomsökningen i CI-lösningen ser till att avbildningar som klarar alla testerna skickas till det privata registret som produktionsarbetsbelastningarna distribueras från. 

  Ett problem med en CI-pipeline garanterar att sårbara avbildningar inte skickas till det privata registret som används för distributioner av produktions arbets belastningar. Dessutom automatiseras avbildnings säkerhets genomsökningen om det finns ett stort antal avbildningar. Att manuellt söka efter säkerhetsrisker i avbildningar kan vara en enormt tidskrävande och felbenägen uppgift. 

### <a name="enforce-least-privileges-in-runtime"></a>Framtvinga minst behörighet i körnings miljön 

Begreppet minst privilegier är en grundläggande säkerhets metod som också gäller för behållare. När ett säkerhets problem utnyttjas ger det vanligt vis angripare åtkomst och privilegier som motsvarar de i det komprometterade programmet eller processen. Att se till att behållare använder de lägsta privilegier och åtkomst som krävs för att få jobbet utfört minskar exponeringen för risk. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Minska behållar attack ytan genom att ta bort onödiga privilegier 

Du kan också minimera den potentiella angrepps ytan genom att ta bort oanvända eller onödiga processer eller behörigheter från container Runtime. Privilegierade behållare körs som rot. Om en obehörig användare eller arbets belastning utsätts i en privilegie rad behållare, körs behållaren som rot på det systemet.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Vitlista-filer och körbara filer som behållaren kan komma åt eller köra 

Genom att minska antalet variabler eller okända, kan du underhålla en stabil, tillförlitlig miljö. Begränsa behållare så att de kan komma åt eller köra enbart förgodkännda eller vit listas filer och körbara filer är en beprövad metod för att begränsa exponeringen för risker.  

Det är mycket enklare att hantera en vitlista när den implementeras från början. En vitlista tillhandahåller ett mått på kontroll och hanterbarhet när du lär dig vilka filer och körbara filer som krävs för att programmet ska fungera korrekt. 

En vitlista minskar inte bara angrepps ytan, men kan också tillhandahålla en bas linje för avvikelser och förhindra användnings fall av scenarier med "bruset" och container grupp. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Tvinga nätverks segmentering på behållare som körs  

Du kan skydda behållare i ett undernät från säkerhets risker i ett annat undernät genom att underhålla nätverks segmentering (eller nano-segmentering) eller uppdelning mellan behållare som körs. Det kan också vara nödvändigt att underhålla nätverks segmentering för att kunna använda behållare i branscher som krävs för att uppfylla villkoren för efterlevnad.  

Till exempel tillhandahåller partner verktyget [turkos](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) en automatiserad metod för nano-segmentering. Turkos övervakar nätverks aktiviteter för behållare i körnings miljön. Den identifierar alla inkommande och utgående nätverks anslutningar till/från andra behållare, tjänster, IP-adresser och det offentliga Internet. Nano-segmentering skapas automatiskt baserat på övervakad trafik. 

### <a name="monitor-container-activity-and-user-access"></a>Övervaka container aktivitet och användar åtkomst 

Precis som med en IT-miljö bör du konsekvent övervaka aktivitet och användares åtkomst till ditt container eko system för att snabbt identifiera misstänkt eller skadlig aktivitet. Azure tillhandahåller övervaknings lösningar för behållare, inklusive:

* [Azure Monitor för behållare](../azure-monitor/insights/container-insights-overview.md) för att övervaka prestanda för dina arbets belastningar som distribueras till Kubernetes-miljöer som finns i Azure Kubernetes service (AKS). Azure Monitor för behållare ger dig insyn i prestanda genom att samla in minne och processor mått från domänkontrollanter, noder och behållare som är tillgängliga i Kubernetes via mått-API. 

* [Azure Container Monitoring-lösningen](../azure-monitor/insights/containers.md) hjälper dig att visa och hantera andra Docker-och Windows container-värdar på en enda plats. Exempel:

  * Visa detaljerad gransknings information som visar kommandon som används med behållare. 
  * Felsök behållare genom att visa och söka i centraliserade loggar utan att behöva fjärrans luta till Docker eller Windows-värdar.  
  * Hitta behållare som kan vara störningar och förbruka överskott av resurser på en värd.
  * Visa centraliserad processor, minne, lagring och nätverks användning och prestanda information för behållare.  

  Lösningen stöder behållar dirigering, inklusive Docker Swarm, DC/OS, ohanterad Kubernetes, Service Fabric och Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Övervaka container resurs aktivitet 

Övervaka resurs aktiviteten, t. ex. filer, nätverk och andra resurser som behållarna har åtkomst till. Övervakning av resurs aktivitet och konsumtion är användbart både för prestanda övervakning och som ett säkerhets mått. 

[Azure Monitor](../azure-monitor/overview.md) möjliggör kärn övervakning av Azure-tjänster genom att tillåta insamling av mått, aktivitets loggar och diagnostikloggar. Aktivitetsloggen ger dig t.ex. information om när nya resurser skapas eller ändras. 

Tillgängliga mått tillhandahåller prestandastatistik för olika resurser och även för operativsystemet i en virtuell dator. Du kan visa dessa data med någon av utforskarna i Azure Portal och skapa aviseringar som baseras på måtten. Azure Monitor ger en pipeline för snabbast mått (5 minuter ned till 1 minut), så du bör använda den för tids kritiska aviseringar och meddelanden. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Logga all behållar administrativ användar åtkomst för granskning 

Ha en korrekt Gransknings logg över administrativ åtkomst till ditt eko system, behållar register och behållar avbildningar. Dessa loggar kan vara nödvändiga för gransknings syfte och kommer att vara användbara som kriminal tekniska-bevis efter eventuella säkerhets incidenter. Du kan använda [Azure Container Monitor-lösningen](../azure-monitor/insights/containers.md) för att uppnå detta syfte. 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att hantera sårbarheter för behållare med lösningar från [twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) och [turkos säkerhet](https://www.aquasec.com/solutions/azure-container-security/).

* Lär dig mer om [behållar säkerhet i Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).