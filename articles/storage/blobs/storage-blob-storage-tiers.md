---
title: Frekventa, låg frekventa och Arkiv åtkomst nivåer för blobbar – Azure Storage
description: Åtkomst nivåer för frekvent åtkomst, låg frekvent åtkomst och arkivering för Azure Storage-konton.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 4593ee875f98e2c9f2f9406f8b9d4146e06a573d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825453"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv lag rings nivåer

Azure Storage erbjuder olika åtkomst nivåer, vilket gör att du kan lagra BLOB-Datadata på det mest kostnads effektiva sättet. Tillgängliga åtkomst nivåer är:

- **Snabb** – optimerad för att lagra data som används ofta.
- **Cool** -optimerad för att lagra data som inte används ofta och som lagras i minst 30 dagar.
- **Arkiv** -optimerad för att lagra data som sällan används och lagras i minst 180 dagar med flexibla latens krav (i antal timmar).

Följande överväganden gäller för olika åtkomst nivåer:

- Endast frekventa och frekventa åtkomst nivåer kan ställas in på konto nivå. Arkiv åtkomst nivån är inte tillgänglig på konto nivå.
- Nivåerna frekvent, låg frekvent och Arkiv lag rings nivå kan anges på blobnivå.
- Data i låg frekvent åtkomst nivå kan tolerera något lägre tillgänglighet, men kräver fortfarande hög hållbarhet, hämtnings tid och data flödes egenskaper som liknar frekventa data. För låg frekvent data är ett något lägre tillgänglighets avtal på service nivå (SLA) och högre åtkomst kostnader jämfört med frekventa data acceptabla för lägre lagrings kostnader.
- Arkiv lag ring lagrar data offline och erbjuder de lägsta lagrings kostnaderna, men även de högsta data rehydratiseras-och åtkomst kostnaderna.

Data som lagras i molnet växer i exponentiell takt. Om du vill hålla kontroll på och optimera kostnaderna för dina växande lagringsbehov är det en bra idé att ordna data baserat på attribut som åtkomstfrekvens och planerad kvarhållningsperiod. Data som lagras i molnet kan vara olika beroende på hur de genereras, bearbetas och används under dess livs längd. Vissa data används aktivt och ändras under livslängden. Vissa data används ofta i början av livslängden och sedan minskar användning drastiskt när dessa data blir äldre. Vissa data är inaktiva i molnet och är sällan, om de någonsin används, efter att de har lagrats.

Var och en av dessa data åtkomst scenarier fördelar från en annan åtkomst nivå som är optimerad för ett visst åtkomst mönster. Med nivåerna frekvent åtkomst, låg frekvent åtkomst och Arkiv lag rings nivå kan Azure Blob-lagring hantera det här behovet av differentierade åtkomst nivåer med separata pris modeller.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Lagringskonton med stöd för flera lagringsnivåer

Objekt lagrings data skiktning mellan frekvent, låg frekvent och arkivering stöds bara i Blob Storage-och Generell användning v2-konton (GPv2). Generell användning v1-konton (GPv1) stöder inte nivåer. Kunder kan enkelt konvertera sina befintliga GPv1-eller Blob Storage-konton till GPv2-konton via Azure Portal. GPv2 tillhandahåller nya priser och funktioner för blobbar, filer och köer. Vissa funktioner och priser är bara tillgängliga i GPv2-konton. Utvärdera användningen av GPv2-konton efter en omfattande omvisning av priserna. Vissa arbets belastningar kan vara dyrare på GPv2 än GPv1. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

Blob Storage-och GPv2-konton exponerar attributet **åtkomst nivå** på konto nivå. Med det här attributet kan du ange standard åtkomst nivå för alla blobar som inte har angetts explicit på objekt nivå. För objekt med nivån angiven på objekt nivå gäller inte konto nivån. Arkiv nivån kan endast tillämpas på objekt nivå. Du kan när som helst växla mellan dessa åtkomst nivåer.

## <a name="hot-access-tier"></a>Frekvent åtkomstnivå

Frekvent åtkomst nivå har högre lagrings kostnader än låg frekvent lagring och Arkiv lag ring, men de lägsta åtkomst kostnaderna. Exempel på användnings scenarier för frekvent åtkomst nivå är:

