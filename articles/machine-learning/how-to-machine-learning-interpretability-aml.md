---
title: Utiliser Python pour interpréter et expliquer des modèles (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment obtenir des explications sur la façon dont votre modèle de Machine Learning détermine l’importance des fonctionnalités et effectue des prédictions lors de l’utilisation du kit de développement logiciel (SDK) Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, responsible-ml, mktng-kw-nov2021
ms.openlocfilehash: f22bee60a86d162c57313ab33811a21408eefd74
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847448"
---
# <a name="use-the-python-interpretability-package-to-explain-ml-models--predictions-preview"></a>Utiliser le package d’interprétabilité Python pour expliquer des modèles et prédictions de Machine Learning (préversion)

Dans ce guide pratique, vous allez apprendre à effectuer les tâches suivantes en utilisant le package d’interprétabilité du SDK Python Azure Machine Learning :


* Expliquer la totalité du comportement du modèle ou des prédictions individuelles sur votre ordinateur personnel localement

* Activer les techniques d’interprétabilité pour les caractéristiques traitées

* Expliquer le comportement de la totalité du modèle et de prédictions individuelles dans Azure

* Téléchargez des explications sur l’historique des exécutions Azure Machine Learning.

* Utilisez un tableau de bord de visualisation pour interagir avec les explications de votre modèle, à la fois dans un notebook Jupyter et dans Azure Machine Learning Studio.

* Déployer un explicatif de scoring à côté de votre modèle pour observer les explications au cours de l’inférence


Pour plus d’informations sur les techniques d’interprétabilité et les modèles Machine Learning pris en charge, consultez [Interprétabilité des modèles dans Azure Machine Learning](how-to-machine-learning-interpretability.md) et les [exemples de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).

Pour obtenir des conseils sur l’activation de l’interprétabilité des modèles entraînés avec le machine learning automatisé, consultez l’article [Interprétabilité : explications des modèles en machine learning automatisé (version préliminaire)](how-to-machine-learning-interpretability-automl.md). 

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Générer la valeur d’importance d’une caractéristique sur votre ordinateur personnel 
L’exemple suivant montre comment utiliser le package d’interprétabilité sur votre ordinateur personnel sans contacter les services Azure.

1. Installez le package `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```

2. Entraînez un exemple de modèle dans un notebook Jupyter local.

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

3. Appelez l’explicatif localement.
   * Pour initialiser un objet d’explicatif, transmettez votre modèle et des données de formation au constructeur de l’explicatif.
   * Vous pouvez transmettre des noms de fonctionnalités et des noms de classes de sortie, en cas de classification, qui seront utilisés pour que vos descriptions et visualisations soient plus explicites encore.

   Les blocs de code suivants montrent comment instancier un objet explicatif avec `TabularExplainer`, `MimicExplainer` et `PFIExplainer` localement.
   * `TabularExplainer` appelle un des trois explicatifs SHAP sous-jacents (`TreeExplainer`, `DeepExplainer` ou `KernelExplainer`).
   * `TabularExplainer` sélectionne automatiquement le plus approprié pour votre cas d’utilisation, mais vous pouvez appeler chacun des trois explicatifs sous-jacents directement.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    or

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

    or

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Expliquer le comportement de la totalité du modèle (explication globale) 

Reportez-vous à l’exemple suivant pour vous aider à obtenir les valeurs d’importance de caractéristique d’agrégat (globale).

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Expliquer une prédiction individuelle (explication locale)
Obtenez les valeurs d’importance de caractéristique individuelle de différents points de données en appelant des explicatifs pour une instance individuelle ou un groupe d’instances.
> [!NOTE]
> `PFIExplainer` ne prend pas en charge les explicatifs locaux.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Transformations de fonctionnalité brute

Vous pouvez choisir d’obtenir des explications en termes de fonctionnalités brutes, non transformées plutôt que de fonctionnalités conçues. Pour cette option, vous transmettez votre pipeline de transformation de fonctionnalité à l’explicatif dans `train_explain.py`. Sinon, l’explicatif fournit des explications en termes de fonctionnalités conçues.

Le format de transformations prises en charge est le même que celui décrit dans [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). En règle générale, toutes les transformations sont prises en charge tant qu’elles s’appliquent à une colonne unique et qu’elles constituent ainsi clairement des transformations un à plusieurs.

Pour obtenir une explication sur les fonctionnalités brutes, utilisez un `sklearn.compose.ColumnTransformer` ou une liste de tuples de transformateur adaptés. L’exemple suivant utilise `sklearn.compose.ColumnTransformer`.

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

Si vous souhaitez exécuter l’exemple avec la liste de tuples de transformateur adaptés, utilisez le code suivant :

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Générer des valeurs d’importance d’une caractéristique par le biais d’exécutions distantes

L’exemple suivant montre comment vous pouvez utiliser la classe `ExplanationClient` pour activer l’interprétation du modèle pour les exécutions distantes. Son concept est similaire à celui du processus local, à l’exception des éléments suivants :

* Utilisez `ExplanationClient` dans l’exécution à distance pour télécharger le contexte d’interprétation.
* Téléchargez le contexte ultérieurement dans un environnement local.

1. Installez le package `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```
1. Créez un script d’entraînement dans un notebook Jupyter local. Par exemple : `train_explain.py`.

    ```python
    from azureml.interpret import ExplanationClient
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

1. Configurez une capacité de calcul Machine Learning comme cible de calcul et envoyez votre exécution d’apprentissage. Pour obtenir des instructions, consultez [Créer et gérer des clusters de calcul Azure Machine Learning](how-to-create-attach-compute-cluster.md). Vous pourriez également trouver les [exemples de notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) utiles.

1. Téléchargez l’explication dans votre notebook Jupyter local.

    ```python
    from azureml.interpret import ExplanationClient
    
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


## <a name="visualizations"></a>Visualisations

Une fois que vous avez téléchargé les explications dans votre instance locale de Jupyter Notebook, vous pouvez utiliser les visualisations du tableau de bord des explications pour comprendre et interpréter votre modèle. Pour charger le widget de tableau de bord des explications dans Jupyter Notebook, utilisez le code suivant :

```python
from raiwidgets import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

Les visualisations prennent en charge les explications sur les caractéristiques développées et les caractéristiques brutes. Les explications brutes sont basées sur les caractéristiques issues du jeu de données d’origine et les explications de conception sont basées sur les caractéristiques issues du jeu de données auquel s’applique l’ingénierie des caractéristiques.

Lorsque vous tentez d’interpréter un modèle par rapport au jeu de données d’origine, il est recommandé d’utiliser des explications brutes, car l’importance de chaque caractéristique correspond à une colonne du jeu de données d’origine. L’examen de l’impact des catégories individuelles à partir d’une caractéristique catégorielle est un scénario dans lequel les explications de conception peuvent être utiles. Si un encodage à chaud est appliqué à une caractéristique catégorielle, les explications de conception qui en résultent comportent une valeur d’importance différente par catégorie, une par caractéristique de conception à chaud. Cet encodage peut être utile lorsque vous vous concentrez sur la partie du jeu de données qui fournit le plus d’informations au modèle.

> [!NOTE]
> Les explications de conception et brutes sont calculées séquentiellement. En premier lieu, une explication de conception est créée sur la base du modèle et du pipeline de caractérisation. Ensuite, l’explication brute est créée sur la base de cette explication de conception, en agrégeant l’importance des caractéristiques de conception issues de la même caractéristique brute.

### <a name="create-edit-and-view-dataset-cohorts"></a>Créer, modifier et afficher des cohortes de jeu de données

Le ruban supérieur affiche des statistiques globales sur votre modèle et vos données. Vous pouvez segmenter et découper vos données en cohortes de jeu de données, ou sous-groupes, pour examiner ou comparer les performances et les explications de votre modèle dans ces sous-groupes définis. En comparant les statistiques et les explications de votre jeu de données entre ces sous-groupes, vous pouvez vous faire une idée de la raison pour laquelle des erreurs peuvent se produire dans un groupe plutôt qu’un autre.

[![Création, modification et affichage de cohortes de jeu de données](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Comprendre le comportement de la totalité du modèle (explication globale) 

Les trois premiers onglets du tableau de bord d’explication fournissent une analyse globale du modèle entraîné, avec ses prédictions et explications.

#### <a name="model-performance"></a>Performances du modèle
Évaluez les performances de votre modèle en explorant la distribution de vos valeurs de prédiction et les valeurs des métriques de performances de votre modèle. Vous pouvez approfondir l’étude de votre modèle en examinant une analyse comparative de ses performances entre différentes cohortes ou sous-groupes de votre jeu de données. Sélectionnez des filtres le long de la valeur y et de la valeur x pour couper différentes dimensions. Affichez les métriques telles que la justesse, la précision, le rappel, le taux de faux positifs (FPR) et le taux de faux négatif (FNR).

[![Onglet des performances du modèle dans la visualisation des explications](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Explorateur de jeu de données
Explorez les statistiques de votre jeu de données en sélectionnant différents filtres le long des axes X, Y et des couleurs pour découper vos données selon différentes dimensions. Créez les cohortes de jeu de données ci-dessus pour analyser les statistiques du jeu de données à l’aide de filtres, tels que le résultat prédit, les caractéristiques du jeu de données et les groupes d’erreurs. Utilisez l’icône d’engrenage en haut à droite du graphique pour changer les types de graphique.

[![Onglet de l’explorateur de jeu de données dans la visualisation des explications](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Agréger l’importance des caractéristiques
Explorez les k caractéristiques les plus importantes qui ont un impact sur vos prédictions de modèle globales (également appelées explication globale). Utilisez le curseur pour afficher les valeurs d’importance des caractéristiques décroissantes. Sélectionnez jusqu’à trois cohortes pour voir leurs valeurs d’importance de caractéristiques côte à côte. Cliquez sur l’une des barres de caractéristiques du graphique pour voir comment les valeurs de la caractéristique sélectionnée influencent la prédiction de modèle dans le tracé des dépendances ci-dessous.

[![Onglet Agréger l’importance des caractéristiques dans la visualisation des explications](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Comprendre les prédictions individuelles (explication locale) 

Le quatrième onglet des explications vous permet d’explorer un point de données individuel et l’importance de ses caractéristiques individuelles. Vous pouvez charger le tracé de l’importance des caractéristiques individuelles pour n’importe quel point de données en cliquant sur l’un des points de données individuels dans le nuage de points principal ou en sélectionnant un point de données spécifique dans le volet d’Assistant de droite.

|Tracé|Description|
|----|-----------|
|Importance des caractéristiques individuelles|Montre les k caractéristiques les plus importantes pour une prédiction individuelle. Permet d’illustrer le comportement local du modèle sous-jacent sur un point de données spécifique.|
|Analyse de simulation|Autorise les modifications des valeurs de caractéristiques du point de données réel sélectionné et observe les modifications résultantes de la valeur de prédiction en générant un point de données hypothétique avec les nouvelles valeurs de caractéristiques.|
|Attente conditionnelle individuelle (ICE)|Autorise les modifications de valeur de fonctionnalité d’une valeur minimale à une valeur maximale. Permet d’illustrer la façon dont la prédiction du point de données change en cas de modification d’une fonctionnalité.|

[![Importance des caractéristiques individuelles et onglet Simulation dans le tableau de bord d’explication](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Il s’agit d’explications basées sur de nombreuses approximations et non pas de la « cause » des prédictions. Sans une robustesse mathématique stricte de l’inférence de causalité, nous ne conseillons pas aux utilisateurs de prendre des décisions concrètes en fonction des perturbations des caractéristiques de l’outil de simulation. Cet outil est principalement destiné à la compréhension de votre modèle et au débogage.

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisation dans Azure Machine Learning Studio

Si vous effectuez les étapes décrites d’[interprétabilité à distance](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (chargement des explications générées dans l’historique des exécutions d’Azure Machine Learning), vous pouvez voir les visualisations du tableau de bord des explications dans [Azure Machine Learning Studio](https://ml.azure.com). Ce tableau de bord est une version simplifiée du widget de tableau de bord qui est généré dans votre instance Jupyter Notebook. Les tracés ICE et de génération de point de données de simulation sont désactivés, car il n’existe aucun calcul actif dans Azure Machine Learning Studio à même d’effectuer leurs calculs en temps réel.

Si les explications de jeu de données, globales et locales sont disponibles, les données remplissent tous les onglets. Si seule une explication globale est disponible, l’onglet Importance des caractéristiques individuelles est désactivé.

Suivez l’un de ces parcours pour accéder au tableau de bord des explications dans Azure Machine Learning Studio :

* Volet des **Expériences** (préversion)
  1. Sélectionnez **Expériences** dans le volet gauche pour afficher la liste des expériences que vous avez exécutées sur Azure Machine Learning.
  1. Sélectionnez une expérience particulière pour afficher toutes les exécutions de cette expérience.
  1. Sélectionnez une exécution, puis l'onglet **Explications** pour voir le tableau de bord de visualisation des explications.

   [![Tableau de bord de visualisation avec Agréger l’importance des caractéristiques dans AzureML Studio dans les expériences](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* Volet **Modèles**
  1. Si vous avez enregistré votre modèle d’origine en suivant les étapes décrites dans [Déployer des modèles avec Azure Machine Learning](./how-to-deploy-and-where.md), vous pouvez sélectionner **Modèles** dans le volet gauche pour l’afficher.
  1. Sélectionnez un modèle, puis l'onglet **Explications** pour voir le tableau de bord des explications.

## <a name="interpretability-at-inference-time"></a>Interprétabilité au moment de l’inférence

Vous pouvez déployer l’explicatif avec le modèle d’origine et l’utiliser au moment de l’inférence pour fournir les valeurs d’importance des caractéristiques individuelles (explication locale) pour tout nouveau point de donnée. Nous proposons également des explicatifs de scoring plus légers pour améliorer les performances d’interprétabilité au moment de l’inférence, qui sont actuellement pris en charge uniquement dans le kit SDK Azure Machine Learning. Le processus de déploiement d’un explicatif de scoring plus léger est similaire au déploiement d’un modèle et comprend les étapes suivantes :

1. Créez un objet d’explication. Par exemple, vous pouvez utiliser `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Créer un explicatif de scoring avec l’objet d’explication.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configurez et inscrivez une image qui utilise le modèle d’explicatif de scoring.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Vous pouvez éventuellement récupérer l’explicatif de scoring sur le cloud et tester les explications.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Déployez l’image sur une cible de calcul en procédant comme suit :

   1. Si nécessaire, enregistrez votre modèle de prédiction d’origine en suivant les étapes décrites dans [Déployer des modèles avec Azure Machine Learning](./how-to-deploy-and-where.md).

   1. Créez un fichier de scoring.

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
   1. Définissez la configuration du déploiement.

         Cette configuration dépend des exigences de votre modèle. L’exemple suivant définit une configuration qui utilise un seul cœur d’UC et 1 Go de mémoire.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Créer un fichier avec des dépendances d’environnement.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Créer un fichier dockerfile personnalisé avec g++ installé.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Déployez l’image créée.
   
         Ce processus prend environ cinq minutes.

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

1. Testez le déploiement.

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

1. Nettoyer.

   Pour supprimer un service web déployé, utilisez `service.delete()`.

## <a name="troubleshooting"></a>Résolution des problèmes

* **Données éparses non prises en charge** : Le tableau de bord d’explication de modèle s’interrompt/ralentit considérablement avec un grand nombre de caractéristiques. Par conséquent, nous ne prenons actuellement pas en charge le format de données éparses. En outre, des problèmes de mémoire générale surviendront avec les jeux de données volumineux et un grand nombre de caractéristiques. 
* **Matrice des fonctionnalités d’explications prises en charge**

Onglet explication prise en charge | Caractéristiques brutes (denses) | Caractéristiques brutes (éparses) | Caractéristiques développées (denses) | Caractéristiques développées (éparses)
:----- | :--- | :--- | :---- | :---- |
Performances du modèle   | Pris en charge (sans prévision) | Pris en charge (sans prévision)  | Prise en charge | Prise en charge |
Explorateur de jeu de données  | Pris en charge (sans prévision)   | Non pris en charge. Étant donné que les données éparses ne sont pas chargées et que l’interface utilisateur rencontre des problèmes de rendu des données éparses. | Prise en charge | Non pris en charge. Étant donné que les données éparses ne sont pas chargées et que l’interface utilisateur rencontre des problèmes de rendu des données éparses. | 
 Agréger l’importance des caractéristiques | Prise en charge | Prise en charge | Prise en charge | Prise en charge |
 Importance des caractéristiques individuelles| Pris en charge (sans prévision)   | Non pris en charge. Étant donné que les données éparses ne sont pas chargées et que l’interface utilisateur rencontre des problèmes de rendu des données éparses. | Prise en charge | Non pris en charge. Étant donné que les données éparses ne sont pas chargées et que l’interface utilisateur rencontre des problèmes de rendu des données éparses. | 


* **Modèles de prévision non pris en charge avec les explications de modèle** : L’interprétabilité, la meilleure explication du modèle, n’est pas disponible pour les expériences de prévision AutoML, qui recommandent les algorithmes suivants comme modèle optimal : TCNForecaster, AutoArima, Prophet, ExponentialSmoothing, Moyenne, Naïf, Moyenne saisonnière et Naïf saisonnier. La prévision AutoML a des modèles de régression qui prennent en charge les explications. Toutefois, dans le tableau de bord d’explication, l’onglet « Importance des caractéristiques individuelles » n’est pas pris en charge pour la prévision en raison de la complexité des pipelines de données.

* **Explication locale d’index de données** : Le tableau de bord d’explication ne prend pas en charge l’association des valeurs d’importance locale à un identificateur de ligne issu du jeu de données de validation d’origine si ce jeu de données dépasse les 5 000 points de données, car le tableau de bord sous-échantillonne les données de manière aléatoire. Toutefois, le tableau de bord affiche les valeurs de caractéristiques de jeu de données brutes pour chaque point de données transmis dans le tableau de bord sous l’onglet Importance des caractéristiques individuelles. Les utilisateurs peuvent mapper les importantes locales en retour sur le jeu de données d’origine en faisant correspondre les valeurs de caractéristiques de jeu de données brutes. Si la taille du jeu de données de validation est inférieure à 5 000 échantillons, la caractéristique `index` dans AzureML Studio correspond à l’index dans le jeu de données de validation.

* **Tracés de simulation/ICE non pris en charge dans Studio** : Les tracés de simulation et d’attente conditionnelle individuelle (ICE) ne sont pas pris en charge dans Azure Machine Learning Studio sous l’onglet Explications puisque l’explication chargée a besoin d’un calcul actif pour recalculer les prédictions et les probabilités des caractéristiques perturbées. Ils sont actuellement pris en charge dans les notebooks Jupyter lorsqu’ils sont exécutés en tant que widget à l’aide du kit SDK.


## <a name="next-steps"></a>Étapes suivantes

[Techniques pour l’interprétabilité des modèles dans Azure ML](how-to-machine-learning-interpretability.md)

[Consultez les exemples de notebooks sur l’interprétabilité d’Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
