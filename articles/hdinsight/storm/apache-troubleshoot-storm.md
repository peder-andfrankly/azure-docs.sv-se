---
title: Felsöka Storm med Azure HDInsight
description: Få svar på vanliga frågor om hur du använder Apache Storm med Azure HDInsight.
keywords: Azure HDInsight, Storm, vanliga frågor och svar, felsökningsguide för vanliga problem
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: e2cc9dd81a0bbefa5cf37facb6067bda07117eaf
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903728"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Felsöka Apache Storm med Azure HDInsight

Lär dig mer om de viktigaste problemen och sina lösningar för att arbeta med [Apache Storm](https://storm.apache.org/) nyttolaster i [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hur kommer jag åt Storm-Användargränssnittet på ett kluster?

Har du två alternativ för att komma åt Användargränssnittet för Storm från en webbläsare:

### <a name="apache-ambari-ui"></a>Apache Ambari-användargränssnitt

1. Gå till Ambari-instrumentpanelen.
2. Välj i listan över tjänster, **Storm**.
3. I den **snabblänkar** menyn och välj **Storm-Användargränssnittet**.

### <a name="direct-link"></a>Direktlänk

Du kan komma åt Storm-Användargränssnittet på följande URL:

`https://<cluster DNS name>/stormui`

Exempel: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hur jag överföra kontrollpunktsinformation för Storm event hub från en topologi till en annan?

När du utvecklar topologier som läser från Azure Event Hubs med hjälp av HDInsight Storm event hub-kanalen .jar-filen måste du distribuera en topologi som har samma namn på ett nytt kluster. Men du måste behålla kontrollpunktsdata som har bekräftats att [Apache ZooKeeper](https://zookeeper.apache.org/) på det gamla klustret.

### <a name="where-checkpoint-data-is-stored"></a>Kontrollpunktsdata ska lagras

Kontrollpunktsdata för förskjutningar lagras av event hub kanal i ZooKeeper i två rot-sökvägar:

- Kanalen-kontrollpunkter som inte är transaktionella lagras i `/eventhubspout`.

- Transaktions kontroll punkts data för kanalen lagras i `/transactional`.

### <a name="how-to-restore"></a>Så här återställer du

Om du vill hämta skript och bibliotek som används för att exportera data från ZooKeeper och importera sedan data tillbaka till ZooKeeper med ett nytt namn, se [HDInsight Storm-exempel](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Lib-mappen har .jar-filerna som innehåller implementeringen för export/import-åtgärden. Bash-mappen innehåller ett exempelskript som visar hur du exporterar data från ZooKeeper-server på det gamla klustret och sedan importera den tillbaka till ZooKeeper-server på det nya klustret.

Kör den [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) skriptet från ZooKeeper-noder att exportera och importera data. Uppdatera skriptet till rätt Hortonworks Data Platform (HDP) version. (Vi arbetar på att göra dessa skript allmän i HDInsight. Allmänt skript kan köras från en nod i klustret utan ändringar av användaren.)

Export kommandot skriver metadata till en Apache Hadoop Distributed File System-sökväg (HDFS) (i Azure Blob Storage eller Azure Data Lake Storage) på en plats som du anger.

### <a name="examples"></a>Exempel

#### <a name="export-offset-metadata"></a>Exportera förskjutningen metadata

1. Använda SSH för att gå till ZooKeeper-klustret på klustret där kontrollpunkten förskjutning måste exporteras.
2. Kör följande kommando (när du har uppdaterat HDP-versions strängen) för att exportera ZooKeeper offset-data till `/stormmetadta/zkdata` HDFS-sökvägen:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importera förskjutningen metadata

1. Använda SSH för att gå till ZooKeeper-klustret på klustret där kontrollpunkten förskjutning måste importeras.
2. Kör följande kommando (när du har uppdaterat HDP-versions strängen) för att importera ZooKeeper offset-data från HDFS-sökvägen `/stormmetadata/zkdata` till ZooKeeper-servern i mål klustret:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Ta bort förskjutningen metadata så att topologier kan börja bearbetningsdata från början eller från en tidsstämpel som användaren väljer

1. Använda SSH för att gå till ZooKeeper-klustret på klustret där kontrollpunkten förskjutning måste tas bort.
2. Kör följande kommando (när du har uppdaterat versionssträng HDP) förskjuter för att ta bort alla ZooKeeper data i det aktuella klustret:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Hur hittar jag Storm-binärfiler i ett kluster?

Storm-binärfiler för den aktuella HDP-stacken är i `/usr/hdp/current/storm-client`. Platsen är samma både för huvudnoderna och arbetsnoder.

Det kan finnas flera binärfiler för vissa HDP-versioner i/usr/HDP (till exempel `/usr/hdp/2.5.0.1233/storm`). `/usr/hdp/current/storm-client`-mappen är symlinked till den senaste versionen som körs i klustret.

Mer information finns i [ansluta till ett HDInsight-kluster med hjälp av SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) och [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Hur vet jag topologi för distribution av ett Storm-kluster?

Först identifiera alla komponenter som installeras med HDInsight Storm. Ett Storm-kluster består av fyra nod kategorier:

* Gateway-noder
* Huvudnoder
* ZooKeeper-noder
* Arbetsnoder

### <a name="gateway-nodes"></a>Gateway-noder

En gateway-noden är en gateway och omvänd proxy-tjänst som gör att offentlig åtkomst till en aktiv Ambari management-tjänsten. Den hanterar också Ambari val av ledare.

### <a name="head-nodes"></a>Huvudnoder

Storm huvudnoderna kör följande tjänster:
* Nimbus
* Ambari-servern
* Ambari mått-server
* Ambari mått insamlaren
 
### <a name="zookeeper-nodes"></a>ZooKeeper-noder

HDInsight levereras med en zookeeperkvorum med tre noder. Kvorum storleken är fast och kan inte konfigureras.

Storm-tjänsterna i klustret har konfigurerats för att automatiskt använda zookeeperkvorum.

### <a name="worker-nodes"></a>Arbetsnoder

Storm arbetsnoder kör följande tjänster:
* Överordnad
* Worker Java-datorer (JVMs) för att köra topologier
* Ambari-agenten

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Hur hittar jag Storm event hub spout-binärfiler för utveckling?

Mer information om hur du använder Storm event hub spout .jar-filerna med topologin finns i följande resurser.

### <a name="java-based-topology"></a>Java-baserad topologi

[Bearbeta händelser från Azure Event Hubs med Apache Storm i HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-baserad topologi (Mono på Storm för HDInsight 3.4 + Linux-kluster)

[Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Senaste Apache Storm event hub-kanalen binärfiler för HDInsight 3.5 + Linux Storm-kluster

Information om hur du använder den senaste Storm Event Hub-kanalen som fungerar med HDInsight 3.5 + Linux Storm-kluster finns i [Readme-filen mvn-lagrings platsen](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Kodexempel för källa

Se [exempel](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) på hur du kan läsa och skriva från Azure Event Hub med ett Apache Storm-topologi (skrivna i Java) på ett Azure HDInsight-kluster.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Hur hittar jag Storm Log4J 2 konfigurationsfiler på kluster?

Identifiera [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) konfigurationsfiler för Storm-tjänster.

### <a name="on-head-nodes"></a>På huvudnoderna

Nimbus Log4J-konfigurationen läses från `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

### <a name="on-worker-nodes"></a>På arbetsnoder

Log4J-konfigurationen för administratören läses från `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

Konfigurations filen för Worker-Log4J läses från `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`.

Exempel: `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Inget ledar undantag

När du skickar en topologi kan användaren få ett fel meddelande som liknar: `Topology submission exception, cause not a leader, the current leader is NimbusInfo`.

För att lösa problemet kan användaren behöva File a Ticket för att noderna ska starta om/starta om. Mer information finns på [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Tillgång till support för prenumerationshantering och fakturering ingår i din Microsoft Azure-prenumeration och teknisk support ges via ett [supportavtal för Azure](https://azure.microsoft.com/support/plans/).
