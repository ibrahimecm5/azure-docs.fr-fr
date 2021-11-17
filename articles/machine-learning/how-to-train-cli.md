---
title: Entraîner des modèles avec l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Découvrez comment entraîner des modèles (créer des travaux) à l’aide de l’extension Azure CLI pour Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: amibp
ms.author: amipatel
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: b849293d94b435fa74378d80d8989e6075f78d86
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493663"
---
# <a name="train-models-with-the-cli-v2-preview"></a>Entraîner des modèles avec l’interface CLI (v2) (préversion)

L’interface CLI Azure Machine Learning (v2) est une extension Azure CLI qui vous permet d’accélérer le processus d’entraînement de modèles tout en effectuant le scale-up et le scale-out au niveau de la capacité de calcul Azure. Par ailleurs, le cycle de vie des modèles fait l’objet d’un suivi et peut être audité.

L’entraînement d’un modèle de machine learning est normalement un processus itératif. Grâce aux outils modernes, l’entraînement des modèles volumineux avec plus de données est plus facile et plus rapide que jamais. Les processus auparavant manuels et fastidieux comme le réglage hyperparamétrique et même la sélection d’algorithmes sont souvent automatisés. Avec l’interface CLI (v2) d’Azure Machine Learning, vous pouvez suivre vos travaux (et modèles) dans un [espace de travail](concept-workspace.md) avec des balayages hyperparamétriques, effectuer un scale-up des capacités de calcul Azure hautes performances et effectuer un scale-out utilisant un entraînement distribué.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

- Pour utiliser l’interface CLI (v2), vous devez disposer d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui.
- [Installez et configurez l’interface CLI (v2)](how-to-configure-cli.md).

> [!TIP]
> Pour bénéficier d’un environnement de développement complet avec validation de schéma et autocomplétion des fichiers YAML de travaux, utilisez Visual Studio Code et l’[extension Azure Machine Learning](how-to-setup-vs-code.md).

### <a name="clone-examples-repository"></a>Cloner un dépôt d’exemples

Pour exécuter les exemples d’entraînement, commencez par cloner le dépôt d’exemples et placez-le dans le répertoire `cli` :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

`--depth 1` clone uniquement le dernier commit dans le dépôt, ce qui réduit le temps nécessaire à l’exécution de l’opération.

### <a name="create-compute"></a>Créer une capacité de calcul

Vous pouvez créer un cluster de calcul Azure Machine Learning à partir de la ligne de commande. Par exemple, les commandes suivantes créent un cluster nommé `cpu-cluster` et un autre nommé `gpu-cluster`.

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-compute.sh" id="create_computes":::

Le calcul ne vous est pas facturé à ce stade, car `cpu-cluster` et `gpu-cluster` restent à 0 nœud tant qu’aucun travail n’est soumis. Apprenez-en davantage sur [la gestion et l’optimisation des coûts pour AmlCompute](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute).

Les exemples de travaux suivants fournis dans cet article utilisent `cpu-cluster` ou `gpu-cluster`. Si nécessaire, ajustez ces noms dans les exemples de travaux tout au long de cet article en fonction du nom de votre ou vos clusters. Utilisez `az ml compute create -h` pour plus d’informations sur les options de création de capacités de calcul.

## <a name="hello-world"></a>Hello World

Pour l’interface CLI d’Azure Machine Learning (v2), les travaux sont créés au format YAML. Un travail englobe les aspects suivants :

- Quoi exécuter
- Comment l’exécuter
- Où l’exécuter

Le travail « hello world » comporte les trois :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world.yml":::

> [!WARNING]
> Python doit être installé dans l’environnement utilisé pour les travaux. Exécutez `apt-get update -y && apt-get install python3 -y` dans votre Dockerfile pour effectuer l’installation le cas échéant ou dérivez d’une image de base dans laquelle Python est déjà installé.

> [!TIP]
> Les exemples `$schema:` de bout en bout autorisent la validation de schéma et l’autocomplétion si les fichiers YAML sont créés dans [VS Code avec l’extension Azure Machine Learning](how-to-setup-vs-code.md).

Vous pouvez l’exécuter ainsi :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world":::

> [!TIP]
> Le paramètre `--web` tente d’ouvrir votre travail dans Azure Machine Learning studio en utilisant votre navigateur web par défaut. Le paramètre `--stream` peut être utilisé pour diffuser en streaming des journaux sur la console et bloquer d’autres commandes.