- Data som används i aktivt läge eller förväntas nås (läses från och skrivs till) ofta.
- Data som mellanlagras för bearbetning och eventuell migrering till låg frekvent åtkomst nivå.

## <a name="cool-access-tier"></a>Lågfrekvent åtkomstnivå

Låg frekvent åtkomst nivå har lägre kostnader för lagring och högre åtkomst kostnader jämfört med frekvent lagring. Den här nivån är avsedd för data som är kvar på den lågfrekventa nivån i minst 30 dagar. Exempel på användnings scenarier för låg frekvent åtkomst nivå är:

- Datauppsättningar för kortsiktig säkerhetskopiering och haveriberedskap.
- Äldre medieinnehåll som inte visas så ofta längre, men som förväntas vara tillgängligt direkt vid behov.
- Stora datamängder som behöver en kostnadseffektiv lagring under tiden mer data samlas in för framtida bearbetning. (*Till exempel* långsiktig lagring av vetenskapliga data eller telemetridata (rådata) från en tillverkningsanläggning.)

## <a name="archive-access-tier"></a>Arkivåtkomstnivå

Arkiv åtkomst nivån har lägst lagrings kostnad. Men den har högre kostnader för data hämtning jämfört med frekventa och låg frekventa nivåer. Det kan ta flera timmar att hämta data på Arkiv nivån. Data måste finnas kvar på Arkiv nivå i minst 180 dagar eller omfattas av en avgift för tidig borttagning.

När en BLOB finns i Arkiv lag ring är BLOB-data offline och kan inte läsas, kopieras, skrivas över eller ändras. Du kan inte ta ögonblicks bilder av en BLOB i Arkiv lag ring. BLOB-metadata är dock online och tillgängliga, så att du kan lista bloben och dess egenskaper. För blobbar i arkivet är de enda giltiga åtgärderna GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier och DeleteBlob.

Exempel på användnings scenarier för Arkiv åtkomst nivån är:

- Långsiktig säkerhetskopiering, sekundär säkerhetskopiering och datauppsättningar för arkivering
- Ursprungliga rådata som måste bevaras, även efter att de har bearbetats till ett slutligt användbart format.
- Efterlevnads- och arkiveringsdata som behöver lagras under en längre tid och som nästan aldrig används.

## <a name="account-level-tiering"></a>Skiktning på konto nivå

Blobbar i alla tre åtkomst nivåer kan finnas i samma konto. Alla blobar som inte har en uttryckligen tilldelad nivå härleder nivån från kontots åtkomst nivå inställning. Om åtkomst nivån kommer från kontot ser du att egenskapen **åtkomst nivå härledd** BLOB har angetts till true och att **åtkomst nivåns** BLOB-egenskap matchar konto nivån. I Azure Portal visas egenskapen _härledd åtkomst nivå_ med BLOB-åtkomst nivån som **aktiv (härledd)** eller låg frekvent **(härledd)** .

Att ändra kontots åtkomst nivå gäller för alla _härledda objekt i åtkomst nivån_ som lagras i kontot som inte har någon explicit nivå uppsättning. Om du växlar konto nivån från frekvent till låg frekvent kommer du att debiteras för Skriv åtgärder (per 10 000) för alla blobbar utan en uppsättnings nivå i GPv2-konton. Det kostar inget att ändra i Blob Storage-konton. Du debiteras för både Läs åtgärder (per 10 000) och data hämtning (per GB) om du växlar från låg frekvent till frekvent i Blob Storage-eller GPv2-konton.

## <a name="blob-level-tiering"></a>Blobnivåindelning

Med blobnivåindelning kan du ändra nivå för dina data på objektnivå med hjälp av en enda åtgärd som kallas [Ange blobnivå](/rest/api/storageservices/set-blob-tier). Du kan enkelt ändra åtkomstnivå för en blob mellan frekvent, lågfrekvent eller arkivnivå när användningsmönster ändras, utan att behöva flytta data mellan konton. Alla nivå ändringar sker omedelbart. Det kan dock ta flera timmar att återuppväcks en BLOB från arkivet.

