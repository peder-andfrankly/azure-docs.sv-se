---
title: Apache Spark strömmande program stoppas efter 24 dagar i Azure HDInsight
description: Ett Apache Spark streaming-program stoppas efter att det har körts i 24 dagar och det finns inga fel i loggfilerna.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 946c54f6c1048c5f22b520333daf0da47740f2e4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087210"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenario: Apache Spark strömmande program stoppas efter att ha körts i 24 dagar i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Ett Apache Spark streaming-program stoppas efter att det har körts i 24 dagar och det finns inga fel i loggfilerna.

## <a name="cause"></a>Orsak

`livy.server.session.timeout` Värdet styr hur lång tid Apache livy ska vänta tills en session har slutförts. När sessionens längd når `session.timeout` värdet avslutas livy-sessionen och programmet automatiskt.

## <a name="resolution"></a>Lösning

För tids krävande jobb ökar du värdet för `livy.server.session.timeout` att använda Ambari-användargränssnittet. Du kan komma åt livy-konfigurationen från Ambari-gränssnittet med hjälp `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`av URL: en.

Ersätt `<yourclustername>` med namnet på ditt HDInsight-kluster på det sätt som visas i portalen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
