---
title: 'Självstudie: mata in övervaknings data i Azure Datautforskaren utan kodrad'
description: I den här självstudien får du lära dig att mata in övervaknings data till Azure Datautforskaren utan en kodrad och fråga dessa data.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 11/17/2019
ms.openlocfilehash: 2574f27b4b86bab276a56f95fda9fa2a1434c095
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995940"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Självstudie: mata in och fråga övervaknings data i Azure Datautforskaren 

I den här självstudien lär du dig att mata in data från diagnostikloggar och aktivitetsloggar till ett Azure Data Explorer-kluster utan att skriva kod. Med den här enkla inmatningsmetoden kan du snabbt börja köra frågor mot Azure Data Explorer för dataanalys.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa tabeller och inmatningsmappning i en Azure Data Explorer-databas.
> * Formatera inmatade data med hjälp av en uppdateringsprincip.
> * Skapa en [händelsehubb](/azure/event-hubs/event-hubs-about) och ansluta den till Azure Data Explorer.
> * Strömma data till en händelsehubben från Azure Monitor [diagnostiska mått och loggar](/azure/azure-monitor/platform/diagnostic-settings) och [aktivitets loggar](/azure/azure-monitor/platform/activity-logs-overview).
> * Köra frågor mot inmatade data med hjälp av Azure Data Explorer.

> [!NOTE]
> Skapa alla resurser på samma Azure-plats eller region. 

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Data Explorer-kluster och en databas](create-cluster-database-portal.md). I den här självstudien är databasnamnet *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Azure Monitor data leverantör: diagnostiska mått och loggar och aktivitets loggar

Visa och förstå de data som tillhandahålls av Azure Monitor diagnostiska mått och loggar och aktivitets loggar nedan. Du skapar en pipeline för inmatning baserat på dessa data scheman. Observera att varje händelse i en logg har en post mat ris. Denna post mat ris kommer att delas senare i självstudien.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Exempel på diagnostiska mått och loggar och aktivitets loggar

