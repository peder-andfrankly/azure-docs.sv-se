---
title: Transformera data med hjälp av aktiviteten lagrad procedur
description: Förklarar hur du använder SQL Server lagrade procedur aktiviteter för att anropa en lagrad procedur i ett Azure SQL Database/informations lager från en Data Factory pipeline.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: 4a0709b4eaa8742069eecb4c39712e384645304b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926666"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformera data med hjälp av aktiviteten SQL Server lagrad procedur i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-stored-proc-activity.md)
> * [Aktuell version](transform-data-using-stored-procedure.md)

Du använder data omvandlings aktiviteter i en Data Factory [pipeline](concepts-pipelines-activities.md) för att transformera och bearbeta rå data i förutsägelser och insikter. Den lagrade procedur aktiviteten är en av de omvandlings aktiviteter som Data Factory stöder. Den här artikeln bygger på artikeln [transformera data](transform-data.md) som visar en översikt över Datatransformeringen och de omvandlings aktiviteter som stöds i Data Factory.

> [!NOTE]
> Om du är nybörjare på Azure Data Factory läser du [introduktionen till Azure Data Factory](introduction.md) och gör självstudien: [Självstudier: transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

Du kan använda den lagrade procedur aktiviteten för att anropa en lagrad procedur i något av följande data lager i företaget eller på en virtuell Azure-dator (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server databas.  Om du använder SQL Server ska du installera integration runtime med egen värd på samma dator som är värd för databasen eller på en annan dator som har åtkomst till databasen. Integration runtime med egen värd är en komponent som ansluter data källor lokalt/på virtuella Azure-datorer med moln tjänster på ett säkert och hanterat sätt. Mer information finns i artikeln om [integration runtime med egen värd](create-self-hosted-integration-runtime.md) .

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server kan du konfigurera **SqlSink** i kopierings aktiviteten så att en lagrad procedur anropas med hjälp av egenskapen **sqlWriterStoredProcedureName** . Mer information om egenskapen finns i följande artiklar om koppling: [Azure SQL Database](connector-azure-sql-database.md) [SQL Server](connector-sql-server.md). Det går inte att anropa en lagrad procedur medan data kopieras till en Azure SQL Data Warehouse med hjälp av en kopierings aktivitet. Men du kan använda den lagrade procedur aktiviteten för att anropa en lagrad procedur i en SQL Data Warehouse. 
>
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure SQL Data Warehouse kan du konfigurera **SqlSource** i kopierings aktivitet för att anropa en lagrad procedur för att läsa data från käll databasen med hjälp av egenskapen **sqlReaderStoredProcedureName** . Mer information finns i följande artiklar om koppling: [Azure SQL Database](connector-azure-sql-database.md) [SQL Server](connector-sql-server.md) [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Information om syntax
Här är JSON-formatet för att definiera en lagrad procedur aktivitet:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

Följande tabell beskriver de här JSON-egenskaperna:

| Egenskap                  | Beskrivning                              | Krävs |
| ------------------------- | ---------------------------------------- | -------- |
| namn                      | Namn på aktiviteten                     | Ja      |
| beskrivning               | Text som beskriver vad aktiviteten används för | Nej       |
| typ                      | För lagrad procedur aktivitet är aktivitets typen **SqlServerStoredProcedure** | Ja      |
| linkedServiceName         | Referens till **Azure SQL Database** eller **Azure SQL Data Warehouse** eller **SQL Server** registreras som en länkad tjänst i Data Factory. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) . | Ja      |
| storedProcedureName       | Ange namnet på den lagrade proceduren som ska anropas. | Ja      |
| storedProcedureParameters | Ange värdena för parametrar för lagrad procedur. Använd `"param1": { "value": "param1Value","type":"param1Type" }` för att skicka parameter värden och deras typ som stöds av data källan. Om du behöver skicka null för en parameter ska du använda `"param1": { "value": null }` (alla gemener). | Nej       |

## <a name="parameter-data-type-mapping"></a>Parameter data typs mappning
Den datatyp som du anger för parametern är den Azure Data Factorys typ som mappar till data typen i data källan som du använder. Du kan hitta data typs mappningar för data källan i kopplings avsnittet. Några exempel är

| Datakälla          | Data typs mappning |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Fel information

När en lagrad procedur Miss lyckas och returnerar fel information kan du inte samla in fel informationen direkt i aktivitetens utdata. Dock Data Factory pumpar alla dess aktivitet körnings händelser till Azure Monitor. Bland de händelser som Data Factory pumpar för att Azure Monitor, push-överför den fel information där. Du kan till exempel ställa in e-postaviseringar från dessa händelser. Mer information finns i [avisering och övervaka data fabriker med hjälp av Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning körnings aktivitet för Bach](transform-data-using-machine-learning.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)
