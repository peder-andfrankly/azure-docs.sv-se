---
title: Skapa data pipeliner för förutsägelser
description: Lär dig hur du skapar en förutsägelse pipeline med hjälp av Azure Machine Learning körnings aktivitet för batch i Azure Data Factory.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/20/2019
ms.openlocfilehash: b5b7b69f49efa6cc0d5f3e42fc8a3aca855bca9a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913004"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Skapa förutsägande pipelines med hjälp av Azure Machine Learning och Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuell version](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) ger dig möjlighet att bygga, testa och distribuera lösningar för förutsägelse analys. Från en överblick på hög nivå görs det i tre steg:

1. **Skapa ett övnings experiment**. Du utför det här steget med hjälp av Azure Machine Learning Studio. Azure Machine Learning Studio är en miljö för utveckling av visuella objekt som du använder för att träna och testa en förutsägelse analys modell med tränings data.
2. **Konvertera det till ett förutsägelse experiment**. När din modell har tränats med befintliga data och du är redo att använda den för att skapa nya data, förbereder du och effektiviserar experimentet med poäng.
3. **Distribuera den som en webb tjänst**. Du kan publicera bedömnings experimentet som en Azure-webbtjänst. Du kan skicka data till din modell via den här webb tjänst slut punkten och ta emot resultat förutsägelser från modellen.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory och Machine Learning tillsammans
Med Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) webb tjänst för förutsägelse analys. Med hjälp av **aktiviteten kör batch-körning** i en Azure Data Factory pipeline kan du anropa en Azure Machine Learning Studio-webbtjänst för att göra förutsägelser för data i batch.

Med tiden måste förutsägande modeller i test experimenten med Azure Machine Learning Studio återskapas med hjälp av nya data uppsättningar för indata. Du kan omträna en modell från en Data Factory pipeline genom att utföra följande steg:

1. Publicera utbildnings experimentet (inte förutsägande experiment) som en webb tjänst. Du gör det här steget i Azure Machine Learning Studio eftersom du gjorde ett förutsägelse experiment som en webb tjänst i föregående scenario.
2. Använd aktiviteten för batch-körning i Azure Machine Learning Studio för att anropa webb tjänsten för inlärnings experimentet. I princip kan du använda batch-körningen Azure Machine Learning Studio för att anropa webb tjänsten för webb tjänster och Poäng för utbildning.

När du är färdig med omträningen uppdaterar du bedömnings webb tjänsten (förutsägelse experiment som exponeras som en webb tjänst) med den nya modellen genom att använda **Azure Machine Learning Studio-uppdateringen av resurs aktiviteten**. Mer information finns i uppdatera [modeller med hjälp av artikeln Uppdatera resurs aktivitet](update-machine-learning-models.md) .

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst

