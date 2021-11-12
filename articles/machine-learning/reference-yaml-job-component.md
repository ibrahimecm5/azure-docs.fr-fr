---
title: Schéma YAML du travail du composant CLI (v2)
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
ms.openlocfilehash: fc1f9580b44132b172c7f58a93d75943dea63efd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566799"
---
# <a name="cli-v2-component-job-yaml-schema"></a>Schéma YAML du travail du composant CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/commandJob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Le schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, en incluant `$schema` en haut de votre fichier, vous pouvez appeler des complétions de schémas et de ressources. | | |
| `type` | const | Le type de travail. | `component` | |
| `component` | object | **Obligatoire.** Composant à appeler et à exécuter dans un travail. Cette valeur peut être une référence à un composant avec version existant dans l’espace de travail, une spécification de composant inline ou le chemin d’accès local à un fichier de spécification YAML de composant distinct. <br><br> Pour référencer un composant existant, utilisez la syntaxe `azureml:<component-name>:<component-version>`. <br><br> Pour définir un composant inline ou dans un fichier YAML distinct, suivez le [schéma du composant de commande](reference-yaml-component-command.md#yaml-syntax). Excluez les propriétés `name` et `version`, car elles ne s’appliquent pas aux spécifications de composant inline. | | |
| `compute` | string | Nom de la cible de calcul sur laquelle exécuter le travail. Cette valeur doit être une référence à un calcul existant dans l’espace de travail avec la syntaxe `azureml:<compute-name>`. Si vous l’omettez, Azure ML utilise le calcul défini dans la propriété `compute` du travail de pipeline. | | |
| `inputs` | object | Dictionnaire d’entrées du travail. La clé correspond au nom de l’une des entrées de composant, et la valeur est la valeur d’entrée du runtime. <br><br> Les entrées peuvent être référencées dans la `command` à l’aide de l’expression `${{ inputs.<input_name> }}`. | | |
| `inputs.<input_name>` | nombre, entier, booléen, chaîne ou objet | Une valeur littérale (de type nombre, entier, booléen ou chaîne), [JobInputUri](#jobinputuri) ou [JobInputDataset](#jobinputdataset). Vous pouvez également référencer les sorties d’un autre travail dans le même pipeline via `jobs.<COMPONENT_NAME>.outputs.<OUTPUT_NAME>` | | |
| `outputs` | object | Dictionnaire des configurations de sortie du travail. La clé correspond au nom de l’une des sorties du composant, et la valeur est la configuration de sortie du runtime. <br><br> Les sorties peuvent être référencées dans la `command` à l’aide de l’expression `${{ outputs.<output_name> }}`. | |
| `outputs.<output_name>` | object | Vous pouvez éventuellement spécifier un `mode` ou laisser l’objet vide. Pour chaque sortie nommée spécifiée dans le dictionnaire `outputs`, Azure ML génère automatiquement un emplacement de sortie en fonction du modèle de chemin d’accès suivant : `{default-datastore}/azureml/{job-name}/{output-name}/`. Les utilisateurs seront autorisés à fournir un emplacement personnalisé dans une version ultérieure. | |
| `outputs.<output_name>.mode` | string | Mode de remise du ou des fichiers de sortie dans le stockage de destination. Pour le mode de montage en lecture-écriture, le répertoire de sortie est un répertoire monté. Pour le mode de téléchargement, les fichiers écrits dans le répertoire de sortie sont chargés à la fin du travail. | `rw_mount`, `upload` | `rw_mount` |
| `overrides` | object | Certains paramètres d’un composant peuvent être remplacés par des paramètres d’exécution différents lorsque le composant est exécuté dans un travail. Pour un composant de commande, les propriétés `resources` et `distribution` peuvent être remplacées via `overrides.resources` et `overrides.distribution`. | | |

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

Les travaux de composant peuvent être exécutés dans des travaux de pipeline. Les commandes `az ml job` peuvent être utilisées pour gérer les travaux de pipeline Azure Machine Learning.

Actuellement, les travaux ne peuvent pas être exécutés en tant que travaux autonomes et ne peuvent être exécutés que dans des pipelines.

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
