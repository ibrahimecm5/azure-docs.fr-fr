---
title: Schéma YAML du composant de commande CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML du composant de commande CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 53b64b804b07f60741be1a72e671e8ade4710e15
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055091"
---
# <a name="cli-v2-command-component-yaml-schema"></a>Schéma YAML du composant de commande CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, en incluant `$schema` en haut de votre fichier, vous pouvez appeler des complétions de schémas et de ressources. | | |
| `type` | const | Le type de composant. | `command` | `command` |
| `name` | string | **Obligatoire.** Nom du composant. | | |
| `version` | string | Numéro de version du composant. En cas d’omission, Azure ML génère automatiquement une version. | | |
| `display_name` | string | Nom complet du composant dans l’interface utilisateur de Studio. Peut être non unique dans l’espace de travail. | | |
| `description` | string | Description du composant. | | |
| `tags` | object | Dictionnaire d’étiquettes pour le composant. | | |
| `command` | string | **Obligatoire.** Commande à exécuter. | | |
| `code.local_path` | string | Chemin d’accès local au répertoire du code source à télécharger et à utiliser pour le composant. | | |
| `environment` | chaîne ou objet | **Obligatoire.** L’environnement à utiliser pour le composant. Cette valeur peut être une référence à un environnement avec version existant dans l’espace de travail ou une spécification d’environnement inline. <br><br> Pour référencer un environnement existant, utilisez la syntaxe `azureml:<environment-name>:<environment-version>`. <br><br> Pour définir un environnement inline, suivez le [schéma de l’environnement](reference-yaml-environment.md#yaml-syntax). Excluez les propriétés `name` et `version`, car elles ne sont pas prises en charge pour les environnements inline. | | |
| `distribution` | object | Configuration de la distribution pour les scénarios de formation distribués. [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration) ou [TensorFlowConfiguration](#tensorflowconfiguration). | | |
| `resources.instance_count` | entier | Nombre de nœuds à dédier au travail. | | `1` |
| `inputs` | object | Dictionnaire d’entrées du composant. La clé est un nom pour l’entrée dans le contexte du composant, et la valeur est la définition de l’entrée du composant. <br><br> Les entrées peuvent être référencées dans la `command` à l’aide de l’expression `${{ inputs.<input_name> }}`. | | |
| `inputs.<input_name>` | object | Définition d’entrée du composant. Consultez [Entrée de composant](#component-input) pour l’ensemble de propriétés configurables. | | |
| `outputs` | object | Dictionnaire de sorties du composant. La clé est un nom pour la sortie dans le contexte du composant, et la valeur est la définition de sortie du composant. <br><br> Les sorties peuvent être référencées dans la `command` à l’aide de l’expression `${{ outputs.<output_name> }}`. | |
| `outputs.<output_name>` | object | Définition de sortie du composant. Consultez [Sortie du composant](#component-output) pour l’ensemble des propriétés configurables. | |

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

### <a name="component-input"></a>Entrée du composant

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | string | **Obligatoire.** Type d’entrée du composant. <br><br> Utilisez `type: path` si vous souhaitez que la valeur d’entrée du travail d’exécution soit un URI de données ou un jeu de données Azure ML lors de l’exécution du composant. | `number`, `integer`, `boolean`, `string`, `path` | |
| `description` | string | Description de l’entrée. | | |
| `default` | nombre, entier, booléen ou chaîne | Valeur par défaut de l’entrée. | | |
| `optional` | boolean | Indique si l’entrée est requise. | | `false` |
| `min` | entier ou nombre | Valeur minimale acceptée pour l’entrée. Ce champ ne peut être spécifié que si le champ `type` est `number` ou `integer`. | |
| `max` | entier ou nombre | Valeur maximale acceptée pour l’entrée. Ce champ ne peut être spécifié que si le champ `type` est `number` ou `integer`. | |
| `enum` | tableau | Liste des valeurs autorisées pour l’entrée. Non applicable si le champ `type` est `boolean`.  | |

### <a name="component-output"></a>Sortie du composant

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | string | **Obligatoire.** Type de sortie du composant. | `path` | |
| `description` | string | Description de la sortie. | | |

## <a name="remarks"></a>Remarques

La commande `az ml component` peut être utilisée pour gérer les composants Azure Machine Learning.

## <a name="examples"></a>Exemples

Les exemples de composant de commande sont disponibles dans le référentiel d’exemples GitHub. Certains exemples sont affichés ci-dessous.

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/pipeline-with-components). Vous en trouverez plusieurs ci-dessous.

## <a name="hello-world-command-component"></a>Composant de commande Hello World

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/pipelines-with-components/basics/2a_basic_component/component.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
