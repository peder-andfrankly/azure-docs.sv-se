---
title: Skapa Apache Hadoop-kluster med hjälp av webbläsare, Azure HDInsight
description: Lär dig hur du skapar Apache Hadoop, Apache HBase, Apache Storm eller Apache Spark kluster på Linux för HDInsight med hjälp av en webbläsare och Azure Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215878"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal är ett webbaserat hanterings verktyg för tjänster och resurser som finns i Microsoft Azure molnet. I den här artikeln får du lära dig hur du skapar Linux-baserade Azure HDInsight-kluster med hjälp av portalen.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [hur du får en kostnads fri utvärderings version av Azure i HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **En modern webbläsare**. Azure Portal använder HTML5 och Java Script. Det kanske inte fungerar korrekt i äldre webbläsare.

## <a name="create-clusters"></a>Skapa kluster

Azure Portal visar de flesta av kluster egenskaperna. Med hjälp av Azure Resource Manager mallar kan du dölja många detaljer. Mer information finns i [skapa Apache Hadoop kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Logga in på [Azure Portal](https://portal.azure.com).

1. På den vänstra menyn navigerar du till **+ skapa en resurs** >  **Analytics** > **Azure HDInsight**.

    ![Skapa ett nytt kluster i Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Skapa ett nytt kluster i Azure Portal")

1. På sidan **skapa HDInsight-kluster** väljer **du gå till klassisk skapa upplevelse**.

    ![Gå till klassisk skapa upplevelse](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. På **HDInsight** -sidan väljer du **Anpassad (storlek, inställningar, appar)** .

1. Välj **1 grunder**. Ange följande information:

    |Egenskap |Beskrivning |
    |---|---|
    |Klusternamn|Det här namnet måste vara globalt unikt.|
    |Prenumeration|I list rutan väljer du den Azure-prenumeration som används för klustret.|
    |Klustertyp|Välj den typ av kluster som du vill skapa. Exempel är Hadoop och Apache Spark. **Operativ systemet** är **Linux**. Välj sedan en kluster typ version. Använd standard versionen om du inte vet vad du ska välja. Mer information finns i [HDInsight-klusterversioner](hdinsight-component-versioning.md).|
    |Användarnamn för klusterinloggning|Ange användar namnet, standard är **administratör**.|
    |Lösenord för klusterinloggning|Ange lösen ordet.|
    |Secure Shell (SSH)-användarnamn|Standardvärdet är **sshuser**. Om du vill använda samma SSH-lösenord som administratörs lösen ord som du angav tidigare, markerar du kryss rutan **Använd lösen ord för kluster inloggning för SSH** . Om inte, anger du antingen ett **lösen ord** eller en **offentlig nyckel** för att autentisera SSH-användaren. En offentlig nyckel är den metod vi rekommenderar. Välj **Välj** längst ned för att spara konfigurationen av autentiseringsuppgifter.  Mer information finns i [ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Resursgrupp|Ange om du vill skapa en ny resursgrupp eller använda en befintlig.|
    |Plats|Ange ett Data Center där klustret skapas.|

    ![Grunderna för HDInsight skapa kluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Skapa ett nytt kluster i Azure Portal")

    > [!IMPORTANT]  
    > HDInsight-kluster ingår i olika typer. De motsvarar den arbets belastning eller teknik som klustret är anpassat för. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer. Exempel är storm och HBase på ett kluster.

    Välj **Nästa** för att gå till nästa sida.

1. Från **2 säkerhet och nätverk**kan du ansluta klustret till ett virtuellt nätverk med hjälp av den nedrullningsbara menyn. Välj ett virtuellt Azure-nätverk och under nätet om du vill placera klustret i ett virtuellt nätverk. Information om hur du använder HDInsight med ett virtuellt nätverk finns i [Planera en virtuell nätverks distribution för Azure HDInsight-kluster](hdinsight-plan-virtual-network-deployment.md). Artikeln innehåller särskilda konfigurations krav för det virtuella nätverket.

    Följ dessa anvisningar om du vill använda **Enterprise Security Package**: [Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Välj **Nästa** för att gå till nästa sida.

1. Ange om du vill Azure Storage eller Azure Data Lake Storage som standard lagring för **lagrings konto inställningar**från **3 lagrings**enheter. Mer information finns i följande tabell.

    | Primär lagrings typ | Beskrivning |
    |------------------|-------------|
    | Azure Storage   |  * För **urvals metod**väljer du **Mina prenumerationer** om du vill ange ett lagrings konto som tillhör din Azure-prenumeration. Välj sedan lagrings kontot. Annars väljer du **åtkomst nyckel**. Ange sedan informationen för det lagrings konto som du vill välja utifrån utanför Azure-prenumerationen.</br></br> * För **standard container**väljer du standard behållar namnet som föreslås av portalen eller anger din egen.</br></br> * Om Azure Blob Storage är standard lagringen kan du också välja **ytterligare lagrings konton** för att ange ytterligare lagrings konton som ska associeras med klustret. Välj **Lägg till en lagrings nyckel**för **Azure Storage nycklar**. Sedan kan du ange ett lagrings konto från dina Azure-prenumerationer eller från andra prenumerationer. Ange lagrings kontots åtkomst nyckel.</br></br> * Om Blob Storage är standard lagringen kan du också välja **data Lake Storage åtkomst** för att ange Azure Data Lake Storage som ytterligare lagrings utrymme. Mer information finns i [snabb start: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
    | Azure Data Lake Storage | Välj **Azure Data Lake Storage gen1** eller **Azure Data Lake Storage Gen2**. Läs sedan artikeln [snabb start: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) för instruktioner. |

    **Metaarkiv-inställningar (valfritt)**

    Som ett alternativ anger du en SQL-databas för att spara Apache Hive och Apache Oozie-metadata som är associerade med klustret. Välj en SQL-databas för **Välj en SQL-databas för Hive**. Ange sedan användar namn och lösen ord för databasen. Upprepa de här stegen för Oozie metadata.

    Några saker att tänka på när du använder Azure SQL Database för metastores är följande:
    * Den Azure SQL-databas som används för metaarkiv måste tillåta anslutning till andra Azure-tjänster, inklusive Azure HDInsight. Välj Server namnet på höger sida av Azure SQL Database-instrumentpanelen. Den här servern är den som SQL Database-instansen körs på. När du är i vyn server väljer du **Konfigurera**. Välj sedan **Ja**för **Azure-tjänster**. Välj sedan **Spara**.
    * När du skapar en metaarkiv ska du inte namnge en databas med bindestreck eller bindestreck. Dessa tecken kan orsaka att klustrets skapande process kraschar.

    ![HDInsight skapa kluster lagring](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Skapa ett nytt kluster i Azure Portal")

    > [!WARNING]  
    > Det finns inte stöd för att använda ett ytterligare lagrings konto på en annan plats än HDInsight-klustret.

    Välj **Nästa** för att gå till nästa sida.

1. Från **4 program (valfritt)** väljer du de program som du vill använda. Microsoft, oberoende program varu leverantörer (ISV), eller så kan du utveckla dessa program. Mer information finns i [installera program när klustret skapas](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Välj **Nästa** för att gå till nästa sida.

1. **5 kluster storlek** visar information om noderna som används för det här klustret. Ange antalet arbetsnoder som du behöver för klustret. Den uppskattade kostnaden för att köra klustret visas också.

    ![HDInsight skapa klusternoder](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Ange antal klusternoder")

   > [!IMPORTANT]  
   > Om du planerar över 32 arbetsnoder väljer du en huvudnods storlek med minst åtta kärnor och 14 GB RAM. Planera noderna antingen vid skapande av kluster eller genom att skala klustret när det har skapats.
   >
   > Mer information om Node-storlekar och tillhör ande kostnader finns i [priser för Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Välj **Nästa** för att gå till nästa sida.

1. Från **6 skript åtgärder**kan du anpassa ett kluster för att installera anpassade komponenter. Det här alternativet fungerar om du vill använda ett anpassat skript för att anpassa ett kluster, eftersom klustret skapas. Mer information om skript åtgärder finns i [Anpassa Linux-baserade HDInsight-kluster med hjälp av skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).

   Välj **Nästa** för att gå till nästa sida.

1. Från **7 Sammanfattning**kontrollerar du den information som du angav tidigare. Välj sedan **Skapa**.

    ![HDInsight skapa kluster Sammanfattning](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Ange antal klusternoder")

    > [!NOTE]  
    > Det tar lite tid att skapa klustret, normalt cirka 20 minuter. Övervaka **aviseringar** för att kontrol lera etablerings processen.

1. När processen har skapats väljer **du gå till resurs** från meddelandet **distributionen har slutförts** . Kluster fönstret innehåller följande information.

    ![Översikt över HDI Azure Portal-kluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Kluster egenskaper")

    Några av ikonerna i fönstret förklaras på följande sätt:

    |Egenskap | Beskrivning |
    |---|---|
    |Översikt|Innehåller all viktig information om klustret. Exempel är namnet, resurs gruppen som den tillhör, platsen, operativ systemet och URL: en för kluster instrument panelen.|
    |Klusterinstrumentpaneler|Dirigerar dig till Ambari-portalen som är kopplad till klustret.|
    |SSH + kluster inloggning|Ger information som krävs för att få åtkomst till klustret med hjälp av SSH.|
    |Ta bort|Tar bort HDInsight-klustret.|

## <a name="customize-clusters"></a>Anpassa kluster

* [Anpassa HDInsight-kluster med hjälp av bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Anpassa Linux-baserade HDInsight-kluster med hjälp av skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Nästa steg

Du har skapat ett HDInsight-kluster. Lär dig nu hur du arbetar med klustret.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm kluster

* [Utveckla Java-topologier för Apache Storm i HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark kluster

* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärr anslutning på ett Apache Spark kluster med Apache livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](spark/apache-spark-machine-learning-mllib-ipython.md)