## <a name="overriding-values-on-create-or-update"></a>Remplacement de valeurs lors de la création ou de la mise à jour

Vous pouvez remplacer les valeurs de spécification de travaux YAML en utilisant `--set` lors de la création ou de la mise à jour d’un travail. Exemple :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_set":::

## <a name="job-names"></a>Noms de travail

La plupart des commandes `az ml job`, sauf `create` et `list`, demandent `--name/-n`, qui est le nom d’un travail ou l’ID d’exécution (« Run ID ») dans le studio. Ne définissez pas directement la propriété `name` d’un travail lors de la création, car elle doit être unique à chaque espace de travail. Azure Machine Learning génère un GUID aléatoire pour le nom du travail s’il n’est pas défini. Pour l’obtenir, examinez la sortie de la création du travail dans l’interface CLI ou copiez la propriété « Run ID » dans le studio et les API MLflow.

Pour automatiser des travaux dans des scripts et des flux CI/CD, vous pouvez capturer le nom d’un travail lors de sa création. Pour cela, interrogez la sortie et ajoutez `--query name -o tsv`. Les caractéristiques varient en fonction de l’interpréteur de commandes, mais pour Bash :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_name":::

Vous pouvez ensuite utiliser `$run_id` dans les commandes suivantes comme `update`, `show` ou `stream` :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_show":::

## <a name="organize-jobs"></a>Organiser les travaux

Pour organiser les travaux, vous pouvez définir un nom complet, un nom d’expérience, une description et des étiquettes. Les descriptions prennent en charge la syntaxe Markdown dans le studio. Ces propriétés sont mutables après la création d’un travail. Exemple complet :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-org.yml":::

Si vous exécutez ce travail, ces propriétés sont immédiatement visibles dans le studio :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_org":::

Utilisez `--set` pour mettre à jour les valeurs mutables après la création du travail :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_org_set":::

## <a name="environment-variables"></a>Variables d'environnement

Vous pouvez définir des variables d’environnement à utiliser dans votre travail :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-env-var.yml":::

Vous pouvez exécuter ce travail :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_env_var":::

