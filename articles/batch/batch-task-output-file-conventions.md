---
title: Spara jobb-och uppgifts utdata till Azure Storage med fil konventions biblioteket för .NET-Azure Batch | Microsoft Docs
description: Lär dig hur du använder bibliotek med Azure Batch fil konventioner för .NET för att spara batch-aktivitet och jobb utdata till Azure Storage och se beständiga utdata i Azure Portal.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a2970c46c7cbc978bf6d7491c9258dcccc5404bd
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302674"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Spara jobb-och uppgifts data till Azure Storage med bibliotek för batch-fil konventioner för .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Ett sätt att spara uppgifts data är att använda [biblioteket för Azure batch fil konventioner för .net][nuget_package]. Biblioteket fil konventioner fören klar processen att lagra Uppgiftsutdata för att Azure Storage och hämta den. Du kan använda biblioteket fil konventioner i både aktivitets-och klient kod &mdash; i uppgifts kod för att spara filer, och i klient kod till lista och hämta dem. Din uppgifts kod kan också använda biblioteket för att hämta utdata från överordnade aktiviteter, till exempel i ett scenario med [aktivitets beroenden](batch-task-dependencies.md) .

Om du vill hämta utdatafiler med biblioteket med fil konventioner kan du hitta filerna för ett jobb eller en aktivitet genom att ange dem efter ID och syfte. Du behöver inte känna till namn eller platser för filerna. Du kan till exempel använda biblioteket fil konventioner för att visa en lista över alla mellanliggande filer för en specifik uppgift eller hämta en förhands gransknings fil för ett specifikt jobb.

