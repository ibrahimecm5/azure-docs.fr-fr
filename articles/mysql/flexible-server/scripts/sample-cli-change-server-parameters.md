---
title: Script CLI - Lister et modifier les paramètres de serveur d’une instance d’Azure Database pour MySQL - Serveur flexible
description: Cet exemple de script Azure CLI montre comment lister et modifier les paramètres de serveur d’une instance d’Azure Database pour MySQL - Serveur flexible.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 4316669346f8bedec7a674da3e7114b95dd8c4ca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844256"
---
# <a name="list-and-change-server-parameters-of-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Lister et modifier les paramètres de serveur d’une instance d’Azure Database pour MySQL - Serveur flexible avec Azure CLI

Cet exemple de script CLI liste tous les [paramètres de serveur](../concepts-server-parameters.md) disponibles ainsi que leurs valeurs autorisées pour Azure Database pour MySQL - Serveur flexible. En outre, il définit le paramètre *max_connections* et le paramètre global *time_zone* sur des valeurs autres que celles par défaut.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="sample-script"></a>Exemple de script

Remplacez les lignes mises en surbrillance dans le script par vos valeurs de variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/change-server-parameters.sh?highlight=7,10-11 "Change server parameters for Azure Database for MySQL - Flexible Server.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois que l’exemple de script a été exécuté, vous pouvez utiliser l’extrait de code suivant pour nettoyer les ressources.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crée un groupe de ressources dans lequel toutes les ressources sont stockées|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crée un serveur flexible qui héberge les bases de données.|
|[az mysql flexible-server parameter list](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_list)|Liste les valeurs de paramètre d’un serveur flexible.|
|[az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_set)|Met à jour les paramètres d’un serveur flexible.|
|[az mysql flexible-server parameter show](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_show)|Récupère une valeur de paramètre spécifique pour un serveur flexible.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Supprime un serveur flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées.|

## <a name="next-steps"></a>Étapes suivantes

- Pour essayer d’autres scripts : [Exemples Azure CLI pour Azure Database pour MySQL - Serveur flexible](../sample-scripts-azure-cli.md)
- Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).