---
title: Schéma YAML de déploiement par lots CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence sur le schéma YAML de déploiement par lots CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: tracychms
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 53e83f76e61038bc1fb95bfba653b18f63febede
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054502"
---
# <a name="cli-v2-batch-deployment-yaml-schema"></a>Schéma YAML de déploiement par lots CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, l’inclusion de `$schema` en haut de votre fichier vous permet d’appeler des exécutions de schéma et de ressource. | | |
| `name` | string | **Obligatoire.** Nom du déploiement. | | |
| `description` | string | Description du déploiement. | | |
| `tags` | object | Dictionnaire d’étiquettes pour le déploiement. | | |
| `endpoint_name` | string | **Obligatoire.** Nom du point de terminaison sous lequel créer le déploiement. | | |
| `model` | chaîne ou objet | **Obligatoire.** Modèle à utiliser pour le déploiement. Cette valeur peut être une référence à un modèle versionné existant dans l’espace de travail ou une spécification de modèle inline. <br><br> Pour référencer un modèle existant, utilisez la syntaxe `azureml:<model-name>:<model-version>`. <br><br> Pour définir un modèle inline, suivez le [schéma du modèle](reference-yaml-model.md#yaml-syntax). <br><br> En guise de bonne pratique pour les scénarios de production, vous devez créer le modèle séparément et le référencer ici. | | |
| `code_configuration` | object | Configuration de la logique du code de scoring. <br><br> Cette propriété n’est pas requise si votre modèle est au format MLflow. | | |
| `code_configuration.code.local_path` | string | Chemin local au répertoire de code source pour le scoring du modèle. | | |
| `code_configuration.scoring_script` | string | Chemin relatif au fichier de scoring dans le répertoire de code source. | | |
| `environment` | chaîne ou objet | Environnement à utiliser pour le déploiement. Cette valeur peut être une référence à un environnement versionné existant dans l’espace de travail ou une spécification d’environnement inline. <br><br> Cette propriété n’est pas requise si votre modèle est au format MLflow. <br><br> Pour référencer un environnement existant, utilisez la syntaxe `azureml:<environment-name>:<environment-version>`. <br><br> Pour définir un environnement inline, suivez le [schéma de l’environnement](reference-yaml-environment.md#yaml-syntax). <br><br> En guise de bonne pratique pour les scénarios de production, vous devez créer l’environnement séparément et le référencer ici. | | |
| `compute` | string | **Obligatoire.** Nom de la cible de calcul sur laquelle exécuter les travaux de scoring par lots. Cette valeur doit être une référence à un calcul existant dans l’espace de travail avec la syntaxe `azureml:<compute-name>`. | | |
| `resources.instance_count` | entier | Nombre de nœuds à utiliser pour chaque travail de scoring par lots. | | `1` |
| `max_concurrency_per_instance` | entier | Nombre maximal d’exécutions `scoring_script` parallèles par instance. | | `1` |
| `error_threshold` | entier | Nombre d’échecs de fichier qui doivent être ignorés. Si le nombre d’erreurs présentes dans la totalité de l’entrée dépasse cette valeur, le travail de scoring par lots va se terminer. `error_threshold` est destiné à l’ensemble de l’entrée et non aux mini-lots individuels. En cas d’omission, un nombre quelconque d’échecs de fichier est autorisé sans terminer la tâche.  | | `-1` |
| `logging_level` | string | Niveau de verbosité du journal. | `warning`, `info`, `debug` | `info` |
| `mini_batch_size` | entier | Nombre de fichiers qu’un `code_configuration.scoring_script` peut traiter en un appel `run()`. | | `10` |
| `retry_settings` | object | Paramètres de nouvelle tentative pour le scoring de chaque mini-lot. | | |
| `retry_settings.max_retries` | entier | Nombre maximal de nouvelles tentatives pour un mini-lot ayant échoué ou dépassé. | | `3` |
| `retry_settings.timeout` | entier | Délai d’attente, en secondes, pour le scoring d’un mini-lot. | | `30` |
| `output_action` | string | Indique le mode d’organisation de la sortie dans le fichier de sortie. | `append_row`, `summary_only` | `append_row` |
| `output_file_name` | string | Nom du fichier de sortie de scoring par lots. | | `predictions.csv` |
| `environment_variables` | object | Dictionnaire de paires nom-valeur de variable d’environnement à définir pour chaque travail de scoring par lots. | | |

## <a name="remarks"></a>Remarques

La commande `az ml batch-deployment` peut être utilisée pour gérer les déploiements de lots Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-basic-mlflow"></a>YAML : de base (MLflow)

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/mlflow-deployment.yml":::

## <a name="yaml-custom-model-and-scoring-code"></a>YAML : modèle personnalisé et code de scoring

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/nonmlflow-deployment.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
