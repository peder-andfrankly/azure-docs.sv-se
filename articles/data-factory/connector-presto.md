---
title: Kopiera data från Presto med Azure Data Factory (förhandsversion)
description: Lär dig hur du kopierar data från Presto till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.openlocfilehash: 71bff5e3761d72236e6896733b96bd6e01460e52
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927807"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Kopiera data från Presto med Azure Data Factory (förhandsversion)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Presto. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

> [!IMPORTANT]
> Den här anslutningsappen är för närvarande i förhandsversion. Du kan testa och ge oss feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Presto-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Presto till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, måste du därför inte att manuellt installera en drivrutin som använder den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Presto connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Presto länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **Presto** | Ja |
| värd | IP-adressen eller värdnamnet namnet på Presto servern. (d.v.s. 192.168.222.160)  | Ja |
| serverVersion | Versionen av Presto server. (d.v.s. 0.148-t)  | Ja |
| catalog | Katalogen kontext för alla begäranden mot servern.  | Ja |
| port | TCP-porten som Presto servern använder för att lyssna efter klientanslutningar. Standardvärdet är 8080.  | Nej |
| authenticationType | Den autentiseringsmetod som används för att ansluta till Presto-servern. <br/>Tillåtna värden är: **anonym**, **LDAP** | Ja |
| användarnamn | Användarnamnet som används för att ansluta till Presto-servern.  | Nej |
| lösenord | Lösenordet för användarnamnet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| enableSsl | Anger om anslutningar till servern krypteras med SSL. Standardvärdet är FALSKT.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till filen .pem som innehåller certifikat från betrodda Certifikatutfärdare för att verifiera servern när du ansluter via SSL. Den här egenskapen kan bara anges när du använder SSL på lokal IR. Standardvärdet är filen cacerts.pem installerad med i IR.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från arkivet med betrodda system eller från en angiven PEM-fil. Standardvärdet är FALSKT.  | Nej |
| allowHostNameCNMismatch | Anger om en CA-utfärdade SSL-certifikatnamnet att matcha värdnamnet för servern när du ansluter via SSL. Standardvärdet är FALSKT.  | Nej |
| allowSelfSignedServerCert | Anger om du vill tillåta självsignerade certifikat från servern. Standardvärdet är FALSKT.  | Nej |
| timeZoneID | Den lokala tidszonen som används av anslutningen. Giltiga värden för det här alternativet har angetts i databasen med IANA tidszoner. Standardvärdet är datorns tidszon.  | Nej |

**Exempel:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Presto datauppsättning.

Om du vill kopiera data från Presto, ange typegenskapen på datauppsättningen till **PrestoObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **PrestoObject** | Ja |
| schema | Schemats namn. |Nej (om ”query” i aktivitetskälla har angetts)  |
| table | Namnet på tabellen. |Nej (om ”query” i aktivitetskälla har angetts)  |
| tableName | Namnet på tabellen med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table` för nya arbets belastningar. | Nej (om ”query” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Presto källa.

### <a name="presto-as-source"></a>Presto som källa

Om du vill kopiera data från Presto, ange typ av datakälla i kopieringsaktiviteten till **PrestoSource**. Följande egenskaper stöds i kopieringsaktiviteten **source** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **PrestoSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
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
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
