---
title: Fractionnements de données et validation croisée dans le Machine Learning automatisé
titleSuffix: Azure Machine Learning
description: Découvrez comment configurer la formation, la validation, la validation croisée et les données de test pour les expériences AutoML.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.custom: automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 11/15/2021
ms.openlocfilehash: 69f5913b03db51561cde17117ef97ec3c9e50868
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520614"
---
# <a name="configure-training-validation-cross-validation-and-test-data-in-automated-machine-learning"></a>Configurer la formation, la validation, la validation croisée et les données de test en AutoML

Dans cet article, vous allez découvrir les différentes options de configuration des fractionnements de données de formation et de validation et des paramètres de la validation croisée pour vos expériences de Machine Learning automatisé (AutoML).

Dans Azure Machine Learning, quand vous utilisez AutoML pour générer plusieurs modèles de ML, chaque exécution enfant doit valider le modèle associé en calculant les métriques de qualité pour ce modèle, telles que la précision ou la pondération AUC. Ces métriques sont calculées en comparant les prédictions effectuées avec chaque modèle avec des balises d’observations passées basées sur les données de validation. [En savoir plus sur le mode de calcul des métriques en fonction du type de validation](#metric-calculation-for-cross-validation-in-machine-learning). 

Les expériences d’AutoML effectuent automatiquement la validation du modèle. Les sections suivantes décrivent comment vous pouvez personnaliser davantage les paramètres de validation avec le [Kit de développement logiciel (SDK) Python pour Azure Machine Learning](/python/api/overview/azure/ml/). 

Pour une expérience avec peu, voire sans code, consultez la section [Créer vos expériences de machine learning automatisé dans Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment). 

## <a name="prerequisites"></a>Prérequis

Pour cet article, vous avez besoin des éléments suivants :

* Un espace de travail Azure Machine Learning. Pour créer l’espace de travail, voir [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Une connaissance de base en matière de configuration d’une expérience de Machine Learning automatisé avec le Kit de développement logiciel (SDK) pour Azure Machine Learning. Suivez le [didacticiel](tutorial-auto-train-models.md) ou les [procédures](how-to-configure-auto-train.md) pour afficher les modèles de conception fondamentaux des expériences de Machine Learning automatisé.

* Une compréhension des fractionnements de données de formation et de validation et de la validation croisée en tant que concepts de Machine Learning. Pour obtenir une explication plus poussée,

    * [À propos des données de formation, de validation et de test dans le Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Comprendre la validation croisée dans le Machine Learning](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

[!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>Fractionnements de données et validation croisée par défaut dans le Machine Learning

Utilisez l’objet [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) pour définir vos paramètres d’expérimentation et de formation. Dans l’extrait de code suivant, Notez que seuls les paramètres requis sont définis, c’est-à-dire que les paramètres pour `n_cross_validations` ou `validation_data` ne sont **pas** inclus.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Si vous ne spécifiez pas explicitement un paramètre `validation_data` ou `n_cross_validations`, l’AutoML applique les techniques par défaut en fonction du nombre de lignes fournies dans le jeu de données unique `training_data`.

|Formation sur la taille des&nbsp;données&nbsp;| Technique de validation |
|---|-----|
|**Contient plus&nbsp;de&nbsp;20 000&nbsp;lignes**| Le fractionnement des données de formation/validation est appliqué. La valeur par défaut consiste à prendre 10 % du jeu de données d’apprentissage initial en tant que jeu de validation. Ce jeu de validation est ensuite utilisé pour le calcul des métriques.
|**Contient moins&nbsp;de&nbsp;20 000&nbsp;lignes**| L’approche de validation croisée est appliquée. Le nombre de plis par défaut dépend du nombre de lignes. <br> **Si le jeu de données est inférieur à 1 000 lignes**, 10 plis sont utilisés. <br> **S’il y a entre 1 000 et 20 000 lignes**, trois plis sont utilisés.


## <a name="provide-validation-data"></a>Fournir des données de validation

Dans ce cas, vous pouvez démarrer avec un seul fichier de données et le fractionner en jeux de données de formation et de validation, ou vous pouvez fournir un fichier de données distinct pour le jeu de validation. Dans les deux cas, le paramètre `validation_data` de votre objet `AutoMLConfig` assigne les données à utiliser comme jeu de validation. Ce paramètre n’accepte que les jeux de données sous la forme d’un [jeu données Azure Machine Learning](how-to-create-register-datasets.md) ou d’un cadre de données Pandas.   

> [!NOTE]
> Le paramètre `validation_data` nécessite que les paramètres `training_data` et `label_column_name` soient également définis. Vous ne pouvez définir qu’un seul paramètre de validation ; en d’autres termes, vous ne pouvez spécifier que `validation_data` ou `n_cross_validations`, pas les deux.

L’exemple de code suivant définit explicitement la partie des données fournies dans `dataset` à utiliser pour l’apprentissage et la validation.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Fournir la taille du jeu de données de validation

Dans ce cas, un seul jeu de données est fourni pour l’expérience. Autrement dit, le paramètre `validation_data` n’est **pas** spécifié, et le jeu de données fourni est assigné au paramètre `training_data`.  

Dans votre objet `AutoMLConfig`, vous pouvez définir le paramètre `validation_size` pour qu’il contienne une partie des données d’apprentissage pour la validation. Cela signifie que le jeu de validation sera fractionné par Machine Learning automatisé à partir du paramètre initial `training_data` fourni. Cette valeur doit être comprise entre 0,0 et 1,0 non inclus (par exemple, 0,2 signifie que 20 % des données sont conservées pour les données de validation).

> [!NOTE]
> Le paramètre `validation_size` n’est pas pris en charge dans les scénarios de prévision. 

Prenez l'exemple du code suivant :

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>Validation croisée k-fold

Pour effectuer une validation croisée k-fold, incluez le paramètre `n_cross_validations` et affectez-lui une valeur. Ce paramètre définit le nombre de validations croisées à effectuer, en fonction du même nombre de plis.

> [!NOTE]
> Le paramètre `n_cross_validations` n’est pas pris en charge dans les scénarios de classification qui utilisent des réseaux neuronaux profonds.
 
Dans le code suivant, cinq plis pour la validation croisée sont définis. Par conséquent, il est question de cinq formations différentes. Chaque formation utilise 4/5 des données, et chaque validation utilise 1/5 des données avec un pli de données d'exclusion différent pour chaque formation.

Par conséquent, les mesures sont calculées avec la moyenne des cinq métriques de validation.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>Validation croisée Monte-Carlo

Pour effectuer une validation croisée Monte-Carlo, incluez les paramètres `validation_size` et `n_cross_validations` dans votre objet `AutoMLConfig`. 

Pour la validation croisée de Monte-Carlo, Machine Learning automatisé met de côté la partie des données d’entraînement spécifiée par le paramètre `validation_size` pour la validation, puis affecte le reste des données pour l’entraînement. Ce processus est ensuite répété en fonction de la valeur précisée dans le paramètre `n_cross_validations`, ce qui génère de nouveaux fractionnements d’entraînement et de validation, de manière aléatoire, à chaque fois.

> [!NOTE]
> La validation croisée de Monte-Carlo n’est pas prise en charge dans les scénarios de prévision.

Le code suivant définit 7 replis pour la validation croisée tandis que 20 % des données d’entraînement seront utilisés pour la validation. Ainsi, il est question de 7 entraînements différents ; chaque entraînement utilise 80 % des données, et chaque validation utilise 20 % des données avec un repli de données d’exclusion différent à chaque fois.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Spécifier des plis de données de validation croisée personnalisés

Vous pouvez également fournir vos propres plis de données de validation croisée (CV). Cela est considéré comme un scénario plus avancé, car vous spécifiez les colonnes à fractionner et à utiliser pour la validation.  Incluez les colonnes de fractionnement CV personnalisées dans vos données d’apprentissage et spécifiez les colonnes en remplissant les noms de colonnes dans le paramètre `cv_split_column_names`. Chaque colonne représente un fractionnement de validation croisée et est remplie avec des valeurs entières 1 ou 0, où 1 indique que la ligne doit être utilisée pour la formation et 0 indique que la ligne doit être utilisée pour la validation.

L’extrait de code suivant contient des données de marketing bancaires avec deux colonnes de fractionnement CV « CV1 » et « CV2 ».

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Pour utiliser `cv_split_column_names` avec `training_data` et `label_column_name`, veuillez mettre à niveau votre Kit de développement logiciel (SDK) Python pour Azure Machine Learning version 1.6.0 ou ultérieure. Pour les versions précédentes du Kit de développement logiciel (SDK), consultez l’utilisation de `cv_splits_indices`, mais notez qu’il est utilisé avec les entrées de jeu de données `X` et `y` uniquement. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Calcul métrique pour la validation croisée dans le Machine Learning

Lorsque la validation croisée k-fold ou Monte Carlo est utilisée, les métriques sont calculées sur chaque pli de validation, puis agrégées. L’opération d’agrégation est une moyenne pour les métriques scalaires et une somme pour les graphiques. Les métriques calculées pendant la validation croisée sont basées sur tous les plis et, par conséquent, sur tous les échantillons du jeu de formation. [En savoir plus sur les métriques dans le Machine Learning automatisé](how-to-understand-automated-ml.md).

Lorsqu’un jeu de validation personnalisé ou un jeu de validation sélectionné automatiquement est utilisé, les métriques d’évaluation du modèle sont calculées uniquement à partir de ce jeu de validation, et non à partir des données de formation.

## <a name="provide-test-data-preview"></a>Fournir des données de test (version préliminaire)

Vous pouvez également fournir des données de test pour évaluer le modèle recommandé que l’AutoML génère automatiquement à l’issue de l’expérience. Lorsque vous fournissez les données de test, elles sont considérées comme un distinct de la formation et de la validation, afin de ne pas biaiser les résultats de la série de tests du modèle recommandé. [En savoir plus sur les données d’apprentissage, de validation et de test dans l’apprentissage automatique.](concept-automated-ml.md#training-validation-and-test-data) 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Les jeux de données de test doivent se présenter sous la forme d’un [Azure Machine Learning TabularDataset](how-to-create-register-datasets.md#tabulardataset). Vous pouvez spécifier un jeu de données de test avec les paramètres `test_data` et `test_size` dans votre objet `AutoMLConfig`.  Ces paramètres s’excluent mutuellement et ne peuvent pas être spécifiés en même temps. 

Avec le paramètre `test_data`, spécifiez un jeu de données existant à transmettre à votre objet `AutoMLConfig`. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             ...
                             # Provide an existing test dataset
                             test_data=test_dataset,
                             ...
                             forecasting_parameters=forecasting_parameters)
```

Pour utiliser un fractionnement formation/test au lieu de fournir des données de test directement, utilisez le paramètre `test_size` lors de la création de `AutoMLConfig`. Ce paramètre doit être une valeur à virgule flottante comprise entre 0,0 et 1,0 exclusivement, et spécifie le pourcentage du jeu de données d’apprentissage qui doit être utilisé pour le jeu de données de test.

```python
automl_config = AutoMLConfig(task = 'regression',
                             ...
                             # Specify train/test split
                             training_data=training_data,
                             test_size=0.2)
```

> [!Note]
> Pour les tâches de régression, l’échantillonnage aléatoire est utilisé.<br>
> Pour les tâches de classification, l’échantillonnage stratifié est utilisé, mais l’échantillonnage aléatoire est utilisé comme un recul lorsque l’échantillonnage stratifié n’est pas possible. <br>
> Les prévisions ne prennent pas actuellement en charge la spécification d’un jeu de données de test à l’aide d’un fractionnement formation/test.

Le passage des paramètres `test_data` ou `test_size` dans le `AutoMLConfig` , déclenche automatiquement une série de tests à distance à la fin de votre expérience. Cette série de tests utilise les données de test fournies pour évaluer le meilleur modèle recommandé par l’AutoML. En savoir plus sur l'[obtention des prédictions à partir de la série de tests](how-to-configure-auto-train.md#test-models-preview).

## <a name="next-steps"></a>Étapes suivantes

* [Empêcher les données déséquilibrées et le surajustement](concept-manage-ml-pitfalls.md).
* [Tutoriel : Utiliser le machine learning automatisé pour prédire le prix des courses de taxi : division des données](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets)
* Guide pratique pour [entraîner automatiquement un modèle de prévision de série chronologique](how-to-auto-train-forecast.md).