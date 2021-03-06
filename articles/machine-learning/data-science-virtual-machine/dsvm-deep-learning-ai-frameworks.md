---
title: Djup inlärning & AI-ramverk
titleSuffix: Azure Data Science Virtual Machine
description: Tillgängliga ramverk för djup inlärning och verktyg på Azure Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/1/2019
ms.openlocfilehash: db95788b0f2c041157bdc16000d0328c042e86d5
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973697"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Djup inlärnings-och AI-ramverk för Azure-Data Science VM
Ramverk för djup inlärning i DSVM visas nedan.

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | |
| DSVM-versioner som stöds      | Linux (Ubuntu)     |
| Hur är det konfigurerade / installerad på DSVM?  | Caffe är installerat i `/opt/caffe`.   Exemplen är i `/opt/caffe/examples`.|
| Så här kör du det      | Använd X2Go för att logga in på den virtuella datorn och starta sedan en ny terminal och ange följande:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Ett nytt webbläsarfönster öppnas med exempelanteckningsböcker. Binärfiler installeras i /opt/caffe/build/install/bin.<br/><br/>Den installerade versionen av Caffe kräver python 2,7 och fungerar inte med python 3,5, som aktive ras som standard. Växla till python 2,7 genom att köra `source activate root` för att växla till Anaconda-miljön.|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | |
| DSVM-versioner som stöds      | Linux (Ubuntu)     |
| Hur är det konfigurerade / installerad på DSVM?  | Caffe2 installeras i Conda-miljön [python 2,7 (root). |
| Så här kör du det      | Slutliga Starta python och importera Caffe2. <br/> * JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och gå sedan till katalogen Caffe2 för att hitta exempel antecknings böcker. Vissa anteckningsböcker kräver Caffe2 roten anges i Python-kod. Ange /opt/caffe2. |

## <a name="chainerhttpschainerorg"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 5.2 |
| DSVM-versioner som stöds      | Linux (Ubuntu)     |
| Hur är det konfigurerade / installerad på DSVM?  | Kedjor installeras i python 3,5. |
| Så här kör du det      | Slutliga Aktivera python 3,5-miljön, kör `python` och klicka sedan `import chainer`. <br/> * JupyterHub: [Anslut till JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och gå sedan till katalogen kedjar för att hitta exempel antecknings böcker.| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CUDA, cuDNN, NVIDIA-drivrutin](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 10.0.130|
| DSVM-versioner som stöds      | Windows och Linux   |
| Hur är det konfigurerade / installerad på DSVM?  |_NVIDIA smi_ finns på systemsökvägen.  |
| Så här kör du det      | Öppna en kommando tolk (i Windows) eller en Terminal (på Linux) och kör sedan _NVIDIA-SMI_. |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 0.16.1|
| DSVM-versioner som stöds      | Linux (Ubuntu)   |
| Hur är det konfigurerade / installerad på DSVM?  | Horovod installeras i python 3,5 |
| Så här kör du det      | Aktivera rätt miljö vid terminalen och kör sedan python. |

## <a name="kerashttpskerasio"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 2.2.4 |
| DSVM-versioner som stöds      | Windows och Linux   |
| Hur är det konfigurerade / installerad på DSVM?  | Keras installeras i python 3,6 på Windows och i python 3,5 i Linux |
| Så här kör du det      | Aktivera rätt miljö vid terminalen och kör sedan python. |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 2.5.1 |
| DSVM-versioner som stöds      | Windows och Linux   |
| Hur är det konfigurerade / installerad på DSVM?  | CNTK installeras i python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) och i python 3,5 på [Linux](./dsvm-languages.md#python-linux-edition)) |
| Så här kör du det      | Slutliga Aktivera rätt miljö och kör python. <br/>Jupyter Anslut till [Jupyter](provision-vm.md) eller [JUPYTERHUB](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och öppna CNTK-katalogen för exempel. |

## <a name="mxnethttpsmxnetapacheorg"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1.3.0 |
| DSVM-versioner som stöds      | Windows och Linux   |
| Hur är det konfigurerade / installerad på DSVM?  | MXNet installeras i `C:\dsvm\tools\mxnet` i Windows och `/dsvm/tools/mxnet` på Ubuntu. Python-bindningar installeras i python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) och i python 3,5 på [Linux](./dsvm-languages.md#python-linux-edition)) R-bindningar ingår också i Ubuntu-DSVM. |
| Så här kör du det      | Slutliga Aktivera rätt Conda-miljö och kör sedan `import mxnet`. <br/>Jupyter Anslut till [Jupyter](provision-vm.md#access-the-dsvm) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och öppna sedan `mxnet`-katalogen för exempel. |

## <a name="mxnet-model-serverhttpsgithubcomawslabsmxnet-model-serverquick-start"></a>[MXNet-modell Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1.0.1 |
| DSVM-versioner som stöds      | Windows och Linux   |
| Hur är det konfigurerade / installerad på DSVM?  | MXNet modell Server installeras i python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) och i python 3,5 på [Linux](./dsvm-languages.md#python-linux-edition)) |
| Så här kör du det      | Slutliga Kör `sudo systemctl stop jupyterhub` för att stoppa JupyterHub-tjänsten först, eftersom båda lyssnar på samma port. Aktivera sedan rätt Conda-miljö och kör `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smihttpsdevelopernvidiacomnvidia-system-management-interface"></a>[NVIDIA-gränssnitt för system hantering (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds |  |
| DSVM-versioner som stöds      | Windows och Linux   |
| Vad är det för? | NVIDIA-verktyget för att fråga GPU-aktivitet |
| Hur är det konfigurerade / installerad på DSVM?  | `nvidia-smi` finns på system Sök vägen. |
| Så här kör du det      | Öppna en kommando tolk (i Windows) eller en Terminal (på Linux) på en virtuell dator **med GPU: n**och kör sedan `nvidia-smi`. |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1.2.0 |
| DSVM-versioner som stöds      | Linux |
| Hur är det konfigurerade / installerad på DSVM?  | Installerat i [Python 3,5](dsvm-languages.md#python-linux-edition). Exempel på Jupyter-anteckningsböcker ingår och exempel finns i/dsvm/samples/pytorch. |
| Så här kör du det      | Slutliga Aktivera rätt miljö och kör sedan python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Anslut och öppna PyTorch-katalogen för exempel.  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1,13 |
| DSVM-versioner som stöds      | Windows, Linux |
| Hur är det konfigurerade / installerad på DSVM?  | Installerat i python 3,5 på [Linux](dsvm-languages.md#python-linux-edition) och python 3,6 på [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) |
| Så här kör du det      | Slutliga Aktivera rätt miljö och kör sedan python. <br/> Jupyter Anslut till [Jupyter](provision-vm.md) eller [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)och öppna TensorFlow-katalogen för exempel.   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[TensorFlow-servar](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1,12 |
| DSVM-versioner som stöds      | Linux |
| Hur är det konfigurerade / installerad på DSVM?  | tensorflow_model_server är tillgänglig i terminalen. |
| Så här kör du det      |  Exempel finns [online](https://www.tensorflow.org/serving/).   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Version (er) som stöds | 1.0.3 |
| DSVM-versioner som stöds      | Linux |
| Hur är det konfigurerade / installerad på DSVM?  |Theano installeras i python 2,7 (_root_) och i python 3,5 (_py35_)-miljö. |
| Så här kör du det      |  Slutliga Aktivera den python-version som du vill använda (root eller py35), kör python och importera sedan Theano.<br/>Jupyter Välj python 2,7-eller 3,5-kernel och importera sedan Theano.  <br/>För att undvika ett tidigare MKL-fel (math kernel Library) måste du först ställa in MKL-tråd skiktet på följande sätt:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |