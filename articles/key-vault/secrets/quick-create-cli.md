---
title: 'Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault'
description: Démarrage rapide montrant comment définir et récupérer un secret depuis Azure Key Vault à l’aide d’Azure CLI
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: cae19bc96cc689a3cb10f1c9820f95e13c9b4e4a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593709"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide d’Azure CLI

Dans ce guide de démarrage rapide, vous créez un coffre de clés dans Azure Key Vault avec Azure CLI. Azure Key Vault est un service cloud qui fonctionne comme un magasin des secrets sécurisé. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Pour plus d’informations sur Key Vault, consultez la [présentation](../general/overview.md). Azure CLI vous permet de créer et gérer des ressources Azure à l’aide de commandes ou de scripts. Ensuite, vous y stockerez un secret.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Ce guide de démarrage rapide nécessite la version 2.0.4 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Ajouter un secret au coffre de clés

Pour ajouter un secret au coffre, vous devez effectuer deux autres opérations. Ce mot de passe peut être utilisé par une application. Le mot de passe sera appelé **ExamplePassword** et stockera la valeur **hVFkk965BuUv**.

Utilisez la commande Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) suivante pour créer une clé secrète dans Key Vault appelée **ExamplePassword** et qui stocke la valeur **hVFkk965BuUv** :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

## <a name="retrieve-a-secret-from-key-vault"></a>Récupérer un secret à partir de Key Vault

Vous pouvez maintenant référencer ce mot de passe que vous avez ajouté à Azure Key Vault à l’aide de son URI. Utilisez **`https://<your-unique-keyvault-name>.vault.azure.net/secrets/ExamplePassword`** pour obtenir la version actuelle.

Pour afficher la valeur contenue dans le secret sous forme de texte brut, utilisez la commande Azure CLI [az keyvault secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show) :

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Vous venez de créer un coffre de clés, d’y stocker un secret et de récupérer ce dernier.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés Key Vault et vous y avez stocké un secret. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Découvrez la procédure de [stockage des secrets multilignes dans Key Vault](multiline-secrets.md)
- Consulter la référence des [commandes az keyvault Azure CLI](/cli/azure/keyvault)
- Passer en revue la [Vue d’ensemble de la sécurité de Key Vault](../general/security-features.md)
