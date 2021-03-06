---
title: Kopiera data från Impala med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från Impala till mottagarens datalager genom att använda en Kopieringsaktivitet i en data factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: f465fe4bb69bc5ae81db6c78df51bf5133de1b60
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929309"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Kopiera data från Impala med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Impala. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Impala-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Impala till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Data Factory tillhandahåller en inbyggd drivrutin om du vill aktivera anslutningen. Därför behöver du inte manuellt har installerat en drivrutin för att använda den här anslutningen.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom i gång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Impala-anslutningsapp.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Impala länkad tjänst.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **Impala**. | Ja |
| värd | IP-adressen eller värdnamnet namnet på Impala-server (det vill säga 192.168.222.160).  | Ja |
| port | TCP-porten som Impala-servern använder för att lyssna efter klientanslutningar. Standardvärdet är 21050.  | Nej |
| authenticationType | Autentiseringstypen som ska användas. <br/>Tillåtna värden är **anonym**, **SASLUsername**, och **UsernameAndPassword**. | Ja |
| användarnamn | Användarnamnet som används för att komma åt Impala-server. Standardvärdet är anonym när du använder SASLUsername.  | Nej |
| lösenord | Lösenordet som motsvarar användarnamnet när du använder UsernameAndPassword. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| enableSsl | Anger om anslutningar till servern krypteras med hjälp av SSL. Standardvärdet är **FALSKT**.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till PEM-fil som innehåller certifikat från betrodda Certifikatutfärdare används för att verifiera servern när du ansluter via SSL. Den här egenskapen kan anges endast när du använder SSL på lokal Integration Runtime. Standardvärdet är filen cacerts.pem installerad med integration runtime.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från arkivet med betrodda system eller från en angiven PEM-fil. Standardvärdet är **FALSKT**.  | Nej |
| allowHostNameCNMismatch | Anger om en CA-utfärdade SSL-certifikatnamnet att matcha värdnamnet för servern när du ansluter via SSL. Standardvärdet är **FALSKT**.  | Nej |
| allowSelfSignedServerCert | Anger om du vill tillåta självsignerade certifikat från servern. Standardvärdet är **FALSKT**.  | Nej |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Läs mer från avsnittet [krav](#prerequisites) . Om den inte anges används standard Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Impala-datauppsättningen.

Om du vill kopiera data från Impala, ange typegenskapen på datauppsättningen till **ImpalaObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **ImpalaObject** | Ja |
| schema | Schemats namn. |Nej (om ”query” i aktivitetskälla har angetts)  |
| table | Namnet på tabellen. |Nej (om ”query” i aktivitetskälla har angetts)  |
| tableName | Namnet på tabellen med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table` för nya arbets belastningar. | Nej (om ”query” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av typen av datakälla Impala.

### <a name="impala-as-a-source-type"></a>Impala som en typ av datakälla

Om du vill kopiera data från Impala, ange typ av datakälla i kopieringsaktiviteten till **ImpalaSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till **ImpalaSource**. | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
