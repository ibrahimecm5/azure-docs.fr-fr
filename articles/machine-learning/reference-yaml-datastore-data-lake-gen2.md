---
title: Schéma YAML de magasin de données Azure Data Lake Gen2 avec l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML de magasin de données Azure Data Lake Gen2 avec l’interface CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 56041a7fc16b09f1622d7090a3ef6e2d088742ee
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556062"
---
# <a name="cli-v2-azure-data-lake-gen2-yaml-schema"></a>Schéma YAML pour Azure Data Lake Gen2 avec l’interface CLI (v2)

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code Azure Machine Learning pour créer le fichier YAML, en incluant `$schema` en haut de votre fichier, vous pouvez appeler des complétions de schémas et de ressources. | | |
| `type` | string | **Obligatoire.** Type de magasin de données. | `azure_data_lake_gen2` | |
| `name` | string | **Obligatoire.** Nom du magasin de données. | | |
| `description` | string | Description du magasin de données. | | |
| `tags` | object | Dictionnaire d’étiquettes pour le magasin de données. | | |
| `account_name` | string | **Obligatoire.** Nom du compte de stockage Azure. | | |
| `filesystem` | string | **Obligatoire.** Nom du système de fichiers. Répertoire parent contenant les fichiers et les dossiers. Cela équivaut à un conteneur dans Stockage Blob Azure. | | |
| `endpoint` | string | Suffixe du point de terminaison du service de stockage, utilisé pour créer l’URL du point de terminaison du compte de stockage en combinant le nom du compte de stockage et `endpoint`. Exemple d’URL de compte de stockage : `https://<storage-account-name>.dfs.core.windows.net`. | | `core.windows.net` |
| `protocol` | string | Protocole à utiliser pour se connecter au système de fichiers. | `https`, `abfss` | `https` |
| `credentials` | object | Informations d’identification du principal de service pour la connexion au compte de stockage Azure. Les secrets des informations d’identification sont stockés dans le coffre de clés de l’espace de travail. | | |
| `credentials.tenant_id` | string | ID locataire du principal de service. **Obligatoire si `credentials` est spécifié.** | | |
| `credentials.client_id` | string | ID client du principal de service. **Obligatoire si `credentials` est spécifié.** | | |
| `credentials.client_secret` | string | Secret client du principal de service. **Obligatoire si `credentials` est spécifié.** | | |
| `credentials.resource_url` | string | URL de ressource qui détermine les opérations à effectuer sur le compte Azure Data Lake Storage Gen2. | | `https://storage.azure.com/` |
| `credentials.authority_url` | string | URL de l’autorité utilisée pour authentifier l’utilisateur. | | `https://login.microsoftonline.com` |

## <a name="remarks"></a>Remarques

La commande `az ml datastore` peut être utilisée pour gérer les magasins de données d’Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [dépôt d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-identity-based-access"></a>YAML : accès basé sur l’identité

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/adls-gen2-credless.yml":::

## <a name="yaml-tenant-id-client-id-client-secret"></a>YAML : ID locataire, ID client, secret client

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/datastore/adls-gen2.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
