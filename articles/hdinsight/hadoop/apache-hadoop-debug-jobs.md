---
title: 'Felsöka Apache Hadoop: loggar och fel meddelanden i Azure HDInsight'
description: Lär dig mer om fel meddelanden som du kan få när du administrerar HDInsight med hjälp av PowerShell och vilka steg du kan utföra för att återställa.
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 2e5cb1676670642121caec71a973374063fe4320
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044847"
---
# <a name="analyze-apache-hadoop-logs-in-azure-hdinsight"></a>Analysera Apache Hadoop loggar i Azure HDInsight

Varje Apache Hadoop kluster i Azure HDInsight har ett Azure Storage-konto som används som standard fil system. Lagrings kontot kallas för standard lagrings kontot. Klustret använder Azure Table Storage och Blob Storage på standard lagrings kontot för att lagra loggfilerna.  Information om hur du hittar ett standard lagrings konto för klustret finns i [hantera Apache Hadoop kluster i HDInsight](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts). Loggarna sparas i lagrings kontot även efter att klustret har tagits bort.

## <a name="logs-written-to-azure-tables"></a>Loggar som skrivs till Azure-tabeller

Loggarna som skrivs till Azure-tabeller ger en inblick i vad som händer med ett HDInsight-kluster.

När du skapar ett HDInsight-kluster skapas sex tabeller automatiskt för Linux-baserade kluster i standard tabell lagringen:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Tabell fil namnen är **u\<kluster namn > DDMonYYYYatHHMMSSsss\<TableName >** .

Tabellerna innehåller följande fält:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Värd
* MALoggingHash
* Meddelande
* N
* PreciseTimeStamp
* Roll
* RowIndex
* Klientorganisation
* TIDSSTÄMPEL
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Verktyg för att komma åt loggarna
Det finns många tillgängliga verktyg för att komma åt data i följande tabeller:

* Visual Studio
* Azure Storage Explorer
* Power Query för Excel

