---
title: Schéma YAML de l’environnement CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML de l’environnement CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 8f812b9c02ef14d7e098c7f50e3a1c3cfabe97a6
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058351"
---
# <a name="cli-v2-environment-yaml-schema"></a>Schéma YAML de l’environnement CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/environment.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, l’inclusion de `$schema` en haut de votre fichier vous permet d’appeler des exécutions de schéma et de ressource. | | |
| `name` | string | **Obligatoire.** Nom de l’environnement. | | |
| `version` | string | Version de l’environnement. En cas d’omission, Azure ML génère automatiquement une version. | | |
| `description` | string | Description de l’environnement. | | |
| `tags` | object | Dictionnaire d’étiquettes pour l’environnement. | | |
| `image` | string | Image Docker à utiliser pour l’environnement. **`image` ou `build` est obligatoire**. | | |
| `conda_file` | chaîne ou objet | Fichier de configuration standard Conda YAML des dépendances pour un environnement Conda. Consultez https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually. <br> <br> En cas de spécification, `image` doit également être spécifié. Azure ML crée l’environnement Conda au-dessus de l’image Docker fournie. | | |
| `build` | object | Configuration du contexte de build Docker à utiliser pour l’environnement. **`image` ou `build` est obligatoire**. | | |
| `build.local_path` | string | Chemin d’accès local du répertoire à utiliser comme contexte de build. | | |
| `build.dockerfile_path` | string | Chemin d’accès relatif du Dockerfile dans le contexte de build. | | `Dockerfile` |
| `os_type` | string | Le type de système d’exploitation. | `linux`, `windows` | `linux` |  
| `inference_config` | object | Configurations de conteneur d’inférence. Applicables uniquement si l’environnement est utilisé pour générer un conteneur de service pour les déploiements en ligne. Consultez [Attributs de la clé `inference_config`](#attributes-of-the-inference_config-key). | | |

### <a name="attributes-of-the-inference_config-key"></a>Attributs de la clé `inference_config`

| Clé | Type | Description |
| --- | ---- | ----------- |
| `liveness_route` | object | Itinéraire liveness pour le conteneur de service. |
| `liveness_route.path` | string | Chemin d’accès vers lequel acheminer les demandes liveness. |
| `liveness_route.port` | entier | Port vers lequel acheminer les demandes liveness. |
| `readiness_route` | object | Itinéraire readiness pour le conteneur de service. |
| `readiness_route.path` | string | Chemin d’accès vers lequel acheminer les demandes readiness. |
| `readiness_route.port` | entier | Port vers lequel acheminer les demandes readiness. |
| `scoring_route` | object | Itinéraire scoring pour le conteneur de service. |
| `scoring_route.path` | string | Chemin d’accès vers lequel acheminer les demandes scoring. |
| `scoring_route.port` | entier | Port vers lequel acheminer les demandes scoring. |

## <a name="remarks"></a>Remarques

La commande `az ml environment` peut être utilisée pour gérer les environnements d’Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/assets/environment). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-local-docker-build-context"></a>YAML : contexte de build du Docker local

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-context.yml":::

## <a name="yaml-docker-image"></a>YAML : image Docker

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image.yml":::

## <a name="yaml-docker-image-plus-conda-file"></a>YAML : image Docker plus fichier Conda

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image-plus-conda.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
