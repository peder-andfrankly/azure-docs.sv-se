---
title: Modell tolkning för lokala och fjärrkörningar
titleSuffix: Azure Machine Learning
description: Lär dig hur du får förklaringar för hur din Machine Learning-modell fastställer funktions prioritet och gör förutsägelser när du använder Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: ffb9e0547c44ee47a43de00e51933ce7d0584759
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158719"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Modell tolkning för lokala och fjärrkörningar

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln lär du dig att använda tolknings paketet för Azure Machine Learning python SDK för att ta reda på varför din modell gjorde förutsägelserna. Lär dig att:

* Tolka modeller för maskin inlärning som har tränat både lokalt och på fjärrberäknings resurser.
* Lagra lokala och globala förklaringar på Azures körnings historik.
* Visa tolknings visualiseringar i [Azure Machine Learning Studio](https://ml.azure.com).
* Distribuera en bedömnings förklaring med din modell.

Mer information finns i [modell tolkning i Azure Machine Learning-tjänsten](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Lokal tolkning

I följande exempel visas hur du använder tolknings paketet lokalt utan att kontakta Azure-tjänster.

1. Använd `pip install azureml-interpret` för att hämta tolknings paketet om det behövs.

1. Träna en exempel modell i en lokal Jupyter-anteckningsbok.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

1. Anropa förklaringen lokalt.
   * Om du vill initiera ett förklarings objekt skickar du din modell och några tränings data till förklaringens konstruktor.
   * Om du vill göra dina förklaringar och visualiseringar mer informativa kan du välja att skicka in funktions namn och utgångs klass namn om du gör klassificering.

   Följande kod block visar hur du instansierar ett förklarings objekt med `TabularExplainer`, `MimicExplainer`och `PFIExplainer` lokalt.
   * `TabularExplainer` anropar en av de tre SHAP-förklaringarna under (`TreeExplainer`, `DeepExplainer`eller `KernelExplainer`).
   * `TabularExplainer` väljer automatiskt den lämpligaste för ditt användnings fall, men du kan anropa var och en av de tre underliggande förklaringarna direkt.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    eller

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    eller

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>Generella, globala funktions prioritets värden

Se följande exempel för att få hjälp med att hämta de globala funktionernas prioritets värden.

```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>Prioritets värden för lokala funktioner på instans nivå

Hämta prioritets värden för den lokala funktionen genom att anropa förklaringar för en enskild instans eller en grupp av instanser.
> [!NOTE]
> `PFIExplainer` stöder inte lokala förklaringar.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Tolkning för fjärrkörningar

I följande exempel visas hur du kan använda klassen `ExplanationClient` för att aktivera modell tolkning för fjärrkörningar. Det är konceptuellt precis som den lokala processen, förutom du:

* Använd `ExplanationClient` i fjärrkörningen för att ladda upp tolknings kontexten.
* Ladda ned kontexten senare i en lokal miljö.

1. Om det behövs använder `pip install azureml-contrib-interpret` för att hämta det nödvändiga paketet.

1. Skapa ett inlärningsskript i en lokal Jupyter-anteckningsbok. Till exempel `train_explain.py`.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Konfigurera en Azure Machine Learning Compute som ditt beräknings mål och skicka din utbildnings körning. Instruktioner finns i Konfigurera [beräknings mål för modell utbildning](how-to-set-up-training-targets.md#amlcompute) . Du kan också hitta [exempel på bärbara datorer](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) .

1. Hämta förklaringen i den lokala Jupyter-anteckningsboken.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>Transformeringar av RAW-funktioner

Du kan välja att få förklaringar vad gäller RAW, ej transformerade funktioner i stället för de som har funktioner. För det här alternativet skickar du funktions omvandlings pipelinen till förklaringen i `train_explain.py`. Annars innehåller förklaringen förklaringar vad gäller de tekniker som finns.

Formatet på omvandlingar som stöds är detsamma som det beskrivs i [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). I allmänhet stöds alla omvandlingar så länge de arbetar på en enda kolumn så att de är tydliga.

Få en förklaring till RAW-funktioner med hjälp av en `sklearn.compose.ColumnTransformer` eller med en lista över monterade transformatorer. I följande exempel används `sklearn.compose.ColumnTransformer`.

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Om du vill köra exemplet med listan över monterade transformatorer, använder du följande kod:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>Visualiseringar

När du har laddat ned förklaringarna i din lokala Jupyter-anteckningsbok kan du använda visualiserings instrument panelen för att förstå och tolka din modell.

### <a name="global-visualizations"></a>Globala visualiseringar

I följande områden får du en global vy över den tränade modellen tillsammans med dess förutsägelser och förklaringar.

|Basera|Beskrivning|
|----|-----------|
|Data utforskning| Visar en översikt över data uppsättningen tillsammans med förutsägelse värden.|
|Global prioritet|Visar främsta K (konfigurerbara K) viktiga funktioner globalt. Hjälper till att förstå den underliggande modellens globala beteende.|
|Förklarings utforskning|Visar hur en funktion påverkar en ändring i modellens förutsägelse värden eller sannolikheten för förutsägelse värden. Visar effekten av funktions interaktion.|
|Sammanfattnings prioritet|Använder lokala, funktions prioritets värden för alla data punkter för att Visa fördelningen av varje funktions effekt på förutsägelse värdet.|

[Global instrument panel för ![visualisering](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Lokala visualiseringar

Du kan läsa in det lokala området för funktions prioritet för alla data punkter genom att välja den enskilda data punkten i observations området.

|Basera|Beskrivning|
|----|-----------|
|Lokal prioritet|Visar de viktigaste K-funktionerna (konfigurerbart K) globalt. Hjälper till att illustrera den underliggande modellens lokala beteende på en viss data punkt.|
|Perturbation-utforskning|Tillåter ändringar av funktions värden för den valda data punkten och observerar resulterande ändringar i förutsägelse värde.|
|Individuell villkorlig förväntad (ICE)| Tillåter funktions värdes ändringar från ett minsta värde till ett högsta värde. Hjälper dig att illustrera hur data punktens förutsägelse ändras när en funktion ändras.|

[Prioritet för den lokala funktionen i ![visualiserings instrument panelen](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[Instrument panels funktionen för ![visualisering perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![visualiserings instrument panels ICE-kurvor](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

> [!NOTE]
> Innan Jupyter-kärnan startar, se till att du aktiverar widgets tillägg för visualiserings instrument panelen.

* Jupyter Notebooks

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Använd följande kod för att läsa in visualiserings instrument panelen:

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisering i Azure Machine Learning Studio

Om du slutför stegen för [Fjärrtolkning](#interpretability-for-remote-runs) kan du Visa instrument panelen för visualiseringar i [Azure Machine Learning Studio](https://ml.azure.com). Den här instrument panelen är en enklare version av instrument panelen för visualiseringar som beskrivs ovan. Den stöder bara två flikar:

|Basera|Beskrivning|
|----|-----------|
|Global prioritet|Visar främsta K (konfigurerbara K) viktiga funktioner globalt. Hjälper till att förstå den underliggande modellens globala beteende.|
|Sammanfattnings prioritet|Använder lokala, funktions prioritets värden för alla data punkter för att Visa fördelningen av varje funktions effekt på förutsägelse värdet.|

Om både globala och lokala förklaringar är tillgängliga, fyller data i båda flikarna. Om det bara finns en global förklaring är fliken sammanfattnings prioritet inaktive rad.

Följ någon av dessa sökvägar för att få åtkomst till instrument panelen för visualiseringar i Azure Machine Learning Studio:

* **Experiment** fönstret (förhands granskning)
  1. Välj **experiment** i det vänstra fönstret om du vill se en lista över experiment som du har kört i Azure Machine Learning-tjänsten.
  1. Välj ett särskilt experiment för att visa alla körningar i experimentet.
  1. Välj en körning och sedan fliken **förklaringar** till instrument panelen förklarings visualisering.

   [Prioritet för den lokala funktionen i ![visualiserings instrument panelen](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)

* Fönstret **modeller**
  1. Om du har registrerat din ursprungliga modell genom att följa stegen i [Distribuera modeller med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where), kan du välja **modeller** i det vänstra fönstret för att visa den.
  1. Välj en modell och sedan fliken **förklaringar** för att visa instrument panelen för förklarings visualisering.

## <a name="interpretability-at-inference-time"></a>Tolkning vid en fördröjning

Du kan distribuera förklaringen tillsammans med den ursprungliga modellen och använda den vid en fördröjning för att tillhandahålla den lokala förklarings informationen. Vi erbjuder även välklarade bedömnings förklaringar för att förbättra tolknings prestanda vid en fördröjning. Processen för att distribuera en undervisad resultat förklaring liknar att distribuera en modell och innehåller följande steg:

1. Skapa ett förklarings objekt. Du kan till exempel använda `TabularExplainer`:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Skapa en bedömnings förklaring med förklarings objekt.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfigurera och registrera en avbildning som använder bedömnings modellen bedömning.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Som ett valfritt steg kan du hämta bedömnings förklaringen från molnet och testa förklaringen.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Distribuera avbildningen till ett beräknings mål genom att följa dessa steg:

   1. Om det behövs registrerar du den ursprungliga förutsägelse modellen genom att följa stegen i [Distribuera modeller med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where).

   1. Skapa en bedömnings fil.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Definiera distributionskonfigurationen.

         Den här konfigurationen beror på modellens krav. I följande exempel definieras en konfiguration som använder en processor kärna och en GB minne.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Skapa en fil med miljö beroenden.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Skapa en anpassad Dockerfile med g + + installerat.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Distribuera den skapade avbildningen.
   
         Den här processen tar cirka fem minuter.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Testa distributionen.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Rensa.

   Ta bort en distribuerad webbtjänst genom att använda `service.delete()`.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om modell tolkning](how-to-machine-learning-interpretability.md)