> [!TIP]
> Från och med version 2017-05-01 stöder batch service-API: et att spara utdata till Azure Storage för aktiviteter och jobb hanterarens aktiviteter som körs på pooler som skapats med konfigurationen av den virtuella datorn. Batch-tjänstens API är ett enkelt sätt att spara utdata från koden som skapar en uppgift och som fungerar som ett alternativ till biblioteket med fil konventioner. Du kan ändra batch-klientprogram för att spara utdata utan att behöva uppdatera programmet som aktiviteten körs på. Mer information finns i [Spara uppgifts data till Azure Storage med batch-tjänst-API: et](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>När ska jag använda biblioteket fil konventioner för att spara Uppgiftsutdata?

Azure Batch innehåller fler än ett sätt att spara Uppgiftsutdata. Fil konventionerna passar bäst i följande scenarier:

- Du kan enkelt ändra koden för programmet som aktiviteten körs för att spara filer med hjälp av biblioteket fil konventioner.
- Du vill strömma data till Azure Storage medan aktiviteten fortfarande körs.
- Du vill bevara data från pooler som skapats med antingen moln tjänst konfigurationen eller konfigurationen för den virtuella datorn.
- Ditt klient program eller andra uppgifter i jobbet måste hitta och hämta utdata för Uppgiftsutdata efter ID eller efter syfte.
- Du vill visa Uppgiftsutdata i Azure Portal.

Om ditt scenario skiljer sig från de som anges ovan kan du behöva överväga en annan metod. Mer information om andra alternativ för att spara Uppgiftsutdata finns i [Spara jobb-och Uppgiftsutdata till Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Vad är standard satserna för sats fils konventioner?

[Standard konventionerna för batch-filen](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) är ett namngivnings schema för de mål behållare och blob-sökvägar som utdatafilerna skrivs till. Filer som är beständiga för Azure Storage som följer fil konventions standarderna är automatiskt tillgängliga för visning i Azure Portal. Portalen är medveten om namngivnings konventionen och kan därför Visa filer som följer den.

Bibliotek för fil konventioner för .NET namnger automatiskt dina lagrings behållare och Uppgiftsutdata enligt fil konventions standarden. Biblioteket fil konventioner innehåller också metoder för att fråga utdatafiler i Azure Storage i enlighet med jobb-ID, aktivitets-ID eller syfte.

Om du utvecklar med ett annat språk än .NET kan du implementera filen Conventions standard i ditt program. Mer information finns i [implementera standard konventionerna för sats fil](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Länka ett Azure Storage konto till ditt batch-konto

Om du vill spara utdata för att Azure Storage med hjälp av biblioteket fil konventioner måste du först länka ett Azure Storage-konto till batch-kontot. Om du inte redan har gjort det kan du länka ett lagrings konto till ditt batch-konto med hjälp av [Azure Portal](https://portal.azure.com):

1. Navigera till ditt Batch-konto i Azure Portal.
1. Under **Inställningar**väljer du **lagrings konto**.
1. Om du inte redan har ett lagrings konto som är kopplat till ditt batch-konto klickar du på **lagrings konto (ingen)** .
1. Välj ett lagrings konto i listan för din prenumeration. För bästa prestanda bör du använda ett Azure Storage konto som finns i samma region som batch-kontot där dina aktiviteter körs.

## <a name="persist-output-data"></a>Spara utdata

Om du vill spara jobb-och uppgifts utmatnings data med biblioteket fil konventioner skapar du en behållare i Azure Storage och sparar sedan utdata i behållaren. Använd [Azure Storage klient bibliotek för .net](https://www.nuget.org/packages/WindowsAzure.Storage) i din uppgifts kod för att överföra Uppgiftsutdata till behållaren.

Mer information om hur du arbetar med behållare och blobbar i Azure Storage finns i [komma igång med Azure Blob Storage med hjälp av .net](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Alla utdata för jobb och uppgifter sparas i biblioteket med fil konventioner, lagras i samma behållare. Om ett stort antal aktiviteter försöker spara filer samtidigt kan Azure Storage begränsnings gränser vara tvingande. Mer information om begränsnings gränser finns i [Check lista för prestanda och skalbarhet för Blob Storage](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Skapa lagringscontainer

Om du vill spara Uppgiftsutdata för att Azure Storage ska du först skapa en behållare genom att anropa [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync][net_prepareoutputasync]. Den här tilläggs metoden tar ett [CloudStorageAccount][net_cloudstorageaccount] -objekt som en parameter. Den skapar en behållare som heter enligt standard fil konventionerna, så att innehållet kan identifieras av Azure Portal och de hämtnings metoder som beskrivs senare i artikeln.

Normalt placerar du koden för att skapa en behållare i klient programmet &mdash; det program som skapar pooler, jobb och uppgifter.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Lagra Uppgiftsutdata

Nu när du har för berett en behållare i Azure Storage kan aktiviteter spara utdata i behållaren med hjälp av klassen [TaskOutputStorage][net_taskoutputstorage] som finns i biblioteket fil konventioner.

I din uppgifts kod måste du först skapa ett [TaskOutputStorage][net_taskoutputstorage] -objekt, sedan när aktiviteten har slutfört sitt arbete, anropa [TaskOutputStorage][net_taskoutputstorage]. [SaveAsync][net_saveasync] -Metod för att spara utdata till Azure Storage.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

Parametern `kind` för [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage). Metoden [SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) kategoriserar de sparade filerna. Det finns fyra fördefinierade [TaskOutputKind][net_taskoutputkind] -typer: `TaskOutput`, `TaskPreview`, `TaskLog`och `TaskIntermediate.` du kan också definiera anpassade kategorier av utdata.

Med dessa typer av utdata kan du ange vilken typ av utdata som ska visas när du kör en fråga i batch för de sparade utdata för en specifik aktivitet. Med andra ord kan du filtrera listan på en av utdatatyperna när du listar utdata för en aktivitet. Till exempel "ge mig för *hands* utdata för uppgift *109*". Mer information om att visa och hämta utdata visas i Hämta utdata senare i artikeln.

> [!TIP]
> Utmatnings typen avgör också var i Azure Portal en viss fil visas: *TaskOutput*-kategoriserade filer visas under **Uppgiftsutdata**och *TaskLog* -filer visas under **aktivitets loggar**.

### <a name="store-job-outputs"></a>Lagra utdata från jobb

Förutom att lagra Uppgiftsutdata, kan du lagra de utdata som är associerade med ett helt jobb. I sammanfognings uppgiften för ett film åter givnings jobb kan du till exempel behålla den helt återgivna filmen som en jobb utskrift. När jobbet har slutförts kan klient programmet Visa och hämta utdata för jobbet och behöver inte fråga de enskilda uppgifterna.

Lagra jobbets utdata genom att anropa [JobOutputStorage][net_joboutputstorage]. [SaveAsync][net_joboutputstorage_saveasync] -Metod och ange [JobOutputKind][net_joboutputkind] och filename:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Precis som med **TaskOutputKind** -typen för Uppgiftsutdata använder du typen [JobOutputKind][net_joboutputkind] för att kategorisera ett jobbs sparade filer. Med den här parametern kan du senare fråga efter (lista) en bestämd typ av utdata. **JobOutputKind** -typen omfattar både utdata-och för hands kategorier och har stöd för att skapa anpassade kategorier.

### <a name="store-task-logs"></a>Lagra aktivitets loggar

Förutom att spara en fil till varaktig lagring när en aktivitet eller ett jobb slutförs, kan du till exempel behöva spara filer som uppdateras under körningen av en aktivitet &mdash; loggfiler eller `stdout.txt` och `stderr.txt`. För det här ändamålet innehåller biblioteket för Azure Batch fil konventioner [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync][net_savetrackedasync] -metod. Med [SaveTrackedAsync][net_savetrackedasync]kan du spåra uppdateringar till en fil på noden (med ett intervall som du anger) och spara uppdateringarna till Azure Storage.

I följande kodfragment använder vi [SaveTrackedAsync][net_savetrackedasync] för att uppdatera `stdout.txt` i Azure Storage var 15: e sekund under körningen av uppgiften:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

Det kommenterade avsnittet `Code to process data and produce output file(s)` är en plats hållare för koden som din aktivitet normalt utför. Du kan till exempel ha kod som laddar ned data från Azure Storage och utför omvandling eller beräkning på den. Den viktiga delen av det här kodfragmentet demonstrerar hur du kan figursätta sådan kod i ett `using` block för att regelbundet uppdatera en fil med [SaveTrackedAsync][net_savetrackedasync].

Node agent är ett program som körs på varje nod i poolen och tillhandahåller kommando-och-Control-gränssnittet mellan noden och batch-tjänsten. `Task.Delay`-anropet krävs i slutet av det här `using`-blocket för att säkerställa att Node-agenten har tid att rensa innehållet i standard ut till filen STDOUT. txt på noden. Utan denna fördröjning är det möjligt att förlora de senaste sekunderna utdata. Den här fördröjningen kanske inte krävs för alla filer.

> [!NOTE]
> När du aktiverar fil spårning med **SaveTrackedAsync**sparas bara *tillägg* till den spårade filen Azure Storage. Använd endast den här metoden för att spåra icke-roterande loggfiler eller andra filer som skrivs till med åtgärderna Lägg till i slutet av filen.

## <a name="retrieve-output-data"></a>Hämta utdata

När du hämtar beständiga utdata med hjälp av biblioteket Azure Batch fil konventioner gör du det på ett sätt som är inriktad på aktivitet och jobb. Du kan begära utdata för en aktivitet eller ett jobb utan att behöva känna till sökvägen i Azure Storage eller till och med dess fil namn. I stället kan du begära utdatafiler efter uppgift eller jobb-ID.

Följande kodfragment upprepas genom ett jobbs aktiviteter, skriver ut information om utdata för aktiviteten och laddar ned dess filer från Storage.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Visa utdatafiler i Azure Portal

I Azure Portal visas Uppgiftsutdata och loggar som är beständiga till ett länkat Azure Storage-konto med hjälp av [satserna för fil konventioner som standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Du kan implementera dessa konventioner själv på valfritt språk, eller så kan du använda biblioteket fil konventioner i dina .NET-program.

Om du vill aktivera visning av dina utdatafiler i portalen måste du uppfylla följande krav:

1. Länka ett Azure Storage-konto till ditt batch-konto.
1. Följ de fördefinierade namngivnings konventionerna för lagrings behållare och filer när du sparar utdata. Du hittar definitionen av dessa konventioner i filen Conventions Library [README][github_file_conventions_readme]. Om du använder biblioteket för [Azure batch fil konventioner][nuget_package] för att spara utdata, sparas filerna i enlighet med fil konventions standarden.

Om du vill visa Uppgiftsutdata och loggar i Azure Portal navigerar du till den aktivitet vars utdata du är intresse rad av och klickar sedan på antingen **sparade utdatafiler** eller **sparade loggar**. Den här bilden visar **sparade utdatafiler** för uppgiften med ID "007":

![Bladet uppgifter utdata i Azure Portal][2]

## <a name="code-sample"></a>Kodexempel

[PersistOutputs][github_persistoutputs] -exempelprojektet är ett av [Azure Batch kod exempel][github_samples] på GitHub. Den här Visual Studio-lösningen visar hur du använder biblioteket Azure Batch fil konventioner för att spara Uppgiftsutdata till varaktig lagring. Följ dessa steg om du vill köra exemplet:

1. Öppna projektet i **Visual Studio 2019**.
2. Lägg till dina autentiseringsuppgifter för batch-och lagrings **konto** i **AccountSettings. Settings** i Microsoft. Azure. batch. Samples. common Project.
3. **Skapa** (men kör inte) lösningen. Återställ eventuella NuGet-paket om du uppmanas att göra det.
4. Använd Azure Portal för att ladda upp ett [programpaket](batch-application-packages.md) för **PersistOutputsTask**. Inkludera `PersistOutputsTask.exe` och dess beroende sammansättningar i. zip-paketet, ange program-ID till "PersistOutputsTask" och programpaket versionen till "1,0".
5. **Starta** (kör) **PersistOutputs** -projektet.
6. När du uppmanas att välja den beständiga teknik som ska användas för att köra exemplet anger du **1** för att köra exemplet med hjälp av biblioteket fil konventioner för att spara Uppgiftsutdata. 

## <a name="next-steps"></a>Nästa steg

### <a name="get-the-batch-file-conventions-library-for-net"></a>Hämta bibliotek för batch-fil konventioner för .NET

Bibliotek för batch-fil konventioner för .NET finns på [NuGet][nuget_package]. Biblioteket utökar [CloudJob][net_cloudjob] -och [CloudTask][net_cloudtask] -klasserna med nya metoder. Se även [referens dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) för biblioteket med fil konventioner.

[Käll koden][github_file_conventions] för fil konventions biblioteket finns på GitHub i Microsoft Azure SDK för .net-lagringsplatsen. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Utforska andra metoder för att spara utdata

- Se [Spara jobb-och Uppgiftsutdata för att Azure Storage](batch-task-output.md) en översikt över att spara uppgifter och jobb data.
- Se [Spara uppgifts data till Azure Storage med batch-tjänst-API:](batch-task-output-files.md) et för att lära dig hur du använder batch-tjänstens API för att spara utdata.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Sparade utdatafiler och väljare för sparade loggar i portalen"
[2]: ./media/batch-task-output/task-output-02.png "Bladet uppgifter utdata i Azure Portal"
