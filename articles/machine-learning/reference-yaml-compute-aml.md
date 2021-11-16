---
title: Schéma YAML du cluster de calcul (AmlCompute) CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML du cluster de calcul (AmlCompute) CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: eac97f87de7edafc45f0e434188579df19d99c36
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057608"
---
# <a name="cli-v2-compute-cluster-amlcompute-yaml-schema"></a>Schéma YAML du cluster de calcul (AmlCompute) CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, l’inclusion de `$schema` en haut de votre fichier vous permet d’appeler des exécutions de schéma et de ressource. | | |
| `type` | string | **Obligatoire.** Type de calcul. | `amlcompute` | |
| `name` | string | **Obligatoire.** Nom du calcul. | | |
| `description` | string | Description du calcul. | | |
| `location` | string | Emplacement pour le calcul. En cas d’omission, par défaut, il s’agit de l’emplacement de l’espace de travail. | | |
| `size` | string | Taille de machine virtuelle à utiliser pour le cluster. Pour plus d’informations, consultez [Séries et tailles de machines virtuelles prises en charge](concept-compute-target.md#supported-vm-series-and-sizes). Notez que toutes les tailles ne sont pas disponibles dans toutes les régions. | Pour obtenir la liste des tailles prises en charge dans une région donnée, utilisez la commande `az ml compute list-sizes`.  | `Standard_DS3_v2` |
| `tier` | string | Niveau de priorité de machine virtuelle à utiliser pour le cluster. Les machines virtuelles de faible priorité sont préemprives, mais leur coût est réduit par rapport à celui de machines virtuelles dédiées. | `dedicated`, `low_priority` | `dedicated` |
| `min_instances` | entier | Nombre minimal de nœuds à utiliser sur le cluster. La définition du nombre minimal de nœuds sur `0` permet à Azure ML de mettre automatiquement à l’échelle le cluster à zéro nœud quand il n’est pas utilisé. Toute valeur supérieure à `0` conserve ce nombre de nœuds en cours d’exécution, même si le cluster n’est pas utilisé. | | `0` |
| `max_instances` | entier | Nombre maximal de nœuds à utiliser sur le cluster. | | `1` |
| `idle_time_before_scale_down` | entier | Durée d’inactivité du nœud, en secondes, avant la mise à l’échelle du cluster. | | `120` |
| `ssh_public_access_enabled` | boolean | Indique s’il faut activer l’accès SSH public sur les nœuds du cluster. | | `false` |
| `ssh_settings` | object | Paramètres SSH pour la connexion au cluster. | | |
| `ssh_settings.admin_username` | string | Nom du compte d’utilisateur administrateur qui peut être utilisé pour établir une connexion SSH à des nœuds. | | |
| `ssh_settings.admin_password` | string | Mot de passe du compte d’utilisateur administrateur. **`admin_password` ou `ssh_key_value` est obligatoire**. | | |
| `ssh_settings.ssh_key_value` | string | Clé publique SSH du compte d’utilisateur administrateur. **`admin_password` ou `ssh_key_value` est obligatoire**. | | |
| `network_settings` | object | Paramètres de sécurité réseau. | | |
| `network_settings.vnet_name` | string | Nom du réseau virtuel lors de la création d’un nouveau réseau ou de la référence à un réseau existant. | | |
| `network_settings.subnet` | string | Le nom du sous-réseau lors de la création d’un nouveau réseau virtuel ou de la référence à un réseau existant, ou l’ID de ressource complet d’un sous-réseau dans un réseau virtuel existant. Ne spécifiez pas `network_settings.vnet_name` si l’ID de sous-réseau est spécifié. L’ID de sous-réseau peut faire référence à un réseau virtuel/sous-réseau dans un autre groupe de ressources. | | |
| `identity` | object | Configuration d’identité managée à attribuer au calcul. Les clusters AmlCompute prennent en charge soit une seule identité attribuée par le système, soit plusieurs identités attribuées par l’utilisateur, mais pas les deux simultanément. | | |
| `identity.type` | string | Type d’identité managée à attribuer au calcul. Si le type est `user_assigned`, la propriété `identity.user_assigned_identities` doit également être spécifiée. | `system_assigned`, `user_assigned` | |
| `identity.user_assigned_identities` | tableau | Liste des ID de ressource complets des identités affectées par l’utilisateur. | | |

## <a name="remarks"></a>Remarques

La commande `az ml compute` peut être utilisée pour gérer les clusters de calcul Azure Machine Learning (AmlCompute).

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/resources/compute). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-minimal"></a>YAML : minimal

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-minimal.yml":::

## <a name="yaml-basic"></a>YAML : de base

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-basic.yml":::

## <a name="yaml-custom-location"></a>YAML : emplacement personnalisé

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-location.yml":::

## <a name="yaml-low-priority"></a>YAML : priorité basse

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-low-priority.yml":::

## <a name="yaml-ssh-username-and-password"></a>YAML : nom d’utilisateur et mot de passe SSH

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/cluster-ssh-password.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