Du skapar en **Azure Machine Learning** länkad tjänst för att länka en Azure Machine Learning webb tjänst till en Azure Data Factory. Den länkade tjänsten används av Azure Machine Learning aktivitet för batch-körning och [uppdatering av resurs aktivitet](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Se artikeln om att [Beräkna länkade tjänster](compute-linked-services.md) för beskrivningar av egenskaper i JSON-definitionen.

Azure Machine Learning stödja både klassiska webb tjänster och nya webb tjänster för ditt förutsägelse experiment. Du kan välja den rätt som du vill använda från Data Factory. För att hämta den information som krävs för att skapa den länkade tjänsten Azure Machine Learning går du till https://services.azureml.net där alla dina (nya) webb tjänster och klassiska webb tjänster visas. Klicka på den webb tjänst som du vill få åtkomst till och klicka på **Använd sida.** Kopiera **primär nyckel** för egenskapen **apiKey** och **batch-begäranden** för egenskapen **mlEndpoint** .

![Azure Machine Learning webb tjänster](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Azure Machine Learning batch-körning, aktivitet

Följande JSON-kodfragment definierar en Azure Machine Learning batch execution-aktivitet. Aktivitets definitionen har en referens till den Azure Machine Learning länkade tjänsten som du skapade tidigare.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| namn              | Namn på aktiviteten i pipelinen     | Ja      |
| beskrivning       | Text som beskriver vad aktiviteten gör.  | Nej       |
| typ              | För Data Lake Analytics U-SQL-aktivitet är aktivitets typen **AzureMLBatchExecution**. | Ja      |
| linkedServiceName | Länkade tjänster till den länkade tjänsten Azure Machine Learning. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) . | Ja      |
| webServiceInputs  | Nyckel, värdepar, mappar namnen på Azure Machine Learning webb tjänst indata. Nyckeln måste matcha de indataparametrar som definierats i webb tjänsten för publicerade Azure Machine Learning. Värdet är ett Azure Storage länkade tjänster och egenskaper för fil Sök väg som anger indata-BLOB-platser. | Nej       |
| webServiceOutputs | Nyckel, värdepar, mappar namnen på Azure Machine Learning webb tjänstens utdata. Nyckeln måste matcha de utdataparametrar som definierats i den publicerade Azure Machine Learning-webbtjänsten. Värdet är ett Azure Storage länkade tjänster och egenskaper för fil Sök väg som anger utgående BLOB-platser. | Nej       |
| Dublettparameternamnet  | Nyckel, värde-par som ska skickas till den Azure Machine Learning Studio batch execution service-slutpunkten. Nycklar måste matcha namnen på de webb tjänst parametrar som definierats i den publicerade Azure Machine Learning Studio-webbtjänsten. Värden skickas i egenskapen Dublettparameternamnet för batch-körningen Azure Machine Learning Studio batch | Nej       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario 1: experiment som använder webb tjänst indata/utdata som refererar till data i Azure Blob Storage

I det här scenariot gör Azure Machine Learning-webbtjänsten förutsägelser med hjälp av data från en fil i Azure Blob Storage och lagrar förutsägelse resultatet i blob-lagringen. Följande JSON definierar en Data Factory pipeline med en AzureMLBatchExecution-aktivitet. Indata och utdata i Azure blogg lagring refereras till med hjälp av ett LinkedName och ett fil Sök väg par. I det exempel länkade tjänsten för indata och utdata är olika kan du använda olika länkade tjänster för var och en av dina indata/utdata för Data Factory för att kunna hämta rätt filer och skicka till Azure Machine Learning Studio-webbtjänst.

> [!IMPORTANT]
> I ditt Azure Machine Learning Studio experiment, webb tjänstens indata-och utgående portar och globala parametrar har standard namn ("INPUT1", "INPUT2") som du kan anpassa. De namn du använder för inställningarna webServiceInputs, webServiceOutputs och Dublettparameternamnet måste exakt matcha namnen i experimenten. Du kan visa nytto lasten för exempel förfrågan på hjälp sidan för batch-körning för din Azure Machine Learning Studio-slutpunkt för att verifiera den förväntade mappningen.
>
>

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario 2: experiment med Reader/Writer-moduler för att referera till data i olika lagrings utrymmen
Ett annat vanligt scenario när du skapar Azure Machine Learning Studio-experiment är att använda moduler för att importera data och utdata. Modulen importera data används för att läsa in data i ett experiment och modulen utdata är att spara data från experimenten. Mer information om hur du importerar data och utdata-moduler finns i avsnittet [Importera data](https://msdn.microsoft.com/library/azure/dn905997.aspx) och [utdata](https://msdn.microsoft.com/library/azure/dn905984.aspx) i MSDN Library.

När du använder modulerna importera data och utdata, är det bra att använda en webb tjänst parameter för varje egenskap i dessa moduler. Med de här webb parametrarna kan du konfigurera värdena under körning. Du kan till exempel skapa ett experiment med en modul för att importera data som använder en Azure SQL Database: XXX.database.windows.net. När webb tjänsten har distribuerats vill du göra det möjligt för användare av webb tjänsten att ange en annan Azure-SQL Server som kallas `YYY.database.windows.net`. Du kan använda en webb tjänst parameter för att tillåta att det här värdet konfigureras.

> [!NOTE]
> Webb tjänstens indata och utdata skiljer sig från webb tjänst parametrarna. I det första scenariot har du sett hur indata och utdata kan anges för en Azure Machine Learning Studio-webbtjänst. I det här scenariot skickar du parametrar för en webb tjänst som motsvarar egenskaper för modulerna importera data/utdata.
>
>

Nu ska vi titta på ett scenario för att använda webb tjänst parametrar. Du har en distribuerad Azure Machine Learning-webbtjänst som använder en läsar modul för att läsa data från en av de data källor som stöds av Azure Machine Learning (till exempel: Azure SQL Database). När batch-körningen har utförts skrivs resultatet med en Writer-modul (Azure SQL Database).  Inga indata och utdata för webb tjänsten definieras i experimenten. I det här fallet rekommenderar vi att du konfigurerar relevanta webb tjänst parametrar för modulerna läsare och skrivare. Med den här konfigurationen kan läsaren/skrivar-modulerna konfigureras när du använder AzureMLBatchExecution-aktiviteten. Du anger webb tjänst parametrar i **Dublettparameternamnet** -avsnittet i AKTIVITETS-JSON på följande sätt.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> Webb tjänst parametrarna är Skift läges känsliga, så se till att namnen som du anger i aktivitets-JSON matchar de som exponeras av webb tjänsten.
>

När du är färdig med omträningen uppdaterar du bedömnings webb tjänsten (förutsägelse experiment som exponeras som en webb tjänst) med den nya modellen genom att använda **Azure Machine Learning Studio-uppdateringen av resurs aktiviteten**. Mer information finns i uppdatera [modeller med hjälp av artikeln Uppdatera resurs aktivitet](update-machine-learning-models.md) .

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)
