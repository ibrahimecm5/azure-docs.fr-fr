---
title: Schéma YAML du travail de balayage CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML du travail de balayage CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 90a69fceb96067ad92cc4d68f8b3e0929d2622a6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135108"
---
# <a name="cli-v2-sweep-job-yaml-schema"></a>Schéma YAML du travail de balayage CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, en incluant `$schema` en haut de votre fichier, vous pouvez appeler des complétions de schémas et de ressources. | | |
| `type` | const | **Obligatoire.** Le type de travail. | `sweep` | `sweep` |
| `name` | string | Nom du travail. Doit être unique pour tous les travaux de l’espace de travail. En cas d’omission, Azure ML génère automatiquement un GUID pour le nom. | | |
| `display_name` | string | Nom complet du travail dans l’interface utilisateur de Studio. Peut être non unique dans l’espace de travail. En cas d’omission, Azure ML génère automatiquement un identificateur de nom convivial explicite pour le nom d’affichage. | | |
| `experiment_name` | string | Nom de l’expérience sous lequel organiser le travail. Chaque enregistrement d’exécution du travail est organisé sous l’expérience correspondante dans l’onglet « Expérience » du studio. En cas d’omission, Azure ML prend par défaut le nom du répertoire de travail dans lequel le travail a été créé. | | |
| `description` | string | Description du travail. | | |
| `tags` | object | Dictionnaire d’étiquettes pour le travail. | | |
| `sampling_algorithm` | string | **Obligatoire.** Algorithme d’échantillonnage d’hyperparamètres à utiliser sur le `search_space`. | `random`, `grid`, `bayesian` | |
| `search_space` | object | **Obligatoire.** Définir l’espace de recherche d’hyperparamètres. La clé est le nom de l’hyperparamètre, et la valeur est l’expression de paramètre. <br><br> Les hyperparamètres peuvent être référencés dans `trial.command` à l’aide de l’expression `${{ search_space.<hyperparameter> }}`. | | |
| `search_space.<hyperparameter>` | object | Pour voir le jeu d’expressions possibles à utiliser, consultez [Expressions de paramètre](#parameter-expressions). | | |
| `objective.primary_metric` | string | **Obligatoire.** Nom de la métrique principale signalée par chaque travail d’évaluation. La métrique doit être enregistrée dans le script de formation de l’utilisateur en utilisant `mlflow.log_metric()` avec le nom de métrique correspondant. | | |
| `objective.goal` | string | **Obligatoire.** Objectif d’optimisation de `objective.primary_metric`. | `maximize`, `minimize` | |
| `early_termination` | object | Stratégie d’arrêt anticipé à utiliser. Un travail d’évaluation est annulé quand les critères de la stratégie spécifiée sont satisfaits. En cas d’omission, aucune stratégie d’arrêt anticipé n’est appliquée. [BanditPolicy](#banditpolicy), [MedianStoppingPolicy](#medianstoppingpolicy) ou [TruncationSelectionPolicy](#truncationselectionpolicy). | | |
| `limits` | object | Limites du travail de balayage. Consultez [Attributs de la clé `limits`](#attributes-of-the-limits-key). | | |
| `compute` | string | **Obligatoire.** Nom de la cible de calcul sur laquelle exécuter le travail, selon la syntaxe `azureml:<compute_name>`. | | |
| `trial` | object | **Obligatoire.** Modèle de travail pour chaque évaluation. Chaque travail d’évaluation est fourni avec une combinaison différente de valeurs d’hyperparamètres que le système échantillonne à partir de `search_space`. Consultez [Attributs de la clé `trial`](#attributes-of-the-trial-key). | | |
| `inputs` | object | Dictionnaire d’entrées du travail. La clé est un nom pour l’entrée dans le contexte du travail et la valeur est la valeur d’entrée. <br><br> Les entrées peuvent être référencées dans la `trial.command` à l’aide de l’expression `${{ inputs.<input_name> }}`. | | |
| `inputs.<input_name>` | nombre, entier, booléen, chaîne ou objet | Une valeur littérale (de type nombre, entier, booléen ou chaîne), [JobInputUri](#jobinputuri) ou [JobInputDataset](#jobinputdataset). | | |
| `outputs` | object | Dictionnaire des configurations de sortie du travail. La clé est un nom pour l’entrée dans le contexte du travail et la valeur est la configuration de sortie. <br><br> Les sorties peuvent être référencées dans la `trial.command` à l’aide de l’expression `${{ outputs.<output_name> }}`. | |
| `outputs.<output_name>` | object | Vous pouvez éventuellement spécifier un `mode` ou laisser l’objet vide. Pour chaque sortie nommée spécifiée dans le dictionnaire `outputs`, Azure ML génère automatiquement un emplacement de sortie. | |
| `outputs.<output_name>.mode` | string | Mode de remise du ou des fichiers de sortie dans le stockage de destination. Pour le mode de montage en lecture-écriture, le répertoire de sortie est un répertoire monté. Pour le mode de téléchargement, les fichiers écrits dans le répertoire de sortie sont chargés à la fin du travail. | `rw_mount`, `upload` | `rw_mount` |

### <a name="early-termination-policies"></a>Stratégies d’arrêt anticipé

#### <a name="banditpolicy"></a>BanditPolicy

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatoire.** Le type de stratégie. | `bandit` | |
| `slack_factor` | nombre | Ratio utilisé pour calculer la distance autorisée par rapport à l’évaluation la plus performante. **`slack_factor` ou `slack_amount` est obligatoire**. | | |
| `slack_amount` | nombre | Distance absolue autorisée à partir de l’évaluation la plus performante. **`slack_factor` ou `slack_amount` est obligatoire**. | | |
| `evaluation_interval` | entier | Fréquence d’application de la stratégie. | | `1` |
| `delay_evaluation` | entier | Nombre d’intervalles pour lesquels retarder la première évaluation de la stratégie. S’il est spécifié, la stratégie s’applique à chaque multiple de `evaluation_interval` supérieur ou égal à `delay_evaluation`. | | `0` |

#### <a name="medianstoppingpolicy"></a>MedianStoppingPolicy

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatoire.** Le type de stratégie. | `median_stopping` | |
| `evaluation_interval` | entier | Fréquence d’application de la stratégie. | | `1` |
| `delay_evaluation` | entier | Nombre d’intervalles pour lesquels retarder la première évaluation de la stratégie. S’il est spécifié, la stratégie s’applique à chaque multiple de `evaluation_interval` supérieur ou égal à `delay_evaluation`. | | `0` |

#### <a name="truncationselectionpolicy"></a>TruncationSelectionPolicy

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatoire.** Le type de stratégie. | `truncation_selection` | |
| `truncation_percentage` | entier | **Obligatoire.** Pourcentage de travaux d’évaluation à annuler à chaque intervalle d’évaluation. | | |
| `evaluation_interval` | entier | Fréquence d’application de la stratégie. | | `1` |
| `delay_evaluation` | entier | Nombre d’intervalles pour lesquels retarder la première évaluation de la stratégie. S’il est spécifié, la stratégie s’applique à chaque multiple de `evaluation_interval` supérieur ou égal à `delay_evaluation`. | | `0` |

### <a name="parameter-expressions"></a>Expressions de paramètre

#### <a name="choice"></a>choice

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatoire.** Type d’expression. | `choice` |
| `values` | tableau | **Obligatoire.** Liste des valeurs discrètes parmi lesquelles choisir. | |

#### <a name="randint"></a>randint

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatoire.** Type d’expression. | `randint` |
| `upper` | entier | **Obligatoire.** Limite supérieure exclusive pour la plage d’entiers. | |

#### <a name="qlognormal-qnormal"></a>qlognormal, qnormal

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatoire.** Type d’expression. | `qlognormal`, `qnormal` |
| `mu` | nombre | **Obligatoire.** Moyenne de la distribution normale. | |
| `sigma` | nombre | **Obligatoire.** Écart type de la distribution normale. | |
| `q` | entier | **Obligatoire.** Facteur de lissage. | |

#### <a name="qloguniform-quniform"></a>qloguniform, quniform

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatoire.** Type d’expression. | `qloguniform`, `quniform` |
| `min_value` | nombre | **Obligatoire.** Valeur minimale de la plage (inclusive). | |
| `max_value` | nombre | **Obligatoire.** Valeur maximale de la plage (inclusive). | |
| `q` | entier | **Obligatoire.** Facteur de lissage. | |

#### <a name="lognormal-normal"></a>lognormal, normal

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatoire.** Type d’expression. | `lognormal`, `normal` |
| `mu` | nombre | **Obligatoire.** Moyenne de la distribution normale. | |
| `sigma` | nombre | **Obligatoire.** Écart type de la distribution normale. | |

#### <a name="loguniform"></a>loguniform

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatoire.** Type d’expression. | `loguniform` |
| `min_value` | nombre | **Obligatoire.** La valeur minimale de la plage sera `exp(min_value)` (inclusive). | |
| `max_value` | nombre | **Obligatoire.** La valeur maximale de la plage sera `exp(max_value)` (inclusive). | |

#### <a name="uniform"></a>uniforme

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatoire.** Type d’expression. | `uniform` |
| `min_value` | nombre | **Obligatoire.** Valeur minimale de la plage (inclusive). | |
| `max_value` | nombre | **Obligatoire.** Valeur maximale de la plage (inclusive). | |

### <a name="attributes-of-the-limits-key"></a>Attributs de la clé `limits`

| Clé | Type | Description | Valeur par défaut |
| --- | ---- | ----------- | ------------- |
| `max_total_trials` | entier | Durée maximale, en secondes, pendant laquelle le travail est autorisé à s’exécuter. Une fois cette limite atteinte, le système annule le travail. | `1000` |
| `max_concurrent_trials` | entier | | La valeur par défaut est `max_total_trials`. |
| `timeout` | entier | Durée maximale, en minutes, pendant laquelle l’exécution du travail de balayage est autorisée. Une fois cette limite atteinte, le système annule le travail de balayage, y compris toutes ses évaluations. | `10080` |
| `trial_timeout` | entier | Durée maximale, en minutes, pendant laquelle l’exécution de chaque travail d’évaluation est autorisée. Une fois cette limite atteinte, le système annule l’évaluation. | |

### <a name="attributes-of-the-trial-key"></a>Attributs de la clé `trial`

| Clé | Type | Description | Valeur par défaut |
| --- | ---- | ----------- | ------------- |
| `command` | string | **Obligatoire.** Commande à exécuter. | |
| `code.local_path` | string | Chemin d’accès local au répertoire du code source à télécharger et à utiliser pour le travail. | |
| `environment` | chaîne ou objet | **Obligatoire.** L’environnement à utiliser pour le travail. Il peut s’agir d’une référence à un environnement versionné existant dans l’espace de travail ou d’une spécification d’environnement inline. <br> <br> Pour référencer un environnement existant, utilisez la syntaxe `azureml:<environment-name>:<environment-version>`. <br><br> Pour définir un environnement inline, veuillez suivre le [schéma d’environnement](reference-yaml-environment.md#yaml-syntax). Excluez les propriétés `name` et `version`, car elles ne sont pas prises en charge pour les environnements inline. | |
| `environment_variables` | object | Dictionnaire de paires nom-valeur de variable d’environnement à définir sur le processus dans lequel la commande est exécutée. | |
| `distribution` | object | Configuration de la distribution pour les scénarios de formation distribués. [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration) ou [TensorFlowConfiguration](#tensorflowconfiguration). | |
| `resources.instance_count` | entier | Nombre de nœuds à dédier au travail. | `1` |

#### <a name="distribution-configurations"></a>Configurations de distribution

##### <a name="mpiconfiguration"></a>MpiConfiguration

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatoire.** Type de distribution.  | `mpi` |
| `process_count_per_instance` | entier | **Obligatoire.** Nombre de processus par nœud à lancer pour le travail.  | |

##### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatoire.** Type de distribution.  | `pytorch` | |
| `process_count_per_instance` | entier | Nombre de processus par nœud à lancer pour le travail. | |  `1` |

##### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatoire.** Type de distribution.  | `tensorflow` |
| `worker_count` | entier | Nombre de Workers à lancer pour le travail. | | La valeur par défaut est `resources.instance_count`. |
| `parameter_server_count` | entier | Nombre de serveurs de paramètres à lancer pour le travail. | | `0` |

### <a name="job-inputs"></a>Entrées du travail

#### <a name="jobinputuri"></a>JobInputUri

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | string | URI vers un fichier unique à utiliser comme entrée. Les types d’URI pris en charge sont `azureml`, `https`, `wasbs`, `abfss` et `adl`. Pour plus d’informations sur l’utilisation du format d’URI `azureml://`, consultez [Syntaxe YAML fondamentale](reference-yaml-core-syntax.md). **`file` ou `folder` est obligatoire**.  | | |
| `folder` | string | URI vers un dossier à utiliser comme entrée. Les types d’URI pris en charge sont `azureml`, `wasbs`, `abfss` et `adl`. Pour plus d’informations sur l’utilisation du format d’URI `azureml://`, consultez [Syntaxe YAML fondamentale](reference-yaml-core-syntax.md). **`file` ou `folder` est obligatoire**.   | | |
| `mode` | string | Mode de remise des données à la cible de calcul. Pour le montage en lecture seule et le montage en lecture-écriture, les données seront consommées en tant que chemin de montage. Un dossier est monté en tant que dossier et un fichier est monté en tant que fichier. Pour le mode de téléchargement, les données seront consommées en tant que chemin d’accès téléchargé. | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | chaîne ou objet | **Obligatoire.** Un jeu de données à utiliser comme entrée. Il peut s’agir d’une référence à un jeu de données avec version existant dans l’espace de travail ou d’une spécification de jeu de données inline. <br><br> Pour référencer un jeu de données existant, utilisez la syntaxe `azureml:<dataset_name>:<dataset_version>`. <br><br> Pour définir un jeu de données inline, veuillez suivre le [schéma du jeu de données](reference-yaml-dataset.md#yaml-syntax). Excluez les propriétés `name` et `version`, car elles ne sont pas prises en charge pour les jeux de données inline. | | |
| `mode` | string | Mode de remise du jeu de données à la cible de calcul. Pour un montage en lecture seule, le jeu de données est consommé en tant que chemin de montage. Un dossier est monté en tant que dossier et un fichier est monté en tant que dossier parent. Pour le mode de téléchargement, le jeu de données sera consommé en tant que chemin d’accès téléchargé. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>Remarques

La commande `az ml job` peut être utilisée pour gérer les tâches Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/jobs). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-hello-sweep"></a>YAML : hello sweep

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-sweep.yml":::

## <a name="yaml-basic-python-model-hyperparameter-tuning"></a>YAML : réglage d’hyperparamètre du modèle Python de base

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job-sweep.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
