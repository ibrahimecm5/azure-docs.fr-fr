---
title: Informations de référence YAML sur les points de terminaison en ligne (préversion)
titleSuffix: Azure Machine Learning
description: Découvrir les fichiers YAML utilisés pour déployer des modèles en tant que points de terminaison en ligne
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 2fe01750fdce0a3d5c3fcb5211485666caf95bb4
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057743"
---
# <a name="cli-v2-online-endpoint-yaml-schema"></a>Schéma YAML d’un point de terminaison en ligne avec l’interface CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> Un exemple YAML complet pour les points de terminaison en ligne managés est disponible à des fins de [référence](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, l’inclusion de `$schema` en haut de votre fichier vous permet d’appeler des exécutions de schéma et de ressource. | | |
| `name` | string | **Obligatoire.** Nom du point de terminaison, Doit être unique au niveau de la région Azure. <br><br> Les règles de nommage sont définies [ici](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).| | |
| `description` | string | Description du point de terminaison. | | |
| `tags` | object | Dictionnaire d’étiquettes pour le point de terminaison. | | |
| `auth_mode` | string | Méthode d’authentification pour le point de terminaison. L’authentification basée sur une clé et l’authentification basée sur un jeton Azure ML sont prises en charge. L’authentification basée sur une clé n’expire pas, contrairement à l’authentification basée sur un jeton Azure ML. | `key`, `aml_token` | `key` |
| `allow_public_access` | boolean | Indique s’il faut autoriser l’accès public quand Private Link est activé. | | `true` |
| `identity` | object | Configuration de l’identité managée afin d’accéder aux ressources Azure pour le provisionnement et l’inférence des points de terminaison. | | |
| `identity.type` | string | Type d’identité managée. Si le type est `user_assigned`, la propriété `identity.user_assigned_identities` doit également être spécifiée. | `system_assigned`, `user_assigned` | |
| `identity.user_assigned_identities` | tableau | Liste des ID de ressource complets des identités affectées par l’utilisateur. | | |

## <a name="remarks"></a>Remarques

La commande `az ml online-endpoint` peut être utilisée pour gérer les points de terminaison en ligne Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/batch). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-basic"></a>YAML : de base

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/endpoint.yml":::

## <a name="yaml-system-assigned-identity"></a>YAML : identité affectée par le système

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/1-sai-create-endpoint.yml":::

## <a name="yaml-user-assigned-identity"></a>YAML : identité affectée par l’utilisateur

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/1-uai-create-endpoint.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
- Découvrez comment [déployer un modèle avec un point de terminaison en ligne managé](how-to-deploy-managed-online-endpoints.md).
- [Résolution des problèmes de déploiement et de scoring de points de terminaison en ligne managés (préversion)](./how-to-troubleshoot-online-endpoints.md)