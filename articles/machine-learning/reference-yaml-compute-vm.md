---
title: Schéma YAML de la machine virtuelle attachée à CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma de machine virtuelle attachée à CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: b639c4193a246ccf7f57520089132f0f49d16ebb
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062431"
---
# <a name="cli-v2-attached-virtual-machine-yaml-schema"></a>Schéma YAML de la machine virtuelle attachée à CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/vmCompute.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, l’inclusion de `$schema` en haut de votre fichier vous permet d’appeler des exécutions de schéma et de ressource. | | |
| `type` | string | **Obligatoire.** Type de calcul. | `virtualmachine` | |
| `name` | string | **Obligatoire.** Nom du calcul. | | |
| `description` | string | Description du calcul. | | |
| `resource_id` | string | **Obligatoire.** ID de ressource complet de la machine virtuelle Azure à attacher à l’espace de travail en tant que cible de calcul. | | |
| `ssh_settings` | object | Paramètres SSH pour la connexion à la machine virtuelle. | | |
| `ssh_settings.admin_username` | string | Nom du compte d’utilisateur administrateur qui peut être utilisé pour établir une connexion SSH à la machine virtuelle. | | |
| `ssh_settings.admin_password` | string | Mot de passe du compte d’utilisateur administrateur. **`admin_password` ou `ssh_private_key_file` est obligatoire**. | | |
| `ssh_settings.ssh_private_key_file` | string | Chemin d’accès local au fichier de clé privée SSH du compte d’utilisateur administrateur. **`admin_password` ou `ssh_private_key_file` est obligatoire.** | | |
| `ssh_settings.ssh_port` | entier | Port SSH sur la machine virtuelle. | | `22` |

## <a name="remarks"></a>Remarques

La commande `az ml compute` permet de gérer des machines virtuelles attachées à un espace de travail Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/resources/compute). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-basic"></a>YAML : de base

:::code language="yaml" source="~/azureml-examples-main/cli/resources/compute/vm-attach.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
