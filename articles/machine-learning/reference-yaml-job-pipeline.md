---
title: Schéma YAML du travail de pipeline CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML du travail de pipeline CLI (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 93c8f11fbfab8386e1083985399d8138e2b2b7d6
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057078"
---
# <a name="cli-v2-pipeline-job-yaml-schema"></a>Schéma YAML du travail de pipeline CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, en incluant `$schema` en haut de votre fichier, vous pouvez appeler des complétions de schémas et de ressources. | | |
| `type` | const | **Obligatoire.** Le type de travail. | `pipeline` | |
| `name` | string | Nom du travail. Doit être unique pour tous les travaux de l’espace de travail. En cas d’omission, Azure ML génère automatiquement un GUID pour le nom. | | |
| `display_name` | string | Nom complet du travail dans l’interface utilisateur de Studio. Peut être non unique dans l’espace de travail. En cas d’omission, Azure ML génère automatiquement un identificateur de nom convivial explicite pour le nom d’affichage. | | |
| `experiment_name` | string | Nom de l’expérience sous lequel organiser le travail. Chaque enregistrement d’exécution du travail est organisé sous l’expérience correspondante dans l’onglet « Expérience » du studio. En cas d’omission, Azure ML prend par défaut le nom du répertoire de travail dans lequel le travail a été créé. | | |
| `description` | string | Description du travail. | | |
| `tags` | object | Dictionnaire d’étiquettes pour le travail. | | |
| `settings` | object | Paramètres par défaut pour le travail de pipeline. Consultez les [attributs de la clé `settings`](#attributes-of-the-settings-key) pour l’ensemble des propriétés configurables. | | |
| `jobs` | object | **Obligatoire.** Dictionnaire de l’ensemble des tâches individuelles à exécuter en tant qu’étapes dans le pipeline. Ces tâches sont considérées comme des tâches enfants du travail de pipeline parent. <br><br> La clé est le nom de l’étape dans le contexte du travail de pipeline. Ce nom est différent du nom de travail unique du travail enfant. La valeur est la spécification du travail, qui peut suivre le [schéma du travail de commande](reference-yaml-job-command.md#yaml-syntax) ou le [schéma du travail de composant](reference-yaml-job-component.md#yaml-syntax). Actuellement, seules les travaux de commande et les travaux de composants en cours d’exécution peuvent être exécutés dans un pipeline. Les versions ultérieures prendront en charge d’autres types de travaux. | | |
| `inputs` | object | Dictionnaire d’entrées du travail de pipeline. La clé est un nom pour l’entrée dans le contexte du travail et la valeur est la valeur d’entrée. <br><br> Ces entrées de pipeline peuvent être référencées par les entrées d’un travail d’étape individuel dans le pipeline à l’aide de l’expression `${{ inputs.<input_name> }}`. | | |
| `inputs.<input_name>` | nombre, entier, booléen, chaîne ou objet | Une valeur littérale (de type nombre, entier, booléen ou chaîne), [JobInputUri](#jobinputuri) ou [JobInputDataset](#jobinputdataset). | | |
| `outputs` | object | Dictionnaire des configurations de sortie du travail de pipeline. La clé est un nom pour l’entrée dans le contexte du travail et la valeur est la configuration de sortie. <br><br> Ces sorties de pipeline peuvent être référencées par les sorties d’un travail d’étape individuel dans le pipeline à l’aide de l’expression `${{ outputs.<output_name> }}`. | |
| `outputs.<output_name>` | object | Vous pouvez éventuellement spécifier un `mode` ou laisser l’objet vide. Pour chaque sortie nommée spécifiée dans le dictionnaire `outputs`, Azure ML génère automatiquement un emplacement de sortie en fonction du modèle de chemin d’accès suivant : `{default-datastore}/azureml/{job-name}/{output-name}/`. Les utilisateurs seront autorisés à fournir un emplacement personnalisé dans une version ultérieure. | |
| `outputs.<output_name>.mode` | string | Mode de remise du ou des fichiers de sortie dans le stockage de destination. Pour le mode de montage en lecture-écriture, le répertoire de sortie est un répertoire monté. Pour le mode de téléchargement, les fichiers écrits dans le répertoire de sortie sont chargés à la fin du travail. | `rw_mount`, `upload` | `rw_mount` |
| `compute` | string | Nom de la cible de calcul par défaut sur laquelle exécuter un travail d’étape, si le calcul n’est pas spécifié pour l’étape individuelle. Cette valeur doit être une référence à un calcul existant dans l’espace de travail avec la syntaxe `azureml:<compute-name>`. |

### <a name="attributes-of-the-settings-key"></a>Attributs de la clé `settings`

| Clé | Type | Description |
| --- | ---- | ----------- |
| `datastore` | string | Nom du magasin de données à utiliser comme magasin de données par défaut pour le travail de pipeline. Cette valeur doit être une référence à un magasin de données existant dans l’espace de travail avec la syntaxe `azureml:<datastore-name>`. Toute sortie définie dans la propriété `outputs` du travail de pipeline parent ou des travaux d’étape enfants sera stockée dans ce magasin de données. En cas d’omission, les sorties sont stockées dans le magasin de données d’objets blob de l’espace de travail. |

### <a name="job-inputs"></a>Entrées du travail

#### <a name="jobinputuri"></a>JobInputUri

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `file` | string | URI vers un fichier unique à utiliser comme entrée. Les types d’URI pris en charge sont `azureml`, `https`, `wasbs`, `abfss` et `adl`. Pour plus d’informations sur l’utilisation du format d’URI `azureml`, consultez [Syntaxe YAML fondamentale](reference-yaml-core-syntax.md). **Vous devez avoir `file` ou `folder`.**  | | |
| `folder` | string | URI vers un dossier à utiliser comme entrée. Les types d’URI pris en charge sont `azureml`, `wasbs`, `abfss` et `adl`. Pour plus d’informations sur l’utilisation du format d’URI `azureml`, consultez [Syntaxe YAML fondamentale](reference-yaml-core-syntax.md). **Vous devez avoir `file` ou `folder`.**   | | |
| `mode` | string | Mode de remise des données à la cible de calcul. Pour le montage en lecture seule et le montage en lecture-écriture, les données seront consommées en tant que chemin de montage. Un dossier est monté en tant que dossier et un fichier est monté en tant que fichier. Pour le mode de téléchargement, les données seront consommées en tant que chemin d’accès téléchargé. | `ro_mount`, `rw_mount`, `download` | `ro_mount` |

#### <a name="jobinputdataset"></a>JobInputDataset

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `dataset` | chaîne ou objet | **Obligatoire.** Un jeu de données à utiliser comme entrée. Cette valeur peut être une référence à un jeu de données avec version existant dans l’espace de travail ou une spécification de jeu de données inline. <br><br> Pour référencer un jeu de données existant, utilisez la syntaxe `azureml:<dataset-name>:<dataset-version>`. <br><br> Pour définir un jeu de données inline, suivez le [schéma du jeu de données](reference-yaml-dataset.md#yaml-syntax). Excluez les propriétés `name` et `version`, car elles ne sont pas prises en charge pour les jeux de données inline. | | |
| `mode` | string | Mode de remise du jeu de données à la cible de calcul. Pour un montage en lecture seule, le jeu de données est consommé en tant que chemin de montage. Un dossier est monté en tant que dossier et un fichier est monté en tant que dossier parent. Pour le mode de téléchargement, le jeu de données sera consommé en tant que chemin d’accès téléchargé. | `ro_mount`, `download` | `ro_mount` |

## <a name="remarks"></a>Remarques

Les commandes `az ml job` peuvent être utilisées pour gérer les travaux de pipeline Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/jobs). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-hello-pipeline"></a>YAML : pipeline Hello

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline.yml":::

## <a name="yaml-inputoutput-dependency"></a>YAML : dépendance d’entrée/de sortie

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-io.yml":::

## <a name="yaml-common-pipeline-job-settings"></a>YAML : paramètres courants du travail de pipeline

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-settings.yml":::

## <a name="yaml-top-level-input-and-overriding-common-pipeline-job-settings"></a>YAML : entrée de niveau supérieur et remplacement des paramètres de travail de pipeline courants

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-pipeline-abc.yml":::

## <a name="yaml-model-training-pipeline"></a>YAML : pipeline d’apprentissage du modèle

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines/cifar-10/job.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
