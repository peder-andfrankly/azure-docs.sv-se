---
title: Använda Hadoop Oozie-arbetsflöden i Linux-baserade Azure HDInsight
description: Använda Hadoop-Oozie i Linux-baserade HDInsight. Lär dig hur du definierar ett Oozie-arbetsflöde och skickar ett Oozie-jobb.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 89364a3ee948abbe5d233052878abe92bc7663a7
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241687"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Använd Apache Oozie med Apache Hadoop för att definiera och köra ett arbets flöde på Linux-baserade Azure HDInsight

Lär dig hur du använder Apache Oozie med Apache Hadoop på Azure HDInsight. Oozie är ett arbets flödes-och samordnings system som hanterar Hadoop-jobb. Oozie är integrerat med Hadoop-stacken och stöder följande jobb:

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Du kan också använda Oozie för att schemalägga jobb som är speciella för ett system, t. ex. Java-program eller Shell-skript.

> [!NOTE]  
> Ett annat alternativ för att definiera arbets flöden med HDInsight är att använda Azure Data Factory. Läs mer om Data Factory i [använda Apache gris och Apache Hive med Data Factory][azure-data-factory-pig-hive]. Om du vill använda Oozie i kluster med Enterprise Security Package kan du läsa [köra apache Oozie i HDInsight Hadoop-kluster med Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).

## <a name="prerequisites"></a>Krav

