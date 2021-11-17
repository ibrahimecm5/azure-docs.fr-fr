---
title: Schéma YAML du jeu de données CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML du modèle CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 4a22fee30a28008fa440d606360623e156c332a0
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133679"
---
# <a name="cli-v2-dataset-yaml-schema"></a>Schéma YAML du jeu de données CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/dataset.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées |
| --- | ---- | ----------- | -------------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, l’inclusion de `$schema` en haut de votre fichier vous permet d’appeler des exécutions de schéma et de ressource. | |
| `name` | string | **Obligatoire.** Nom du jeu de données | |
| `version` | string | Version du jeu de données. En cas d’omission, Azure ML génère automatiquement une version. | |
| `description` | string | Description du jeu de données. | |
| `tags` | object | Dictionnaire d’étiquettes pour le jeu de données. | |
| `local_path` | string | Chemin d’accès absolu ou relatif d’un fichier ou dossier local à partir duquel le jeu de données est créé. **`local_path` ou `paths` est obligatoire**. | |
| `paths` | tableau | Liste de sources d’URI à partir desquelles le jeu de données est créé. Chaque entrée de la liste doit adhérer au schéma défini dans le [chemin source du jeu de données](#dataset-source-path). Actuellement, une seule source est prise en charge.  **`local_path` ou `paths` est obligatoire**. | |

### <a name="dataset-source-path"></a>Chemin source du jeu de données

| Clé | Type | Description |
| --- | ---- | ----------- |
| `file` | string | URI d’un fichier utilisé comme source pour le jeu de données. Les types d’URI pris en charge sont `azureml`, `https`, `wasbs`, `abfss` et `adl`. Pour plus d’informations sur l’utilisation du format d’URI `azureml://`, consultez [Syntaxe YAML fondamentale](reference-yaml-core-syntax.md). **`file` ou `folder` est obligatoire**. |
| `folder` | string | URI d’un dossier utilisé comme source pour le jeu de données. Les types d’URI pris en charge sont `azureml`, `https`, `wasbs`, `abfss` et `adl`. Pour plus d’informations sur l’utilisation du format d’URI `azureml://`, consultez [Syntaxe YAML fondamentale](reference-yaml-core-syntax.md). **`file` ou `folder` est obligatoire**. |

## <a name="remarks"></a>Remarques

La commande `az ml dataset` peut être utilisée pour gérer les jeux de données d’Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/assets/dataset). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-datastore-file"></a>YAML : fichier datastore

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file.yml":::

## <a name="yaml-datastore-folder"></a>YAML : dossier datastore

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder.yml":::

## <a name="yaml-https-file"></a>YAML : fichier https

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file-https.yml":::

## <a name="yaml-https-folder"></a>YAML : dossier https

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder-https.yml":::

## <a name="yaml-wasbs-file"></a>YAML : fichier wasbs

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-file-wasbs.yml":::

## <a name="yaml-wasbs-folder"></a>YAML : dossier wasbs

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/cloud-folder-wasbs.yml":::

## <a name="yaml-local-file"></a>YAML : fichier local

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/local-file.yml":::

## <a name="yaml-local-folder"></a>YAML : dossier local

:::code language="yaml" source="~/azureml-examples-main/cli/assets/dataset/local-folder.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
