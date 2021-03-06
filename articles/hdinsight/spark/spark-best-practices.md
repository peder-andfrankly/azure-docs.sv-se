---
title: Apache Spark bästa praxis på Azure HDInsight
description: Lär dig metod tips för att använda Apache Spark i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106130"
---
# <a name="apache-spark-best-practices"></a>Metod tips för Apache Spark

Den här artikeln innehåller olika metod tips för att använda Apache Spark på Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Hur gör jag för att köra eller skicka Spark-jobb?

| Alternativ | Dokument |
|---|---|
| VSCode | [Använda Spark & Hive-verktyg för Visual Studio Code](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [Självstudier: Läs in data och kör frågor på ett Apache Spark kluster i Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Självstudier: Använd Azure Toolkit for IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Självstudier: Skapa ett Scala maven-program för Apache Spark i HDInsight med hjälp av IntelliJ](./apache-spark-create-standalone-application.md) |
| Zeppelin-anteckningsböcker | [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Fjärrjobbet skickas med livy | [Använda REST-API:et för Apache Spark för att skicka fjärrstyrda jobb till ett HDInsight Spark-kluster](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Hur gör jag för att övervaka och felsöka Spark-jobb?

| Alternativ | Dokument |
|---|---|
| Azure Toolkit för IntelliJ | [Fel Spark fel sökning av jobb med Azure Toolkit for IntelliJ (för hands version)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ via SSH | [Felsöka Apache Spark-program lokalt eller via fjärranslutning i ett HDInsight-kluster med Azure Toolkit for IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ via VPN | [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via fjärr anslutning i HDInsight via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Jobb diagram på Apache Spark historik Server | [Använda utökad Apache Spark-historikserver för att felsöka och diagnostisera Apache Spark-program](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Hur gör jag för att gör mina Spark-jobb effektivare att köra?

| Alternativ | Dokument |
|---|---|
| IO-cache | [Förbättra prestanda för Apache Spark arbets belastningar med Azure HDInsight IO-cache (för hands version)](./apache-spark-improve-performance-iocache.md) |
| Konfigurationsalternativ | [Optimera Apache Spark jobb](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Hur gör jag för att ansluta till andra Azure-tjänster?

| Alternativ | Dokument |
|---|---|
| Apache Hive i HDInsight | [Integrera Apache Spark och Apache Hive med Hive-lagrets koppling](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase på HDInsight | [Använd Apache Spark för att läsa och skriva apache HBase-data](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka på HDInsight | [Självstudier: Använda Apache Spark strukturerad strömning med Apache Kafka på HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Implementera en lambda-arkitektur på Azure-plattformen](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Vad är mina lagrings alternativ?

| Alternativ | Dokument |
|---|---|
| Data Lake Storage Gen2 | [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Använda Azure-lagring med Azure HDInsight-kluster](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Apache Spark inställningar](apache-spark-settings.md)
* [Optimera Apache Spark jobb i HDInsight](apache-spark-perf.md)
