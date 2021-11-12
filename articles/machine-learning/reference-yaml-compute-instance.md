---
title: Schéma YAML de l’instance de calcul CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML d’instance de calcul CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 2b4f8ddb82e758899c0de70bf722f1184f2f7aff
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056527"
---
# <a name="cli-v2-compute-instance-yaml-schema"></a>Schéma YAML de l’instance de calcul CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, l’inclusion de `$schema` en haut de votre fichier vous permet d’appeler des exécutions de schéma et de ressource. | | |
| `type` | string | **Obligatoire.** Type de calcul. | `computeinstance` | |
| `name` | string | **Obligatoire.** Nom du calcul. | | |
| `description` | string | Description du calcul. | | |
| `size` | string | La taille de machine virtuelle à utiliser pour l’instance de calcul. Pour plus d’informations, consultez [Séries et tailles de machines virtuelles prises en charge](concept-compute-target.md#supported-vm-series-and-sizes). Notez que toutes les tailles ne sont pas disponibles dans toutes les régions. | Pour obtenir la liste des tailles prises en charge dans une région donnée, utilisez la commande `az ml compute list-sizes`.  | `Standard_DS3_v2` |
| `create_on_behalf_of` | object | Paramètres pour la création de l’instance de calcul pour le compte d’un autre utilisateur. Vérifiez que l’utilisateur affecté dispose des autorisations RBAC appropriées. |  |  |
| `create_on_behalf_of.user_tenant_id` | string | ID de locataire AAD de l’utilisateur affecté. |  |  |
| `create_on_behalf_of.user_object_id` | string | ID d’objet AAD de l’utilisateur affecté. |  |  |
| `ssh_public_access_enabled` | boolean | Indique s’il faut activer l’accès SSH public sur l’instance de calcul. | | `false` |
| `ssh_settings` | object | Paramètres SSH pour la connexion à l’instance de calcul. | | |
| `ssh_settings.ssh_key_value` | string | Clé publique SSH du compte d’utilisateur administrateur. | | |
| `network_settings` | object | Paramètres de sécurité réseau. | | |
| `network_settings.vnet_name` | string | Nom du réseau virtuel lors de la création d’un nouveau réseau ou de la référence à un réseau existant. | | |
| `network_settings.subnet` | string | Le nom du sous-réseau lors de la création d’un nouveau réseau virtuel ou de la référence à un réseau existant, ou l’ID de ressource complet d’un sous-réseau dans un réseau virtuel existant. Ne spécifiez pas `network_settings.vnet_name` si l’ID de sous-réseau est spécifié. L’ID de sous-réseau peut faire référence à un réseau virtuel/sous-réseau dans un autre groupe de ressources. | | |

## <a name="remarks"></a>Remarques

La commande `az ml compute` peut être utilisée pour gérer les instances de calcul Azure Machine Learning.

## <a name="yaml-minimal"></a>YAML : minimal

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/instance-minimal.yml":::

## <a name="yaml-basic"></a>YAML : de base

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/instance-basic.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
