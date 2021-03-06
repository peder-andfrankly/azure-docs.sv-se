---
title: Skapa uppgifter för att förbereda jobb och slutföra jobb på datornoder – Azure Batch | Microsoft Docs
description: Använd förberedande uppgifter på jobb nivå för att minimera data överföringen till Azure Batch Compute-noder och släpp uppgifter för noden rensa vid jobb slut för ande.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 2dbdbc8b13a75b72ca09a319c6925d0835a52e13
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095126"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Kör jobb förberedelse-och jobb publicerings aktiviteter i batch Compute-noder

 Ett Azure Batch jobb kräver ofta en viss typ av installation innan dess aktiviteter utförs och underhåll av jobb när dess aktiviteter har slutförts. Du kan behöva hämta vanliga uppgifter från uppgifts indata till dina datornoder eller ladda upp Uppgiftsutdata till Azure Storage när jobbet har slutförts. Du kan använda **jobb förberedelse** -och **jobb publicerings** aktiviteter för att utföra dessa åtgärder.

## <a name="what-are-job-preparation-and-release-tasks"></a>Vad är jobb förberedelse-och publicerings uppgifter?
Innan jobbet körs körs jobb förberedelse aktiviteten på alla Compute-noder som schemalagts för att köra minst en aktivitet. När jobbet har slutförts körs jobb publicerings aktiviteten på varje nod i poolen som utförde minst en aktivitet. Precis som med vanliga batch-uppgifter kan du ange en kommando rad som ska anropas när en jobb förberedelse-eller publicerings uppgift körs.

Jobb förberedelse-och publicerings aktiviteter erbjuder välkända batch-funktioner, till exempel fil hämtning ([resursfiler][net_job_prep_resourcefiles]), förhöjd körning, anpassade miljövariabler, maximal körnings tid, antal försök och kvarhållning av filer.

I följande avsnitt får du lära dig hur du använder de [aktivitets typerna jobpreparationtask][net_job_prep] -och [JobReleaseTask][net_job_release] -klasser som finns i [batch .net][api_net] -biblioteket.

> [!TIP]
> Jobb förberedelse-och publicerings aktiviteter är särskilt användbara i miljöer med delade pooler, där en pool av datornoder behålls mellan jobb körningar och används av många jobb.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>När du ska använda jobb förberedelse-och publicerings uppgifter
Jobb förberedelse-och jobb publicerings aktiviteter passar bra för följande situationer:

**Hämta vanliga uppgifts data**

Batch-jobb kräver ofta en gemensam uppsättning data som indata för jobbets aktiviteter. För dagliga risk analys beräkningar är till exempel marknads data projektspecifika, men gemensamma för alla aktiviteter i jobbet. Dessa marknads data, ofta flera gigabyte i storlek, bör bara hämtas till varje Compute-nod så att alla aktiviteter som körs på noden kan använda den. Använd en **jobb förberedelse uppgift** för att ladda ned dessa data till varje nod innan jobbets övriga aktiviteter körs.

**Ta bort jobb-och Uppgiftsutdata**

I en "delad pool"-miljö, där en Pools datornoder inte inaktive ras mellan jobb, kan du behöva ta bort jobb data mellan körningar. Du kan behöva spara disk utrymme på noderna eller uppfylla organisationens säkerhets principer. Använd en **jobb publicerings aktivitet** för att ta bort data som hämtades av en jobb förberedelse aktivitet eller genererades under aktivitets körningen.

**Logg kvarhållning**

Du kanske vill behålla en kopia av loggfiler som aktiviteterna genererar, eller så kanske du vill att krasch dum par filer som kan genereras av misslyckade program. Använd en **jobb publicerings aktivitet** i sådana fall för att komprimera och överföra dessa data till ett [Azure Storage][azure_storage] -konto.

> [!TIP]
> Ett annat sätt att spara loggar och andra jobb-och Uppgiftsutdata är att använda biblioteket för [Azure batch fil konventioner](batch-task-output.md) .
> 
> 

## <a name="job-preparation-task"></a>Jobbförberedelseuppgift
Innan du utför ett jobbs aktiviteter kör batch jobb förberedelse aktiviteten på varje Compute-nod som är schemalagd för att köra en aktivitet. Batch-tjänsten väntar som standard på att jobb förberedelse aktiviteten ska slutföras innan aktiviteter som är schemalagda att köras på noden körs. Du kan dock konfigurera tjänsten att inte vänta. Om noden startas om körs jobb förberedelse åtgärden igen, men du kan också inaktivera det här beteendet.

Jobb förberedelse aktiviteten körs bara på noder som är schemalagda att köra en aktivitet. Detta förhindrar onödig körning av en förberedande uppgift om en nod inte är tilldelad en aktivitet. Detta kan inträffa när antalet aktiviteter för ett jobb är mindre än antalet noder i en pool. Den gäller även när [samtidiga uppgifts körningar](batch-parallel-node-tasks.md) är aktiverat, vilket innebär att vissa noder är inaktiva om antalet aktiviteter är lägre än de totala möjliga samtidiga aktiviteterna. Genom att inte köra jobb förberedelse aktiviteten på inaktiva noder kan du spendera mindre pengar på avgifterna för data överföring.

