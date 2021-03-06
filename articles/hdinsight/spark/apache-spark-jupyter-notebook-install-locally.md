---
title: Installera Jupyter lokalt och ansluta till Spark i Azure HDInsight
description: Lär dig hur du installerar Jupyter Notebook lokalt på din dator och ansluter den till ett Apache Spark-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 225ee7028b9610a4974f9bee05da667d78d3355e
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903739"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installera Jupyter Notebook på din dator och Anslut till Apache Spark på HDInsight

I den här artikeln får du lära dig hur du installerar Jupyter Notebook med anpassade PySpark (för python) och Apache Spark (för Scala) kernel med Spark Magic och ansluter antecknings boken till ett HDInsight-kluster. Det kan finnas flera skäl att installera Jupyter på din lokala dator, och det kan också finnas vissa utmaningar. Mer information finns i avsnittet [Varför ska jag installera Jupyter på](#why-should-i-install-jupyter-on-my-computer) den här datorn i slutet av den här artikeln.

Det finns fyra viktiga steg när du installerar Jupyter och ansluter till Apache Spark i HDInsight.

* Konfigurera Spark-kluster.
* Installera Jupyter Notebook.
* Installera PySpark-och Spark-kernel med Spark Magic.
* Konfigurera Spark Magic för att få åtkomst till Spark-kluster i HDInsight.

Mer information om anpassade kärnor och Spark Magic tillgängligt för Jupyter Notebooks med HDInsight-kluster finns i [kernels som är tillgängliga för Jupyter-anteckningsböcker med Apache Spark Linux-kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md). Detta är en förutsättning för att ansluta Jupyter Notebook till ett HDInsight-kluster när antecknings boken har installerats.

* Kunskaper om Jupyter Notebooks med Spark på HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Installera Jupyter Notebook på datorn

Du måste installera python innan du kan installera Jupyter-anteckningsböcker. [Anaconda-distributionen](https://www.anaconda.com/download/) kommer att installera både, Python och Jupyter Notebook.

Ladda ned installations programmet för [Anaconda](https://www.anaconda.com/download/) för din plattform och kör installationen. När du kör installations guiden kontrollerar du att du väljer alternativet för att lägga till Anaconda i din PATH-variabel.  Se även [Installera Jupyter med hjälp av AnaConDa](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Installera Spark Magic

1. Ange ett av kommandona nedan för att installera Spark Magic. Se även [sparkmagic-dokumentationen](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Kluster version | Installations kommando |
    |---|---|
    |v 3.6 och v 3.5 |`pip install sparkmagic==0.13.1`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. Se till att `ipywidgets` är korrekt installerat genom att köra följande kommando:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Installera PySpark-och Spark-kärnor

1. Identifiera var `sparkmagic` installeras genom att ange följande kommando:

    ```cmd
    pip show sparkmagic
    ```

    Ändra sedan arbets katalogen till den plats som identifierades med kommandot ovan.

1. Från din nya arbets katalog anger du ett eller flera av kommandona nedan för att installera önskade kernel (er):

    |Kernellägestid | Kommando |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Valfri. Ange kommandot nedan för att aktivera server tillägget:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurera Spark Magic för att ansluta till HDInsight Spark-kluster

I det här avsnittet konfigurerar du Spark Magic som du installerade tidigare för att ansluta till ett Apache Spark-kluster.

1. Starta python-gränssnittet med följande kommando:

    ```cmd
    python
    ```

2. Konfigurations informationen för Jupyter lagras vanligt vis i användarens arbets katalog. Ange följande kommando för att identifiera arbets katalogen och skapa en mapp med namnet **. sparkmagic**.  Den fullständiga sökvägen kommer att returneras.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. I mappen `.sparkmagic`skapar du en fil med namnet **config. JSON** och lägger till följande JSON-kodfragment inuti den.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Gör följande ändringar i filen:

    |Mall-värde | Nytt värde |
    |---|---|
    |ANVÄNDAR|Kluster inloggning, standard är `admin`.|
    |CLUSTERDNSNAME|Klusternamn|
    |{BASE64ENCODEDPASSWORD}|Ett base64-kodat lösen ord för det faktiska lösen ordet.  Du kan generera ett base64-lösenord på [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Behåll om du använder `sparkmagic 0.12.7` (kluster v 3.5 och v 3.6).  Om du använder `sparkmagic 0.2.3` (kluster v 3.4) ersätter du med `"should_heartbeat": true`.|

    Du kan se en fullständig exempel fil vid [exempel config. JSON](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Pulsslag skickas för att säkerställa att sessioner inte läcker. När en dator försätts i vilo läge eller stängs av skickas inte pulsslaget, vilket leder till att sessionen rensas. För kluster v 3.4, om du vill inaktivera det här beteendet, kan du ange livy-konfigurations `livy.server.interactive.heartbeat.timeout` att `0` från användar gränssnittet för Ambari. För kluster som är v 3.5, om du inte ställer in 3,5-konfigurationen ovan, tas sessionen inte bort.

5. Starta Jupyter. Använd följande kommando från kommando tolken.

    ```cmd
    jupyter notebook
    ```

6. Kontrol lera att du kan använda Spark Magic tillgängligt med kernels. Utför följande steg.

    a. Skapa en ny anteckningsbok. Välj **ny**i det högra hörnet. Du bör se standard kerneln **python 2** eller **python 3** och de kerneler som du har installerat. De faktiska värdena kan variera beroende på dina installations val.  Välj **PySpark**.

    ![Tillgängliga kärnor i Jupyter Notebook](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kärnor i Jupyter Notebook")

    > [!IMPORTANT]  
    > När du har valt **ny** granska ditt gränssnitt för fel.  Om du ser felet `TypeError: __init__() got an unexpected keyword argument 'io_loop'` kanske du råkat ut för ett känt problem med vissa versioner av storm.  Stoppa i så fall kerneln och nedgradera sedan Storm-installationen med följande kommando: `pip install tornado==4.5.3`.

    b. Kör följande kodfragment.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Om du kan hämta utdata testas anslutningen till HDInsight-klustret.

    Om du vill uppdatera Notebook-konfigurationen för att ansluta till ett annat kluster uppdaterar du config. JSON med den nya värde uppsättningen, som du ser i steg 3 ovan.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Varför ska jag installera Jupyter på datorn?

Det kan finnas flera orsaker till varför du kanske vill installera Jupyter på datorn och sedan ansluta den till ett Apache Spark kluster i HDInsight.

* Även om Jupyter Notebooks redan är tillgängliga i Spark-klustret i Azure HDInsight, ger det dig möjligheten att skapa dina antecknings böcker lokalt, testa programmet mot ett kluster som körs och sedan ladda upp antecknings böcker till klustret. Om du vill ladda upp antecknings böckerna till klustret kan du antingen överföra dem med hjälp av Jupyter Notebook som körs eller klustret, eller spara dem i mappen/HdiNotebooks i det lagrings konto som är associerat med klustret. Mer information om hur antecknings böcker lagras i klustret finns i [var är Jupyter Notebooks lagrade](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Med antecknings böcker som är tillgängliga lokalt kan du ansluta till olika Spark-kluster baserat på ditt program krav.
* Du kan använda GitHub för att implementera ett käll kontroll system och har versions kontroll för antecknings böckerna. Du kan också ha en samarbets miljö där flera användare kan arbeta med samma bärbara dator.
* Du kan arbeta med antecknings böcker lokalt utan att ens ha ett kluster. Du behöver bara ett kluster för att testa dina antecknings böcker mot, inte för att manuellt hantera dina antecknings böcker eller utvecklings miljöer.
* Det kan vara lättare att konfigurera en egen lokal utvecklings miljö än att konfigurera Jupyter-installationen på klustret.  Du kan dra nytta av all program vara som du har installerat lokalt utan att konfigurera ett eller flera fjärranslutna kluster.

> [!WARNING]  
> När Jupyter är installerat på den lokala datorn kan flera användare köra samma bärbara dator i samma Spark-kluster på samma tid. I en sådan situation skapas flera livy-sessioner. Om du stöter på ett problem och vill felsöka det, är det en komplex uppgift för att spåra vilken livy-session som tillhör vilken användare.  

## <a name="next-steps"></a>Nästa steg

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)
* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