Azures diagnostiska mått och loggar och aktivitets loggar genereras av en Azure-tjänst och tillhandahåller information om driften av tjänsten. 

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostiska mått](#tab/diagnostic-metrics)
#### <a name="example"></a>Exempel

Diagnostiska mått sammanställs med en tids kornig het på 1 minut. Följande är ett exempel på ett Azure Datautforskaren Metric-schema för frågans varaktighet:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostikloggar](#tab/diagnostic-logs)
#### <a name="example"></a>Exempel

Följande är ett exempel på en logg för Azure Datautforskaren- [diagnostik](using-diagnostic-logs.md#diagnostic-logs-schema):

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logstabactivity-logs"></a>[Aktivitets loggar](#tab/activity-logs)
#### <a name="example"></a>Exempel

Azure aktivitets loggar är prenumerations nivå loggar som ger inblick i de åtgärder som utförs på resurser i din prenumeration. Följande är ett exempel på en aktivitet – logg händelse för att kontrol lera åtkomst:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Konfigurera en inmatningspipeline i Azure Data Explorer

Konfiguration av en Azure Data Explorer-pipeline omfattar flera steg, däribland [tabellskapande och datainmatning](/azure/data-explorer/ingest-sample-data#ingest-data). Du kan även ändra, mappa och uppdatera data.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Ansluta till webbgränssnittet för Azure Data Explorer

I din Azure Data Explorer-databas *AzureMonitoring* väljer du **Query** (Fråga) för att öppna webbgränssnittet för Azure Data Explorer.

![Frågesida](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Skapa måltabellerna

Strukturen i Azure Monitors loggarna är inte i tabell form. Du kommer att ändra data och expandera varje händelse till en eller flera poster. Rå data matas in i en mellanliggande tabell med namnet *ActivityLogsRawRecords* för aktivitets loggar och *DiagnosticRawRecords* för diagnostiska mått och loggar. Då ändras och expanderas data. Med hjälp av en uppdaterings princip kommer de expanderade data att matas in i tabellen *ActivityLogs* för aktivitets loggar, *DiagnosticMetrics* för diagnostiska mått och *DiagnosticLogs* för diagnostikloggar. Det innebär att du måste skapa två separata tabeller för att mata in aktivitets loggar och tre separata tabeller för att mata in diagnostiska mått och loggar.

Använd webbgränssnittet för Azure Data Explorer till att skapa måltabellerna i Azure Data Explorer-databasen.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostiska mått](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Skapa tabeller för diagnostiska mått

1. I *TestDatabase* -databasen skapar du en tabell med namnet *DiagnosticMetrics* för att lagra diagnostiska mått poster. Ange följande `.create table`-kontrollkommando:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Välj **Kör** för att skapa tabellen.

    ![Kör frågan](media/ingest-data-no-code/run-query.png)

1. Skapa den mellanliggande data tabellen med namnet *DiagnosticRawRecords* i *TestDatabase* -databasen för data manipulation med hjälp av följande fråga. Välj **Kör** för att skapa tabellen.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Ange ingen [bevarande princip](/azure/kusto/management/retention-policy) för den mellanliggande tabellen:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostikloggar](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Skapa tabeller för diagnostikloggar 

1. I *TestDatabase* -databasen skapar du en tabell med namnet *DiagnosticLogs* för att lagra diagnostikloggar. Ange följande `.create table`-kontrollkommando:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Välj **Kör** för att skapa tabellen.

1. Skapa den mellanliggande data tabellen med namnet *DiagnosticRawRecords* i *TestDatabase* -databasen för data manipulation med hjälp av följande fråga. Välj **Kör** för att skapa tabellen.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Ange ingen [bevarande princip](/azure/kusto/management/retention-policy) för den mellanliggande tabellen:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logstabactivity-logs"></a>[Aktivitets loggar](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Skapa tabeller för aktivitets loggarna 

1. Skapa en tabell med namnet *ActivityLogs* i *TestDatabase* -databasen för att ta emot aktivitets logg poster. Skapa tabellen genom att köra följande Azure Data Explorer-fråga:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Skapa den mellanliggande datatabellen med namnet *ActivityLogsRawRecords* i databasen *TestDatabase* för datamanipulering:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Ange ingen [bevarande princip](/azure/kusto/management/retention-policy) för den mellanliggande tabellen:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Skapa tabellmappningar

 Eftersom dataformatet är `json` krävs datamappning. `json`-mappningen matchar varje json-sökväg till ett kolumnnamn i tabellen.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Diagnostiska mått/diagnostikloggar](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Mappa diagnostiska mått och loggar till tabellen

Om du vill mappa diagnostiskt mått och loggdata till tabellen använder du följande fråga:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logstabactivity-logs"></a>[Aktivitets loggar](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Mappa aktivitets loggar till tabellen

Om du vill mappa aktivitets logg data till tabellen använder du följande fråga:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Skapa en uppdaterings princip för mått-och loggdata

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostiska mått](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Skapa data uppdaterings princip för diagnostiska mått

1. Skapa en [funktion](/azure/kusto/management/functions) som utökar insamlingen av diagnostiska mått poster så att varje värde i samlingen får en separat rad. Använd operatorn [`mv-expand`](/azure/kusto/query/mvexpandoperator):
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Lägg till [uppdateringsprincipen](/azure/kusto/concepts/updatepolicy) i måltabellen. Den här principen kör frågan automatiskt på alla nyligen inmatade data i tabellen *DiagnosticRawRecords* mellanliggande data och matar in resultatet i tabellen *DiagnosticMetrics* :

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True"}]'
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostikloggar](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Skapa data uppdaterings princip för diagnostikloggar

1. Skapa en [funktion](/azure/kusto/management/functions) som utökar samlingen av diagnostiska logg poster så att varje värde i samlingen får en separat rad. Du aktiverar inmatnings loggar i ett Azure Datautforskaren-kluster och använder [schemat](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema)för inmatnings loggar. Du skapar en tabell för lyckad och för misslyckad inmatning, medan vissa av fälten är tomma för lyckad inmatning (felkod till exempel). Använd operatorn [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Lägg till [uppdateringsprincipen](/azure/kusto/concepts/updatepolicy) i måltabellen. Den här principen kör frågan automatiskt på alla nyligen inmatade data i tabellen *DiagnosticRawRecords* mellanliggande data och matar in resultatet i tabellen *DiagnosticLogs* :

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True"}]'
    ```

# <a name="activity-logstabactivity-logs"></a>[Aktivitets loggar](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Skapa data uppdaterings princip för aktivitets loggar

1. Skapa en [funktion](/azure/kusto/management/functions) som utökar samlingen av aktivitets logg poster så att varje värde i samlingen får en separat rad. Använd operatorn [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Lägg till [uppdateringsprincipen](/azure/kusto/concepts/updatepolicy) i måltabellen. Den här principen kör frågan automatiskt på alla nyligen inmatade data i tabellen *ActivityLogsRawRecords* mellanliggande data och matar in resultatet i tabellen *ActivityLogs* :

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Skapa en Event Hubs-namnrymd

Med Azure Diagnostic-inställningar kan du exportera mått och loggar till ett lagrings konto eller till en Event Hub. I den här självstudien dirigerar vi mått och loggar via en Event Hub. Du skapar ett Event Hubs-namnområde och en händelsehubben för diagnostiska mått och loggar i följande steg. Azure Monitor skapar händelsehubben *insights-operational-logs* för aktivitetsloggarna.

1. Skapa en händelsehubb med hjälp av en Azure Resource Manager-mall i Azure-portalen. Följ resten av stegen i den här artikeln genom att högerklicka på knappen **Distribuera till Azure** och sedan välja **Öppna i nytt fönster**. Knappen **Distribuera till Azure** tar dig till Azure-portalen.

    [![Knappen Distribuera till Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Skapa en Event Hubs-namnrymd och en händelsehubb för diagnostikloggarna.

    ![Skapande av händelsehubb](media/ingest-data-no-code/event-hub.png)

1. Fyll i formuläret med följande information. För alla inställningar som inte visas i följande tabell använder du standardvärdena.

    **Inställning** | **Föreslaget värde** | **Beskrivning**
    |---|---|---|
    | **Prenumeration** | *Din prenumeration* | Välj den Azure-prenumeration som ska användas för händelsehubben.|
    | **Resursgrupp** | *test-resource-group* | Skapa en ny resursgrupp. |
    | **Plats** | Välj den region som bäst uppfyller dina behov. | Skapa Event Hubs-namnrymden på samma plats som andra resurser.
    | **Namn på namnrymd** | *AzureMonitoringData* | Välj ett unikt namn som identifierar namnområdet.
    | **Namn på händelsehubb** | *DiagnosticData* | Händelsehubben finns under namnområdet, som tillhandahåller en unik omfångscontainer. |
    | **Namn på konsumentgrupp** | *adxpipeline* | Skapa ett konsumentgruppsnamn. Konsumentgrupper gör att flera konsumerande program kan ha en separat vy över händelseströmmen. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Anslut Azure Monitor Mät värden och loggar till händelsehubben

Nu måste du ansluta dina diagnostiska mått och loggar och dina aktivitets loggar till händelsehubben.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Diagnostiska mått/diagnostikloggar](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Ansluta diagnostiska mått och loggar till händelsehubben

Välj en resurs som du vill exportera mått från. Flera resurs typer stöder export av diagnostikdata, inklusive Event Hubs namnrymd, Azure Key Vault, Azure IoT Hub och Azure Datautforskaren-kluster. I den här självstudien använder vi ett Azure Datautforskaren-kluster som vår resurs. vi kommer att gå igenom resultat loggarna för frågor och resultat.

1. Välj ditt Kusto-kluster i Azure-portalen.
1. Välj **diagnostikinställningar** och välj sedan länken **Slå på diagnostik**. 

    ![Diagnostikinställningar](media/ingest-data-no-code/diagnostic-settings.png)

1. Fönstret **Diagnostikinställningar** öppnas. Utför följande steg:
   1. Ge dina diagnostikloggdata namnet *ADXExportedData*.
   1. Under **loggen**markerar du kryss rutorna både **SucceededIngestion** och **FailedIngestion** .
   1. Under **mått**markerar du kryss rutan **Frågeresultat** .
   1. Markera kryssrutan **Strömma till en händelsehubb**.
   1. Välj **Konfigurera**.

      ![Fönstret Diagnostikinställningar](media/ingest-data-no-code/diagnostic-settings-window.png)

1. I fönstret **Välj händelsehubb** konfigurerar du hur data ska exporteras från diagnostikloggar till den händelsehubb som du skapade:
    1. I listan **Select event hub namespace** (Välj namnrymd för händelsehubb) väljer du *AzureMonitoringData*.
    1. I listan **Välj namn på händelsehubben** väljer du *DiagnosticData*.
    1. I listan **Select event hub policy name** (Välj namn på händelsehubbsprincip) väljer du **RootManagerSharedAccessKey**.
    1. Välj **OK**.

1. Välj **Spara**.

# <a name="activity-logstabactivity-logs"></a>[Aktivitets loggar](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Ansluta aktivitetsloggar till händelsehubben

1. På den vänstra menyn i Azure-portalen väljer du **Aktivitetslogg**.
1. Fönstret **Aktivitetslogg** öppnas. Välj **Exportera till händelsehubb**.

    ![Fönstret Aktivitetslogg](media/ingest-data-no-code/activity-log.png)

1. Fönstret **Exportera aktivitetslogg** öppnas:
 
    ![Fönstret Exportera aktivitetslogg](media/ingest-data-no-code/export-activity-log.png)

1. I fönstret **Exportera aktivitetslogg** utför du följande steg:
      1. Välj din prenumeration.
      1. I listan **Regioner** väljer du **Markera alla**.
      1. Markera kryssrutan **Exportera till en händelsehubb**.
      1. Välj **Select a service bus namespace** (Välj en Service Bus-namnrymd) för att öppna fönstret **Välj händelsehubb**.
      1. I fönstret **Välj händelsehubb** väljer du din prenumeration.
      1. I listan **Select event hub namespace** (Välj namnrymd för händelsehubb) väljer du *AzureMonitoringData*.
      1. I listan **Select event hub policy name** (Välj namn på händelsehubbsprincip) väljer du standardnamnet på händelsehubbsprincip.
      1. Välj **OK**.
      1. I fönstrets övre vänstra hörn väljer du **Spara**.
   En händelsehubb med namnet *insights-operational-logs* skapas.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Se data som flödar till dina händelsehubbar

1. Vänta några minuter tills anslutningen har definierats och exporten av aktivitetslogg till händelsehubben är klar. Gå till din Event Hubs-namnrymd för att se de händelsehubbar som du skapade.

    ![Skapade händelsehubbar](media/ingest-data-no-code/event-hubs-created.png)

1. Se data som flödar till din händelsehubb:

    ![Data för händelsehubb](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Ansluta en händelsehubb till Azure Data Explorer

Nu måste du skapa data anslutningarna för dina diagnostiska mått och loggar och aktivitets loggar.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Skapa data anslutningen för diagnostiska mått och loggar och aktivitets loggar

1. I det Azure Data Explorer-kluster som heter *kustodocs* väljer du **Databaser** på den vänstra menyn.
1. I fönstret **Databaser** väljer du databasen *TestDatabase*.
1. Välj **Datainmatning** på menyn till vänster.
1. I fönstret **Datainmatning** klickar du på **+ Lägg till dataanslutning**.
1. I fönstret **Dataanslutning** anger du följande information:

    ![Dataanslutning för händelsehubb](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Diagnostiska mått/diagnostikloggar](#tab/diagnostic-metrics+diagnostic-logs) 

1. Använd följande inställningar i fönstret **Dataanslutning**:

    Datakälla:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | **Namn på dataanslutning** | *DiagnosticsLogsConnection* | Namnet på anslutningen som du vill skapa i Azure Data Explorer.|
    | **Namnrymd för händelsehubb** | *AzureMonitoringData* | Namnet som du valde tidigare, som identifierar ditt namnområde. |
    | **Händelsehubb** | *DiagnosticData* | Händelsehubben som du skapade. |
    | **Konsumentgrupp** | *adxpipeline* | Konsumentgruppen som definierades i hubben som du skapade. |
    | | |

    Måltabell:

    Det finns två alternativ för routning: *statisk* och *dynamisk*. För den här självstudien använder du statisk routning (standardinställning), där du anger tabellnamn, dataformat och mappning. Låt **My data includes routing info** (Mina data innehåller routningsinformation) vara avmarkerad.

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | **Tabell** | *DiagnosticRawRecords* | Den tabell som du skapade i databasen *TestDatabase*. |
    | **Dataformat** | *JSON* | Det format som används i tabellen. |
    | **Kolumnmappning** | *DiagnosticRawRecordsMapping* | Mappningen som du skapade i *TestDatabase* -databasen, som mappar inkommande JSON-data till kolumn namn och data typer i *DiagnosticRawRecords* -tabellen.|
    | | |

1. Välj **Skapa**.  

# <a name="activity-logstabactivity-logs"></a>[Aktivitets loggar](#tab/activity-logs)

1. Använd följande inställningar i fönstret **Dataanslutning**:

    Datakälla:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | **Namn på dataanslutning** | *ActivityLogsConnection* | Namnet på anslutningen som du vill skapa i Azure Data Explorer.|
    | **Namnrymd för händelsehubb** | *AzureMonitoringData* | Namnet som du valde tidigare, som identifierar ditt namnområde. |
    | **Händelsehubb** | *insights-operational-logs* | Händelsehubben som du skapade. |
    | **Konsumentgrupp** | *$Default* | Den förinställda konsumentgruppen. Om det behövs kan du skapa en annan konsumentgrupp. |
    | | |

    Måltabell:

    Det finns två alternativ för routning: *statisk* och *dynamisk*. För den här självstudien använder du statisk routning (standardinställning), där du anger tabellnamn, dataformat och mappning. Låt **My data includes routing info** (Mina data innehåller routningsinformation) vara avmarkerad.

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | **Tabell** | *ActivityLogsRawRecords* | Den tabell som du skapade i databasen *TestDatabase*. |
    | **Dataformat** | *JSON* | Det format som används i tabellen. |
    | **Kolumnmappning** | *ActivityLogsRawRecordsMapping* | Den mappning som du skapade i databasen *TestDatabase*, som mappar inkommande JSON-data till kolumnnamnen och datatyperna i tabellen *ActivityLogsRawRecords*.|
    | | |

1. Välj **Skapa**.  
---

## <a name="query-the-new-tables"></a>Köra frågor mot de nya tabellerna

Du har nu en pipeline med dataflöde. Inmatning via klustret tar 5 minuter som standard, så låt data flöda i några minuter innan du börjar köra frågor.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostiska mått](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Fråga tabellen med diagnostiska mått

I följande fråga analyseras frågans varaktighets data från diagnostiska mått poster i Azure Datautforskaren:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Frågeresultat:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostikloggar](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Fråga tabellen diagnostikloggar

Den här pipelinen skapar inmatningar via en händelsehubben. Du granskar resultatet av dessa inmatningar.
Följande fråga analyserar hur många inmatningar som påförs i en minut, inklusive ett exempel på `Database``Table` och `IngestionSourcePath` för varje intervall:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Frågeresultat:

|   |   |
| --- | --- |
|   |  antal_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logstabactivity-logs"></a>[Aktivitets loggar](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Fråga tabellen aktivitets loggar

Följande fråga analyserar data från aktivitetsloggposter i Azure Data Explorer:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Frågeresultat:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>Nästa steg

* Lär dig att skriva många fler frågor om de data som du har extraherat från Azure Datautforskaren med hjälp av [Skriv frågor för Azure datautforskaren](write-queries.md).
* [Övervaka Azure Datautforskaren-inmatnings åtgärder med hjälp av diagnostikloggar](using-diagnostic-logs.md)
* [Använd mått för att övervaka kluster hälsa](using-metrics.md)