#### <a name="use-power-query-for-excel"></a>Använda Power Query för Excel
Power Query kan installeras från [Microsoft Power Query för Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Se hämtnings sidan för system kraven.

**Använd Power Query för att öppna och analysera tjänst loggen**

1. Öppna **Microsoft Excel**.
2. Klicka på **från Azure**på **Power Query** -menyn och klicka sedan på **från Microsoft Azure Table Storage**.
   
    ![HDInsight Hadoop Excel-PowerQuery öppna Azure Table Storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Ange lagrings kontots namn, antingen det korta namnet eller det fullständiga domän namnet.
4. Ange lagrings konto nyckeln. Du ska se en lista över tabeller:
   
    ![HDInsight Hadoop-loggar lagrade i Azure Table Storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Högerklicka på tabellen hadoopservicelog i **navigerings** fönstret och välj **Redigera**. Du ska se fyra kolumner. Du kan också ta bort kolumnerna **partitionsnyckel**, **rad nyckel**och **tidsstämpel** genom att markera dem och sedan klicka på **ta bort kolumner** från alternativen i menyfliksområdet.
6. Klicka på ikonen Expandera i kolumnen innehåll för att välja de kolumner som du vill importera till Excel-kalkylbladet. I den här demonstrationen valde jag TraceLevel och ComponentName: den kan ge mig viss grundläggande information om vilka komponenter som har problem.
   
    ![HDInsight Hadoop-loggar Välj kolumner Excel](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png "HDInsight Hadoop-loggar Välj kolumner Excel")
7. Importera data genom att klicka på **OK** .
8. Välj kolumnerna **TraceLevel**, Role och **ComponentName** och klicka sedan på **Gruppera efter** kontroll i menyfliksområdet.
9. Klicka på **OK** i dialog rutan Gruppera efter
10. Klicka på * * Använd & Close * *.

Nu kan du använda Excel för att filtrera och sortera efter behov. Du kanske vill inkludera andra kolumner (t. ex. meddelande) för att öka detalj nivån i problem när de inträffar, men om du väljer och grupperar de kolumner som beskrivs ovan visas en vettigt bild av vad som händer med Hadoop-tjänster. Samma idé kan tillämpas på tabellerna Setuplog och hadoopinstalllog.

#### <a name="use-visual-studio"></a>Använd Visual Studio
**Använda Visual Studio**

1. Öppna Visual Studio.
2. I menyn **Visa** klickar du på **Cloud Explorer**. Eller klicka bara på **CTRL +\, CTRL + X**.
3. Välj **resurs typer**i **Cloud Explorer**.  De andra tillgängliga alternativen är **resurs grupper**.
4. Expandera **lagrings konton**, standard lagrings kontot för klustret och sedan **tabeller**.
5. Dubbelklicka på **hadoopservicelog**.
6. Lägg till ett filter. Exempel:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop-loggar Välj kolumner vs](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png "HDInsight Hadoop-loggar Välj kolumner vs")
   
    Mer information om hur du skapar filter finns i [Skapa filter strängar för Tabelldesigner](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Loggar som skrivs till Azure Blob Storage

Loggarna som skrivs till Azure-tabeller ger en inblick i vad som händer med ett HDInsight-kluster. Dessa tabeller innehåller dock inte loggar på uppgifts nivå, som kan vara till hjälp vid detalj granskning i problem när de inträffar. För att tillhandahålla den här nästa detalj nivån konfigureras HDInsight-kluster att skriva aktivitets loggar till ditt Blob Storage-konto för alla jobb som skickas via Templeton. Praktiskt taget innebär det att jobb som skickas med hjälp av Microsoft Azure PowerShell-cmdlets eller .NET-jobb sändnings-API: er, inte jobb som skickas via RDP/kommando rads åtkomst till klustret. 

Information om hur du visar loggarna finns i [Access Apache HADOOP garn program loggar på Linux-baserade HDInsight](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

## <a name="view-cluster-health-and-job-logs"></a>Visa kluster hälsa och jobb loggar

### <a name="access-the-ambari-ui"></a>Komma åt Ambari-ANVÄNDARGRÄNSSNITTET

Klicka på ett HDInsight-kluster namn i Azure Portal för att öppna kluster fönstret. I rutan kluster klickar du på **instrument panel**.

![Starta kluster instrument panel för HDInsight](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Få åtkomst till garn gränssnittet
Klicka på ett HDInsight-kluster namn i Azure Portal för att öppna kluster fönstret. I rutan kluster klickar du på **instrument panel**. När du uppmanas till det anger du autentiseringsuppgifter för kluster administratören. I Ambari väljer du **garn** i listan över tjänster till vänster. På sidan som visas väljer du **snabb länkar**och väljer sedan posten Active Head Node och Resource Manager-gränssnittet.

Du kan använda garn gränssnittet för att göra följande:

* **Hämta kluster status**. I den vänstra rutan expanderar du **kluster**och klickar på **om**. Den här informationen om kluster status, t. ex. totalt allokerat minne, kärnor som används, tillstånd för kluster resurs hanteraren, kluster version och så vidare.
  
    ![Instrument panels garn för HDInsight-lansering](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "Instrument panels garn för HDInsight-lansering")
* **Hämta Node-status**. Expandera **kluster**i den vänstra rutan och klicka på **noder**. Här visas alla noder i klustret, HTTP-adress för varje nod, resurser som har allokerats till varje nod osv.
* **Övervaka jobb status**. I den vänstra rutan expanderar du **kluster**och klickar sedan på **program** för att visa alla jobb i klustret. Om du vill titta på jobb i ett särskilt tillstånd (till exempel ny, skickad, körs osv.) klickar du på lämplig länk under **program**. Du kan ytterligare Klicka på jobb namnet för att få mer information om jobbet, till exempel utdata, loggar osv.

### <a name="access-the-hbase-ui"></a>Komma åt HBase-ANVÄNDARGRÄNSSNITTET
Klicka på ett HDInsight HBase-kluster namn i Azure Portal för att öppna kluster fönstret. I rutan kluster klickar du på **instrument panel**. När du uppmanas till det anger du autentiseringsuppgifter för kluster administratören. I Ambari väljer du HBase i listan över tjänster. Välj **snabb länkar** överst på sidan, peka på länken aktiva Zookeeper och klicka sedan på HBase Master UI.

## <a name="hdinsight-error-codes"></a>Fel koder för HDInsight
Fel meddelandena som beskrivs i det här avsnittet ges för att hjälpa användare av Hadoop i Azure HDInsight att förstå möjliga fel villkor som de kan stöta på när de administrerar tjänsten med hjälp av Azure PowerShell och för att meddela dem om de steg som kan vidtas för att återställa från felet.

Vissa av dessa fel meddelanden kan också visas i Azure Portal när det används för att hantera HDInsight-kluster. Men andra fel meddelanden du kan stöta på är mindre detaljerade på grund av begränsningarna för de åtgärder som kan utföras i den här kontexten. Det finns andra fel meddelanden i de kontexter där minskningen är uppenbar. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Beskrivning**: Ange Azure SQL Database-information för minst en komponent för att kunna använda anpassade inställningar för Hive-och Oozie-metastores.
* **Minskning**: användaren måste ange ett giltigt SQL Azure-metaarkiv och försöka utföra begäran igen.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Beskrivning**: det gick inte att skapa kluster i regionen *nameOfYourRegion*. Använd en giltig HDInsight-region och försök igen.
* **Minskning**: kunden bör skapa den kluster region som för närvarande stöder dem: Sydostasien, Västeuropa, norra Europa, östra USA eller västra USA.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Beskrivning**: det gick inte att hitta den begärda kluster posten i servern.  
* **Minskning**: försök igen.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Beskrivning**: KLUSTRETS DNS-namn *yourDnsName* är ogiltigt. Se till att namn börjar och slutar med alfanumeriska tecken och bara får innehålla specialtecken  
* Lösning **: kontrol**lera att du har använt ett giltigt DNS-namn för klustret som börjar och slutar med alfanumeriska tecken och som inte innehåller några specialtecken än bindestrecket-och försök sedan igen.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Beskrivning**: kluster namnet *yourClusterName* är inte tillgängligt. Välj ett annat namn.  
* **Minskning**: användaren bör ange ett kluster namn som är unikt och som inte finns och försöker igen. Om användaren använder portalen meddelar användar gränssnittet om ett kluster namn redan används under steget Skapa.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Beskrivning**: kluster lösen ordet är ogiltigt. Lösen ordet måste bestå av minst 10 tecken och måste innehålla minst en siffra, versal bokstav, gemen bokstav och specialtecken utan blank steg och får inte innehålla användar namnet som en del av det.  
* **Minskning**: Ange ett giltigt kluster lösen ord och försök igen.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Beskrivning**: klustrets användar namn är ogiltigt. Kontrol lera att användar namnet inte innehåller specialtecken eller blank steg.  
* **Minskning**: Ange ett giltigt kluster användar namn och försök igen.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Beskrivning**: KLUSTRETS DNS-namn *yourDnsClusterName* är ogiltigt. Se till att namn börjar och slutar med alfanumeriska tecken och bara får innehålla specialtecken  
* **Minskning**: Ange ett giltigt DNS-kluster användar namn och försök igen.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Beskrivning**: container namnet i URI- *yourcontainerURI* och DNS-namnet *yourDnsName* i begär ande texten måste vara detsamma.  
* **Minskning**: kontrol lera att behållar namnet och ditt DNS-namn är desamma och försök igen.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Beskrivning**: ogiltig kluster konfiguration. Det gick inte att hitta några data Node-definitioner i Node-storlek.  
* **Minskning**: försök igen.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Beskrivning**: det gick inte att ta bort distributionen för klustret  
* **Minskning**: försök att ta bort igen.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Beskrivning**: tjänst konfigurations fel. Det gick inte att hitta nödvändig DNS-mappnings information.  
* **Minskning**: ta bort kluster och skapa ett nytt kluster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Beskrivning**: det gick inte att skapa flera kluster behållare. Det finns en post för *nameOfYourContainer* men ETags stämmer inte överens.
* **Minskning**: Ange ett unikt namn för behållaren och försök att skapa igen.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Beskrivning**: den värdbaserade tjänstens *nameOfYourHostedService* innehåller redan ett kluster. En värdbaserad tjänst kan inte innehålla flera kluster  
* **Minskning**: var värd för klustret i en annan värdbaserad tjänst.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Beskrivning**: servern kunde inte uppdatera kluster distributionens status.  
* **Minskning**: försök igen. Om detta inträffar flera gånger, kontakta CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Beskrivning**: klustrets *yourClusterName* togs bort som en del av underhållet. Återskapa klustret.
* **Minskning**: återskapa klustret.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Beskrivning**: ogiltig kluster konfiguration. Nödvändig huvudnode-konfiguration hittades inte i Node-storlekar.
* **Minskning**: försök igen.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Beskrivning**: det gick inte att skapa den värdbaserade tjänsten *nameOfYourHostedService*. Försök att begära igen.  
* **Minskning**: försök utföra begäran igen.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Beskrivning**: värdbaserad service- *nameOfYourHostedService* har redan en produktions distribution. En värdbaserad tjänst kan inte innehålla flera produktions distributioner. Gör om begäran med ett annat kluster namn.
* **Minskning**: Använd ett annat kluster namn och försök utföra begäran igen.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Beskrivning**: det gick inte att hitta den värdbaserade tjänstens *nameOfYourHostedService* för klustret.  
* **Minskning**: om klustret har fel tillstånd tar du bort det och försöker sedan igen.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Beskrivning**: värdbaserade tjänste- *nameOfYourHostedService* har ingen kopplad distribution.  
* **Minskning**: om klustret har fel tillstånd tar du bort det och försöker sedan igen.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Beskrivning**: SubscriptionId- *yourSubscriptionId* har inte några kärnor kvar för att skapa kluster *yourClusterName*. Krävs: *resourcesRequired*, available: *resourcesAvailable*.  
* **Minskning**: frigör resurser i din prenumeration eller öka resurserna som är tillgängliga för prenumerationen och försök att skapa klustret igen.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Beskrivning**: PRENUMERATIONS-ID *yourSubscriptionId* saknar kvot för en ny HostedService för att skapa kluster *yourClusterName*.  
* **Minskning**: frigör resurser i din prenumeration eller öka resurserna som är tillgängliga för prenumerationen och försök att skapa klustret igen.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Beskrivning**: servern påträffade ett internt fel. Försök att begära igen.  
* **Minskning**: försök utföra begäran igen.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Beskrivning**: Azure Storage platsens *dataRegionName* är inte en giltig plats. Se till att regionen är korrekt och försök igen.
* **Minskning**: Välj en lagrings plats som har stöd för HDInsight, kontrol lera att klustret är Samplacerat och försök sedan igen.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Beskrivning**: ogiltig VM-storlek för datanoder. Endast storleken på stor virtuell dator stöds för alla datanoder.  
* **Minskning**: Ange den Node-storlek som stöds för datanoden och försök igen.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Beskrivning**: ogiltig storlek på virtuell dator för Head-noden. Endast storleken ExtraLarge VM stöds för Head-noden.  
* **Minskning**: Ange den nods storlek som stöds för Head-noden och försök igen

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Beskrivning**: PRENUMERATIONS-ID *yourSubscriptionId* som används har inte tillräcklig behörighet för att köra borttagnings åtgärden för kluster *yourClusterName*.  
* **Minskning**: om klustret har fel tillstånd tar du bort det och försöker sedan igen.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Beskrivning**: det externa lagrings kontots BLOB container name *yourContainerName* är ogiltigt. Se till att namnet börjar med en bokstav och bara innehåller små bokstäver, siffror och bindestreck.  
* **Minskning**: Ange ett giltigt namn på BLOB-containern för lagrings kontot och försök igen.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Beskrivning**: konfigurationen för det externa lagrings kontot *yourStorageAccountName* krävs för att information om hemliga nycklar ska kunna anges.  
* **Minskning**: Ange en giltig hemlig nyckel för lagrings kontot och försök igen.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Beskrivning**: versions huvudets *yourVersionHeader* har ett ogiltigt format av åååå-mm-dd.  
* **Minskning**: Ange ett giltigt format för versions huvudet och försök igen.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Beskrivning**: ogiltig kluster konfiguration. Fler än en konfiguration för huvudnoder hittades.  
* **Minskning**: redigera konfigurationen så att endast en huvudnod anges.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Beskrivning**: åtgärden kunde inte slutföras inom den tillåtna tiden eller de maximalt tillåtna försöken. Försök att begära igen.  
* **Minskning**: försök utföra begäran igen.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Beskrivning**: parametern *yourParameterName* får inte vara null eller tom.  
* **Minskning**: Ange ett giltigt värde för parametern.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Beskrivning**: en eller flera av de begärda indata för klustret är inte giltiga. Kontrol lera att indatavärdena är korrekta och försök igen.  
* **Minskning**: kontrol lera att indatavärdena är korrekta och försök igen.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Beskrivning**: regions funktionen är inte tillgänglig för regions- *yourRegionName* och prenumerations-ID *yourSubscriptionId*.  
* **Minskning**: Ange en region som stöder HDInsight-kluster. De offentligt stödda regionerna är: Sydostasien, Västeuropa, norra Europa, östra USA eller västra USA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Beskrivning**: lagrings kontots *yourStorageAccountName* finns i regionen *currentRegionName*. Den bör vara samma som kluster regionen *yourClusterRegionName*.  
* **Minskning**: Ange antingen ett lagrings konto i samma region som klustret finns i eller om dina data redan finns i lagrings kontot, skapar du ett nytt kluster i samma region som det befintliga lagrings kontot. Om du använder portalen kommer användar gränssnittet att meddela dem om det här problemet i förväg.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Beskrivning**: angivet PRENUMERATIONS-ID *yourSubscriptionId* är inte aktivt.  
* **Minskning**: återaktivera din prenumeration eller skaffa en ny giltig prenumeration.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Beskrivning**: det gick inte att hitta PRENUMERATIONS-ID *yourSubscriptionId* .  
* **Minskning**: kontrol lera att ditt PRENUMERATIONS-ID är giltigt och försök igen.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Beskrivning**: det gick inte att matcha DNS- *yourDnsUrl*. Kontrol lera att den fullständiga URL: en för BLOB-slutpunkten har angetts.  
* **Minskning**: Ange en giltig BLOB-URL. URL: en måste vara fullständigt giltig, inklusive från och med *http://* och slutar med *. com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Beskrivning**: det gick inte att verifiera platsen för resurs *yourDnsUrl*. Kontrol lera att den fullständiga URL: en för BLOB-slutpunkten har angetts.  
* **Minskning**: Ange en giltig BLOB-URL. URL: en måste vara fullständigt giltig, inklusive från och med *http://* och slutar med *. com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Beskrivning**: versions kapacitet är inte tillgängligt för version *specifiedVersion* och prenumerations-ID *yourSubscriptionId*.  
* **Minskning**: Välj en version som är tillgänglig och försök igen.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Beskrivning**: version *specifiedVersion* stöds inte.
* **Minskning**: Välj en version som stöds och försök sedan igen.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Beskrivning**: version *specifiedVersion* är inte tillgänglig i Azure-regionen *specifiedRegion*.  
* **Minskning**: Välj en version som stöds i den angivna regionen och försök sedan igen.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Beskrivning**: ogiltig kluster konfiguration. Nödvändig WASB konto konfiguration hittades inte i externa konton.  
* **Minskning**: kontrol lera att kontot finns och att det har angetts korrekt i konfigurationen och försök sedan igen.

## <a name="next-steps"></a>Nästa steg

* [Aktivera heap-dum par för Apache Hadoop tjänster på Linux-baserade HDInsight](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Hantera HDInsight-kluster med hjälp av Apache Ambari-webbgränssnittet](../hdinsight-hadoop-manage-ambari.md)
