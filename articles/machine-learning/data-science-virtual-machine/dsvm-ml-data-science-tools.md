---
title: Machine Learning och data science-verktyg
description: Lär dig mer om de maskin inlärnings verktyg och ramverk som är förinstallerade på Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 8eae1501973d24dc468056229e8a762910f2f181
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330761"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Maskin inlärnings-och data vetenskaps verktyg i Azure Data Science Virtual Machines
Azure Data Science Virtual Machines (Dsvm) har en omfattande uppsättning verktyg och bibliotek för maskin inlärning som är tillgängligt på populära språk, till exempel python, R och Julia.

Här följer några av verktygen och biblioteken för maskin inlärning på Dsvm.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK för python

Se den fullständiga referensen för [Azure Machine Learning SDK för python](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| Vad är det?   |   Azure Machine Learning är en moln tjänst som du kan använda för att utveckla och distribuera maskin inlärnings modeller. Du kan spåra dina modeller när du skapar, tränar, skalar och hanterar dem med hjälp av python SDK. Distribuera modeller som behållare och kör dem i molnet, lokalt eller på Azure IoT Edge.   |
| Versioner som stöds     | Windows (Conda-miljö: AzureML), Linux (Conda-miljö: py36)    |
| Vanliga användnings områden      | Allmän plattform för maskin inlärning      |
| Hur konfigureras den eller installeras?      |  Installerat med GPU-stöd   |
| Använda eller köra det      | Som ett python SDK och i Azure CLI. Aktivera i Conda-miljön `AzureML` på Windows-utgåvan *eller* till `py36` på Linux Edition.      |
| Länka till exempel      | Exempel på Jupyter-anteckningsböcker ingår i katalogen `AzureML` under antecknings böcker.  |
| Relaterade verktyg      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Vad är det?   | En AI-plattform med öppen källkod som har stöd för minnes intern, distribuerad, snabb och skalbar maskin inlärning.  |
| Versioner som stöds      | Linux   |
| Vanliga användnings områden      | Allmänt distribuerad, skalbar maskin inlärning   |
| Hur konfigureras den eller installeras?      | H2O har installerats i `/dsvm/tools/h2o`.      |
| Använda eller köra det      | Anslut till den virtuella datorn med hjälp av X2Go. Starta en ny terminal och kör `java -jar /dsvm/tools/h2o/current/h2o.jar`. Starta sedan en webbläsare och Anslut till `http://localhost:54321`.      |
| Länka till exempel      | Exempel finns tillgängliga på den virtuella datorn i Jupyter under katalogen `h2o`.      |
| Relaterade verktyg      | Apache Spark, MXNet, XGBoost, mousserande vatten, djup vatten    |

Det finns flera andra maskin inlärnings bibliotek på Dsvm, till exempel det populära `scikit-learn`-paketet som är en del av AnaConDa python-distributionen för Dsvm. Ta en titt på listan över tillgängliga paket i python, R och Julia genom att köra respektive paket hanterare.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Vad är det?   | En snabb, distribuerad, högpresterande gradient-förstärkning (GBDT, GBRT, GBM eller MART) som baseras på algoritmer för besluts träd. Den används för rankning, klassificering och många andra uppgifter för maskin inlärning.    |
| Versioner som stöds      | Windows, Linux    |
| Vanliga användnings områden      | Generell användnings ramverk för tonings förstärkning      |
| Hur konfigureras den eller installeras?      | I Windows installeras LightGBM som ett python-paket. På Linux är kommando raden i `/opt/LightGBM/lightgbm`, R-paketet är installerat och python-paket installeras.     |
| Länka till exempel      | [LightGBM-guide](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Relaterade verktyg      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett grafiskt användar gränssnitt för Data utvinning med hjälp av R.   |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användnings områden      | Allmänna GRÄNSSNITTs data utvinnings verktyg för R    |
| Använda eller köra det      | Som ett UI-verktyg. I Windows startar du en kommando tolk, kör R och sedan i R, kör `rattle()`. I Linux ansluter du till X2Go, startar en Terminal, kör R och sedan i R, kör `rattle()`. |
| Länka till exempel      | [Rattle](https://togaware.com/onepager/) |
| Relaterade verktyg      |LightGBM, Wekas, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett snabbt och enkelt utbildnings system bibliotek med öppen källkod    |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användnings områden      | Allmänt maskin inlärnings bibliotek      |
| Hur konfigureras den eller installeras?      |  Windows: MSI installations program<br/>Linux: apt-get |
| Använda eller köra det      | Som ett kommando rads verktyg för på väg (`C:\Program Files\VowpalWabbit\vw.exe` i Windows `/usr/bin/vw` på Linux)    |
| Länka till exempel      | [VowPal Wabbit-exempel](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Relaterade verktyg      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Wekas
|    |           |
| ------------- | ------------- |
| Vad är det?   |  En samling Machine Learning-algoritmer för data utvinnings aktiviteter. Algoritmerna kan antingen appliceras direkt på en data uppsättning eller anropas från din egen Java-kod. Wekas innehåller verktyg för för bearbetning av data, klassificering, regression, klustring, associerings regler och visualisering. |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användnings områden      | Allmänt verktyg för maskin inlärning     |
| Använda eller köra det      | I Windows söker du efter Wekas på **Start** menyn. Logga in med X2Go i Linux och gå sedan till **program** > **utvecklings** > **wekas**. |
| Länka till exempel      | [Wekas-exempel](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett snabb, portabelt och distribuerat GBDT-bibliotek (, GBRT eller GBM) för python, R, Java, Scala, C++med mera. Den körs på en enskild dator och på Apache Hadoop och Spark.    |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användnings områden      | Allmänt maskin inlärnings bibliotek      |
| Hur konfigureras den eller installeras?      |  Installerat med GPU-stöd   |
| Använda eller köra det      | Som ett Python-bibliotek (2,7 och 3,5), R-paket och kommando rads verktyget på bana (`C:\dsvm\tools\xgboost\bin\xgboost.exe` för Windows och `/dsvm/tools/xgboost/xgboost` för Linux)    |
| Länkar till exempel      | Exempel finns på den virtuella datorn i `/dsvm/tools/xgboost/demo` på Linux och `C:\dsvm\tools\xgboost\demo` i Windows.   |
| Relaterade verktyg      | LightGBM, MXNet   |


