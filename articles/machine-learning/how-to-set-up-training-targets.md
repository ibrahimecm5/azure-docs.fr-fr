---
title: Configurer une exécution d’entraînement
titleSuffix: Azure Machine Learning
description: Effectuez l’apprentissage de votre modèle Machine Learning sur divers environnements de formation (cibles de calcul). Vous pouvez facilement basculer entre différents environnements d’entraînement.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1
ms.openlocfilehash: 020296957e09743610ab46db74e663d86901d116
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179584"
---
# <a name="configure-and-submit-training-runs"></a>Configurer et soumettre des exécutions d’entraînement

Dans cet article, vous allez apprendre à configurer et à soumettre des exécutions Azure Machine Learning pour entraîner vos modèles. Des extraits de code décrivent les éléments clés de la configuration et de la soumission d'un script de formation.  Utilisez ensuite l'un des [exemples de notebooks](#notebooks) pour accéder aux exemples fonctionnels complets de bout en bout.

Lors de l’entraînement, il est courant de démarrer sur votre ordinateur local, puis d’effectuer un scale-out vers un cluster cloud. Avec Azure Machine Learning, vous pouvez exécuter votre script sur différentes cibles de calcul sans avoir à modifier votre script de formation.

Il vous suffit de définir l’environnement pour chaque cible de calcul dans une **configuration d’exécution de script**.  Ensuite, lorsque vous souhaitez exécuter votre expérience de formation sur une autre cible de calcul, spécifiez la configuration de série de tests pour celle-ci.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui
* Le [kit de développement logiciel (SDK) Azure Machine Learning pour Python](/python/api/overview/azure/ml/install) (>= 1.13.0)
* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md), `ws`
* Une cible de calcul, `my_compute_target`.  [Créer une cible de calcul](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Qu’est-ce qu’une configuration d’exécution de script ?
Un objet [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) sert à configurer les informations nécessaires pour soumettre une exécution d’entraînement dans le cadre d’une expérience.

Vous soumettez votre expérience de formation à l’aide d’un objet ScriptRunConfig.  Cet objet inclut ce qui suit :

* **source_directory** : Répertoire source contenant votre script d’apprentissage
* **script** : script d’entraînement à exécuter
* **compute_target** : cible de calcul sur laquelle effectuer l’exécution
* **environment** : environnement à utiliser lors de l’exécution du script
* Et d’autres options configurables (consultez la [documentation de référence](/python/api/azureml-core/azureml.core.scriptrunconfig) pour plus d’informations)

## <a name="train-your-model"></a><a id="submit"></a>Effectuer l’apprentissage de votre modèle

Le modèle de code pour soumettre une série de tests d’apprentissage est le même pour tous les types de cibles de calcul :

1. Créer une expérience à exécuter
1. Créer un environnement dans lequel le script sera exécuté
1. Créer un objet ScriptRunConfig, qui spécifie la cible de calcul et l’environnement
1. Soumettre l’exécution
1. Attendre la fin de l’exécution

Vous pouvez également :

* Soumettre une série de tests HyperDrive pour un [réglage d’hyperparamètre](how-to-tune-hyperparameters.md).
* Soumettre une expérience via l’[extension VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Créer une expérience

Créez une [expérience](concept-azure-machine-learning-architecture.md#experiments) dans votre espace de travail. Une expérience est un conteneur léger qui permet d’organiser des soumissions d’exécution et d’effectuer le suivi du code.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Sélectionner une cible de calcul

Sélectionnez la cible de calcul sur laquelle votre script d’entraînement sera exécuté. Si aucune cible de calcul n’est spécifiée dans l’objet ScriptRunConfig, ou si `compute_target='local'`, Azure ML exécute votre script localement. 

L’exemple de code fourni dans cet article suppose que vous avez déjà créé une cible de calcul `my_compute_target` comme indiqué dans la section « Prérequis ».

>[!Note]
>Azure Databricks n’est pas pris en charge comme cible de calcul pour la formation des modèles. Vous pouvez utiliser Azure Databricks pour les tâches de préparation des données et de déploiement.

[!INCLUDE [arc-enabled-kubernetes](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

## <a name="create-an-environment"></a><a name="environment"></a> Créer un environnement
Les [environnements](concept-environments.md) Azure Machine Learning sont une encapsulation de l’environnement dans lequel votre formation Machine Learning se produit. Ils spécifient les packages, image Docker, variables d’environnement et paramètres logiciels Python autour de vos scripts d’entraînement et de scoring. Ils spécifient également les temps d’exécution (Python, Spark ou Docker).

Vous pouvez définir votre propre environnement ou utiliser un environnement organisé Azure ML. Les [environnements organisés](./how-to-use-environments.md#use-a-curated-environment) sont des environnements prédéfinis qui sont disponibles dans votre espace de travail par défaut. Ces environnements s’appuient sur des images Docker mises en cache, ce qui réduit le coût de préparation de l’exécution. Consultez [Environnements organisés Azure Machine Learning](./resource-curated-environments.md) pour obtenir la liste complète des environnements organisés disponibles.

Pour une cible de calcul à distance, vous pouvez utiliser l’un de ces environnements organisés courants pour commencer :

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Pour plus d’informations et pour obtenir des détails sur les environnements, consultez [Créer et utiliser des environnements logiciels dans Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Cible de calcul locale

Si votre cible de calcul est votre **ordinateur local**, vous devez vous assurer que tous les packages nécessaires sont disponibles dans l’environnement Python où le script s’exécute.  Utilisez `python.user_managed_dependencies` pour utiliser votre environnement Python actuel (ou celui sous le chemin d’accès que vous spécifiez).

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>Créer la configuration d’exécution du script

Maintenant que vous avez une cible de calcul (`my_compute_target`, voir [Prérequis](#prerequisites)) et un environnement (`myenv`, voir [Créer un environnement](#environment)), créez une configuration d’exécution de script qui exécute votre script de formation (`train.py`) dans votre répertoire `project_folder` :

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Si vous ne spécifiez pas d’environnement, un environnement par défaut est créé pour vous.

Si vous souhaitez passer des arguments de ligne de commande à votre script d’entraînement, vous pouvez les spécifier par le biais du paramètre **`arguments`** du constructeur ScriptRunConfig, par exemple `arguments=['--arg1', arg1_val, '--arg2', arg2_val]`.

Si vous souhaitez remplacer la durée maximale par défaut autorisée pour l’exécution, vous pouvez le faire avec le paramètre **`max_run_duration_seconds`** . Le système tente d’annuler automatiquement l’exécution si elle prend plus de temps que cette valeur.

### <a name="specify-a-distributed-job-configuration"></a>Spécifier une configuration de travail distribué
Si vous souhaitez exécuter une tâche d’[apprentissage distribué](how-to-train-distributed-gpu.md), renseignez la configuration spécifique à la tâche distribuée au niveau du paramètre **`distributed_job_config`** . Les types de configuration pris en charge sont les suivants : [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) et [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration). 

Pour plus d’informations et des exemples sur l’exécution des travaux distribués Horovod, TensorFlow et PyTorch, consultez :

* [Entraîner des modèles TensorFlow](./how-to-train-tensorflow.md#distributed-training)
* [Entraîner des modèles PyTorch](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Soumettre l’expérimentation

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Quand vous soumettez la série de tests d’apprentissage, un instantané du répertoire contenant vos scripts d’apprentissage est créé et envoyé à la cible de calcul. Il est également stocké dans le cadre de l’expérience dans votre espace de travail. Si vous modifiez des fichiers et que vous soumettez à nouveau l’exécution, seuls les fichiers modifiés seront téléchargés.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Pour plus d’informations sur les instantanés, consultez [Instantanés](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Dossiers spéciaux** : deux dossiers, *outputs* et *logs*, reçoivent un traitement spécial de la part d’Azure Machine Learning. Pendant l’entraînement, quand vous écrivez des fichiers dans les dossiers *outputs* et *logs* relatifs au répertoire racine (`./outputs` et `./logs`, respectivement), les fichiers sont automatiquement chargés dans votre historique des exécutions. Vous pouvez ainsi y accéder une fois l’exécution terminée.
>
> Pour créer des artefacts pendant l’entraînement (par exemple, des fichiers de modèle, des points de contrôle, des fichiers de données ou des images tracées), écrivez-les dans le dossier `./outputs`.
>
> De même, vous pouvez écrire tous les journaux d’activité de votre exécution d’entraînement dans le dossier `./logs`. Pour utiliser [l’intégration TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) d’Azure Machine Learning, écrivez vos journaux d’activité TensorBoard dans ce dossier. Lorsque votre exécution est en cours, vous pouvez lancer TensorBoard et diffuser ces journaux d’activité en continu.  Plus tard, vous pourrez également restaurer les journaux d’activité à partir de l’une de vos exécutions précédentes.
>
> Par exemple, pour télécharger sur votre ordinateur local un fichier écrit dans le dossier *outputs* après votre exécution d’entraînement distant : `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Intégration et suivi Git

Lorsque vous lancez une exécution d’entraînement où le répertoire source est un répertoire Git local, les informations relatives au répertoire sont stockées dans l’historique des exécutions. Pour plus d’informations, consultez [Obtenir une intégration pour Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a><a name="notebooks"></a>Exemples de notebooks

Consultez ces notebooks pour obtenir des exemples de configuration d’exécutions pour différents scénarios d’entraînement :
* [Entraînement sur différentes cibles de calcul](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Entraînement avec des frameworks ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>Résolution des problèmes

* **AttributeError : l’objet « RoundTripLoader »n’a pas d’attribut « comment_handling »**  : cette erreur provient de la nouvelle version (v 0.17.5) de `ruamel-yaml`, une dépendance `azureml-core` qui introduit un changement cassant à `azureml-core` . Pour corriger cette erreur, désinstallez `ruamel-yaml` en exécutant `pip uninstall ruamel-yaml` et en installant une version différente de `ruamel-yaml` ; les versions prises en charge vont de la v0.15.35 à la v0.17.4 (comprises). Pour ce faire, exécutez `pip install "ruamel-yaml>=0.15.35,<0.17.5"`.


* **L’exécution échoue avec `jwt.exceptions.DecodeError`**  : Message d’erreur exact : `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()`. 
    
    Envisagez une mise à niveau vers la dernière version d’azureml-core : `pip install -U azureml-core`.
    
    Si vous rencontrez ce problème pour des exécutions locales, vérifiez la version de PyJWT installée de l’environnement où vous démarrez les exécutions. Les versions prises en charge de PyJWT sont supérieures à la version 2.0.0. Désinstallez PyJWT de l’environnement si la version est > = 2.0.0. Vous pouvez vérifier la version de PyJWT, désinstaller et installer la version correcte comme suit :
    1. Démarrez un interpréteur de commandes, puis activez l’environnement Conda dans lequel azureml-core est installé.
    2. Entrez `pip freeze` et recherchez `PyJWT` : le cas échéant, la version indiquée doit être antérieure à la version 2.0.0.
    3. Si la version indiquée n’est pas une version prise en charge, exécutez `pip uninstall PyJWT` dans l’interpréteur de commandes et entrez « y » pour confirmer.
    4. Installer à l’aide de `pip install 'PyJWT<2.0.0'`
    
    Si vous soumettez un environnement créé par l’utilisateur avec votre exécution, vous pouvez utiliser la dernière version d’azureml-core dans cet environnement. Les versions > = 1.18.0 d’azureml-core sont déjà compatibles avec PyJWT < 2.0.0. Si vous devez utiliser une version d’azureml-core < 1.18.0 dans l’environnement que vous envoyez, veillez à spécifier PyJWT < 2.0.0 dans vos dépendances pip.


 * **ModuleErrors (aucun module nommé)**  :  Si vous rencontrez des erreurs de module (ModuleErrors) lors de l’envoi d’expériences dans Azure ML, le script d’entraînement attend qu’un package soit installé, mais il n’est pas ajouté. Une fois que vous avez fourni le nom du package, Azure ML installe ce dernier dans l’environnement utilisé pour l’entraînement.

    Si vous utilisez des Estimateurs pour soumettre des expériences, vous pouvez spécifier un nom de package à l’aide du paramètre `pip_packages` ou `conda_packages` dans l’estimateur en fonction de la source à partir de laquelle vous souhaitez installer le package. Vous pouvez également spécifier un fichier yml avec toutes vos dépendances à l’aide de `conda_dependencies_file` ou répertorier toutes vos exigences PIP dans un fichier txt à l’aide du paramètre `pip_requirements_file`. Si vous souhaitez remplacer l’image par défaut utilisée par l’estimateur dans votre propre objet d’environnement Azure ML, vous pouvez spécifier cet environnement par le bais du paramètre `environment` du constructeur estimateur.
    
    Azure ML a géré les images Docker dont le contenu est visible dans [AzureML Containers](https://github.com/Azure/AzureML-Containers).
    Les dépendances spécifiques au framework sont listées dans la documentation correspondante du framework :
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer#remarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch#remarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow#remarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn#remarks)
    
    > [!Note]
    > Si vous pensez qu’un package particulier est suffisamment courant pour être ajouté dans des environnements et images gérés par Azure ML, signalez un problème GitHub dans [AzureML Containers](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (nom non défini), AttributeError (objet sans attribut)**  : Cette exception doit provenir de vos scripts d’apprentissage. Vous pouvez consulter les fichiers journaux du portail Azure pour obtenir des informations supplémentaire sur l’erreur de nom non défini ou d’attribut. À partir du Kit de développement logiciel (SDK), vous pouvez utiliser `run.get_details()` pour examiner le message d’erreur. Cette opération répertorie également tous les fichiers journaux générés pour votre exécution. Veillez à examiner votre script d’apprentissage et à corriger l’erreur avant de soumettre à nouveau votre exécution. 


* **Exécuter ou expérimenter la suppression** :  Les expériences peuvent être archivées à l’aide de la méthode [Experiment.archive](/python/api/azureml-core/azureml.core.experiment%28class%29#archive--) ou à partir de l’onglet Expérience dans le client Azure Machine Learning Studio via le bouton « Archiver l’expérience ». Cette action masque l’expérience des requêtes et des vues de liste, mais elle ne la supprime pas.

    La suppression définitive d’expériences ou d’exécutions individuelles n’est actuellement pas prise en charge. Pour plus d’informations sur la suppression de ressources d’espace de travail, consultez [Exporter ou supprimer vos données d’espace de travail du service Machine Learning](how-to-export-delete-data.md).

* **Le document de métriques est trop volumineux** : Azure Machine Learning comporte des limites internes sur la taille des objets métriques qui peuvent être consignés simultanément dans une exécution d’entraînement. Si vous rencontrez une erreur « Le document de métrique est trop volumineux » lors de l’enregistrement d’une mesure de liste, essayez de fractionner la liste en segments plus petits, par exemple :

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    En interne, Azure ML concatène les blocs portant le même nom de métrique dans une liste contiguë.

* **Le démarrage de la cible de calcul prend beaucoup de temps** : les images Docker pour les cibles de calcul sont chargées à partir d’Azure Container Registry (ACR). Par défaut, Azure Machine Learning crée un registre ACR du niveau de service *De base*. Un passage au niveau Standard ou Premium du registre ACR de l’espace de travail est susceptible de réduire le temps nécessaire à la génération et au chargement des images. Pour plus d’informations, consultez [Niveaux de service pour Azure Container Registry](../container-registry/container-registry-skus.md).

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Former un modèle](tutorial-train-models-with-aml.md) utilise une cible de calcul gérée pour former un modèle.
* Découvrez comment entraîner des modèles avec des frameworks ML spécifiques, comme [Scikit-learn](how-to-train-scikit-learn.md), [TensorFlow](how-to-train-tensorflow.md) et [PyTorch](how-to-train-pytorch.md).
* Découvrez comment [optimiser efficacement les hyperparamètres](how-to-tune-hyperparameters.md) afin de générer des modèles plus efficaces.
* Une fois le modèle formé, découvrez [comment et où déployer les modèles](how-to-deploy-and-where.md).
* Consultez la documentation de référence du SDK de la [classe ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).
* [Utiliser Azure Machine Learning avec des réseaux virtuels Azure](./how-to-network-security-overview.md)
