---
title: ta med fil
description: ta med fil
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: 067ac0f7f000f749f61d302db4c5c6b856e698a2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875492"
---
Delade avbildnings galleri är en tjänst som hjälper dig att bygga struktur och organisation runt dina hanterade avbildningar. Delade avbildnings gallerier ger:

- Hanterad global replikering av avbildningar.
- Versions hantering och gruppering av avbildningar för enklare hantering.
- Bilder med hög tillgänglighet med ZRS-konton (Zone redundant Storage) i regioner som stöder Tillgänglighetszoner. ZRS ger bättre motståndskraft mot problem i enskilda zoner.
- Dela över prenumerationer och till och med mellan Active Directory (AD)-klienter med RBAC.
- Skala dina distributioner med avbildnings repliker i varje region.

Med hjälp av ett delat bild galleri kan du dela dina avbildningar till olika användare, tjänst huvud namn eller AD-grupper i din organisation. Delade avbildningar kan replikeras till flera regioner, för snabbare skalning av dina distributioner.

En hanterad avbildning är en kopia av antingen en fullständig virtuell dator (inklusive alla anslutna data diskar) eller bara OS-disken, beroende på hur du skapar avbildningen. När du skapar en virtuell dator från avbildningen används en kopia av de virtuella hård diskarna i avbildningen för att skapa diskarna för den nya virtuella datorn. Den hanterade avbildningen finns kvar i lagrings utrymmet och kan användas över och över igen för att skapa nya virtuella datorer.

Om du har ett stort antal hanterade avbildningar som du behöver underhålla och vill göra dem tillgängliga i hela företaget, kan du använda ett delat avbildnings galleri som en lagrings plats som gör det enkelt att dela dina avbildningar. 

Funktionen för delad bild galleri har flera resurs typer:

