---
title: Schéma YAML du modèle CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence sur le schéma YAML du modèle CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: a9f3ac8ce87c3cec11c7bb5cb17e1004bce9f953
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556880"
---
# <a name="cli-v2-model-yaml-schema"></a>Schéma YAML du modèle CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/model.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `$schema` | string | Schéma YAML. | |
| `name` | string | **Obligatoire.** Nom du modèle. | |
| `version` | string | Version du modèle. En cas d’omission, Azure ML génère automatiquement une version. | |
| `description` | string | Description du modèle. | |
| `tags` | object | Dictionnaire d’étiquettes pour le modèle. | |
| `local_path` | string | Chemin local au ou aux fichiers du modèle. Peut pointer vers un fichier ou un répertoire. **`local_path` ou `model_uri` est obligatoire.** | |
| `model_uri` | string | URI du ou des fichiers de modèle. Peut pointer vers un fichier ou un répertoire. **`local_path` ou `model_uri` est obligatoire.** | |
| `model_format` | string | Format de stockage du modèle. Applicable aux scénarios de déploiement sans code. | `custom`, `mlflow`, `triton`, `openai` |
| `flavors` | object | Saveurs du modèle. Chaque type de format de stockage de modèle peut avoir une ou plusieurs saveurs prises en charge. Applicable aux scénarios de déploiement sans code. | |

## <a name="remarks"></a>Remarques

La commande `az ml model` peut être utilisée pour gérer les modèles Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [dépôt d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/assets/model). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-local-file"></a>YAML : fichier local

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/model/local-file.yml":::

## <a name="yaml-local-folder-in-mlflow-format"></a>YAML : dossier local au format MLflow

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/model/local-mlflow.yml":::

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
