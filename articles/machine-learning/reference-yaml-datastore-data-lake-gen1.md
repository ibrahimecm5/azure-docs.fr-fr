---
title: Schéma YAML de magasin de données CLI (v2) Azure Data Lake Gen1
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma  YAML de magasin de données CLI (v2) Azure Data Lake Gen1.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: ynpandey
ms.author: yogipandey
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: c804d06f7a99590fcc7fd62e8b662e8eaadac2a5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062015"
---
# <a name="cli-v2-azure-data-lake-gen1-yaml-schema"></a>Schéma YAML CLI (v2) Azure Data Lake Gen1

Le schéma JSON source se trouve à l’adresse https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Syntaxe YAML

| Clé | Type | Description | Valeurs autorisées | Valeur par défaut |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Schéma YAML. Si vous utilisez l’extension VS Code d’Azure Machine Learning pour créer le fichier YAML, l’inclusion de `$schema` en haut de votre fichier vous permet d’appeler des exécutions de schéma et de ressource. | | |
| `type` | string | **Obligatoire.** Type de magasin de données. | `azure_data_lake_gen1` | |
| `name` | string | **Obligatoire.** Nom du magasin de données. | | |
| `description` | string | Description du magasin de données. | | |
| `tags` | object | Dictionnaire d’étiquettes pour le magasin de données. | | |
| `store_name` | string | **Obligatoire.** Nom du compte Azure Data Lake Storage Gen1. | | |
| `credentials` | object | Informations d’identification du principal de service pour la connexion au compte de stockage Azure. Les secrets des informations d’identification sont stockés dans le coffre de clés de l’espace de travail. | | |
| `credentials.tenant_id` | string | ID locataire du principal de service. **Obligatoire si `credentials` est spécifié.** | | |
| `credentials.client_id` | string | ID client du principal de service. **Obligatoire si `credentials` est spécifié.** | | |
| `credentials.client_secret` | string | Secret client du principal de service. **Obligatoire si `credentials` est spécifié.** | | |
| `credentials.resource_url` | string | URL de ressource qui détermine les opérations à effectuer sur le compte Azure Data Lake Storage Gen1. | | `https://datalake.azure.net/` |
| `credentials.authority_url` | string | URL de l’autorité utilisée pour authentifier l’utilisateur. | | `https://login.microsoftonline.com` |

## <a name="remarks"></a>Remarques

La commande `az ml datastore` peut être utilisée pour gérer les magasins de données d’Azure Machine Learning.

## <a name="examples"></a>Exemples

Des exemples sont disponibles dans le [référentiel d’exemples GitHub](https://github.com/Azure/azureml-examples/tree/main/cli/resources/datastore). Vous en trouverez plusieurs ci-dessous.

## <a name="yaml-identity-based-access"></a>YAML : accès basé sur l’identité

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/adls-gen1-credless.yml":::

## <a name="yaml-tenant-id-client-id-client-secret"></a>YAML : ID locataire, ID client, secret client

:::code language="yaml" source="~/azureml-examples-main/cli/resources/datastore/adls-gen1.yml":::

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
