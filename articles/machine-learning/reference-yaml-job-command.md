---
title: Schéma YAML de la tâche de commande CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML de la tâche de commande CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ff49606e2f1a3877b6949d9ec0ae8a125c533484
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557924"
---
# <a name="cli-v2-command-job-yaml-schema"></a>Schéma YAML de la tâche de commande CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/commandJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Le schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, en incluant `$schema` en haut de votre fichier, vous pouvez appeler des complétions de schémas et de ressources. | | |
| `type` | const | Le type de travail. | `command` | `command` |
| `name` | string | Nom du travail. Doit être unique pour tous les travaux de l’espace de travail. En cas d’omission, Azure ML génère automatiquement un GUID pour le nom. | | |
| `display_name` | string | Nom complet du travail dans l’interface utilisateur de Studio. Peut être non unique dans l’espace de travail. En cas d’omission, Azure ML génère automatiquement un identificateur de nom convivial explicite pour le nom d’affichage. | | |
| `experiment_name` | string | Nom de l’expérience sous lequel organiser le travail. Chaque enregistrement d’exécution du travail est organisé sous l’expérience correspondante dans l’onglet « Expérience » du studio. En cas d’omission, Azure ML prend par défaut le nom du répertoire de travail dans lequel le travail a été créé. | | |
| `description` | string | Description du travail. | | |
| `tags` | object | Dictionnaire d’étiquettes pour le travail. | | |
| `command` | string | **Obligatoire.** Commande à exécuter. | | |
| `code.local_path` | string | Chemin d’accès local au répertoire du code source à télécharger et à utiliser pour le travail. | | |
| `environment` | chaîne ou objet | **Obligatoire.** L’environnement à utiliser pour le travail. Il peut s’agir d’une référence à un environnement versionné existant dans l’espace de travail ou d’une spécification d’environnement inline. <br><br> Pour référencer un environnement existant, utilisez la syntaxe `azureml:<environment_name>:<environment_version>`. <br><br> Pour définir un environnement inline, veuillez suivre le [schéma d’environnement](reference-yaml-environment.md#yaml-syntax). Excluez les propriétés `name` et `version`, car elles ne sont pas prises en charge pour les environnements inline. | | |
| `environment_variables` | object | Dictionnaire de paires nom-valeur de variable d’environnement à définir sur le processus dans lequel la commande est exécutée. | | |
| `distribution` | object | Configuration de la distribution pour les scénarios de formation distribués. [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration) ou [TensorFlowConfiguration](#tensorflowconfiguration). | | |
| `compute` | string | Nom de la cible de calcul sur laquelle exécuter le travail. Il peut s’agir d’une référence à un calcul existant dans l’espace de travail (à l’aide de la syntaxe `azureml:<compute_name>`) ou de `local` pour désigner l’exécution locale. | | `local` |
| `resources.instance_count` | entier | Nombre de nœuds à dédier au travail. | | `1` |
| `limits.timeout` | entier | Durée maximale, en secondes, pendant laquelle le travail est autorisé à s’exécuter. Une fois cette limite atteinte, le système annule le travail. | | |
| `inputs` | object | Dictionnaire d’entrées du travail. La clé est un nom pour l’entrée dans le contexte du travail et la valeur est la valeur d’entrée. <br><br> Les entrées peuvent être référencées dans la `command` à l’aide de l’expression `${{ inputs.<input_name> }}`. | | |
| `inputs.<input_name>` | nombre, entier, booléen, chaîne ou objet | Une valeur littérale (de type nombre, entier, booléen ou chaîne), [JobInputUri](#jobinputuri) ou [JobInputDataset](#jobinputdataset). | | |
| `outputs` | object | Dictionnaire des configurations de sortie du travail. La clé est un nom pour l’entrée dans le contexte du travail et la valeur est la configuration de sortie. <br><br> Les sorties peuvent être référencées dans la `command` à l’aide de l’expression `${{ outputs.<output_name> }}`. | |
| `outputs.<output_name>` | object | Vous pouvez éventuellement spécifier un `mode` ou laisser l’objet vide. Pour chaque sortie nommée spécifiée dans le dictionnaire `outputs`, Azure ML génère automatiquement un emplacement de sortie. | |
| `outputs.<output_name>.mode` | string | Mode de remise du ou des fichiers de sortie dans le stockage de destination. Pour le mode de montage en lecture-écriture, le répertoire de sortie est un répertoire monté. Pour le mode de téléchargement, les fichiers écrits dans le répertoire de sortie sont chargés à la fin du travail. | `rw_mount`, `upload` | `rw_mount` |

### <a name="distribution-configurations"></a>Configurations de distribution

#### <a name="mpiconfiguration"></a>MpiConfiguration

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Obligatoire.** Type de distribution.  | `mpi` |
| `process_count_per_instance` | entier | **Obligatoire.** Nombre de processus par nœud à lancer pour le travail.  | |

#### <a name="pytorchconfiguration"></a>PyTorchConfiguration

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatoire.** Type de distribution.  | `pytorch` | |
| `process_count_per_instance` | entier | Nombre de processus par nœud à lancer pour le travail. | |  `1` |

#### <a name="tensorflowconfiguration"></a>TensorFlowConfiguration

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Obligatoire.** Type de distribution.  | `tensorflow` |
| `worker_count` | entier | Nombre de Workers à lancer pour le travail. | | La valeur par défaut est `resources.instance_count`. |
| `parameter_server_count` | entier | Nombre de serveurs de paramètres à lancer pour le travail. | | `0` |

### <a name="job-inputs"></a>Entrées du travail

#### <a name="jobinputuri"></a>JobInputUri

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | string | URI vers un fichier unique à utiliser comme entrée. Les types d’URI pris en charge sont `azureml`, `https`, `wasbs`, `abfss` et `adl`. Pour plus d’informations sur l’utilisation du format d’URI `azureml://`, consultez [Syntaxe YAML fondamentale](reference-yaml-core-syntax.md). **`file` ou `folder` est obligatoire.**  | | |
| `folder` | string | URI vers un dossier à utiliser comme entrée. Les types d’URI pris en charge sont `azureml`, `wasbs`, `abfss` et `adl`. Pour plus d’informations sur l’utilisation du format d’URI `azureml://`, consultez [Syntaxe YAML fondamentale](reference-yaml-core-syntax.md). **`file` ou `folder` est obligatoire.**   | | |
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

## <a name="yaml-hello-world"></a>YAML : hello world

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world.yml":::

## <a name="yaml-display-name-experiment-name-description-and-tags"></a>YAML : nom d’affichage, nom de l’expérience, description et étiquettes

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world-org.yml":::

## <a name="yaml-environment-variables"></a>YAML : variables d’environnement

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world-env-var.yml":::

## <a name="yaml-source-code"></a>YAML : code source

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-code.yml":::

## <a name="yaml-literal-inputs"></a>YAML : entrées littérales

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world-input.yml":::

## <a name="yaml-write-to-default-outputs"></a>YAML : écrire dans les sorties par défaut

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world-output.yml":::

## <a name="yaml-write-to-named-data-output"></a>YAML : écrire dans la sortie de données nommée

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-world-output-data.yml":::

## <a name="yaml-datastore-uri-file-input"></a>YAML : entrée du fichier URI du magasin de données

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-iris-datastore-file.yml":::

## <a name="yaml-datastore-uri-folder-input"></a>YAML : entrée du dossier URI du magasin de données

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-iris-datastore-folder.yml":::

## <a name="yaml-uri-file-input"></a>YAML : entrée du fichier URI

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-iris-file.yml":::

## <a name="yaml-uri-folder-input"></a>YAML : entrée du dossier URI

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-iris-folder.yml":::

## <a name="yaml-notebook-via-papermill"></a>YAML : notebook via papermill

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/basics/hello-notebook.yml":::

## <a name="yaml-basic-python-model-training"></a>YAML : apprentissage du modèle Python de base

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/single-step/scikit-learn/iris/job.yml":::

## <a name="yaml-basic-r-model-training-with-local-docker-build-context"></a>YAML : apprentissage du modèle R de base avec le contexte de génération Docker local

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/single-step/r/iris/job.yml":::

## <a name="yaml-distributed-pytorch"></a>YAML : PyTorch distribué

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

## <a name="yaml-distributed-tensorflow"></a>YAML : TensorFlow distribué

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/single-step/tensorflow/mnist-distributed/job.yml":::

## <a name="yaml-distributed-mpi"></a>YAML : MPI distribué

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/jobs/single-step/tensorflow/mnist-distributed-horovod/job.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
