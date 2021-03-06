---
title: Binärt format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar binärt format i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 8ebb4f0d1a06a7bf29dc46cd696b6acfd2527095
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927384"
---
# <a name="binary-format-in-azure-data-factory"></a>Binärt format i Azure Data Factory

Binärt format stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

Du kan använda binär data uppsättning i [kopierings aktivitet](copy-activity-overview.md), [getMetaData aktivitet](control-flow-get-metadata-activity.md)eller [ta bort aktivitet](delete-activity.md). När du använder binär data uppsättning tolkar inte ADF fil innehållet, men behandlar det som det är. 

>[!NOTE]
>När du använder binär data mängd i kopierings aktivitet kan du bara kopiera från binär data uppsättning till binär data mängd.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av den binära data uppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **Binary**. | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har en egen plats typ och egenskaper som stöds under `location`. **Se information i avsnittet kopplings artikel – egenskaper för > data uppsättning**. | Ja      |
| compression | Grupp egenskaper för att konfigurera fil komprimering. Konfigurera det här avsnittet när du vill utföra komprimering/expandering under aktivitets körningen. | Nej |
| typ | Komprimerings-codec som används för att läsa/skriva binära filer. <br>Tillåtna värden är **bzip2**, **gzip**, **DEFLATE**, **ZipDeflate**. att använda när du sparar filen.<br>Obs! när du använder kopierings aktivitet för att expandera ZipDeflate-filer och skriva till filbaserat mottagar data lager extraheras filerna till mappen: `<path specified in dataset>/<folder named as source zip file>/`. | Nej       |
| level | Komprimerings förhållandet. Använd när dataset används i kopierings aktiviteten Sink.<br>Tillåtna värden är **optimalt** eller **snabbast**.<br>- **snabbast:** komprimerings åtgärden bör utföras så snart som möjligt, även om den resulterande filen inte är optimalt komprimerad.<br>- **optimalt**: komprimerings åtgärden bör komprimeras optimalt, även om åtgärden tar längre tid att slutföra. Mer information finns i [komprimeringsnivå](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) avsnittet. | Nej       |

Nedan visas ett exempel på en binär data uppsättning på Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av den binära källan och mottagaren.

>[!NOTE]
>När du använder binär data mängd i kopierings aktivitet kan du bara kopiera från binär data uppsättning till binär data mängd.

### <a name="binary-as-source"></a>Binär som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitet ***\*käll\**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **BinarySource**. | Ja      |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings`. **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Nej       |

### <a name="binary-as-sink"></a>Binär som mottagare

Följande egenskaper stöds i avsnittet kopierings aktivitet ***\*mottagare\**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **BinarySink**. | Ja      |
| storeSettings | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings`. **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Nej       |

## <a name="next-steps"></a>Nästa steg

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)
