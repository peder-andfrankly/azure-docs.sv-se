---
title: Kopiera data från SAP HANA
description: Lär dig hur du kopierar data från SAP HANA till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: 97c277eadbd1b425c50b10d15172c13e17e20eb3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926208"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiera data från SAP HANA med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuell version](connector-sap-hana.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en SAP HANA-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

>[!TIP]
>Om du vill lära dig mer om ADF: s övergripande support i SAP data integrations scenario, se [SAP data integration med Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, comparsion och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SAP HANA anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP HANA-databasen till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen med [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder den här SAP HANA-anslutningen:

- Kopiera data från en version av SAP HANA Database.
- Kopiera data från **Hana-informations modeller** (till exempel analys-och beräknings visningar) och **rad/kolumn-tabeller**.
- Kopiera data med **Basic** -eller **Windows** -autentisering.

> [!TIP]
> Om du vill kopiera data **till** SAP HANA data lager använder du allmän ODBC-anslutning. Se [SAP HANA Sink](connector-odbc.md#sap-hana-sink) med information. Observera att de länkade tjänsterna för SAP HANA koppling och ODBC-koppling är av olika typ, så att de inte kan återanvändas.

## <a name="prerequisites"></a>Krav

Om du vill använda den här SAP HANA-anslutningen måste du:

- Konfigurera en egen värd Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.
- Installera SAP HANA ODBC-drivrutinen på datorn Integration Runtime. Du kan hämta SAP HANA ODBC-drivrutinen från [SAP Software Download Center](https://support.sap.com/swdc). Sök med nyckelordet **SAP HANA-klienten för Windows**.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för SAP HANA koppling.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för SAP HANA länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SapHana** | Ja |
| connectionString | Ange information som behövs för att ansluta till SAP HANA med hjälp av **grundläggande autentisering** eller **Windows-autentisering**. Se följande exempel.<br>I anslutnings strängen är server/port obligatorisk (standard porten är 30015) och användar namn och lösen ord är obligatoriska när du använder grundläggande autentisering. Mer avancerade inställningar finns i [SAP HANA egenskaper för ODBC-anslutning](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Du kan också ställa in lösen ord i Azure Key Vault och hämta lösen ords konfigurationen från anslutnings strängen. Se [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| userName | Ange användar namn när du använder Windows-autentisering. Exempel: `user@domain.com` | Nej |
| lösenord | Ange lösen ordet för användar kontot. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

**Exempel: Använd grundläggande autentisering**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: Använd Windows-autentisering**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
            "password": { 
                "type": "SecureString", 
                "value": "<password>" 
            } 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Om du använder SAP HANA länkade tjänsten med följande nytto Last, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen.

**Exempel:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA data uppsättning.

Följande egenskaper stöds för att kopiera data från SAP HANA:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **SapHanaTable** | Ja |
| schema | Namnet på schemat i SAP HANA databasen. | Nej (om ”query” i aktivitetskälla har angetts) |
| table | Namnet på tabellen i SAP HANA databasen. | Nej (om ”query” i aktivitetskälla har angetts) |

**Exempel:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du använder `RelationalTable` typ av data uppsättning, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya som skickas.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA källa.

### <a name="sap-hana-as-source"></a>SAP HANA som källa

För att kopiera data från SAP HANA, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **SapHanaSource** | Ja |
| DocumentDB | Anger SQL-frågan för att läsa data från SAP HANA-instansen. | Ja |
| packetSize | Anger storleken på nätverks paketet (i kilobyte) för att dela data till flera block. Om du har stora mängder data som ska kopieras kan ökande paket storlek öka Läs hastigheten från SAP HANA i de flesta fall. Prestanda testning rekommenderas när du justerar paket storleken. | Nej.<br>Standardvärdet är 2048 (2 MB). |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder `RelationalSource` typ av kopierings källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen.

## <a name="data-type-mapping-for-sap-hana"></a>Data typs mappning för SAP HANA

När du kopierar data från SAP HANA används följande mappningar från SAP HANA data typer för att Azure Data Factory interimistiska data typer. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| SAP HANA data typ | Data factory tillfälliga datatyp |
| ------------------ | ------------------------------ |
| ALPHANUM           | Sträng                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| Bintext            | Sträng                         |
| BLOB               | Byte[]                         |
| BOOL               | Mottagna byte                           |
| CLOB               | Sträng                         |
| DATE               | DateTime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | Sträng                         |
| NVARCHAR           | Sträng                         |
| REAL               | Enkel                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | Sträng                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | Sträng                         |
| TIME               | TimeSpan                       |
| TINYINT            | Mottagna byte                           |
| VARCHAR            | Sträng                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