> [!WARNING]
> Utilisez `inputs` pour paramétrer des arguments dans `command`. Consultez [Entrées et sorties](#inputs-and-outputs).

## <a name="track-models-and-source-code"></a>Suivre les modèles et le code source

Les modèles de Machine Learning de production doivent être auditables (s’ils ne sont pas reproductibles). Il est essentiel de suivre le code source pour un modèle donné. Azure Machine Learning prend un instantané de votre code source et le conserve avec le travail. Par ailleurs, le dépôt source et la validation sont conservés si vous exécutez des travaux à partir d’un dépôt Git.

> [!TIP]
> Si vous suivez et exécutez l’un des exemples du dépôt d’exemples, vous pouvez voir le dépôt source et la validation dans le studio pour tous les travaux exécutés jusqu’à présent.

Vous pouvez spécifier la clé `code.local_path` dans un travail avec comme valeur le chemin à un répertoire de code source. Un instantané du répertoire est pris et chargé avec le travail. Le contenu du répertoire est directement accessible à partir du répertoire de travail du travail.

> [!WARNING]
> Le code source ne doit pas inclure d’entrées de données volumineuses pour l’entraînement du modèle. Utilisez plutôt des [entrées de données](#data-inputs). Vous pouvez utiliser un fichier `.gitignore` dans le répertoire de code source pour exclure des fichiers de l’instantané. La taille de l’instantané est limitée à 300 Mo ou 2 000 fichiers.

Examinons un travail qui spécifie du code :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-mlflow.yml":::

Le script Python se trouve dans le répertoire de code source local. La commande appelle ensuite `python` pour exécuter le script. Le même modèle peut être appliqué à d’autres langages de programmation.

> [!WARNING]
> Dans cet article, la famille de travaux « hello » est fournie à des fins de démonstration et ne respecte pas nécessairement les bonnes pratiques recommandées. L’utilisation de `&&` ou de code similaire pour exécuter plusieurs commandes dans une séquence n’est pas recommandée. Envisagez plutôt d’écrire les commandes dans un fichier de script dans le répertoire de code source et d’appeler le script dans `command`. De même, l’installation de dépendances dans `command`, comme indiqué ci-dessus avec `pip install`, n’est pas recommandée. Spécifiez plutôt toutes les dépendances du travail dans votre environnement. Pour plus d’informations, consultez [Gérer les environnements avec l’interface CLI (v2)](how-to-manage-environments-v2.md).

### <a name="model-tracking-with-mlflow"></a>Suivi de modèles avec MLflow

Quand ils effectuent des itérations sur des modèles, les scientifiques des données doivent être en mesure de suivre les métriques d’entraînement et les paramètres de ces modèles. Azure Machine Learning s’intègre au suivi MLflow pour permettre la journalisation des modèles, artefacts, métriques et paramètres dans un travail. Pour utiliser MLflow dans vos scripts Python, ajoutez `import mlflow` et appelez l’API `mlflow.log_*` ou `mlflow.autolog()` dans votre code d’entraînement.

> [!WARNING]
> Les packages `mlflow` et `azureml-mlflow` doivent être installés dans votre environnement Python pour que vous puissiez bénéficier des fonctionnalités de suivi MLflow.

> [!TIP]
> Pris en charge par plusieurs frameworks populaires, l’appel `mlflow.autolog()` effectue la plupart des tâches de journalisation pour vous.

Jetons un œil au script Python appelé dans le travail ci-dessus. Ce script utilise `mlflow` pour journaliser un paramètre, une métrique et un artefact :

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-mlflow.py":::

Vous pouvez exécuter ce travail dans le cloud par le biais d’Azure Machine Learning, où il est suivi et auditable :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_mlflow":::

### <a name="query-metrics-with-mlflow"></a>Interroger les métriques avec MLflow

Après l’exécution de travaux, vous pouvez interroger les résultats de ces exécutions et leurs métriques journalisées. Python est mieux adapté à cette tâche qu’une interface CLI. Vous pouvez interroger les exécutions et leurs métriques avec `mlflow`, puis les charger dans des objets familiers comme des dataframes Pandas pour analyse.

Premièrement, récupérez l’URI de suivi MLflow pour votre espace de travail Azure Machine Learning :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="mlflow_uri":::

Utilisez la sortie de cette commande dans `mlflow.set_tracking_uri(<YOUR_TRACKING_URI>)` à partir d’un environnement Python avec MLflow importé. Les appels MLflow correspondent désormais aux travaux dans votre espace de travail Azure Machine Learning.

## <a name="inputs-and-outputs"></a>Entrées et sorties

Les travaux ont généralement des entrées et des sorties. Les entrées peuvent être des paramètres de modèle, qui peuvent être balayés pour l’optimisation hyperparamétrique, ou des entrées de données cloud montées ou téléchargées sur la cible de calcul. Les sorties (métriques exclues) sont des artefacts pouvant être écrits ou copiés dans les sorties par défaut ou une sortie de données nommée.

### <a name="literal-inputs"></a>Entrées littérales

Les entrées littérales sont résolues directement dans la commande. Vous pouvez modifier notre travail « hello world » pour utiliser des entrées littérales :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-input.yml":::

Vous pouvez exécuter ce travail :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_input":::

Vous pouvez utiliser `--set` pour remplacer les entrées :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_input_set":::

Les entrées littérale des travaux peuvent être [converties en entrées d’espace de recherche](#search-space-inputs) pour les balayages hyperparamétriques lors de l’entraînement de modèles.

### <a name="search-space-inputs"></a>Entrées d’espace de recherche

Pour un travail de balayage, vous pouvez spécifier un espace de recherche à partir duquel choisir les entrées littérales. Pour obtenir la gamme complète des options disponibles pour les entrées d’espace de recherche, consultez la [référence sur la syntaxe YAML des travaux de balayage](reference-yaml-job-sweep.md).

> [!WARNING]
> Les travaux de balayage ne sont pas actuellement pris en charge dans les travaux de pipeline.

Démontrons ce concept avec un script Python simple qui accepte des arguments et journalise une métrique aléatoire :

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-sweep.py":::

Créez à présent un travail de balayage correspondant :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-sweep.yml":::

Exécutez-le :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_sweep":::

### <a name="data-inputs"></a>Entrées de données

Les entrées de données sont résolues en chemin sur le système de fichiers local du calcul du travail. À des fins de démonstration, utilisons le jeu de données Iris classique qui est hébergé publiquement dans un conteneur d’objets blob à l’adresse `https://azuremlexamples.blob.core.windows.net/datasets/iris.csv`.

Vous pouvez créer un script Python qui prend comme argument le chemin au fichier CSV Iris, le lit dans un dataframe, imprime les 5 premières lignes et l’enregistre dans le répertoire `outputs`.

:::code language="python" source="~/azureml-examples-main/cli/jobs/basics/src/hello-iris.py":::

Des entrées d’URI de stockage Azure peuvent être spécifiées pour monter ou télécharger les données sur le système de fichiers local. Vous pouvez spécifier un seul fichier :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-file.yml":::

Exécutez ensuite la commande suivante :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="iris_file":::

Ou un dossier entier :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-folder.yml":::

Exécutez ensuite la commande suivante :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="iris_folder":::

#### <a name="private-data"></a>Données privées

Pour des données privées dans Stockage Blob Azure ou Azure Data Lake Storage connectées à Azure Machine Learning par le biais d’un magasin de données, vous pouvez utiliser des URI Azure Machine Learning au format `azureml://datastores/<DATASTORE_NAME>/paths/<PATH_TO_DATA>` pour les données d’entrée. Par exemple, si vous chargez le fichier CSV Iris dans un répertoire nommé `/example-data/` dans le conteneur d’objets blob correspondant au magasin de données nommé `workspaceblobstore`, vous pouvez modifier un travail précédent pour qu’il utilise le fichier dans le magasin de données :

> [!WARNING]
> L’exécution de ces travaux échoue si le fichier CSV Iris n’est pas copié au même emplacement dans `workspaceblobstore`.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-file.yml":::

Ou le répertoire entier :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-folder.yml":::

### <a name="default-outputs"></a>Sorties par défaut

Les répertoires `./outputs` et `./logs` font l’objet d’un traitement spécial de la part d’Azure Machine Learning. Si vous écrivez des fichiers dans ces répertoires durant votre travail, ces fichiers sont chargés dans le travail. Vous pouvez donc y accéder même une fois le travail terminé. Le dossier `./outputs` est chargé à la fin du travail, tandis que les fichiers écrits dans `./logs` sont chargés en temps réel. Vous pouvez utiliser ce dernier si vous souhaitez diffuser des journaux en continu pendant le travail, tels que des journaux TensorBoard.

Vous pouvez modifier le travail « hello world » pour qu’il génère la sortie dans un fichier du répertoire de sorties par défaut, au lieu de l’imprimer dans `stdout` :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output.yml":::

Vous pouvez exécuter ce travail :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_output":::

Téléchargez à présent les journaux, où `helloworld.txt` sera présent dans le répertoire `<RUN_ID>/outputs/` :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world_output_download":::

### <a name="data-outputs"></a>Sorties de données

Vous pouvez spécifier des sorties de données nommées. Un répertoire est alors créé dans le magasin de données par défaut, qui est monté par défaut en lecture/écriture.

Vous pouvez modifier le travail « hello world » précédent pour qu’il écrive dans une sortie de données nommée :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output-data.yml":::

## <a name="hello-pipelines"></a>Pipelines hello

Les travaux de pipeline peuvent exécuter plusieurs travaux en parallèle ou de manière séquentielle. S’il existe des dépendances d’entrée/sortie entre les étapes d’un pipeline, l’étape dépendante s’exécute une fois l’autre étape terminée.

Vous pouvez diviser un travail « hello world » en deux :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline.yml":::

Exécutez-le :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline":::

Les travaux « hello » et « world » sont exécutés en parallèle si la cible de calcul dispose des ressources disponibles pour le faire.

Pour passer des données entre les étapes d’un pipeline, définissez une sortie de données dans le travail « hello » et une entrée correspondante dans le travail « world », qui fait référence à la sortie du travail précédent :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-io.yml":::

Exécutez-le :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_io":::

Cette fois-ci, le travail « world » s’exécute une fois le travail « hello » terminé.

Pour éviter la duplication de paramètres communs entre travaux dans un pipeline, vous pouvez les définir en dehors des travaux :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-settings.yml":::

Vous pouvez exécuter ceci :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_settings":::

Le paramètre correspondant sur un travail individuel remplace les paramètres communs pour un travail de pipeline. Les concepts présentés jusqu’à présent peuvent être combinés en un travail de pipeline en trois étapes avec les travaux « A », « B » et « C ». Le travail « C » dépend des données du travail « B », tandis que le travail « A » peut s’exécuter de façon indépendante. Le travail « A » utilise également un environnement défini individuellement et lie l’une de ses entrées à une entrée de travail de pipeline de niveau supérieur :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-abc.yml":::

Vous pouvez exécuter ceci :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_pipeline_abc":::

## <a name="train-a-model"></a>Effectuer l’apprentissage d’un modèle

À ce stade, un modèle n’a toujours pas été entraîné. Ajoutons du code `sklearn` dans un script Python avec suivi MLflow pour entraîner un modèle sur le fichier CSV Iris :

:::code language="python" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/src/main.py":::

Le framework scikit-learn est pris en charge par MLflow pour la journalisation automatique. Un seul appel à `mlflow.autolog()` dans le script journalise donc tous les paramètres du modèle, métriques d’entraînement, artefacts du modèle et certains artefacts supplémentaires (dans ce cas, une image de matrice de confusion).

Pour l’exécuter dans le cloud, spécifiez-le en tant que travail :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job.yml":::

Exécutez-le :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_iris":::

Pour inscrire un modèle, vous pouvez télécharger les sorties et créer un modèle à partir du répertoire local :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_download_register_model":::

## <a name="sweep-hyperparameters"></a>Balayer les hyperparamètres

Vous pouvez modifier le travail précédent pour balayer les hyperparamètres :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job-sweep.yml":::

Exécutez-le :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="sklearn_sweep":::

> [!TIP]
> Examinez l’onglet « Exécutions enfants » dans le studio pour monitorer la progression et consulter des graphiques de paramètres.

Pour d’autres options de balayage, consultez la [référence sur la syntaxe YAML des travaux de balayage](reference-yaml-job-sweep.md).

## <a name="distributed-training"></a>Entraînement distribué

Azure Machine Learning prend en charge l’entraînement distribué basé sur PyTorch, TensorFlow et MPI. Pour plus d’informations, consultez la [section des configurations distribuées de la référence sur la syntaxe YAML des travaux de commande](reference-yaml-job-command.md#distribution-configurations) .

Par exemple, vous pouvez entraîner un réseau neuronal convolutif (CNN) sur le jeu de données CIFAR-10 avec un travail PyTorch distribué. Le script complet est [disponible dans le dépôt d’exemples](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/single-step/pytorch/cifar-distributed).

Le jeu de données CIFAR-10 dans `torchvision` attend en entrée un répertoire contenant le répertoire `cifar-10-batches-py`. Vous pouvez télécharger la source compressée et l’extraire dans un répertoire local :

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="download_untar_cifar":::

Créez ensuite un jeu de données Azure Machine Learning à partir du répertoire local. Ce jeu de données est chargé dans le magasin de données par défaut :

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="create_cifar":::

Si vous le souhaitez, supprimez le fichier et le répertoire locaux :

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/create-datasets.sh" id="cleanup_cifar":::

Les jeux de données (fichier uniquement) peuvent être référencés dans un travail avec la clé `dataset` d’une entrée de données. Le format est `azureml:<DATASET_NAME>:<DATASET_VERSION>`. Le jeu de données CIFAR-10 qui vient d’être créé s’écrit donc `azureml:cifar-10-example:1`.

Une fois le jeu de données en place, vous pouvez créer un travail PyTorch distribué pour entraîner notre modèle :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

Exécutez-le :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pytorch_cifar":::

## <a name="build-a-training-pipeline"></a>Créer un pipeline d’entraînement

L’exemple CIFAR-10 ci-dessus convient bien à un travail de pipeline. Le travail précédent peut être divisée en trois travaux pour orchestration dans un pipeline :

- « get-data » pour exécuter un script Bash afin de télécharger et d’extraire `cifar-10-batches-py`
- « train-model » pour prendre les données et entraîner un modèle avec un travail PyTorch distribué
- « eval-model » pour prendre les données et le modèle entraîné et évaluer l’exactitude

Notez que « train-model » et « eval-model » dépendent de la sortie du travail « get-data ». De plus, « eval-model » dépend de la sortie du travail « train-model ». Les trois travaux s’exécutent donc de manière séquentielle.

Vous pouvez orchestrer ces trois travaux dans un travail de pipeline :

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines/cifar-10/job.yml":::

Exécutez ensuite la commande suivante :

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pipeline_cifar":::

Les pipelines peuvent également être écrits à l’aide de composants réutilisables. Pour plus d’informations, consultez [Créer et exécuter des pipelines Machine Learning à l’aide de composants avec l’interface CLI d’Azure Machine Learning (préversion)](how-to-create-component-pipelines-cli.md).

## <a name="next-steps"></a>Étapes suivantes

- [Déployer et scorer un modèle de machine learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md)