> [!NOTE]
> [Aktivitets typerna jobpreparationtask][net_job_prep_cloudjob] skiljer sig från [CloudPool. StartTask][pool_starttask] i som aktivitets typerna jobpreparationtask körs i början av varje jobb, medan StartTask körs endast när en Compute Node först ansluter en pool eller startar om.
> 
> 

## <a name="job-release-task"></a>Jobbpubliceringsuppgift
När ett jobb har marker ATS som slutfört körs jobb publicerings aktiviteten på varje nod i poolen som utförde minst en aktivitet. Du markerar ett jobb som slutfört genom att utfärda en avslutnings förfrågan. Batch-tjänsten ställer sedan in jobbets tillstånd till att *Avsluta*, avslutar alla aktiva eller pågående aktiviteter som är kopplade till jobbet och Kör jobb publicerings aktiviteten. Jobbet flyttas sedan till slutfört tillstånd.

> [!NOTE]
> Jobb borttagning kör även jobb publicerings aktiviteten. Men om ett jobb redan har avslut ATS körs inte publicerings aktiviteten en andra gång om jobbet tas bort senare.

Jobb lanserings aktiviteter kan köras i högst 15 minuter innan de avslutas av batch-tjänsten. Mer information finns i [referens dokumentationen för REST API](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask).
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Förberedelse av jobb och publicerings uppgifter med batch .NET
Om du vill använda en uppgift för jobb förberedelse tilldelar du ett [aktivitets typerna jobpreparationtask][net_job_prep] -objekt till ditt jobbs egenskap [CloudJob. aktivitets typerna jobpreparationtask][net_job_prep_cloudjob] . På samma sätt initierar du en [JobReleaseTask][net_job_release] och tilldelar den till jobbets egenskap [CloudJob. JobReleaseTask][net_job_prep_cloudjob] för att ange jobbets versions aktivitet.

I det här `myBatchClient` kodfragmentet är en instans av [metoden batchclient][net_batch_client]och `myPool` är en befintlig pool i batch-kontot.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Som tidigare nämnts körs versions uppgiften när ett jobb avbryts eller tas bort. Avsluta ett jobb med [JobOperations. TerminateJobAsync][net_job_terminate]. Ta bort ett jobb med [JobOperations. DeleteJobAsync][net_job_delete]. Du kan vanligt vis avsluta eller ta bort ett jobb när dess aktiviteter har slutförts, eller när en tids gräns som du har definierat har uppnåtts.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Kod exempel på GitHub
Om du vill se jobb förberedelse-och publicerings aktiviteter i praktiken kan du kolla [JobPrepRelease][job_prep_release_sample] -exempelprojektet på GitHub. Det här konsol programmet gör följande:

1. Skapar en pool med två noder.
2. Skapar ett jobb med jobb förberedelse-, versions-och standard uppgifter.
3. Kör uppgiften jobb Preparation, som först skriver Node-ID: t till en textfil i nodens delade katalog.
4. Kör en aktivitet på varje nod som skriver dess aktivitets-ID till samma textfil.
5. När alla aktiviteter har slutförts (eller om tids gränsen har nåtts) skriver ut innehållet i varje nods textfil till-konsolen.
6. När jobbet har slutförts kör jobb publicerings aktiviteten för att ta bort filen från noden.
7. Skriver ut avslutnings koderna för jobb förberedelse-och publicerings aktiviteterna för varje nod som de utförde.
8. Pausar körningen för att tillåta bekräftelse av jobb och/eller borttagning av pooler.

Utdata från exempel programmet ser ut ungefär så här:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> På grund av variabel skapande och start tid för noder i en ny pool (vissa noder är klara för aktiviteter innan andra) kan du se olika utdata. I synnerhet, eftersom uppgifterna slutförs snabbt, kan en av poolens noder köra alla jobbets aktiviteter. Om detta inträffar ser du att aktiviteterna förberedelse-och publicerings aktiviteter inte finns för den nod som körde inga aktiviteter.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Granska jobb förberedelse-och publicerings uppgifter i Azure Portal
När du kör exempel programmet kan du använda [Azure Portal][portal] för att visa egenskaperna för jobbet och dess aktiviteter, eller till och med Ladda ned den delade text filen som ändras av jobbets aktiviteter.

Skärm bilden nedan visar **bladet förberedande uppgifter** i Azure Portal efter en körning av exempel programmet. Navigera till egenskaperna för *JobPrepReleaseSampleJob* när dina aktiviteter har slutförts (men innan du tar bort jobbet och poolen) och klickar på **förberedelse aktiviteter** eller **versions aktiviteter** för att visa deras egenskaper.

![Egenskaper för jobb förberedelse i Azure Portal][1]

## <a name="next-steps"></a>Nästa steg
### <a name="application-packages"></a>Programpaket
Förutom uppgiften för jobb förberedelse kan du också använda funktionen programpaket i [](batch-application-packages.md) batch för att förbereda datornoder för uppgifts körning. Den här funktionen är särskilt användbar för att distribuera program som inte kräver att ett installations program körs, program som innehåller många (100 +) filer eller program som kräver en strikt versions kontroll.

### <a name="installing-applications-and-staging-data"></a>Installera program och mellanlagrings data
Det här MSDN forum-inlägget ger en översikt över flera metoder för att förbereda dina noder för att köra aktiviteter:

[Installera program och mellanlagrings data i batch Compute-noder][forum_post]

Det finns flera tekniker som du kan använda för att distribuera program och data till Compute-noder. Azure Batch

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
