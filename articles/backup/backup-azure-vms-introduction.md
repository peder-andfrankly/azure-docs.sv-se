---
title: Om Säkerhetskopiering av virtuella Azure-datorer
description: I den här artikeln lär du dig hur tjänsten Azure Backup säkerhetskopierar virtuella Azure-datorer och hur du följer bästa praxis.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 4bd42acbf682b51e17f60702e5695cfb29db812b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806447"
---
# <a name="an-overview-of-azure-vm-backup"></a>En översikt över säkerhets kopiering av virtuella Azure-datorer

Den här artikeln beskriver hur [tjänsten Azure Backup](backup-introduction-to-azure-backup.md) säkerhetskopierar virtuella datorer i Azure.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

Så här gör Azure Backup slutföra en säkerhets kopiering för virtuella Azure-datorer:

1. För virtuella Azure-datorer som väljs för säkerhets kopiering startar Azure Backup ett säkerhets kopierings jobb enligt det schema som du anger.
1. Under den första säkerhets kopieringen installeras ett säkerhets kopierings tillägg på den virtuella datorn om den virtuella datorn körs.
    - För virtuella Windows-datorer installeras _VMSnapshot_ -tillägget.
    - För virtuella Linux-datorer är _VMSnapshotLinux_ -tillägget installerat.
1. För virtuella Windows-datorer som kör, koordineras säkerhets kopiering med Windows tjänsten Volume Shadow Copy (VSS) för att ta en programkonsekvent ögonblicks bild av den virtuella datorn.
    - Som standard tar säkerhets kopieringen fullständiga VSS-säkerhetskopieringar.
    - Om säkerhets kopieringen inte kan ta en programkonsekvent ögonblicks bild tar det en filkonsekvent ögonblicks bild av den underliggande lagringen (eftersom ingen program skrivning sker medan den virtuella datorn stoppas).
1. För virtuella Linux-datorer tar säkerhets kopieringen en filkonsekvent säkerhets kopia. För programkonsekventa ögonblicks bilder måste du manuellt anpassa för-eller-post-skript.
1. När säkerhets kopieringen tar ögonblicks bilden överförs data till valvet.
    - Säkerhets kopieringen är optimerad genom att säkerhetskopiera varje virtuell dator disk parallellt.
    - För varje disk som ska säkerhets kopie ras läser Azure Backup blocken på disken och identifierar och överför endast de data block som har ändrats (delta) sedan den tidigare säkerhets kopieringen.
    - Ögonblicks bild data kan inte kopieras direkt till valvet. Det kan ta några timmar vid hög belastnings tider. Den totala säkerhets kopierings tiden för en virtuell dator kommer att vara mindre än 24 timmar för dagliga säkerhets kopierings principer.
1. Ändringar som görs i en virtuell Windows-dator efter Azure Backup har Aktiver ATS på följande:
    - Microsoft Visual C++ 2013 Redistributable (x64)-12.0.40660 har installerats på den virtuella datorn
    - Start typen för tjänsten Volume Shadow Copy (VSS) har ändrats till automatisk från manuell
    - IaaSVmProvider Windows-tjänst har lagts till

1. När data överföringen är klar tas ögonblicks bilden bort och en återställnings punkt skapas.

