---
title: 'Själv studie kurs om bild klassificering: Distribuera modeller'
titleSuffix: Azure Machine Learning
description: Den här självstudien visar hur du använder Azure Machine Learning för att distribuera en bild klassificerings modell med scikit – lära dig i en python Jupyter Notebook. Den här självstudien är den andra delen i en serie med två delar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: fb92132a1e8e05052bc2f7c7d19797a4342db54b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974214"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Självstudie: Distribuera en bild klassificerings modell i Azure Container Instances
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Självstudien är **del två i en självstudieserie i två delar**. I den [föregående självstudien](tutorial-train-models-with-aml.md) tränade du maskininlärningsmodeller och registrerade sedan en modell på din arbetsyta i molnet.  

Nu är du redo att distribuera modellen som en webbtjänst i [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/). En webbtjänst är en avbildning, i det här fallet en Docker-avbildning. Den kapslar in bedömningslogiken och själva modellen. 

I den här delen av självstudien använder du Azure Machine Learning för följande uppgifter:

> [!div class="checklist"]
> * Konfigurera din testmiljö.
> * Hämta modellen från din arbetsyta.
> * Testa modellen lokalt.
> * Distribuera modellen till Container Instances.
> * Testa den distribuerade modellen.

Container Instances är en bra lösning för testning och för att förstå arbetsflödet. För skalbara produktionsdistributioner kan du använda Azure Kubernetes Service. Mer information finns i [Hur och var man distribuerar](how-to-deploy-and-where.md).

>[!NOTE]
> Koden i den här artikeln har testats med Azure Machine Learning SDK-version 1.0.41.

## <a name="prerequisites"></a>Krav

Om du vill köra antecknings boken börjar du med att slutföra modell utbildningen i [Självstudier (del 1): träna en bild klassificerings modell](tutorial-train-models-with-aml.md).   Öppna antecknings boken **img-klassificering-part2-Deploy. ipynb** i mappen med klonade **självstudier** .

