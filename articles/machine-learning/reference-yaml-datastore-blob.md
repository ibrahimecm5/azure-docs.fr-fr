---
title: Schéma YAML de magasin de données Azure Blob avec l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML de magasin de données Azure Blob avec l’interface CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: a2eb42974b64cf3d70422b4fef8d4611639cf826
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134563"
---
# <a name="cli-v2-azure-blob-datastore-yaml-schema"></a>Schéma YAML de magasin de données Azure Blob avec l’interface CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, l’inclusion de `$schema` en haut de votre fichier vous permet d’appeler des exécutions de schéma et de ressource. | | |
| `type` | string | **Obligatoire.** Type de magasin de données. | `azure_blob` | |
| `name` | string | **Obligatoire.** Nom du magasin de données. | | |
| `description` | string | Description du magasin de données. | | |
| `tags` | object | Dictionnaire d’étiquettes pour le magasin de données. | | |
| `account_name` | string | **Obligatoire.** Nom du compte de stockage Azure. | | |
| `container_name` | string | **Obligatoire.** Nom du conteneur. | | |
| `endpoint` | string | Suffixe du point de terminaison du service de stockage, utilisé pour créer l’URL du point de terminaison du compte de stockage en combinant le nom du compte de stockage et `endpoint`. Exemple d’URL de compte de stockage : `https://<storage-account-name>.blob.core.windows.net`. | | `core.windows.net` |
| `protocol` | string | Protocole à utiliser pour la connexion au conteneur. | `https`, `wasbs` | `https` |
| `credentials` | object | Informations d’identification d’authentification basées sur les informations d’identification pour la connexion au compte de stockage Azure. Vous pouvez fournir une clé de compte ou un jeton de signature d’accès partagé (SAS). Les secrets des informations d’identification sont stockés dans le coffre de clés de l’espace de travail. | | |
| `credentials.account_key` | string | Clé de compte pour accéder au compte de stockage. **Vous devez fournir `credentials.account_key` ou `credentials.sas_token` si `credentials` est spécifié.** | | |
| `credentials.sas_token` | string | Le jeton SAS pour accéder au compte de stockage. **Vous devez fournir `credentials.account_key` ou `credentials.sas_token` si `credentials` est spécifié.** | | |

## <a name="remarks"></a>Remarques

La commande `az ml datastore` peut être utilisée pour gérer les magasins de données d’Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-identity-based-access"></a>YAML : accès basé sur l’identité

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/blob-credless.yml":::

## <a name="yaml-account-key"></a>YAML : clé de compte

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/blob.yml":::

## <a name="yaml-wasbs-protocol"></a>YAML : protocole wasbs

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/blob-protocol.yml":::

## <a name="yaml-sas-token"></a>YAML : jeton SAS

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/blob-sas.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