| Resurs | Beskrivning|
|----------|------------|
| **Hanterad avbildning** | En grundläggande bild som kan användas separat eller som används för att skapa en **avbildnings version** i ett bild galleri. Hanterade avbildningar skapas från [generaliserade](#generalized-and-specialized-images) virtuella datorer. En hanterad avbildning är en särskild typ av virtuell hård disk som kan användas för att skapa flera virtuella datorer och kan nu användas för att skapa delade avbildnings versioner. |
| **Ögonblicks bild** | En kopia av en virtuell hård disk som kan användas för att skapa en **avbildnings version**. Ögonblicks bilder kan hämtas från en [specialiserad](#generalized-and-specialized-images) virtuell dator (en som inte har generaliserats), sedan används separat eller med ögonblicks bilder av data diskar för att skapa en specialiserad avbildnings version.
| **Bild galleri** | Precis som Azure Marketplace är ett **avbildnings Galleri** en lagrings plats för att hantera och dela bilder, men du styr vem som har åtkomst. |
| **Bild definition** | Avbildningar definieras i ett galleri och bär information om avbildningen och kraven för att använda den i din organisation. Du kan inkludera information, till exempel om avbildningen är generaliserad eller specialiserad, kraven på operativ system, minsta och högsta mängd minne och viktig information. Det är en definition av en typ av bild. |
| **Avbildnings version** | En **avbildnings version** är vad du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. Som en hanterad avbildning används avbildnings versionen för att skapa nya diskar för den virtuella datorn när du använder en **avbildnings version** för att skapa en virtuell dator. Avbildnings versioner kan användas flera gånger. |

<br>

![Bild som visar hur du kan ha flera versioner av en bild i galleriet](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Bild definitioner

Bild definitioner är en logisk gruppering för versioner av en bild. Bild definitionen innehåller information om varför avbildningen skapades, vilket operativ system den är för och information om hur du använder avbildningen. En bild definition är som en plan för all information om hur du skapar en speciell avbildning. Du distribuerar inte en virtuell dator från en avbildnings definition, men från avbildnings versionen som skapats från definitionen.

Det finns tre parametrar för varje avbildnings definition som används i kombinations **utgivare**, **erbjudande** och **SKU**. Dessa används för att hitta en bestämd avbildnings definition. Du kan ha avbildnings versioner som delar en eller två, men inte alla tre värden.  Här är till exempel tre bild definitioner och deras värden:

|Bilddefinition|Utgivare|Erbjudande|Sku|
|---|---|---|---|
|myImage1|Contoso|Ekonomi|Serverdel|
|myImage2|Contoso|Ekonomi|Klientdel|
|myImage3|Testning|Ekonomi|Klientdel|

Alla tre av dessa har unika uppsättningar med värden. Formatet liknar hur du för närvarande kan ange utgivare, erbjudande och SKU för [Azure Marketplace-avbildningar](../articles/virtual-machines/windows/cli-ps-findimage.md) i Azure PowerShell för att hämta den senaste versionen av en Marketplace-avbildning. Varje bild definition måste ha en unik uppsättning av dessa värden.

Följande är andra parametrar som kan ställas in på din avbildnings definition så att du enkelt kan spåra dina resurser:

* Operativ system tillstånd – du kan ange operativ systemets tillstånd till [generaliserad eller specialiserad](#generalized-and-specialized-images).
* Operativ system – kan vara antingen Windows eller Linux.
* Beskrivning – Använd beskrivning för att ge mer detaljerad information om varför avbildnings definitionen finns. Du kan till exempel ha en avbildnings definition för din klient server som har programmet förinstallerat.
* EULA – kan användas för att peka på ett licens avtal för slutanvändare som är speciellt för avbildnings definitionen.
* Sekretess policy och viktig information – Store viktig information och sekretess policys i Azure Storage och ange en URI för att få åtkomst till dem som en del av avbildnings definitionen.
* Datum för livs längd – koppla ett livs cykel datum till din bild definition för att kunna använda Automation för att ta bort gamla avbildnings definitioner.
* Tagg – du kan lägga till taggar när du skapar din avbildnings definition. Mer information om taggar finns i [använda taggar för att organisera resurser](../articles/azure-resource-manager/resource-group-using-tags.md)
* Lägsta och högsta vCPU och minnes rekommendationer – om avbildningen har vCPU och minnes rekommendationer kan du koppla informationen till din avbildnings definition.
* Otillåtna disk typer – du kan ange information om lagrings behoven för den virtuella datorn. Om bilden till exempel inte är lämplig för standard diskar för hård diskar lägger du till dem i listan Tillåt inte.

## <a name="generalized-and-specialized-images"></a>Generaliserade och specialiserade avbildningar

Det finns två operativ system tillstånd som stöds av det delade avbildnings galleriet. Vanligt vis kräver avbildningar att den virtuella datorn som användes för att skapa avbildningen har generaliserats innan avbildningen togs. Att generalisera är en process som tar bort dator-och användarspecifik information från den virtuella datorn. För Windows används Sysprep för även. För Linux kan du använda [waagent](https://github.com/Azure/WALinuxAgent) -`-deprovision` eller `-deprovision+user` parametrar.

Specialiserade virtuella datorer har inte genomgått någon process för att ta bort datorspecifik information och konton. Virtuella datorer som skapats från specialiserade avbildningar har inte heller någon `osProfile` kopplad till sig. Det innebär att specialiserade avbildningar kommer att ha vissa begränsningar.

- Konton som kan användas för att logga in på den virtuella datorn kan också användas på alla virtuella datorer som skapats med hjälp av den specialiserade avbildningen som skapas från den virtuella datorn.
- Virtuella datorer kommer att ha **dator namnet** på den virtuella dator som avbildningen hämtades från. Du bör ändra dator namnet för att undvika kollisioner.
- `osProfile` är hur känslig information överförs till den virtuella datorn med hjälp av `secrets`. Detta kan orsaka problem med hjälp av nyckel valv, WinRM och andra funktioner som använder `secrets` i `osProfile`. I vissa fall kan du använda hanterade tjänst identiteter (MSI) för att undvika de här begränsningarna.

> [!IMPORTANT]
> Specialiserade avbildningar är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Kända för hands versions begränsningar** Virtuella datorer kan bara skapas från specialiserade avbildningar med hjälp av portalen eller API: et. Är inget CLI-eller PowerShell-stöd för förhands granskningen.


## <a name="regional-support"></a>Regional support

Käll regionerna visas i tabellen nedan. Alla offentliga regioner kan vara mål regioner, men för att replikera till Australien Central och Australien, Central 2 måste du ha din prenumeration vit listas. Om du vill begära vit listning går du till: https://azure.microsoft.com/global-infrastructure/australia/contact/


| Käll regioner        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Australien, centrala     | Kina, östra        | Indien, södra        | Europa, västra        |
| Australien, centrala 2   | Kina, östra 2      | Asien, sydöstra     | Storbritannien, södra           |
| Australien, östra        | Kina, norra       | Japan, östra         | Storbritannien, västra            |
| Australien, sydöstra   | Kina, norra 2     | Japan, västra         | US DoD, centrala     |
| Brasilien, södra          | Asien, östra         | Sydkorea, centrala      | USA DoD, östra        |
| Kanada, centrala        | USA, östra           | Sydkorea, södra        | Arizona (USA-förvaltad region)     |
| Kanada, östra           | USA, östra 2         | USA, norra centrala   | Texas (USA-förvaltad region)       |
| Indien, centrala         | USA, östra 2 EUAP    | Europa, norra       | USA Gov Virginia    |
| USA, centrala            | Frankrike, centrala    | USA, södra centrala   | Indien, västra         |
| Centrala USA-EUAP       | Frankrike, södra      | USA, västra centrala    | USA, västra            |
|                       |                   |                    | USA, västra 2          |



## <a name="limits"></a>Begränsningar 

Det finns gränser per prenumeration för att distribuera resurser med hjälp av delade avbildnings gallerier:
- 100 delade avbildnings gallerier, per prenumeration, per region
- 1 000 avbildnings definitioner, per prenumeration, per region
- 10 000 avbildnings versioner, per prenumeration, per region
- Alla diskar som är anslutna till avbildningen måste vara mindre än eller lika med 1 TB i storlek

Mer information finns i [kontrol lera resursanvändningen mot begränsningar](https://docs.microsoft.com/azure/networking/check-usage-against-limits) för att se hur du använder den aktuella användningen.
 
## <a name="scaling"></a>Skalning
Med det delade bild galleriet kan du ange antalet repliker som du vill att Azure ska behålla avbildningarna. Detta bidrar till distributions scenarier med flera virtuella datorer eftersom distributioner av virtuella datorer kan spridas till olika repliker, vilket minskar risken för bearbetning av instans skapande begränsas på grund av överbelastning av en enskild replik.

Med delade avbildnings galleriet kan du nu distribuera upp till en 1 000 VM-instanser i en skalnings uppsättning för virtuella datorer (upp till 600 med hanterade avbildningar). Avbildnings repliker ger bättre distributions prestanda, tillförlitlighet och konsekvens.  Du kan ange ett annat antal repliker i varje mål region, baserat på skalnings behoven för regionen. Eftersom varje replik är en djupgående kopia av din avbildning hjälper det att skala dina distributioner linjärt med varje extra replik. Vi förstår att inga två bilder eller regioner är desamma, här är vår allmänna rikt linje om hur du använder repliker i en region:

- För VMSS-distributioner för icke-virtuella datorer, rekommenderar vi att du behåller en replik för varje 20 virtuella datorer som du skapar samtidigt. Om du till exempel skapar 120 virtuella datorer samtidigt med samma avbildning i en region, rekommenderar vi att du behåller minst 6 repliker av avbildningen. 
- För VMSS-distributioner (Virtual Machine Scale set) – för varje skalnings uppsättnings distribution med upp till 600 instanser rekommenderar vi att du behåller minst en replik. Om du till exempel skapar 5 skalnings uppsättningar samtidigt, var och en med 600 VM-instanser med samma avbildning i en enda region, rekommenderar vi att du behåller minst 5 repliker av avbildningen. 

Vi rekommenderar alltid att du överetablerar antalet repliker på grund av faktorer som bild storlek, innehåll och OS-typ.

![Bild som visar hur du kan skala bilder](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Gör dina bilder med hög tillgänglighet

[ZRS (Azure Zone redundant Storage)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) tillhandahåller återhämtning mot ett problem med en tillgänglighets zon i regionen. Med den allmänna tillgängligheten för delade avbildnings galleriet kan du välja att lagra dina avbildningar i ZRS-konton i regioner med Tillgänglighetszoner. 

Du kan också välja konto typen för varje mål region. Standard typen av lagrings konto är Standard_LRS, men du kan välja Standard_ZRS för regioner med Tillgänglighetszoner. Kontrol lera den regionala tillgängligheten för ZRS [här](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Bild som visar ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replikering
Med det delade bild galleriet kan du också replikera dina avbildningar till andra Azure-regioner automatiskt. Varje delad avbildnings version kan replikeras till olika regioner beroende på vad som passar din organisation. Ett exempel är att alltid replikera den senaste avbildningen i flera regioner medan alla äldre versioner bara är tillgängliga i en region. Detta kan hjälpa till att spara lagrings kostnader för delade avbildnings versioner. 

De regioner som en delad avbildnings version replikeras till kan uppdateras efter skapande tiden. Hur lång tid det tar att replikera till olika regioner beror på hur mycket data som kopieras och hur många regioner som versionen replikeras till. Detta kan ta några timmar i vissa fall. Även om replikeringen sker kan du se status för replikering per region. När replikeringen är klar i en region kan du distribuera en virtuell dator eller en skalnings uppsättning med hjälp av avbildnings versionen i regionen.

![Bild som visar hur du kan replikera bilder](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Access

När galleriet för delad avbildning, bild definition och avbildnings version är alla resurser kan de delas med de inbyggda inbyggda Azure RBAC-kontrollerna. Med RBAC kan du dela dessa resurser till andra användare, tjänstens huvud namn och grupper. Du kan även dela åtkomst till personer utanför den klient organisation de skapades i. När en användare har åtkomst till den delade avbildnings versionen kan de distribuera en virtuell dator eller en skalnings uppsättning för virtuella datorer.  Här är en delnings mat ris som hjälper dig att förstå vad användaren får åtkomst till:

| Delat med användare     | Delat avbildningsgalleri | Bilddefinition | Avbildningsversion |
|----------------------|----------------------|--------------|----------------------|
| Delat avbildningsgalleri | Ja                  | Ja          | Ja                  |
| Bilddefinition     | Nej                   | Ja          | Ja                  |

Vi rekommenderar att du delar på Galleri nivå för bästa möjliga upplevelse. Vi rekommenderar inte att du delar enskilda avbildnings versioner. Mer information om RBAC finns i [Hantera åtkomst till Azure-resurser med RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Avbildningar kan också delas, i stor skala, även mellan klienter som använder en app-registrering för flera innehavare. Mer information om hur du delar avbildningar över klienter finns i [dela Galleri VM-avbildningar i Azure-klienter](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Fakturering
Tjänsten Shared Image Gallery erbjuds utan extra kostnad. Du kommer att debiteras för följande resurser:
- Lagrings kostnader för lagring av delade avbildnings versioner. Kostnaden beror på antalet repliker av avbildnings versionen och antalet regioner som versionen replikeras till. Om du till exempel har två bilder och båda replikeras till tre regioner, ändras du för 6 Managed disks baserat på deras storlek. Mer information finns i [Managed disks prissättning](https://azure.microsoft.com/pricing/details/managed-disks/).
- Nätverks utgående kostnader för replikering av den första avbildnings versionen från käll regionen till de replikerade regionerna. Efterföljande repliker hanteras i regionen, så det finns inga ytterligare avgifter. 

## <a name="updating-resources"></a>Uppdaterar resurser

När du har skapat kan du göra några ändringar i avbildnings Galleri resurserna. Dessa är begränsade till:
 
Galleri för delad avbildning:
- Beskrivning

Bild definition:
- Rekommenderad virtuella processorer
- Rekommenderat minne
- Beskrivning
- Datum för slut på livs längd

Avbildnings version:
- Antal regionala repliker
- Mål regioner
- Exkludera från senaste
- Datum för slut på livs längd

## <a name="sdk-support"></a>SDK-support

Följande SDK: er har stöd för att skapa delade avbildnings gallerier:

- [NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Mallar

Du kan skapa en resurs för delade avbildnings galleri med hjälp av mallar. Det finns flera tillgängliga Azure snabb starts mallar: 

- [Skapa ett galleri för delad avbildning](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildnings definition i ett galleri för delade avbildningar](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildnings version i ett galleri för delad avbildning](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildnings version](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Vanliga frågor 

* [Hur gör jag för att visa en lista över alla delade avbildnings Galleri resurser i prenumerationer?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Kan jag flytta min befintliga avbildning till galleriet för delad avbildning?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Kan jag skapa en avbildnings version från en specialiserad disk?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Kan jag flytta den delade avbildnings Galleri resursen till en annan prenumeration när den har skapats?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Kan jag replikera mina bild versioner över moln som Azure Kina 21Vianet eller Azure Germany eller Azure Government molnet?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Kan jag replikera mina image-versioner mellan prenumerationer?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Kan jag dela avbildnings versioner i Azure AD-klienter?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Hur lång tid tar det att replikera avbildnings versioner i mål regionerna?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Vad är skillnaden mellan käll region och mål region?](#what-is-the-difference-between-source-region-and-target-region)
* [Hur gör jag för att anger du käll regionen när du skapar avbildnings versionen?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Hur gör jag för att ange antalet avbildningar av avbildnings version som ska skapas i varje region?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Kan jag skapa galleriet för delade avbildningar på en annan plats än det för bild definitionen och avbildnings versionen?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Vad kostar det att använda det delade avbildnings galleriet?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Vilken API-version ska jag använda för att skapa delade avbildnings gallerier och bild definitions-och avbildnings versioner?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Vilken API-version ska jag använda för att skapa delad VM eller virtuell dators skalnings uppsättning från avbildnings versionen?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Hur gör jag för att visa en lista över alla delade avbildnings Galleri resurser i prenumerationer?

Om du vill visa en lista över alla delade avbildnings Galleri resurser över prenumerationer som du har åtkomst till på Azure Portal följer du stegen nedan:

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Gå till **alla resurser**.
1. Välj alla prenumerationer som du vill lista alla resurser under.
1. Sök efter resurser av typen **privat Galleri**.
 
   Om du vill se bild definitionerna och bild versionerna bör du även välja **Visa dolda typer**.
 
   Om du vill visa en lista över alla delade avbildnings Galleri resurser över prenumerationer som du har behörighet till använder du följande kommando i Azure CLI:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Kan jag flytta min befintliga avbildning till galleriet för delad avbildning?
 
Ja. Det finns tre scenarier baserat på de typer av avbildningar som du kan ha.

 Scenario 1: om du har en hanterad avbildning kan du skapa en avbildnings definition och avbildnings version från den.

 Scenario 2: om du har en ohanterad avbildning kan du skapa en hanterad avbildning från den och sedan skapa en avbildnings definition och avbildnings version från den. 

 Scenario 3: om du har en virtuell hård disk i det lokala fil systemet måste du ladda upp den virtuella hård disken till en hanterad avbildning. sedan kan du skapa en avbildnings definition och avbildnings version från den.

- Om den virtuella hård disken är en virtuell Windows-dator kan du läsa [Ladda upp en virtuell hård disk](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Om den virtuella hård disken är för en virtuell Linux-dator läser du [Ladda upp en virtuell hård disk](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Kan jag skapa en avbildnings version från en specialiserad disk?

Ja, stöd för specialiserade diskar som bilder är i för hands version. Du kan bara skapa en virtuell dator från en specialiserad avbildning med hjälp av portalen ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) eller [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) och API. Det finns inget PowerShell-stöd för förhands granskningen.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Kan jag flytta den delade avbildnings Galleri resursen till en annan prenumeration när den har skapats?

Nej, du kan inte flytta den delade avbildnings Galleri resursen till en annan prenumeration. Du kommer dock att kunna replikera avbildnings versionerna i galleriet till andra regioner efter behov.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Kan jag replikera mina bild versioner över moln som Azure Kina 21Vianet eller Azure Germany eller Azure Government molnet?

Nej, du kan inte replikera avbildnings versioner över moln.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Kan jag replikera mina image-versioner mellan prenumerationer?

Nej, du kan replikera avbildnings versionerna mellan regioner i en prenumeration och använda dem i andra prenumerationer via RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Kan jag dela avbildnings versioner i Azure AD-klienter? 

Ja, du kan använda RBAC för att dela till individer mellan klienter. Men om du vill dela i skala, se "dela Galleri bilder över Azure-klienter" med hjälp av [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) eller [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Hur lång tid tar det att replikera avbildnings versioner i mål regionerna?

Replikerings tiden för avbildnings versionen är helt beroende av avbildningens storlek och antalet regioner som den replikeras till. Men som bästa praxis rekommenderar vi att du håller bilden liten och att käll-och mål regionerna stängs för bästa möjliga resultat. Du kan kontrol lera status för replikeringen med hjälp av flaggan-ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Vad är skillnaden mellan käll region och mål region?

Käll region är den region där din avbildnings version kommer att skapas och mål regioner är de regioner där en kopia av din avbildnings version ska lagras. För varje avbildnings version kan du bara ha en käll region. Se också till att du överför käll regions platsen som en av mål regionerna när du skapar en avbildnings version.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Hur gör jag för att anger du käll regionen när du skapar avbildnings versionen?

När du skapar en avbildnings version kan du använda taggen **--location** i CLI och taggen **-location** i PowerShell för att ange käll regionen. Kontrol lera att den hanterade avbildningen som du använder som bas avbildning för att skapa avbildnings versionen finns på samma plats som den plats där du vill skapa avbildnings versionen. Se också till att du överför käll regions platsen som en av mål regionerna när du skapar en avbildnings version.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Hur gör jag för att ange antalet avbildningar av avbildnings version som ska skapas i varje region?

Det finns två sätt som du kan använda för att ange hur många avbildningar av avbildnings version som ska skapas i varje region:
 
1. Antalet regionala repliker som anger antalet repliker som du vill skapa per region. 
2. Antal vanliga repliker som är standardvärdet per region, om antalet regionala repliker inte har angetts. 

Ange det regionala replik antalet genom att skicka platsen tillsammans med antalet repliker som du vill skapa i regionen: "södra centrala USA = 2". 

Om det regionala replik antalet inte anges med varje plats, blir standard antalet repliker det vanliga antalet repliker som du har angett. 

Om du vill ange vanliga replik antal i CLI använder du argumentet **--Replica-Count** i kommandot `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Kan jag skapa galleriet för delade avbildningar på en annan plats än det för bild definitionen och avbildnings versionen?

Ja, det kan du. Men vi rekommenderar att du behåller resurs gruppen, delade avbildnings galleriet, avbildnings definitionen och avbildnings versionen på samma plats.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Vad kostar det att använda det delade avbildnings galleriet?

Det kostar inget att använda tjänsten Shared Image Gallery, förutom lagrings kostnaderna för lagring av avbildnings versioner och utgående kostnader för att replikera avbildnings versionerna från käll regionen till mål regioner.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Vilken API-version ska jag använda för att skapa delade avbildnings gallerier och bild definitions-och avbildnings versioner?

Om du vill arbeta med delade avbildnings gallerier, bild definitioner och avbildnings versioner rekommenderar vi att du använder API-version 2018-06-01. ZRS (Zone redundant Storage) kräver version 2019-03-01 eller senare.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Vilken API-version ska jag använda för att skapa delad VM eller virtuell dators skalnings uppsättning från avbildnings versionen?

För virtuella datorer och distributioner av skalnings uppsättningar för virtuella datorer med en avbildnings version rekommenderar vi att du använder API version 2018-04-01 eller senare.