![Arkitektur för säkerhets kopiering av virtuella Azure-datorer](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Kryptering av virtuella Azure-säkerhetskopieringar

När du säkerhetskopierar virtuella Azure-datorer med Azure Backup krypteras de virtuella datorerna i rest med Kryptering för lagringstjänst (SSE). Azure Backup kan också säkerhetskopiera virtuella Azure-datorer som krypteras med hjälp av Azure Disk Encryption.

**Kryptering** | **Detaljer** | **Support**
--- | --- | ---
**Azure Disk Encryption** | Azure Disk Encryption krypterar både OS-och data diskar för virtuella Azure-datorer.<br/><br/> Azure Disk Encryption integreras med BitLocker-BEKs (-krypterings nycklar) som skyddas i ett nyckel valv som hemligheter. Azure Disk Encryption integreras också med Azure Key Vault Key Encryption Keys (KeyExchange). | Azure Backup stöder säkerhets kopiering av hanterade och ohanterade virtuella Azure-datorer med endast BEKs, eller med BEKs tillsammans med KeyExchange.<br/><br/> Både BEKs och KeyExchange säkerhets kopie ras och krypteras.<br/><br/> Eftersom KeyExchange och BEKs säkerhets kopie ras kan användare med de behörigheter som krävs återställa nycklar och hemligheter tillbaka till nyckel valvet om det behövs. Dessa användare kan också återställa den krypterade virtuella datorn.<br/><br/> Krypterade nycklar och hemligheter kan inte läsas av obehöriga användare eller av Azure.
**SSE** | Med SSE ger Azure Storage kryptering i vila genom att automatiskt kryptera data innan de lagras. Azure Storage dekrypterar också data innan de hämtas. | Azure Backup använder SSE för at-rest-kryptering av virtuella Azure-datorer.

För hanterade och ohanterade virtuella Azure-datorer stöder säkerhets kopiering både virtuella datorer som är krypterade med BEKs eller virtuella datorer som har krypterats med BEKs tillsammans med KeyExchange.

De säkerhetskopierade BEKs (hemligheter) och KeyExchange (nycklar) är krypterade. De kan bara läsas och användas när de återställs tillbaka till nyckel valvet av behöriga användare. Varken obehöriga användare eller Azure kan läsa eller använda säkerhetskopierade nycklar eller hemligheter.

BEKs säkerhets kopie ras också. Så om BEKs förloras kan behöriga användare återställa BEKs till nyckel valvet och återställa de krypterade virtuella datorerna. Endast användare med nödvändig behörighets nivå kan säkerhetskopiera och återställa krypterade virtuella datorer eller nycklar och hemligheter.

## <a name="snapshot-creation"></a>Generering av ögonblicksbild

Azure Backup tar ögonblicks bilder enligt schema för säkerhets kopiering.

- **Virtuella Windows-datorer:** För virtuella Windows-datorer koordineras tjänsten för säkerhets kopiering med VSS för att ta en programkonsekvent ögonblicks bild av de virtuella dator diskarna.

  - Som standard tar Azure Backup fullständiga VSS-säkerhetskopieringar. [Läs mer](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Om du vill ändra inställningen så att Azure Backup tar säkerhets kopior av VSS-kopiering anger du följande register nyckel från en kommando tolk:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent"/v USEVSSCOPYBACKUP/t REG_SZ/d TRUE/f**

- **Virtuella Linux-datorer:** Om du vill göra programkonsekventa ögonblicks bilder av virtuella Linux-datorer använder du ramverket för för skript och efter skript för att skriva egna anpassade skript för att säkerställa konsekvens.

  - Azure Backup anropar bara de för hands-skript som skrivs av dig.
  - Om för skripten och post-skripten körs korrekt, Azure Backup markerar återställnings punkten som programkonsekvent. Men när du använder anpassade skript är du i slut änden ansvarig för programmets konsekvens.
  - [Läs mer](backup-azure-linux-app-consistent.md) om hur du konfigurerar skript.

### <a name="snapshot-consistency"></a>Ögonblicks bilds konsekvens

I följande tabell förklaras de olika typerna av ögonblicks bilds konsekvens:

**Ögonblicks bild** | **Detaljer** | **Återställning** | **Övervägande**
--- | --- | --- | ---
**Programkonsekvent** | Programkonsekventa säkerhets kopieringar fångar upp minnes innehåll och väntande I/O-åtgärder. I programkonsekventa ögonblicks bilder används en VSS-skrivare (eller pre/post-skript för Linux) för att säkerställa konsekvensen av AppData innan en säkerhets kopiering sker. | När du återställer en virtuell dator med en programkonsekvent ögonblicks bild startas den virtuella datorn. Det finns inga skadade data eller går förlorade. Apparna startar i ett konsekvent tillstånd. | Windows: alla VSS-skrivare har slutförts<br/><br/> Linux: pre/post-skript har kon figurer ATS och genomförts
**Konsekvent fil system** | Konsekventa säkerhets kopieringar i fil systemet ger konsekvens genom att ta en ögonblicks bild av alla filer på samma gång.<br/><br/> | När du återställer en virtuell dator med en konsekvent fil system ögonblicks bild startas den virtuella datorn. Det finns inga skadade data eller går förlorade. Appar behöver implementera sin egen "Fix"-mekanism för att se till att återställda data är konsekventa. | Windows: vissa VSS-skrivare misslyckades <br/><br/> Linux: standard (om pre/post-skript inte har kon figurer ATS eller misslyckats)
**Krasch-konsekvent** | Krasch-konsekventa ögonblicks bilder inträffar vanligt vis om en virtuell Azure-dator stängs av vid säkerhets kopieringen. Endast de data som redan finns på disken vid tidpunkten för säkerhets kopieringen samlas in och säkerhets kopie ras. | Börjar med den virtuella datorns start process följt av en disk kontroll för att åtgärda skadade fel. Alla minnes minnes data eller Skriv åtgärder som inte överfördes till disken innan kraschen förlorades. Appar implementerar sin egen data verifiering. En Database-app kan till exempel använda transaktions loggen för verifiering. Om transaktions loggen har poster som inte finns i databasen, rullar databas programmet tillbaka transaktioner tills data är konsekventa. | Den virtuella datorn har stängts av (stoppat/frigjord).

## <a name="backup-and-restore-considerations"></a>Överväganden för säkerhets kopiering och återställning

**Övervägande** | **Detaljer**
--- | ---
**Disk** | Säkerhets kopiering av virtuella dator diskar är parallell. Om till exempel en virtuell dator har fyra diskar, försöker säkerhets kopierings tjänsten säkerhetskopiera alla fyra diskarna parallellt. Backup är stegvis (endast ändrade data).
**Schemaläggning** |  Du kan minska säkerhets kopierings trafiken genom att säkerhetskopiera olika virtuella datorer vid olika tidpunkter på dagen och se till att tiderna inte överlappar varandra. När du säkerhetskopierar virtuella datorer samtidigt orsakar det att trafiken fastnar.
**Förbereder säkerhets kopiering** | Tänk på den tid som krävs för att förbereda säkerhets kopieringen. Förberedelse tiden omfattar att installera eller uppdatera säkerhets kopierings tillägget och utlösa en ögonblicks bild enligt schemat för säkerhets kopieringen.
**Dataöverföring** | Ta reda på hur lång tid det tar för Azure Backup att identifiera de stegvisa ändringarna från den tidigare säkerhets kopian.<br/><br/> I en stegvis säkerhets kopia bestämmer Azure Backup ändringarna genom att beräkna blockets kontroll summa. Om ett block ändras är det markerat för överföring till valvet. Tjänsten analyserar de identifierade blocken för att försöka ytterligare minimera mängden data som ska överföras. När du har utvärderat alla ändrade block överför Azure Backup ändringarna till valvet.<br/><br/> Det kan finnas en fördröjning mellan att ta ögonblicks bilden och kopiera den till valvet.<br/><br/> Vid hög belastnings tider kan det ta upp till åtta timmar innan säkerhets kopieringarna bearbetas. Säkerhets kopierings tiden för en virtuell dator kommer att vara mindre än 24 timmar för den dagliga säkerhets kopieringen.
**Första säkerhets kopiering** | Även om den totala säkerhets kopierings tiden för stegvisa säkerhets kopieringar är mindre än 24 timmar, kan det hända att det inte är fallet för den första säkerhets kopieringen. Tiden som krävs för den första säkerhets kopieringen beror på storleken på data och när säkerhets kopieringen bearbetas.
**Återställ kö** | Azure Backup bearbetar återställnings jobb från flera lagrings konton samtidigt och placerar återställnings begär anden i en kö.
**Återställ kopia** | Under återställnings processen kopieras data från valvet till lagrings kontot.<br/><br/> Den totala återställnings tiden beror på i/O-åtgärder per sekund (IOPS) och data flödet för lagrings kontot.<br/><br/> Om du vill minska kopierings tiden väljer du ett lagrings konto som inte har lästs in med andra program skrivningar och läsningar.

### <a name="backup-performance"></a>Säkerhets kopierings prestanda

Dessa vanliga scenarier kan påverka den totala säkerhets kopierings tiden:

- **Lägga till en ny disk till en skyddad virtuell Azure-dator:** Om en virtuell dator genomgår en stegvis säkerhets kopiering och en ny disk läggs till ökar säkerhets kopierings tiden. Den totala säkerhets kopierings tiden kan vara längre än 24 timmar på grund av den första replikeringen av den nya disken, tillsammans med delta replikering av befintliga diskar.
- **Fragmenterade diskar:** Säkerhets kopierings åtgärder går snabbare när disk ändringar är sammanhängande. Om ändringar sprids ut och fragmenteras på en disk går säkerhets kopieringen långsammare.
- **Disk omsättning:** Om skyddade diskar som genomgår en stegvis säkerhets kopiering har en daglig omsättning på över 200 GB, kan säkerhets kopieringen ta lång tid (mer än åtta timmar) att slutföra.
- **Säkerhets kopierings versioner:** Den senaste versionen av backup (kallas snabb återställnings version) använder en mer optimerad process än jämförelse av kontroll summa för att identifiera ändringar. Men om du använder omedelbar återställning och har tagit bort en ögonblicks bild av säkerhets kopia växlar säkerhets kopian till jämförelse av kontroll summa. I det här fallet kommer säkerhets kopieringen att överskrida 24 timmar (eller misslyckande).

## <a name="best-practices"></a>Bästa metoder

När du konfigurerar VM-säkerhetskopieringar föreslår vi följande metoder:

- Ändra standard schema tiderna som anges i en princip. Till exempel, om standard tiden i principen är 12:00 AM, ökar du tiden med flera minuter så att resurserna optimalt används.
- Om du återställer virtuella datorer från ett enda valv rekommenderar vi starkt att du använder olika [generella v2-lagrings konton](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) för att säkerställa att mål lagrings kontot inte får någon begränsning. Till exempel måste varje virtuell dator ha ett annat lagrings konto. Om till exempel 10 virtuella datorer återställs använder du 10 olika lagrings konton.
- För säkerhets kopiering av virtuella datorer som använder Premium Storage, med omedelbar återställning, rekommenderar vi att du allokerar *50%* ledigt utrymme för det totala allokerade lagrings utrymmet, vilket **endast** krävs för den första säkerhets kopieringen. Det lediga utrymmet på 50% är inte ett krav för säkerhets kopieringar när den första säkerhets kopieringen har slutförts
- Restore från ett allmänt lagrings lager (ögonblicks bild) kommer att slutföras på några minuter eftersom ögonblicks bilden finns på samma lagrings konto. Det kan ta flera timmar att återställa från General-Purpose v2-lagrings skiktet (valvet). I de fall där data är tillgängliga i generell användning v1-lagring rekommenderar vi att du använder funktionen för [omedelbar återställning för snabbare återställningar](backup-instant-restore-capability.md) . (Om data måste återställas från ett valv, tar det längre tid.)
- Gränsen för antalet diskar per lagrings konto är i förhållande till hur mycket diskarna som används av program som körs på en virtuell IaaS-dator (Infrastructure as a Service). Som allmän praxis bör du, om det finns 5 till 10 diskar eller mer på ett enda lagrings konto, utjämna belastningen genom att flytta några diskar till separata lagrings konton.

## <a name="backup-costs"></a>Kostnader för säkerhets kopiering

Virtuella Azure-datorer som har säkerhetskopierats med Azure Backup omfattas [Azure Backup prissättning](https://azure.microsoft.com/pricing/details/backup/).

Faktureringen startar inte förrän den första slutförda säkerhets kopieringen har slutförts. I det här läget börjar faktureringen för både lagring och skyddade virtuella datorer. Faktureringen fortsätter så länge som alla säkerhets kopierings data för den virtuella datorn lagras i ett valv. Om du slutar skydda en virtuell dator, men säkerhets kopierings data för den virtuella datorn finns i ett valv, fortsätter faktureringen.

Faktureringen för en angiven virtuell dator stoppar endast om skyddet har stoppats och alla säkerhetskopierade data har tagits bort. När skyddet stoppas och det inte finns några aktiva säkerhets kopierings jobb blir storleken på den senaste virtuella säkerhets kopian den skyddade instans storleken som används för den månatliga fakturan.

Storleken på den skyddade instans storleken baseras på den *faktiska* storleken på den virtuella datorn. Storleken på den virtuella datorn är summan av alla data på den virtuella datorn, förutom den tillfälliga lagringen. Prissättningen baseras på faktiska data som lagras på data diskarna, inte på den högsta storlek som stöds för varje datadisk som är ansluten till den virtuella datorn.

På samma sätt baseras reserv lagrings fakturan på den mängd data som lagras i Azure Backup, vilket är summan av faktiska data i varje återställnings punkt.

Ta till exempel en a2-standard virtuell dator som har två ytterligare data diskar med en maximal storlek på 4 TB. I följande tabell visas de faktiska data som lagras på var och en av dessa diskar:

**Disk** | **Max storlek** | **Faktiska data finns**
--- | --- | ---
OS-disk | 4 095 GB | 17 GB
Lokal/tillfällig disk | 135 GB | 5 GB (ingår inte i säkerhets kopian)
Datadisk 1 | 4 095 GB | 30 GB
Data disk 2 | 4 095 GB | 0 GB

Den faktiska storleken på den virtuella datorn i det här fallet är 17 GB + 30 GB + 0 GB = 47 GB. Den här skyddade instans storleken (47 GB) utgör grunden för månads fakturan. När mängden data i den virtuella datorn växer ökar den skyddade instans storleken som används för fakturerings ändringar som ska matchas.

## <a name="next-steps"></a>Nästa steg

[Förbered dig nu för säkerhets kopiering av virtuella Azure-datorer](backup-azure-arm-vms-prepare.md).