Tiden för den senaste ändringen på blobnivån är tillgänglig via blobegenskapen **Ändringstid för åtkomstnivå**. Om en BLOB finns på Arkiv nivån kan den inte skrivas över, så det är inte tillåtet att ladda upp samma BLOB i det här scenariot. När en BLOB skrivs över på frekvent eller låg frekvent nivå ärver den nya bloben den nivå i blobben som skrevs av, såvida inte den nya BLOB-åtkomst nivån uttryckligen anges när den skapas.

> [!NOTE]
> Arkivlagring och blobnivåindelning stöder endast blockblobar. Du kan inte heller ändra nivån på en Block-Blob som har ögonblicks bilder.

### <a name="blob-lifecycle-management"></a>Hantering av BLOB-livscykel

Blob Storage livs cykel hantering ger en omfattande, regelbaserade princip som du kan använda för att överföra data till bästa åtkomst nivå och för att förfalla data i slutet av livs cykeln. Mer information finns i [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md) .  

> [!NOTE]
> Data som lagras i ett Block Blob Storage-konto (Premium prestanda) kan för närvarande inte på nivå till frekvent, låg frekvent eller arkiveras med hjälp av [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier) eller använda Azure Blob Storage livs cykel hantering.
> Om du vill flytta data måste du synkront kopiera blobar från Block Blob Storage-kontot till frekvent åtkomst nivå i ett annat konto med hjälp av [undantag från URL-API](/rest/api/storageservices/put-block-from-url) eller en version av AzCopy som stöder detta API.
> *Placera block från URL* -API: n kopierar synkront data på servern, vilket innebär att anropet slutförs endast när alla data har flyttats från den ursprungliga Server platsen till mål platsen.

### <a name="blob-level-tiering-billing"></a>Fakturering för blobnivåindelning

När en BLOB flyttas till en låg frekvent nivå (frekvent > låg frekvent lagring, > Arkiv lag ring eller cool-> Arkiv) faktureras åtgärden som en Skriv åtgärd till mål nivån, där avgifter för Skriv åtgärden (per 10 000) och data skrivning (per GB) för mål nivån tillämpas.

När en BLOB flyttas till en varm nivå (Arkiv-> låg frekvent, Arkiv-> frekvent eller låg frekvent > frekvent) debiteras åtgärden som en läsning från käll nivån, där avgifterna Läs åtgärd (per 10 000) och data hämtning (per GB) för käll nivån tillämpas. Kostnader för tidig borttagning av blobbar från lågfrekvent lagring eller arkivlagring kan också tillkomma. I följande tabell sammanfattas hur nivå ändringar faktureras.

| | **Skriv avgifter (åtgärd + åtkomst)** | **Läs avgifter (åtgärd + åtkomst)**
| ---- | ----- | ----- |
| **SetBlobTier riktning** | varm-> sval,<br> Hot-> Arkiv,<br> kall > Arkiv | Arkiv-> häftigt,<br> Arkiv-> varmt,<br> hett > frekvent

### <a name="cool-and-archive-early-deletion"></a>Tidig borttagning i lågfrekvent lagring och i arkivlagring

Alla blobar som flyttas till den låg frekventa nivån (endast GPv2-konton) omfattas av en låg tidig borttagnings period på 30 dagar. En blob som flyttas till Arkiv nivån omfattas av en tidig borttagnings period på 180 dagar. Den här kostnaden beräknas proportionellt. Om en blob till exempel flyttas till arkivet och sedan tas bort eller flyttas till den frekventa nivån efter 45 dagar, kommer du att debiteras en avgift för tidig borttagning som motsvarar 135 (180 minus 45) dagar för att lagra bloben i arkivet.

