---
title: Explicabilité du modèle dans le Machine Learning automatisé (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment obtenir des explications sur la façon dont votre modèle de Machine Learning automatisé détermine l’importance d’une caractéristique et effectue des prédictions lors de l’utilisation du Kit de développement logiciel (SDK) Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.custom: automl, responsible-ml, mktng-kw-nov2021
ms.author: mithigpe
author: minthigpen
ms.date: 10/21/2021
ms.openlocfilehash: f739da0dde7f2ef9935466ebbd4e4d4498355263
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852614"
---
# <a name="interpretability-model-explainability-in-automated-ml-preview"></a>Interprétabilité : Explicabilité du modèle dans le Machine Learning automatisé (préversion)


Cet article explique comment obtenir des explications pour les modèles de Machine Learning automatisé (AutoML) dans Azure Machine Learning à l’aide du Kit de développement logiciel (SDK) Python. Le machine learning automatisé vous aide à comprendre l’importance des caractéristiques des modèles qui sont générés. 

Toutes les versions du Kit de développement logiciel (SDK) après la version 1.0.85 définissent `model_explainability=True` par défaut. Dans le Kit de développement logiciel (SDK) version 1.0.85 et ses versions antérieures, les utilisateurs doivent définir `model_explainability=True` dans l’objet `AutoMLConfig` afin d’utiliser l’interprétabilité du modèle. 


Dans cet article, vous apprendrez comment :

- Effectuer une interprétabilité pendant la formation pour le meilleur modèle ou n’importe quel modèle.
- Activer les visualisations pour vous aider à voir les modèles dans les données et les explications.
- Implémenter une interprétabilité pendant l’inférence ou le scoring.

## <a name="prerequisites"></a>Prérequis

- Fonctionnalités d’interprétabilité. Exécutez `pip install azureml-interpret` pour obtenir le package nécessaire.
- Des connaissances en matière de génération d’expériences de ML automatisé. Pour plus d’informations sur la façon d’utiliser le Kit de développement logiciel (SDK) Azure Machine Learning, suivez ce [tutoriel sur les modèles de régression](tutorial-auto-train-models.md) ou consultez la rubrique [Configurer des expériences de ML automatisé](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Interprétabilité pendant l’entraînement pour le meilleur modèle

Récupérez l’explication à partir de `best_run`, qui inclut des explications pour les caractéristiques traitées et brutes.

> [!NOTE]
> L’interprétation, l’explication du modèle, n’est pas disponible pour le modèle TCNForecaster recommandé par la ML les expériences de prévision.

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Télécharger l’importance des caractéristiques traitées à partir de la meilleure exécution

Vous pouvez utiliser `ExplanationClient` pour télécharger des explications de caractéristiques traitées à partir du magasin d’artefacts de `best_run`. 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Télécharger l’importance des caractéristiques brutes à partir de la meilleure exécution

Vous pouvez utiliser `ExplanationClient` pour télécharger des explications de caractéristiques brutes à partir du magasin d’artefacts de `best_run`.

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interprétabilité pendant l’entraînement de n’importe quel modèle 

Lorsque vous calculez des explications de modèle et que vous les visualisez, vous n’êtes pas limité à une explication de modèle existante pour un modèle AutoML. Vous pouvez également obtenir une explication de votre modèle avec des données de test différentes. Les étapes de cette section vous montrent comment calculer et visualiser l’importance des caractéristiques traitées en fonction de vos données de test.

### <a name="retrieve-any-other-automl-model-from-training"></a>Récupérer tout autre modèle AutoML à partir de l’entraînement

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Configurer les explications de modèle

Utilisez `automl_setup_model_explanations` pour obtenir des explications relatives aux caractéristiques brutes et traitées. Le `fitted_model` peut générer les éléments suivants :

- Données proposées à partir d’exemples formés ou test
- Listes de noms de caractéristiques traitées
- Classes accessibles dans votre colonne étiquetée dans des scénarios de classification

Le `automl_explainer_setup_obj` contient toutes les structures de la liste ci-dessus.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Initialiser l’explicatif d’imitation pour l’importance des caractéristiques

Si vous souhaitez générer une explication pour les modèles de machine learning automatisé, utilisez la classe `MimicWrapper`. Vous pouvez initialiser le MimicWrapper avec les paramètres suivants :

- L’objet de configuration explicatif
- Votre espace de travail
- Modèle de substitution pour expliquer le modèle de ML automatisé `fitted_model`

MimicWrapper prend également l’objet `automl_run` où les explications de caractéristiques traitées seront chargées.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Utiliser l’explicatif d’imitation pour le calcul et la visualisation de l’importance des caractéristiques traitées

Vous pouvez appeler a méthode `explain()` dans MimicWrapper avec les exemples de tests transformés pour obtenir l’importance des caractéristiques traitées générées. Vous pouvez également vous connecter à [Azure Machine Learning Studio](https://ml.azure.com/) pour voir la visualisation du tableau de bord des explications. Vous y verrez les valeurs d’importance des caractéristiques traitées qui sont générées par des caractériseurs AutoML.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```
Pour les modèles formés avec Machine Learning automatisé, vous pouvez obtenir le meilleur modèle en utilisant la méthode `get_output()` et des explications de calcul localement.  Vous pouvez visualiser les résultats des explications avec `ExplanationDashboard` du package `raiwidgets`.

```python
best_run, fitted_model = remote_run.get_output()

from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations
automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train,
                                                             X_test=X_test, y=y_train,
                                                             task='regression')

from interpret.ext.glassbox import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel,
                         init_dataset=automl_explainer_setup_obj.X_transform, run=best_run,
                         features=automl_explainer_setup_obj.engineered_feature_names,
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
                         
pip install interpret-community[visualization]

engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict()),
from raiwidgets import ExplanationDashboard
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, datasetX=automl_explainer_setup_obj.X_test_transform)

 

raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(raw_explanations.get_feature_importance_dict()),
from raiwidgets import ExplanationDashboard
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, datasetX=automl_explainer_setup_obj.X_test_raw)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Utiliser l’explicatif d’imitation pour le calcul et la visualisation de l’importance des caractéristiques brutes

Vous pouvez appeler a méthode `explain()` dans MimicWrapper avec les exemples de tests transformés pour obtenir l’importance des caractéristiques brutes. Dans [Machine Learning Studio](https://ml.azure.com/), vous pouvez afficher la visualisation du tableau de bord qui montre les valeurs d’importance des caractéristiques brutes.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Interprétabilité pendant l’inférence

Dans cette section, vous allez apprendre à rendre un modèle de ML automatisé opérationnel avec l’explicatif qui a été utilisé pour calculer les explications dans la section précédente.

### <a name="register-the-model-and-the-scoring-explainer"></a>Inscrire le modèle et l’explicatif de scoring

Utilisez `TreeScoringExplainer` pour créer l’explicatif de scoring, qui calculera les valeurs d’importance des caractéristiques traitées au moment de l’inférence. Vous initialisez l’explicatif de scoring avec la valeur `feature_map` qui a été calculée précédemment. 

Enregistrez l’explicatif de scoring, puis inscrivez le modèle et l’explicatif de scoring auprès du service Gestion des modèles ML. Exécutez le code ci-dessous :

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Créer les dépendances Conda pour la configuration du service

Ensuite, créez les dépendances d’environnement nécessaires dans le conteneur du modèle déployé. Notez que vous devez faire figurer azureml-defaults avec une version supérieure ou égale à 1.0.45 comme dépendance pip, car elle contient les fonctionnalités nécessaires pour héberger le modèle en tant que service web.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>Créer le script de scoring

Écrivez un script qui charge votre modèle et génère des prédictions et des explications basées sur un nouveau lot de données.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>Déployer le service

Déployez le service à l’aide du fichier Conda et du fichier de scoring des étapes précédentes.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Inférer à l’aide de données de test

Inférez avec quelques données de test pour voir la valeur prédite du modèle AutoML (actuellement pris en charge uniquement dans le SDK Azure Machine Learning). Affichez l’importance des caractéristiques contribuant à une valeur prédite. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

## <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualiser pour découvrir des modèles dans les données et les explications au moment de la formation

Vous pouvez visualiser le graphique d’importance d’une caractéristique dans votre espace de travail dans [Azure Machine Learning Studio](https://ml.azure.com). Une fois votre exécution AutoML terminée, sélectionnez **Afficher les détails du modèle** pour afficher une exécution spécifique. Sélectionnez l’onglet **Explications** pour voir les visualisation dans le tableau de bord des explications.

[![Architecture d’interprétabilité de Machine Learning](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Pour plus d’informations sur les visualisations et les tracés spécifiques du tableau de bord, reportez-vous à la [documentation sur l’interprétabilité](how-to-machine-learning-interpretability-aml.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon dont vous pouvez activer les explications de modèle et l’importance des fonctionnalités dans des zones autres que le Machine Learning automatisé, découvrez d’[autres techniques pour l’interprétabilité des modèles](how-to-machine-learning-interpretability.md).
