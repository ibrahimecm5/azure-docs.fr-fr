---
title: Schéma YAML de déploiement en ligne géré avec l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence sur le schéma YAML de déploiement en ligne géré avec l’interface CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: bb04bfb69052fab8224b70206b3c4cf680f20769
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555885"
---
# <a name="cli-v2-managed-online-deployment-yaml-schema"></a>Schéma YAML de déploiement en ligne géré avec l’interface CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code Azure Machine Learning pour créer le fichier YAML, en incluant `$schema` en haut de votre fichier, vous pouvez appeler des complétions de schémas et de ressources. | | |
| `name` | string | **Obligatoire.** Nom du déploiement. <br><br> Les règles de nommage sont définies [ici](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).| | |
| `description` | string | Description du déploiement. | | |
| `tags` | object | Dictionnaire d’étiquettes pour le déploiement. | | |
| `endpoint_name` | string | **Obligatoire.** Nom du point de terminaison sous lequel créer le déploiement. | | |
| `model` | chaîne ou objet | Modèle à utiliser pour le déploiement. Cette valeur peut être une référence à un modèle versionné existant dans l’espace de travail ou une spécification de modèle inline. <br><br> Pour référencer un modèle existant, utilisez la syntaxe `azureml:<model-name>:<model-version>`. <br><br> Pour définir un modèle inline, suivez le [schéma du modèle](reference-yaml-model.md#yaml-syntax). <br><br> En guise de bonne pratique pour les scénarios de production, vous devez créer le modèle séparément et le référencer ici. <br><br> Ce champ est facultatif pour les scénarios de [déploiement de conteneurs personnalisés](how-to-deploy-custom-container.md).| | |
| `model_mount_path` | string | Chemin pour monter le modèle dans un conteneur personnalisé. S’applique uniquement aux scénarios de [déploiement de conteneurs personnalisés](how-to-deploy-custom-container.md). Si le champ `model` est spécifié, il est monté sur ce chemin dans le conteneur. | | |
| `code_configuration` | object | Configuration de la logique du code de scoring. <br><br> Ce champ est facultatif pour les scénarios de [déploiement de conteneurs personnalisés](how-to-deploy-custom-container.md). | | |
| `code_configuration.code.local_path` | string | Chemin local au répertoire de code source pour le scoring du modèle. | | |
| `code_configuration.scoring_script` | string | Chemin relatif au fichier de scoring dans le répertoire de code source. | | |
| `environment` | chaîne ou objet | **Obligatoire.** Environnement à utiliser pour le déploiement. Cette valeur peut être une référence à un environnement versionné existant dans l’espace de travail ou une spécification d’environnement inline. <br><br> Pour référencer un environnement existant, utilisez la syntaxe `azureml:<environment-name>:<environment-version>`. <br><br> Pour définir un environnement inline, suivez le [schéma de l’environnement](reference-yaml-environment.md#yaml-syntax). <br><br> En guise de bonne pratique pour les scénarios de production, vous devez créer l’environnement séparément et le référencer ici. | | |
| `instance_type` | string | **Obligatoire.** Taille de machine virtuelle à utiliser pour le déploiement. Pour obtenir la liste des tailles prises en charge, consultez la [liste des références SKU des points de terminaison en ligne managés](reference-managed-online-endpoints-vm-sku-list.md). | | |
| `instance_count` | entier | **Obligatoire.** Nombre d’instances à utiliser pour le déploiement. Spécifiez la valeur en fonction de la charge de travail que vous attendez. Pour une haute disponibilité, Microsoft vous recommande de la définir sur au moins `3`. <br><br> Vous pouvez mettre à jour `instance_count` après la création du déploiement en utilisant la commande `az ml online-deployment update`. | | |
| `app_insights_enabled` | boolean | Indique s’il faut activer l’intégration à l’instance Azure Application Insights associée à votre espace de travail. | | `false` |
| `scale_settings` | object | Paramètres de mise à l’échelle pour le déploiement. Actuellement, seul le type de mise à l’échelle `default` est pris en charge. Il est donc inutile de spécifier cette propriété. <br><br> Avec le type de mise à l’échelle `default`, vous pouvez 1) augmenter et diminuer manuellement le nombre d’instances après la création du déploiement en mettant à jour la propriété `instance_count` ou 2) créer une [stratégie de mise à l’échelle automatique](). | | |
| `scale_settings.type` | string | Type de mise à l’échelle. | `default` | `default` |
| `request_settings` | object | Paramètres de la demande de scoring pour le déploiement. Consultez [RequestSettings](#requestsettings) pour obtenir l’ensemble des propriétés configurables. | | |
| `liveness_probe` | object | Paramètres de probe liveness utilisée pour monitorer régulièrement l’intégrité du conteneur. Consultez [ProbeSettings](#probesettings) pour obtenir l’ensemble des propriétés configurables. | | |
| `readiness_probe` | object | Paramètres de la probe readiness utilisée pour valider si le conteneur est prêt à traiter le trafic. Consultez [ProbeSettings](#probesettings) pour obtenir l’ensemble des propriétés configurables. | | |

### <a name="requestsettings"></a>RequestSettings

| Clé | Type | Description | Valeur par défaut |
| --- | ---- | ----------- | ------------- |
| `request_timeout_ms` | entier | Délai d’expiration de scoring en millisecondes. | `5000` |
| `max_concurrent_requests_per_instance` | entier | Nombre maximal de demandes simultanées par instance autorisées pour le déploiement. <br><br> **Ne modifiez pas la valeur par défaut de ce paramètre, sauf indication contraire du support technique Microsoft ou d’un membre de l’équipe Azure ML.** | `1` |
| `max_queue_wait_ms` | entier | Durée maximale en millisecondes pendant laquelle une requête reste dans la file d’attente. | `500` |

### <a name="probesettings"></a>ProbeSettings

| Clé | Type | Description | Valeur par défaut |
| --- | ---- | ----------- | ------------- |
| `period` | entier | Fréquence (en secondes) d’exécution de la probe. | `10` |
| `initial_delay` | entier | Nombre de secondes après le démarrage du conteneur avant le lancement de la probe. La valeur minimale est `1`. | `10` |
| `timeout` | entier | Délai d’expiration de la probe en secondes. La valeur minimale est `1`. | `2` |
| `success_threshold` | entier | Nombre minimal de réussites consécutives pour que la probe soit considérée comme réussie après avoir échoué. La valeur minimale est `1`. | `1` |
| `failure_threshold` | entier | En cas d’échec d’une probe, le réessaie `failure_threshold` fois avant d’abandonner. Dans le cas d’une probe liveness, un abandon implique le redémarrage du conteneur. Dans le cas d’une probe readiness, le conteneur est marqué comme non prêt. La valeur minimale est `1`. | `30` |

## <a name="remarks"></a>Remarques

La commande `az ml online-deployment` peut être utilisée pour gérer les déploiements en ligne managés d’Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [dépôt d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-basic"></a>YAML : de base

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/blue-deployment.yml":::

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/green-deployment.yml":::

## <a name="yaml-system-assigned-identity"></a>YAML : identité affectée par le système

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

## <a name="yaml-user-assigned-identity"></a>YAML : identité affectée par l’utilisateur

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-uai-deployment.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
