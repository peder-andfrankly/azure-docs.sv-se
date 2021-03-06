---
title: Migrera data till Azure File Sync med Azure Data Box och andra metoder
description: Migrera Mass data på ett sätt som är kompatibelt med Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9264aa6d24256b991abefe35b41045caa2e76d67
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997786"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Migrera Mass data till Azure File Sync
Du kan migrera Mass data till Azure File Sync på två sätt:

* **Ladda upp filer med hjälp av Azure File Sync.** Detta är den enklaste metoden. Flytta dina filer lokalt till Windows Server 2012 R2 eller senare och installera Azure File Sync-agenten. När du har konfigurerat synkroniseringen kommer dina filer att överföras från servern. (Våra kunder upplever för närvarande en genomsnittlig uppladdnings hastighet på 1 TiB varannan dag.) För att säkerställa att servern inte använder för mycket bandbredd för ditt data Center, kanske du vill konfigurera ett [schema för bandbredds begränsning](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Överför dina filer offline.** Om du inte har tillräckligt med bandbredd kanske du inte kan ladda upp filer till Azure på en rimlig tids period. Utmaningen är den första synkroniseringen av hela uppsättningen filer. För att undvika den här utmaningen använder du Migreringsverktyg för flera offline-verktyg som [Azure Data Box-serien](https://azure.microsoft.com/services/storage/databox). 

Den här artikeln förklarar hur du migrerar filer offline på ett sätt som är kompatibelt med Azure File Sync. Följ dessa anvisningar för att undvika fil konflikter och bevara åtkomst kontrol listorna för filer och mappar (ACL: er) och tidsstämplar när du har aktiverat synkroniseringen.

## <a name="migration-tools"></a>Migreringsverktyg
Processen som vi beskriver i den här artikeln fungerar inte bara för Data Box-enhet utan även för andra verktyg för offline-migrering. Det fungerar också med verktyg som AzCopy, Robocopy, eller partner verktyg och tjänster som fungerar direkt via Internet. Men för att lösa den inledande uppladdnings utmaningen följer du stegen i den här artikeln för att använda dessa verktyg på ett sätt som är kompatibelt med Azure File Sync.

I vissa fall måste du flytta från en Windows-Server till en annan Windows-Server innan du börjar använda Azure File Sync. [Tjänsten för lagringsmigrering](https://aka.ms/storagemigrationservice) (SMS) kan hjälpa dig med det. Oavsett om du behöver migrera till en server-OS-version som stöds av Azure File Sync (Windows Server-2012R2 och uppåt) eller om du bara behöver migrera eftersom du köper ett nytt system för Azure File Sync, har SMS många funktioner och fördelar som hjälper dig att få din migr ållsplats sker smidigt.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Fördelar med att använda ett verktyg för att överföra data offline
Här är de främsta fördelarna med att använda ett överförings verktyg som Data Box-enhet för migrering offline:

- Du behöver inte ladda upp alla filer över nätverket. För stora namn rymder kan det här verktyget spara betydande nätverks bandbredd och-tid.
- När du använder Azure File Sync oavsett vilket överförings verktyg du använder (Data Box-enhet, Azure import/export-tjänsten och så vidare) laddar din Live-server bara de filer som ändras när du flyttar data till Azure.
- Azure File Sync synkroniserar dina filer och mappar med ACL: er även om verktyget för offline-Migreringsverktyg inte transport-ACL: er.
- Data Box-enhet och Azure File Sync kräver ingen stillestånds tid. När du använder Data Box-enhet för att överföra data till Azure kan du använda nätverks bandbredden effektivt och bevara filens åter givning. Du håller också ditt namn område uppdaterat genom att bara ladda upp de filer som ändras när du flyttar data till Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Krav för data överföring offline
Du bör inte aktivera synkronisering på den server som du migrerar innan du slutför data överföringen offline. Andra saker att tänka på innan du börjar är följande:

- Om du planerar att använda Data Box-enhet för din grupp migrering: Granska [distributions kraven för data Box-enhet](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planera din slutgiltiga Azure File Sync topologi: [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- Välj Azure Storage-konton som ska innehålla de fil resurser som du vill synkronisera med. Se till att din Mass migrering sker för tillfälliga mellanlagrings resurser i samma lagrings konto (n). Mass migrering kan bara aktive ras med en slutgiltig-och en mellanlagrings resurs som finns i samma lagrings konto.
- En Mass migrering kan endast användas när du skapar en ny synkroniseringsrelation med en server plats. Du kan inte aktivera en Mass migrering med en befintlig synkroniseringsrelation.


## <a name="process-for-offline-data-transfer"></a>Process för data överföring offline
Så här konfigurerar du Azure File Sync på ett sätt som är kompatibelt med verktyg för Mass migrering, till exempel Azure Data Box:

![Diagram som visar hur du konfigurerar Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Steg | Detalj |
|---|---------------------------------------------------------------------------------------|
| ![Steg 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Beställ dina data Box-enhet](../../databox/data-box-deploy-ordered.md). Data Box-enhets familjen erbjuder [flera produkter](https://azure.microsoft.com/services/storage/databox/data) som passar dina behov. När du får data Box-enhet ska du följa [dokumentationen för att kopiera data](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) till den här UNC-sökvägen på data Box-enhet:  *\\< DeviceIPAddres\>StorageAccountName_AzFile\> \<\< Resurs\>namn*. Här är *resurs* namn namnet på den mellanlagrings resursen. Skicka Data Box-enhet tillbaka till Azure. |
| ![Steg 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Vänta tills filerna visas i de Azure-filresurser som du valde som tillfälliga mellanlagrings resurser. *Aktivera inte synkronisering till dessa resurser.* |
| ![Steg 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Skapa en ny tom resurs för varje fil resurs som Data Box-enhet skapats åt dig. Den här nya resursen ska finnas i samma lagrings konto som Data Box-enhet resursen. [Så här skapar du en ny Azure](storage-how-to-create-file-share.md)-filresurs. |
| ![Steg 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Skapa en Sync-grupp](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) i en tjänst för synkronisering av lagring. Referera till den tomma resursen som en moln slut punkt. Upprepa det här steget för alla Data Box-enhet fil resurser. [Konfigurera Azure File Sync](storage-sync-files-deployment-guide.md). |
| ![Steg 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Lägg till din Live Server-katalog som en server slut punkt](storage-sync-files-deployment-guide.md#create-a-server-endpoint). I processen anger du att du har flyttat filerna till Azure och hänvisar till mellanlagrings resurserna. Du kan aktivera eller inaktivera moln nivåer efter behov. När du skapar en server slut punkt på din Live-Server refererar du till mellanlagrings resursen. På bladet **Lägg till Server slut punkt** , under **offline-dataöverföring**, väljer du **aktive rad**och väljer sedan den mellanlagrings resurs som måste finnas i samma lagrings konto som moln slut punkten. Här filtreras listan över tillgängliga resurser efter lagrings konto och resurser som inte redan synkroniseras. |

![Skärm bild av Azure Portal användar gränssnitt, som visar hur du aktiverar data överföring offline när du skapar en ny server slut punkt](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synkronisera resursen
När du har skapat din server slut punkt startar synkroniseringen. Sync-processen avgör om varje fil på servern också finns i den fristående resurs där Data Box-enhet deponerade filerna. Om filen finns där kopierar Sync-processen filen från mellanlagrings resursen i stället för att ladda upp den från servern. Om filen inte finns i den fristående resursen, eller om det finns en nyare version på den lokala servern, laddar Sync-processen upp filen från den lokala servern.

> [!IMPORTANT]
> Du kan bara aktivera läget för Mass migrering när du skapar en server slut punkt. När du har upprättat en server slut punkt kan du inte integrera Mass migrerade data från en redan synkroniserad server i namn området.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL: er och tidsstämplar på filer och mappar
Azure File Sync säkerställer att ACL: er för filer och mappar synkroniseras från Live-servern även om verktyget för Mass migrering som du använde inte ursprungligen transport-ACL: er. Därför behöver inte mellanlagrings resursen innehålla några ACL: er för filer och mappar. När du aktiverar funktionen offline-datamigrering när du skapar en ny server slut punkt synkroniseras alla ACL: er för filer på servern. Nyligen skapade och ändrade tidsstämplar synkroniseras också.

## <a name="shape-of-the-namespace"></a>Namn områdets form
När du aktiverar synkroniseringen bestämmer serverns innehåll formen för namn området. Om filerna tas bort från den lokala servern efter att Data Box-enhet ögonblicks bilden och migreringen är slutförd, flyttas dessa filer inte till Live och synkronisering av namn området. De finns kvar i mellanlagrings resursen, men de kopieras inte. Detta är nödvändigt eftersom synkroniseringen behåller namn området enligt Live-servern. Data Box-enhet *ögonblicks bilden* är bara ett mellanlagrings underlag för effektiv fil kopiering. Det är inte auktoritet för formen på det aktiva namn området.

## <a name="cleaning-up-after-bulk-migration"></a>Rensa efter Mass migrering 
När servern har slutfört den första synkroniseringen av namn rummet använder Data Box-enhet Mass-migrerade filer den mellanlagrings fil resursen. På bladet **Server slut punkt egenskaper** i Azure Portal i avsnittet **offline-dataöverföring** ändras statusen från pågår till slutförd. 

![Skärm bild av bladet Server slut punkts egenskaper där status och inaktivera kontroller för data överföring offline finns](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Nu kan du rensa mellanlagrings resursen för att spara kostnader:

1. På bladet **Egenskaper för Server slut punkt** när statusen är **slutförd**väljer du **inaktivera offline**-dataöverföring.
2. Överväg att ta bort mellanlagrings resursen för att spara kostnader. Mellanlagrings resursen innehåller antagligen inte ACL: er för filer och mappar, så det är inte särskilt användbart. För säkerhets kopiering av tidpunkter för säkerhets kopiering skapar du en riktig [ögonblicks bild av synkroniseringen av Azure](storage-snapshots-files.md)-filresursen. Du kan [konfigurera Azure Backup att ta ögonblicks bilder]( ../../backup/backup-azure-files.md) enligt ett schema.

Inaktivera data överförings läget offline endast när statusen är **slutförd** eller när du vill avbryta på grund av en felaktig konfiguration. Om du inaktiverar läget under en distribution kommer filerna att börja överföras från servern även om din mellanlagrings resurs fortfarande är tillgänglig.

> [!IMPORTANT]
> När du har inaktiverat data överförings läget offline kan du inte aktivera det igen, även om den tillfälliga delningen från Mass migreringen fortfarande är tillgänglig.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
