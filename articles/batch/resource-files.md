---
title: Skapa och använda resursfiler – Azure Batch | Microsoft Docs
description: Lär dig hur du skapar Azure Batch resursfiler från olika ingångs källor.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 9c55b22d1cb85fb645087cf48b54f9d5ac12d58f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322170"
---
# <a name="creating-and-using-resource-files"></a>Skapa och använda resursfiler

En Azure Batchs aktivitet kräver ofta viss typ av data att bearbeta. Resursfiler är ett sätt att tillhandahålla dessa data till din virtuella batch-dator (VM) via en aktivitet. Alla typer av aktiviteter stöder resursfiler: aktiviteter, start aktiviteter, jobb förberedelse aktiviteter, jobb publicerings aktiviteter osv. Den här artikeln beskriver några vanliga metoder för hur du skapar resursfiler och placerar dem på en virtuell dator.  

Resursfiler är en mekanism för att placera data på en virtuell dator i batch, men typen av data och hur den används är flexibel. Det finns dock några vanliga användnings fall:

1. Etablera vanliga filer på varje virtuell dator med resursfiler på en start uppgift
1. Etablera indata som ska bearbetas av aktiviteter

Vanliga filer kan exempelvis vara filer på en start-aktivitet som används för att installera program som dina aktiviteter kör. Indata kan vara rå image-eller video data eller information som ska bearbetas av batch.

## <a name="types-of-resource-files"></a>Typer av resursfiler

Det finns flera olika alternativ för att generera resursfiler. Skapande processen för resursfiler varierar beroende på var ursprungliga data lagras.

Alternativ för att skapa en resurs fil:

- [URL till lagrings behållare](#storage-container-url): Genererar en resurs fil från valfri lagrings behållare i Azure
- [Namn på lagrings behållare](#storage-container-name): Genererar en resurs fil från namnet på en behållare i ett Azure Storage-konto som är länkat till batch
- [Webb slut punkt](#web-endpoint): Genererar en resurs fil från en giltig HTTP-URL

### <a name="storage-container-url"></a>URL för lagrings behållare

Genom att använda en URL för lagrings behållare betyder det att du kan komma åt filer i alla lagrings behållare i Azure med rätt behörigheter.

I det C# här exemplet har filerna redan överförts till en Azure Storage-behållare som Blob Storage. För att få åtkomst till de data som krävs för att skapa en resurs fil måste vi först få åtkomst till lagrings behållaren.

Skapa en URL för signatur för delad åtkomst (SAS) med rätt behörighet för att komma åt lagrings behållaren. Ange förfallo tid och behörigheter för SAS. I det här fallet har ingen start tid angetts, så SAS blir giltig omedelbart och upphör att gälla två timmar efter det att den har genererats.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> För att få åtkomst till behållare måste du `Read` ha `List` både och-behörigheterna, och med BLOB- `Read` åtkomst behöver du bara behörighet.

När behörigheterna har kon figurer ATS skapar du SAS-token och formaterar SAS-URL: en för åtkomst till lagrings behållaren. Använd den formaterade SAS-URL: en för lagrings behållaren och generera [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)en resurs fil med.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Ett alternativ till att skapa en SAS-URL är att aktivera Anonym, offentlig Läs behörighet till en behållare och dess blobbar i Azure Blob Storage. Genom att göra det kan du bevilja skrivskyddad åtkomst till dessa resurser utan att dela din konto nyckel och utan att kräva en SAS. Offentlig Läs behörighet används vanligt vis för scenarier där du vill att vissa blobbar alltid ska vara tillgängliga för anonym Läs åtkomst. Om det här scenariot passar din lösning kan du läsa artikeln [anonym åtkomst till blobbar](../storage/blobs/storage-manage-access-to-resources.md) för att lära dig mer om att hantera åtkomst till dina BLOB-data.

### <a name="storage-container-name"></a>Namn på lagrings behållare

I stället för att konfigurera och skapa en SAS-URL kan du använda namnet på din Azure Storage-behållare för att få åtkomst till dina BLOB-data. Den lagrings behållare som används måste finnas på det Azure Storage-konto som är länkat till ditt batch-konto, som kallas för kontot för Autolagring. Genom att använda namnet på lagrings behållaren för ett konto för Autolagring kan du kringgå konfigurering och skapa en SAS-URL för att få åtkomst till en lagrings behållare.

I det här exemplet förutsätter vi att de data som ska användas för att skapa en resurs fil redan finns i ett Azure Storage-konto som är kopplat till ditt batch-konto. Om du inte har ett konto för Autolagring, se stegen i [skapa ett batch-konto](batch-account-create-portal.md) för information om hur du skapar och länkar ett konto.

Genom att använda ett länkat lagrings konto behöver du inte skapa och konfigurera en SAS-URL till en lagrings behållare. Ange i stället namnet på lagrings behållaren i det länkade lagrings kontot.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Webb slut punkt

Data som inte har överförts till Azure Storage kan fortfarande användas för att skapa resursfiler. Du kan ange en giltig HTTP-URL som innehåller dina indata. URL: en har angetts till batch-API och sedan används data för att skapa en resurs fil.

I följande C# exempel finns indata på den fiktiva GitHub-slutpunkten. API: et hämtar filen från den giltiga webb slut punkten och genererar en resurs fil som ska användas av din uppgift. Inga autentiseringsuppgifter krävs för det här scenariot.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Användbara tips och råd

Varje Azure Batch aktivitet använder filer på olika sätt, vilket är anledningen till att batch har alternativ som är tillgängliga för att hantera filer på aktiviteter. Följande scenarier är inte avsedda att vara omfattande, utan omfattar i stället några vanliga situationer och ger rekommendationer.

### <a name="many-resource-files"></a>Många resursfiler

Batch-jobbet kan innehålla flera aktiviteter som alla använder samma gemensamma filer. Om vanliga uppgifter delas mellan många aktiviteter, kan det vara ett bättre alternativ att använda ett programpaket som innehåller filerna i stället för att använda resursfiler. Programpaket tillhandahåller optimering för nedladdnings hastighet. Dessutom cachelagras data i programpaketen mellan aktiviteter, så om dina aktivitets filer inte ändras ofta kan det vara bra att använda programpaket för din lösning. Med programpaket behöver du inte hantera flera resursfiler manuellt eller skapa SAS-URL: er för att komma åt filerna i Azure Storage. Batch fungerar i bakgrunden med Azure Storage för att lagra och distribuera programpaket till Compute-noder.

Om varje aktivitet har många filer som är unika för aktiviteten är resursfiler förmodligen det bästa alternativet. Aktiviteter som använder unika filer behöver ofta uppdateras eller ersättas, vilket inte är lika enkelt att göra med innehållet i programpaketet. Resursfiler ger ytterligare flexibilitet för att uppdatera, lägga till eller redigera enskilda filer.

### <a name="number-of-resource-files-per-task"></a>Antal resursfiler per uppgift

Om det finns flera hundra resursfiler angivna för en aktivitet kan batchen avvisa uppgiften för att den är för stor. Det är bäst att hålla dina uppgifter små genom att minimera antalet resursfiler i själva aktiviteten.

Om det inte finns något sätt att minimera antalet filer som aktiviteten behöver, kan du optimera aktiviteten genom att skapa en enda resurs fil som refererar till en lagrings behållare för resursfiler. Om du vill göra det lägger du till dina resursfiler i en Azure Storage behållare och använder de olika "container"-lägena för resursfiler. Använd alternativen för BLOB-prefix för att ange samlingar av filer som ska laddas ned för dina aktiviteter.

## <a name="next-steps"></a>Nästa steg

- Lär dig [](batch-application-packages.md) mer om programpaket som ett alternativ till resursfiler.
- Mer information om hur du använder behållare för resursfiler finns i [container arbets belastningar](batch-docker-container-workloads.md).
- Information om hur du samlar in och sparar utdata från aktiviteter finns i [Spara jobb-och Uppgiftsutdata](batch-task-output.md).
- Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