Du kan beräkna tidig borttagningen med hjälp av BLOB-egenskapen, **senast ändrad**, om det inte har gjorts några ändringar i åtkomst nivån. Annars kan du använda när åtkomst nivån senast ändrades till låg frekvent eller arkivera genom att visa egenskapen BLOB: **Access-Tier-Change-Time**. Mer information om BLOB-egenskaper finns i [Hämta BLOB-egenskaper](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Jämför lagrings alternativ för Block Blob

I följande tabell visas en jämförelse av Premium Performance Block Blob Storage och åtkomst nivåerna frekvent, låg frekvent och Arkiv lag ring.

|                                           | **Förstklassig prestanda**   | **Frekvent nivå** | **Låg frekvent nivå**       | **Arkiv lag ring**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Tillgänglighet**                          | 99,9 %                     | 99,9 %        | 99 %                 | Anslutningen           |
| **Tillgänglighet** <br> **(RA-GRS-läsningar)**  | Saknas                       | 99,99 %       | 99,9 %               | Anslutningen           |
| **Avgifter för användning**                         | Högre kostnader för lagring, lägre åtkomst och kostnad för transaktioner | Högre kostnader för lagring, lägre åtkomst och transaktionskostnader | Lägre kostnader för lagring, högre åtkomst och transaktionskostnader | Lägsta kostnader för lagring, högsta åtkomst och transaktionskostnader |
| **Minsta objektstorlek**                   | Saknas                       | Saknas          | Saknas                 | Saknas               |
| **Minsta lagringstid**              | Saknas                       | Saknas          | 30 dagar<sup>1</sup> | 180 dagar
| **Svarstid** <br> **(Tid till första byte)** | Ensiffriga millisekunder | millisekunder | millisekunder        | timmar<sup>2</sup> |

<sup>1</sup> objekt på den lägsta nivån i GPv2-konton har en minsta Retentions tid på 30 dagar. Blob Storage-konton har inte minsta Retentions tid för den låg frekventa nivån.

<sup>2</sup> arkivlagring för närvarande stöd för 2 rehydratiseras-prioriteringar, hög och standard, som erbjuder olika hämtnings fördröjningar. Mer information finns i [rehydratisera BLOB-data från Arkiv](storage-blob-rehydration.md)lag rings nivån.

> [!NOTE]
> Blob Storage-konton har stöd för samma prestanda-och skalbarhets mål som lagrings konton för generell användning v2. Mer information finns i [Azure Storage skalbarhets-och prestanda mål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

I det här avsnittet visas följande scenarier på Azure Portal:

- Ändra standardåtkomstnivån för ett GPv2- eller Blob Storage-konto.
- Ändra åtkomstnivån för en blob i ett GPv2- eller Blob Storage-konto.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Ändra standardåtkomstnivå för ett GPv2- eller Blob Storage-konto

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Gå till ditt lagringskonto genom att välja Alla resurser och välj sedan ditt lagringskonto.

1. I inställningar klickar du på **konfiguration** för att visa och ändra konto konfigurationen.

1. Välj rätt åtkomst nivå för dina behov: ange **åtkomst nivå** till antingen **cool** eller **hett**.

1. Klicka på **Spara** högst upp.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Ändra nivån för en BLOB i ett GPv2-eller Blob Storage-konto

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Gå till din blob i lagringskontot genom att välja Alla resurser, ditt lagringskonto, din container och sedan välja din blob.

1. I **BLOB-egenskaperna**väljer du **ändra nivå**.

1. Välj åtkomst nivån frekvent **, låg**frekvent eller **Arkiv** . Om din BLOB för närvarande finns i arkivet och du vill rehydratisera till en onlinenivå, kan du också välja en rehydratiserad prioritet för **standard** eller **hög**.

1. Välj **Spara** längst ned.

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla lagrings konton använder en pris modell för Block-Blob-lagring baserat på nivån för varje blob. Tänk på följande saker om fakturering:

- **Lagringskostnader**: Utöver mängden data som lagras varierar lagringskostnaden beroende på åtkomstnivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.
- **Kostnader för dataåtkomst**: Kostnaderna för dataåtkomst ökar när nivån blir mer lågfrekvent. För data i låg frekvent och Arkiv lag rings nivå debiteras du en åtkomst avgift per Gigabyte för läsningar.
- **Transaktionskostnader**: det finns en avgift per transaktion för alla nivåer som ökar när nivån blir kylare.
- **Dataöverföringskostnader för geo-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.
- **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.
- Om **du ändrar åtkomst nivån**: om du ändrar kontots åtkomst nivå ändras kostnader för nivå ändringar för _åtkomst nivå härledda_ blobbar lagrade i kontot som inte har en explicit nivå uppsättning. Information om hur du ändrar åtkomst nivå för en enskild BLOB finns på [nivå fakturering på BLOB-nivå](#blob-level-tiering-billing).

> [!NOTE]
> Mer information om priser för block blobbar finns [Azure Storage prissättnings](https://azure.microsoft.com/pricing/details/storage/blobs/) sida. Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Bör jag använda Blob Storage- eller GPv2-konton om jag vill lagra data i olika nivåer?**

Vi rekommenderar att du använder GPv2 i stället för Blob Storage-konton om du vill använda lagringsnivåer. GPv2 har stöd för samma funktioner som Blob Storage-konton, och mycket mer. Kostnaden för Blob Storage- och GPv2-konton är i stort sett densamma, men vissa nya funktioner och rabatter kommer bara att vara tillgängliga för GPv2-konton. GPv1-konton stöder inte nivåer.

Prisstrukturen för GPv1- och GPv2-konton skiljer sig åt, så kunderna bör noggrant utvärdera båda alternativen innan de bestämmer sig för att använda GPv2-konton. Du kan enkelt konvertera ett befintligt Blob Storage eller GPv1-konto till GPv2 via en enklicksprocess. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

**Kan jag lagra objekt i alla tre (frekventa, svala och arkiverade) åtkomst nivåer i samma konto?**

Ja. Attributet **åtkomst nivå** som anges på konto nivå är standard konto nivån som gäller för alla objekt i kontot utan en explicit uppsättnings nivå. Med skiktning på blobnivå kan du ange åtkomst nivå på objekt nivå, oavsett vad åtkomst nivå inställningen för kontot är. Blobbar i någon av de tre åtkomst nivåerna (frekvent, låg frekvent eller Arkiv) kan finnas i samma konto.

**Kan jag ändra standard åtkomst nivån för mitt blob-eller GPv2-lagrings konto?**

Ja, du kan ändra standard konto nivån genom att ange attributet **åtkomst nivå** för lagrings kontot. Ändring av konto nivån gäller för alla objekt som lagras i kontot som inte har en explicit nivå uppsättning (till exempel frekvent **(härledd)** eller låg frekvent **(härledd)** ). När konto nivån växlas från frekvent till låg frekvent tillkommer Skriv åtgärder (per 10 000) för alla blobbar utan en uppsättnings nivå i GPv2-konton, och växling från låg frekvent till frekvent medför både Läs åtgärder (per 10 000) och data hämtning (per GB) avgifter för alla blobar i Blob Storage och GPv2-konton.

**Kan jag ange arkivlagring som standardnivå för mitt konto?**

Nej. Endast frekventa och frekventa åtkomst nivåer kan anges som standard åtkomst nivå för kontot. Arkivlagringsnivån kan endast anges på objektnivå. Vid BLOB-uppladdning anger du den åtkomst nivå som du vill använda som frekvent, låg frekvent eller Arkiverad oavsett vilken konto nivå som är standard. Med den här funktionen kan du skriva data direkt till Arkiv nivån för att realisera kostnads besparingar från det ögonblick då du skapar data i Blob Storage.

**I vilka regioner är åtkomst nivåerna frekvent åtkomst, låg frekvent åtkomst och arkivering tillgängliga i?**

Nivåerna frekvent och låg frekvent åtkomst tillsammans med skiktning på BLOB-nivå är tillgängliga i alla regioner. Arkivlagring är inledningsvis endast tillgängligt i vissa regioner. En fullständig lista finns under [Tillgängliga Azure-produkter efter region](https://azure.microsoft.com/regions/services/).

**Beter sig Blobbarna på den frekventa åtkomst nivån annorlunda än de i frekvent åtkomst nivå?**

Blobbar i frekvent åtkomst nivå har samma svars tid som blobbar i GPv1-, GPv2-och Blob Storage-konton. Blobbar i låg frekvent åtkomst nivå har liknande svars tid (i millisekunder) som blobbar i GPv1-, GPv2-och Blob Storage-konton. Blobbar i Arkiv åtkomst nivån har flera timmars svars tid i GPv1-, GPv2-och Blob Storage-konton.

Blobbar i låg frekvent åtkomst nivå har något lägre tillgänglighets nivå (SLA) än blobbar som lagras i frekvent åtkomst nivå. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Kan samma åtgärder användas för lagringsnivåerna frekvent, lågfrekvent och arkivlagring?**

På lagringsnivåerna frekvent och lågfrekvent fungerar alla åtgärder på samma sätt. Alla giltiga Arkiv åtgärder inklusive GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier och DeleteBlob är 100% konsekventa med frekvent och låg frekvent. BLOB-data kan inte läsas eller ändras på Arkiv nivå förrän de har rehydratiserats; endast Läs åtgärder för BLOB-metadata stöds i arkivet.

**När jag återställer en blob från arkivlagring till frekvent eller lågfrekvent lagring, hur vet jag när processen är klar?**

Under ÅTERUPPVÄCKNING kan du använda åtgärden Hämta BLOB-egenskaper för att avsöka attributet **arkivera status** och bekräfta när nivå ändringen har slutförts. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas blobegenskapen Arkivstatus bort och blobegenskapen **Åtkomstnivå** anger den nya nivån: frekvent eller lågfrekvent.  

**När jag har angett nivå för en blob: hur lång tid tar det innan jag börjar debiteras för den nya nivån?**

Varje Blob faktureras alltid enligt den nivå som anges av blobbets egenskap för **åtkomst nivå** . När du anger en ny nivå för en BLOB, Visar egenskapen **åtkomst nivå** omedelbart den nya nivån för alla över gångar. Men att återställa en blob från arkivnivån till en frekvent eller lågfrekvent nivå kan ta flera timmar. I det här fallet debiteras du enligt Arkiv taxa tills ÅTERUPPVÄCKNING är slutförd, där egenskapen **åtkomst nivå** visar den nya nivån. Vid den tidpunkten debiteras du för denna BLOB vid frekvent eller låg frekvent hastighet.

**Hur gör jag för att avgöra om jag får en avgift för tidig borttagning när en BLOB tas bort eller flyttas från låg frekvent lagring eller Arkiv lag ring?**

Om en blob tas bort eller flyttas från lågfrekvent lagring (endast GPv2-konton) inom 30 dagar, eller från arkivlagring inom 180 dagar, påförs en proportionellt beräknad avgift för tidig borttagning. Du kan bestämma hur länge en BLOB har varit i låg frekvent lagrings nivå eller Arkiv lag ring genom att kontrol lera egenskapen för **ändrings tid för åtkomst nivå** , vilket ger en stämpel för den senaste nivå ändringen. Om blobens nivå aldrig har ändrats kan du kontrol lera den **senast ändrade** BLOB-egenskapen. Mer information finns i [kyl och arkivering tidig borttagning](#cool-and-archive-early-deletion).

**Vilka Azure-verktyg och SDK:er stöder blobnivåindelning och arkivlagring?**

Azure Portal, PowerShell, CLI-verktyg och klientbiblioteken för .NET, Java, Python och Node.js har stöd för blobnivåindelning och arkivlagring.  

**Hur mycket data kan jag lagra på lagringsnivåerna frekvent, lågfrekvent och arkiv?**

Data lagring tillsammans med andra gränser anges på konto nivå och inte per åtkomst nivå. Du kan välja att använda hela gränsen på en nivå eller på alla tre nivåer. Mer information finns i [Azure Storage skalbarhets-och prestanda mål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Utvärdera frekvent, låg frekvent och Arkiv lag ring i GPv2-och Blob Storage-konton

- [Kontrollera tillgängligheten för frekvent/lågfrekvent lagring och arkivlagring efter region](https://azure.microsoft.com/regions/#services)
- [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)
- [Lär dig mer om återuppväcks BLOB-data från Arkiv lag rings nivån](storage-blob-rehydration.md)
- [Ta reda på om Premium prestanda skulle dra nytta av din app](storage-blob-performance-tiers.md)
- [Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md)
- [Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)
- [Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