* **Ett Hadoop-kluster i HDInsight**. Se [Kom igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **En SSH-klient**. Se [ansluta till HDInsight (Apache Hadoop) med SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **En Azure SQL Database**.  Se [skapa en Azure SQL-databas i Azure Portal](../sql-database/sql-database-get-started.md).  I den här artikeln används en databas med namnet **oozietest**.

* [URI-schemat](./hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta är `wasb://` för Azure Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage Gen1. Om säker överföring har Aktiver ATS för Azure Storage blir URI: n `wasbs://`. Se även [säker överföring](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Exempel arbets flöde

Arbets flödet som används i det här dokumentet innehåller två åtgärder. Åtgärder är definitioner för aktiviteter, till exempel körning av Hive, Sqoop, MapReduce eller andra processer:

![HDInsight Oozie Workflow-diagram](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. En Hive-åtgärd kör ett HiveQL-skript för att extrahera poster från `hivesampletable` som ingår i HDInsight. Varje datarad beskriver ett besök från en speciell mobil enhet. Post formatet visas som följande text:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Hive-skriptet som används i det här dokumentet räknar antalet besök för varje plattform, till exempel Android eller iPhone, och lagrar antalet i en ny Hive-tabell.

    Mer information om Hive finns i [använda Apache Hive med HDInsight][hdinsight-use-hive].

2. En Sqoop-åtgärd exporterar innehållet i den nya Hive-tabellen till en tabell som skapats i Azure SQL Database. Mer information om Sqoop finns i [använda Apache Sqoop med HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Information om vilka Oozie-versioner som stöds i HDInsight-kluster finns i [Nyheter i de Hadoop-kluster versioner som tillhandahålls av HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Skapa arbets katalogen

Oozie förväntar dig att lagra alla resurser som krävs för ett jobb i samma katalog. I det här exemplet används `wasbs:///tutorials/useoozie`. Utför följande steg för att skapa den här katalogen:

1. Redigera koden nedan för att ersätta `sshuser` med SSH-användarnamnet för klustret och ersätt `CLUSTERNAME` med namnet på klustret.  Ange sedan koden för att ansluta till HDInsight-klustret med [hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Använd följande kommando för att skapa katalogen:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > Parametern `-p` gör att alla kataloger i sökvägen skapas. `data`-katalogen används för att lagra de data som används av `useooziewf.hql`-skriptet.

3. Redigera koden nedan för att ersätta `sshuser` med ditt SSH-användarnamn.  Använd följande kommando för att se till att Oozie kan personifiera ditt användar konto:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Du kan ignorera fel som anger att användaren redan är medlem i `users`s gruppen.

## <a name="add-a-database-driver"></a>Lägg till en databas driv rutin

Eftersom det här arbets flödet använder Sqoop för att exportera data till SQL-databasen, måste du ange en kopia av JDBC-drivrutinen som används för att interagera med SQL-databasen. Om du vill kopiera JDBC-drivrutinen till arbets katalogen använder du följande kommando från SSH-sessionen:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Verifiera den faktiska JDBC-driv rutinen som finns på `/usr/share/java/`.

Om arbets flödet har använt andra resurser, till exempel en jar-app som innehåller ett MapReduce-program, måste du även lägga till dessa resurser.

## <a name="define-the-hive-query"></a>Definiera Hive-frågan

Använd följande steg för att skapa ett HiveQL-skript (Hive Query Language) som definierar en fråga. Du använder frågan i ett Oozie-arbetsflöde senare i det här dokumentet.

1. Från SSH-anslutningen använder du följande kommando för att skapa en fil med namnet `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

1. När GNU nano-redigeraren öppnas använder du följande fråga som filens innehåll:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Det finns två variabler som används i skriptet:

   * `${hiveTableName}`: innehåller namnet på den tabell som ska skapas.

   * `${hiveDataFolder}`: innehåller den plats där datafilerna för tabellen ska lagras.

     Arbets flödes definitions filen, Workflow. xml i den här artikeln, skickar dessa värden till det här HiveQL-skriptet vid körning.

1. Om du vill spara filen väljer du **CTRL + X**, anger **Y**och väljer sedan **RETUR**.  

1. Använd följande kommando för att kopiera `useooziewf.hql` till `wasbs:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Det här kommandot lagrar `useooziewf.hql`-filen i HDFS-kompatibla lagrings utrymme för klustret.

## <a name="define-the-workflow"></a>Definiera arbets flödet

Oozie för arbets flödes definitioner skrivs i hPDL (Hadoop process Definition Language), vilket är ett XML-process definitions språk. Använd följande steg för att definiera arbets flödet:

1. Använd följande instruktion för att skapa och redigera en ny fil:

    ```bash
    nano workflow.xml
    ```

2. När nano-redigeraren öppnas anger du följande XML som fil innehåll:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Det finns två åtgärder definierade i arbets flödet:

   * `RunHiveScript`: den här åtgärden är start åtgärden och kör `useooziewf.hql` Hive-skriptet.

   * `RunSqoopExport`: den här åtgärden exporterar data som skapats från Hive-skriptet till en SQL-databas med hjälp av Sqoop. Den här åtgärden körs bara om åtgärden `RunHiveScript` har slutförts.

     Arbets flödet har flera poster, t. ex. `${jobTracker}`. Du ersätter dessa poster med de värden som du använder i jobb definitionen. Du kommer att skapa jobb definitionen senare i det här dokumentet.

     Observera också `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` posten i avsnittet Sqoop. Den här posten instruerar Oozie att göra det här arkivet tillgängligt för Sqoop när den här åtgärden körs.

3. Om du vill spara filen väljer du **CTRL + X**, anger **Y**och väljer sedan **RETUR**.  

4. Använd följande kommando för att kopiera `workflow.xml`-filen till `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Skapa en tabell

> [!NOTE]  
> Det finns många sätt att ansluta till SQL Database för att skapa en tabell. Följande steg använder [FreeTDS](https://www.freetds.org/) från HDInsight-klustret.

1. Använd följande kommando för att installera FreeTDS i HDInsight-klustret:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Redigera koden nedan för att ersätta `<serverName>` med ditt Azure SQL Server-namn och `<sqlLogin>` med Azure SQL Server-inloggningen.  Ange kommandot för att ansluta till den nödvändiga SQL-databasen.  Ange lösen ordet vid prompten.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Du får utdata som följande text:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Vid uppmaningen `1>` anger du följande rader:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    När instruktionen `GO` har angivits värderas de föregående instruktionerna. Dessa instruktioner skapar en tabell med namnet `mobiledata`som används av arbets flödet.

    Kontrol lera att tabellen har skapats genom att använda följande kommandon:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Du ser utdata som följande text:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Avsluta tsql-verktyget genom att ange `exit` i `1>` prompten.

## <a name="create-the-job-definition"></a>Skapa jobb definitionen

Jobb definitionen beskriver var du hittar Workflow. xml. Den beskriver också var du hittar andra filer som används av arbets flödet, till exempel `useooziewf.hql`. Dessutom definierar den värden för egenskaper som används i arbets flödet och de associerade filerna.

1. Använd följande kommando för att få en fullständig adress till standard lagrings utrymmet. Den här adressen används i konfigurations filen som du skapar i nästa steg.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Det här kommandot returnerar information som till exempel följande XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Om HDInsight-klustret använder Azure Storage som standard lagring börjar `<value>` element innehållet med `wasbs://`. Om Azure Data Lake Storage Gen1 används i stället börjar det med `adl://`. Om Azure Data Lake Storage Gen2 används börjar det med `abfs://`.

    Spara innehållet i `<value>`-elementet som det används i nästa steg.

2. Redigera XML-koden nedan enligt följande:

    |Plats hållarens värde| Ersatt värde|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Värdet togs emot från steg 1.|
    |Innehavaradministration| Ditt inloggnings namn för HDInsight-klustret om det inte är administratör.|
    |Namnet| Namn på Azure SQL Database-Server.|
    |sqlLogin| Inloggning av Azure SQL Database-Server.|
    |sqlPassword| Inloggnings lösen ord för Azure SQL Database-servern.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    Merparten av informationen i den här filen används för att fylla i de värden som används i ooziewf. XML-eller. HQL-filer, t. ex. `${nameNode}`.  Om sökvägen är en `wasbs` sökväg måste du använda den fullständiga sökvägen. Förkorta det inte till bara `wasbs:///`. Posten `oozie.wf.application.path` definierar var du hittar filen Workflow. xml. Den här filen innehåller det arbets flöde som kördes av det här jobbet.

3. Om du vill skapa jobb definitions konfigurationen för Oozie använder du följande kommando:

    ```bash
    nano job.xml
    ```

4. När nano-redigeraren öppnas klistrar du in den redigerade XML-filen som innehållet i filen.

5. Om du vill spara filen väljer du **CTRL + X**, anger **Y**och väljer sedan **RETUR**.

## <a name="submit-and-manage-the-job"></a>Skicka och hantera jobbet

I följande steg används kommandot Oozie för att skicka och hantera Oozie-arbetsflöden i klustret. Oozie-kommandot är ett användarvänligt gränssnitt över [Oozie-REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> När du använder kommandot Oozie måste du använda det fullständiga domän namnet för HDInsight Head-noden. Detta fullständiga domän namn är endast tillgängligt från klustret, eller om klustret finns i ett virtuellt Azure-nätverk, från andra datorer i samma nätverk.

1. Använd följande kommando för att hämta URL: en till Oozie-tjänsten:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Detta returnerar information som till exempel följande XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` delen är URL: en som ska användas med kommandot Oozie.

2. Redigera koden för att ersätta URL: en med den som du fick tidigare. Om du vill skapa en miljö variabel för URL: en använder du följande, så att du inte behöver ange den för varje kommando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Använd följande för att skicka jobbet:

    ```bash
    oozie job -config job.xml -submit
    ```

    Det här kommandot läser in jobb informationen från `job.xml` och skickar den till Oozie, men kör den inte.

    När kommandot har slutförts ska det returnera jobbets ID, till exempel `0000005-150622124850154-oozie-oozi-W`. Detta ID används för att hantera jobbet.

4. Redigera koden nedan för att ersätta `<JOBID>` med det ID som returnerades i föregående steg.  Använd följande kommando för att Visa jobbets status:

    ```bash
    oozie job -info <JOBID>
    ```

    Detta returnerar information som följande text:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Det här jobbet har statusen `PREP`. Den här statusen indikerar att jobbet skapades, men inte startades.

5. Redigera koden nedan för att ersätta `<JOBID>` med det ID som returnerades tidigare.  Starta jobbet med följande kommando:

    ```bash
    oozie job -start <JOBID>
    ```

    Om du kontrollerar status efter det här kommandot är det i ett körnings tillstånd och information returneras för åtgärderna i jobbet.  Det tar några minuter att slutföra jobbet.

6. Redigera koden nedan för att ersätta `<serverName>` med ditt Azure SQL Server-namn och `<sqlLogin>` med Azure SQL Server-inloggningen.  *När aktiviteten har slutförts* kan du kontrol lera att data har genererats och exporter ATS till SQL Database-tabellen med hjälp av följande kommando.  Ange lösen ordet vid prompten.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Ange följande fråga i dialog rutan `1>`:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Den information som returneras liknar följande text:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Mer information om Oozie-kommandot finns i [kommando rads verktyget Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Med Oozie-REST API kan du bygga egna verktyg som fungerar med Oozie. Följande är HDInsight-speciell information om användningen av Oozie-REST API:

* **URI**: du kan komma åt REST API utanför klustret på `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Autentisering**: Använd API: t för klustrets kluster-http-konto (admin) och lösen ord för att autentisera. Exempel:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Mer information om hur du använder Oozie-REST API finns i [Apache Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie webb gränssnitt

Oozie-webbgränssnittet innehåller en webbaserad vy över statusen för Oozie-jobb i klustret. Med webb gränssnittet kan du visa följande information:

   * Jobb status
   * Jobbdefinition
   * Konfiguration
   * Ett diagram över åtgärder i jobbet
   * Loggar för jobbet

Du kan också visa information om åtgärderna i ett jobb.

Utför följande steg för att få åtkomst till Oozie-webbgränssnittet:

1. Skapa en SSH-tunnel till HDInsight-klustret. Mer information finns i [använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. När du har skapat en tunnel öppnar du Ambari-webbgränssnittet i webbläsaren med hjälp av URI `http://headnodehost:8080`.

3. Från vänster sida av sidan väljer du **Oozie** > **snabb länkar** > **Oozie webb gränssnitt**.

    ![Apache Ambari Oozie Web UI-steg](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Oozie webb GRÄNSSNITTets standardinställningar visar de arbets flödes jobb som körs. Om du vill se alla arbets flödes jobb väljer du **alla jobb**.

    ![Arbets flödes jobb för Oozie webb konsol](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Om du vill visa mer information om ett jobb väljer du jobbet.

    ![HDInsight Apache Oozie-jobb information](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. På fliken **jobb information** kan du se grundläggande jobb information och enskilda åtgärder i jobbet. Du kan använda flikarna längst upp för att visa **jobb definitionen**, **jobb konfigurationen**, komma åt **jobb loggen**eller visa ett dirigerat acykliska diagram (DAG) för jobbet under **jobb dag**.

   * **Jobb logg**: Välj knappen **Hämta loggar** för att hämta alla loggar för jobbet eller Använd fältet **Ange Sök filter** för att filtrera loggarna.

       ![HDInsight Apache Oozie-jobb logg](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Jobb dag**: dag är en grafisk översikt över data Sök vägar som tas genom arbets flödet.

       ![HDInsight Apache Oozie Job dag](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Om du väljer någon av åtgärderna på fliken **jobb information** visas information för åtgärden. Välj till exempel åtgärden **RunSqoopExport** .

    ![HDInsight Oozie jobb åtgärds information](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Du kan se information om åtgärden, till exempel en länk till konsol- **URL:** en. Använd den här länken om du vill visa information om jobb Spårare för jobbet.

## <a name="schedule-jobs"></a>Schemalägga jobb

Du kan använda koordinatorn för att ange en start, en slut punkt och förekomst frekvens för jobb. Utför följande steg för att definiera ett schema för arbets flödet:

1. Använd följande kommando för att skapa en fil med namnet **koordinator. XML**:

    ```bash
    nano coordinator.xml
    ```

    Använd följande XML som filens innehåll:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > Variablerna `${...}` ersätts av värden i jobb definitionen vid körning. Variablerna är:
    >
    > * `${coordFrequency}`: tiden mellan aktiva instanser av jobbet.
    > * `${coordStart}`: jobbets start tid.
    > * `${coordEnd}`: jobbets slut tid.
    > * `${coordTimezone}`: koordinator jobb finns i en fast tidszon utan sommar tid, vanligt vis representeras med hjälp av UTC. Den här tids zonen kallas för den *Oozie bearbetnings tids zonen.*
    > * `${wfPath}`: sökvägen till arbets flödets. xml.

2. Om du vill spara filen väljer du **CTRL + X**, anger **Y**och väljer sedan **RETUR**.

3. Om du vill kopiera filen till arbets katalogen för det här jobbet använder du följande kommando:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Om du vill ändra `job.xml`-filen som du skapade tidigare använder du följande kommando:

    ```bash
    nano job.xml
    ```

    Gör följande ändringar:

   * Om du vill instruera Oozie att köra koordinator filen i stället för arbets flödet ändrar du `<name>oozie.wf.application.path</name>` till `<name>oozie.coord.application.path</name>`.

   * Lägg till följande XML om du vill ange variabeln `workflowPath` som används av koordinatorn:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Ersätt `wasbs://mycontainer@mystorageaccount.blob.core.windows` text med det värde som används i de andra posterna i Job. XML-filen.

   * Om du vill definiera Start, slut och frekvens för koordinatorn lägger du till följande XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Dessa värden anger start tiden till 12:00 PM den 10 maj 2018 och slut tiden till 12 maj 2018. Intervallet för att köra det här jobbet är inställt på varje dag. Frekvensen är i minuter, så 24 timmar x 60 minuter = 1440 minuter. Slutligen är tids zonen inställd på UTC.

5. Om du vill spara filen väljer du **CTRL + X**, anger **Y**och väljer sedan **RETUR**.

6. Om du vill skicka och starta jobbet använder du följande kommando:

    ```bash
    oozie job -config job.xml -run
    ```

7. Om du går till Oozie-webbgränssnittet och väljer fliken **koordinator jobb** ser du information som i följande bild:

    ![Oozie-webb konsol koordinator-fliken jobb](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    **Nästa materialization** -post innehåller nästa gång som jobbet körs.

8. Precis som det tidigare arbets flödes jobbet, om du väljer jobb posten i webb gränssnittet, visas information om jobbet:

    ![Apache Oozie-koordinator jobb information](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > I den här bilden visas endast lyckade körningar av jobbet, inte de enskilda åtgärderna i det schemalagda arbets flödet. Välj en av **Åtgärds** posterna för att se de enskilda åtgärderna.

    ![OOzie webb konsol jobb information fliken](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Felsöka

Med Oozie-ANVÄNDARGRÄNSSNITTET kan du Visa Oozie-loggar. Oozie-ANVÄNDARGRÄNSSNITTET innehåller också länkar till JobTracker-loggarna för de MapReduce-uppgifter som startades av arbets flödet. Mönstret för fel sökning bör vara:

   1. Visa jobbet i Oozie-webbgränssnittet.

   2. Om det uppstår ett fel eller fel för en speciell åtgärd väljer du åtgärden för att se om **fel meddelande** fältet innehåller mer information om felet.

   3. Om det är tillgängligt använder du URL: en från åtgärden för att visa mer information, till exempel JobTracker-loggar, för åtgärden.

Följande är vissa fel som du kan stöta på och hur du kan lösa dem.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: kan inte initiera kluster

**Symptom**: jobbets status ändras till **pausad**. Information för jobbet visar `RunHiveScript` status som **START_MANUAL**. När du väljer åtgärden visas följande fel meddelande:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Orsak**: de Azure Blob Storage-adresser som används i **Job. XML-** filen innehåller inte lagrings containern eller lagrings konto namnet. Blob Storage-formatet måste vara `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Lösning**: ändra de Blob Storage-adresser som används av jobbet.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie tillåts inte personifiera &lt;användar&gt;

**Symptom**: jobbets status ändras till **pausad**. Information för jobbet visar `RunHiveScript` status som **START_MANUAL**. Om du väljer åtgärden visas följande fel meddelande:

    JA002: User: oozie is not allowed to impersonate <USER>

**Orsak**: de aktuella behörighets inställningarna tillåter inte att Oozie personifierar det angivna användar kontot.

**Lösning**: Oozie kan personifiera användare i gruppen **användare** . Använd `groups USERNAME` för att se de grupper som användar kontot är medlem i. Om användaren inte är medlem i gruppen **användare** använder du följande kommando för att lägga till användaren i gruppen:

    sudo adduser USERNAME users

> [!NOTE]  
> Det kan ta flera minuter innan HDInsight känner av att användaren har lagts till i gruppen.

### <a name="launcher-error-sqoop"></a>Start fel (Sqoop)

**Symptom**: jobbets status ändras till **avlivat**. Information för jobbet visar `RunSqoopExport` status som **fel**. Om du väljer åtgärden visas följande fel meddelande:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Orsak**: Sqoop kan inte läsa in den databas driv rutin som krävs för att komma åt databasen.

**Lösning**: när du använder Sqoop från ett Oozie-jobb måste du inkludera databas driv rutinen med de andra resurserna, t. ex. Workflow. XML, som används av jobbet. Referera också till arkivet som innehåller databas driv rutinen från `<sqoop>...</sqoop>` avsnittet i arbets flödets. xml.

För jobbet i det här dokumentet kan du till exempel använda följande steg:

1. Kopiera `mssql-jdbc-7.0.0.jre8.jar`-filen till **/tutorials/useoozie** -katalogen:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Ändra `workflow.xml` för att lägga till följande XML på en ny rad ovan `</sqoop>`:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du definierar ett Oozie-arbetsflöde och hur du kör ett Oozie-jobb. Mer information om hur du arbetar med HDInsight finns i följande artiklar:

* [Ladda upp data för Apache Hadoop jobb i HDInsight][hdinsight-upload-data]
* [Använda Apache Sqoop med Apache Hadoop i HDInsight][hdinsight-use-sqoop]
* [Använda Apache Hive med Apache Hadoop på HDInsight][hdinsight-use-hive]
* [Utveckla Java MapReduce-program för HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