Den här själv studie kursen finns också på [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) om du vill använda den i din egen [lokala miljö](how-to-configure-environment.md#local).  Kontrol lera att du har installerat `matplotlib` och `scikit-learn` i din miljö. 

> [!Important]
> Resten av den här artikeln innehåller samma innehåll som du ser i antecknings boken.  
>
> Växla till antecknings boken för Jupyter nu om du vill läsa den samtidigt som du kör koden.
> Om du vill köra en enda kod cell i en bärbar dator klickar du på cellen kod och trycker på **SKIFT + RETUR**. Du kan också köra hela antecknings boken genom att välja **Kör alla** från det översta verktygsfältet.

## <a name="start"></a>Konfigurera miljön

Börja med att konfigurera en testmiljö.

### <a name="import-packages"></a>Importera paket

Importera de Python-paket som behövs för den här självstudien:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Hämta modellen

Du registrerade en modell på din arbetsyta i föregående självstudie. Nu kan läsa in den här arbetsytan och hämta modellen till din lokala katalog:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os
ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>Testa modellen lokalt

Innan du distribuerar bör du kontrollera att din modell fungerar lokalt:
* Läsa in testdata.
* Förutsäga testdata.
* Undersöka felmatrisen.

### <a name="load-test-data"></a>Läsa in testdata

Läs in testdata från katalogen **./data/** som skapades i träningssjälvstudien:

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>Förutsäga testdata

För att få förutsägelser så matar du in testdatauppsättningen i modellen:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Undersöka felmatrisen

Generera en felmatris om du vill se hur många exempel från testuppsättningen som har klassificerats på rätt sätt. Observera det felklassificerade värdet för de felaktiga förutsägelserna: 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

Utdatan visar felmatrisen:

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

Använd `matplotlib` till att visa felmatrisen som ett diagram. I det här diagrammet motsvarar X-axeln faktiska värden och Y-axeln visar de förväntade värdena. Färgen i varje rutnät visar felfrekvensen. Ju ljusare färg, desto högre är felfrekvensen. Till exempel är många 5:or felaktigt klassificerade som 3:or. Du ser ett ljust rutnät vid (5,3):

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Diagram som visar felmatris](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Distribuera som en webbtjänst

När du har testat modellen och är nöjd med resultaten så distribuerar du modellen som en webbtjänst som hanteras i Container Instances. 

Skapa rätt miljö för Container Instances genom att ange följande komponenter:
* Ett bedömningsskript som visar hur man ska använda modellen.
* En miljöfil som visar vilka paket som måste installeras.
* En konfigurationsfil för att skapa containerinstansen.
* Modellen som du tränade tidigare.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Skapa ett bedömningsskript

Skapa bedömningsskriptet som kallas **score.py**. Webbtjänstanropet använder det här skriptet för att visa hur modellen används.

Inkludera de här två obligatoriska funktionerna i bedömningsskriptet:
* Funktionen `init()`, som vanligtvis läser in modellen i ett globalt objekt. Den här funktionen körs endast en gång när Docker-containern startas. 

* Funktionen `run(input_data)` använder modellen till att förutsäga ett värde som baseras på indatan. Indatan och utdatan i körningen använder vanligtvis JSON för serialisering och deserialisering, men andra format stöds också.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Skapa en miljöfil

Därefter skapar du en miljöfil med namnet **myenv.yml**, som anger alla skriptets paketberoenden. Filen används för att säkerställa att alla dessa beroenden är installerade i Docker-avbildningen. Modellen behöver `scikit-learn` och `azureml-sdk`:

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
Granska `myenv.yml`-filens innehåll:

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Skapa en konfigurationsfil

Skapa en distributionskonfigurationsfil. Ange hur många processorer och gigabyte RAM som behövs till din Container Instances-container. Även om det beror på din modell så räcker standardvärdet på en kärna och 1 GB RAM-minne för många modeller. Om du behöver fler senare så måste du återskapa avbildningen och distribuera om tjänsten.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Distribuera i Container Instances
Beräknad tid att slutföra distributionen är **ungefär sju till åtta minuter**.

Konfigurera avbildningen och distribuera. Följande kod går igenom de här stegen:

1. Skapa en avbildning med de här filerna:
   * Bedömningsfilen, `score.py`.
   * Miljöfilen, `myenv.yml`.
   * Modellfilen.
1. Registrera avbildningen under arbetsytan. 
1. Skicka avbildningen till Container Instances-containern.
1. Starta en container i Container Instances genom att använda avbildningen.
1. Hämta webbtjänstens HTTP-slutpunkt.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc',
                       models=[model], 
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Hämta bedömningswebbtjänstens HTTP-slutpunkt, som accepterar REST-klientanrop. Du kan dela den här slutpunkten med alla som vill testa webbtjänsten eller integrera den i ett program: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>Testa den distribuerade tjänsten

Tidigare bedömde du alla testdata med den lokala versionen av modellen. Nu kan du testa den distribuerade modellen med ett slumpmässigt urval av 30 bilder från testdatan.  

Följande kod går igenom de här stegen:
1. Skicka data som en JSON-matris till den webbtjänst som hanteras i Container Instances. 

1. Använd SDK:ernas `run`-API till att anropa tjänsten. Du kan även göra råa-anrop med valfritt HTTP-verktyg, till exempel **curl**.

1. Skriv ut de returnerade förutsägelserna och rita dem tillsammans med indatabilderna. Rött teckensnitt och en inverterad bild, vit på svart, används för att visa de felklassificerade exemplen. 

Eftersom modellens precision är hög så kan du behöva köra följande kod ett par gånger innan du kan se ett felklassificerat exempel:

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize=(20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Det här resultatet är från ett slumpmässigt urval av testbilder:

![Illustration som visar resultat](./media/tutorial-deploy-models-with-aml/results.png)

Du kan också skicka en rå HTTP-begäran för att testa webbtjänsten:

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill behålla resursgruppen och arbetsytan för andra självstudier och undersökningar kan du nöja dig med att ta bort Container Instances-distributionen med hjälp av det här API-anropet:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Nästa steg

+ Lär dig mer om alla [distributions alternativ för Azure Machine Learning](how-to-deploy-and-where.md).
+ Lär dig att [skapa klienter för webbtjänsten](how-to-consume-web-service.md).
+  [Göra förutsägelser kring stora mängder data](how-to-run-batch-predictions.md) asynkront.
+ Övervaka dina Azure Machine Learning-modeller med [Application Insights](how-to-enable-app-insights.md).
+ Prova självstudien [automatiskt algoritmval](tutorial-auto-train-models.md). 
