---
title: HBase hbck returnerar inkonsekvenser i Azure HDInsight
description: HBase hbck returnerar inkonsekvenser i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 5fc338e83c172e26d621ef89dcfb047d01d510fa
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091677"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scenario: `hbase hbck` kommandot returnerar inkonsekvenser i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue-region-is-not-in-hbasemeta"></a>Ärende: Regionen är inte i`hbase:meta`

Region XXX on HDFS, men inte listad `hbase:meta` i eller distribuerad på någon region Server.

### <a name="cause"></a>Orsak

Sig.

### <a name="resolution"></a>Lösning

1. Korrigera meta-tabellen genom att köra:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Tilldela regioner till RegionServers genom att köra:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Ärende: Regionen är offline

Regionen XXX har inte distribuerats på någon RegionServer. Det innebär att regionen är i `hbase:meta`, men offline.

### <a name="cause"></a>Orsak

Sig.

### <a name="resolution"></a>Lösning

Ta med regioner online genom att köra:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Ärende: Regioner har samma start-/slut nycklar

### <a name="cause"></a>Orsak

Sig.

### <a name="resolution"></a>Lösning

Sammanfoga de överlappande regionerna manuellt. Gå till avsnittet HBase HMaster Web UI Table, Välj den tabell länk som har problemet. Du kommer att se start nyckel/slut nyckel för varje region som tillhör tabellen. Sammanfoga sedan de överlappande regionerna. I HBase-gränssnittet gör `merge_region 'xxxxxxxx','yyyyyyy', true`du. Exempel:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

I det här scenariot måste du sammanfoga regiona-och RegionC och få en region med samma nyckel intervall som RegionB, och sedan sammanfoga RegionB och region. xxxxxxx och Yyyyyy är hash-strängen i slutet av varje region namn. Var noga med att inte slå samman två diskontinuerliga regioner. Efter varje sammanslagning, t. ex. sammanfoga A och C, kommer HBase att starta en komprimering på region. Vänta tills komprimeringen har slutförts innan du gör en annan sammanslagning med region. Du hittar komprimerings statusen på den region Server sidan i HBase HMaster UI.

---

## <a name="issue-cant-load-regioninfo"></a>Ärende: Kan inte läsa in`.regioninfo`

Det går `.regioninfo` inte att `/hbase/data/default/tablex/regiony`läsa in region.

### <a name="cause"></a>Orsak

Detta beror troligen på att regionen är delvis borttagen när RegionServer kraschar eller omstarter av virtuella datorer. För närvarande är Azure Storage ett flat BLOB File-System och vissa fil åtgärder är inte atomiska.

### <a name="resolution"></a>Lösning

Rensa de återstående filerna och mapparna manuellt:

1. Kör `hdfs dfs -ls /hbase/data/default/tablex/regiony` för att kontrol lera vilka mappar/filer som finns kvar under den.

1. Kör `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` för att ta bort alla underordnade filer/mappar

1. Kör `hdfs dfs -rmr /hbase/data/default/tablex/regiony` för att ta bort mappen region.

---

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
