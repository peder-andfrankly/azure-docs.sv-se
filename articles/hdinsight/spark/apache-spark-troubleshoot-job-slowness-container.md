---
title: Apache Spark långsam när Azure HDInsight-lagring har många filer
description: Apache Spark jobbet körs långsamt när Azure Storage-behållaren innehåller många filer i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: 82280532fa91923bd08d8ff3164dc841282c392c
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241161"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Apache Spark jobbet körs långsamt när Azure Storage-behållaren innehåller många filer i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du kör ett HDInsight-kluster blir det Apache Spark jobbet som skriver till Azure Storage-behållaren långsamt när det finns många filer/undermappar. Det tar till exempel 20 sekunder att skriva till en ny behållare, men ungefär 2 minuter när du skriver till en behållare som har funktion-filer.

## <a name="cause"></a>Orsak

Detta är ett känt Spark-problem. Den långsammaheten kommer från `ListBlob` och `GetBlobProperties` åtgärder under Spark-jobbets körning.

För att kunna spåra partitioner måste Spark underhålla en `FileStatusCache` som innehåller information om katalog strukturen. Med den här cachen kan Spark parsa Sök vägarna och vara medvetna om tillgängliga partitioner. Fördelen med att spåra partitioner är att Spark bara vidrör de nödvändiga filerna när du läser data. För att den här informationen ska vara aktuell när du skriver nya data, måste Spark lista alla filer i katalogen och uppdatera denna cache.

I Spark 2,1, medan vi inte behöver uppdatera cachen efter varje skrivning, kommer Spark att kontrol lera om en befintlig partitionstabell matchar den föreslagna i den aktuella skrivbegäran, så att den även leder till att åtgärder visas i början av varje skrivning.

I Spark 2,2 bör det här prestanda problemet åtgärdas när du skriver data med append-läge.

## <a name="resolution"></a>Upplösning

När du skapar en partitionerad data uppsättning är det viktigt att använda ett partitionerings schema som begränsar antalet filer som Spark har att uppdatera `FileStatusCache`.

För varje N:te Micro batch där N %100 = = 0 (100 är bara ett exempel), flytta befintliga data till en annan katalog som kan läsas in av Spark.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
