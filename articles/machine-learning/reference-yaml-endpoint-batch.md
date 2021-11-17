---
title: Schéma YAML du point de terminaison de traitement par lots de l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence sur le schéma YAML du point de terminaison de traitement par lots de l’interface CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: tracychms
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: cfb475bef0cb308a8e7570a4cbad71e4e9bebb54
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566785"
---
# <a name="cli-v2-batch-endpoint-yaml-schema"></a>Schéma YAML du point de terminaison de traitement par lots de l’interface CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, le fait d’ajouter la mention `$schema` en haut de votre fichier vous permet d’appeler des achèvements de schémas et de ressources. | | |
| `name` | string | **Obligatoire.** Nom du point de terminaison, Doit être unique au niveau de la région Azure. | | |
| `description` | string | Description du point de terminaison. | | |
| `tags` | object | Dictionnaire d’étiquettes du point de terminaison. | | |
| `auth_mode` | string | Méthode d’authentification du point de terminaison. Actuellement, seule l’authentification par jeton Azure Active Directory (Azure AD) est prise en charge. | `aad_token` | `aad_token` |
| `defaults` | object | Paramètres par défaut du point de terminaison. | | |
| `defaults.deployment_name` | string | Nom du déploiement qui fera office de déploiement par défaut du point de terminaison. | | |

## <a name="remarks"></a>Remarques

Les commandes `az ml batch-endpoint` permettent de gérer les points de terminaison Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel GitHub d’exemples](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-basic"></a>YAML : de base

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/batch/batch-endpoint.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
