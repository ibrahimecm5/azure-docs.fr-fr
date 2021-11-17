---
title: Schéma YAML de l’espace de travail CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML de l’espace de travail CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: ea641b24b43c7ad1d22e497f9d2558ccb163e81b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060015"
---
# <a name="cli-v2-workspace-yaml-schema"></a>Schéma YAML de l’espace de travail CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/workspace.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, l’inclusion de `$schema` en haut de votre fichier vous permet d’appeler des exécutions de schéma et de ressource. | | |
| `name` | string | **Obligatoire.** Nom de l’espace de travail. | | |
| `display_name` | string | Nom complet de l’espace de travail dans l’interface utilisateur du studio. Peut être non unique au sein du groupe de ressources. | | |
| `description` | string | Description de l’espace de travail. | | |
| `tags` | object | Dictionnaire d’étiquettes pour l’espace de travail. | | |
| `location` | string | Emplacement de l’espace de travail. En cas d’omission, la valeur par défaut est l’emplacement du groupe de ressources. | | |
| `resource_group` | string | **Obligatoire.** Groupe de ressources contenant l’espace de travail. Si le groupe de ressources n’existe pas, un groupe est créé. | | |
| `hbi_workspace` | boolean | Si les données client ont un impact élevé sur l’activité parce qu’elles contiennent des informations professionnelles sensibles. Pour plus d’informations, consultez les [Chiffrement de données au repos](concept-data-encryption.md#encryption-at-rest). | | `false` |
| `storage_account` | string | ID de ressource complet d’un compte de stockage Azure existant à utiliser comme compte de stockage par défaut pour l’espace de travail. Un compte de stockage avec un stockage Premium ou un espace de noms hiérarchique ne peut pas être utilisé comme compte de stockage par défaut. En cas d’omission, un nouveau compte de stockage est créé. | | |
| `container_registry` | string | ID de ressource complet d’un registre de conteneurs Azure existant à utiliser comme registre de conteneurs par défaut pour l’espace de travail. Azure ML utilise Azure Container Registry (ACR) pour la gestion des images de conteneur utilisées pour l’apprentissage et le déploiement. En cas d’omission, un nouveau registre de conteneurs est créé. La création étant chargée en différé, le registre de conteneurs est créé la première fois qu’il est nécessaire pour une opération d’apprentissage ou de déploiement. | | |
| `key_vault` | string | ID de ressource complet d’un coffre de clés Azure existant à utiliser comme coffre de clés par défaut pour l’espace de travail. En cas d’omission, un coffre de clés est créé. | | |
| `application_insights` | string | ID de ressource complet d’une fonctionnalité Azure Application Insights existante à utiliser comme fonctionnalité Application Insights par défaut pour l’espace de travail. En cas d’omission, une nouvelle fonctionnalité Application Insights est créée. | | |
| `customer_managed_key` | object | Azure Machine Learning stocke les métadonnées dans une instance d’Azure Cosmos DB. Par défaut, les données sont chiffrées au repos avec des clés gérées par Microsoft. Pour utiliser votre propre clé gérée par le client pour le chiffrement, spécifiez les informations de clé gérée par le client dans cette section. Pour plus d’informations, consultez [Chiffrement de données pour Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db). | | |
| `customer_managed_key.key_vault` | string | ID de ressource complet du coffre de clés contenant la clé gérée par le client. Ce coffre de clés peut être différent du coffre de clés par défaut de l’espace de travail spécifié dans `key_vault`.| | |
| `customer_managed_key.key_uri` | string | URI de clé de la clé gérée par le client pour chiffrer les données au repos. Le format d’URI est `https://<keyvault-dns-name>/keys/<key-name>/<key-version>`. | | |
| `image_build_compute` | string | Nom de la cible de calcul à utiliser pour la génération d’images Docker d’environnement lorsque le registre de conteneurs se trouve derrière un réseau virtuel. Pour plus d’informations, consultez [Sécuriser des ressources d’espace de travail derrière des réseaux virtuels](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr). | | |
| `public_network_access` | string | Indique si l’accès au point de terminaison public est autorisé quand l’espace de travail utilise Private Link. Pour plus d’informations, consultez [Activer l’accès public derrière des réseaux virtuels](how-to-configure-private-link.md#enable-public-access). | `enabled`, `disabled` | `disabled` |

## <a name="remarks"></a>Remarques

La commande `az ml workspace` peut être utilisée pour gérer les espaces de travail Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/resources/workspace). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-basic"></a>YAML : de base

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/basic.yml":::

## <a name="yaml-with-existing-resources"></a>YAML : avec des ressources existantes

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/with-existing-resources.yml":::

## <a name="yaml-customer-managed-key"></a>YAML : clé gérée par le client

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/cmk.yml":::

## <a name="yaml-private-link"></a>YAML : liaison privée

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/privatelink.yml":::

## <a name="yaml-high-business-impact"></a>YAML : impact élevé sur l’activité

:::code language="yaml" source="~/azureml-examples-main/cli/resources/workspace/hbi.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
