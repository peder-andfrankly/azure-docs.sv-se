---
title: Kopiera data från OData-källor med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från OData-källor till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
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
ms.openlocfilehash: e70cd6d7745b2754bdda6992c587c5c72643869b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912516"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopiera data från en OData-källa med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-odata-connector.md)
> * [Aktuell version](connector-odata.md)

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från en OData-källa. Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Denna OData-anslutning stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från en OData-källa till alla mottagar data lager som stöds. En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Mer specifikt stöder denna OData-anslutning:

- OData version 3,0 och 4,0.
- Kopiera data genom att använda någon av följande autentiseringar: **Anonym**, **grundläggande**, **Windows**, **AAD-tjänstens huvud namn**och **hanterade identiteter för Azure-resurser**.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom i gång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för en OData-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för en OData-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **OData**. |Ja |
| url | OData-tjänstens rot-URL. |Ja |
| authenticationType | Den typ av autentisering som används för att ansluta till OData-källan. Tillåtna värden är **Anonymous**, **Basic**, **Windows**, **AadServicePrincipal**och **ManagedServiceIdentity**. Användarbaserad OAuth stöds inte. | Ja |
| userName | Ange **userName** om du använder grundläggande eller Windows-autentisering. | Nej |
| lösenord | Ange **password** för användaren-konto som du angav för **användarnamn**. Markera det här fältet som en **SecureString** Skriv för att lagra den på ett säkert sätt i Data Factory. Du kan också [referera till en hemlighet som lagrats i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| servicePrincipalId | Ange det Azure Active Directory programmets klient-ID. | Nej |
| aadServicePrincipalCredentialType | Ange vilken typ av autentiseringsuppgift som ska användas för autentisering av tjänstens huvud namn. Tillåtna värden är: `ServicePrincipalKey` eller `ServicePrincipalCert`. | Nej |
| servicePrincipalKey | Ange Azure Active Directory programmets nyckel. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| servicePrincipalEmbeddedCert | Ange det Base64-kodade certifikatet för ditt program registrerat i Azure Active Directory. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| servicePrincipalEmbeddedCertPassword | Ange lösen ordet för ditt certifikat om ditt certifikat är skyddat med ett lösen ord. Markera det här fältet som en **SecureString** ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md).  | Nej|
| tenant | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Hämta det håller musen i det övre högra hörnet i Azure Portal. | Nej |
| aadResourceId | Ange den AAD-resurs som du begär för auktorisering.| Nej |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Läs mer från avsnittet [krav](#prerequisites) . Om den inte anges används standard Azure Integration Runtime. |Nej |

**Exempel 1: använda anonym autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: använda grundläggande autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**Exempel 3: använda Windows-autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**Exempel 4: använda autentisering av tjänstens huvud namn**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Exempel 5: använda autentisering av tjänstens huvud certifikat**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som stöds av OData-datauppsättningen.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Om du vill kopiera data från OData ställer du in egenskapen **Type** för data uppsättningen på **ODataResource**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **ODataResource**. | Ja |
| Sökväg | Sökvägen till OData-resursen. | Ja |

**Exempel**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som OData-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData as-källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från OData:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **ODataSource**. | Ja |
| DocumentDB | OData-frågealternativ för att filtrera data. Exempel: `"$select=Name,Description&$top=5"`.<br/><br/>**Obs!** OData-kopplingen kopierar data från den kombinerade URL: en: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`. Mer information finns i [OData URL-komponenter](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nej |

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder `RelationalSource` typ av källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya som skickas.

## <a name="data-type-mapping-for-odata"></a>Data typs mappning för OData

När du kopierar data från OData används följande mappningar mellan OData-datatyper och Azure Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och data typ till mottagaren finns i [schema-och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| OData-datatyp | Data Factory tillfälliga datatyp |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Enkel |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Sträng |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData komplexa data typer (t. ex. **objekt**) stöds inte.


## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md##supported-data-stores-and-formats).