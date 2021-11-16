---
title: Script CLI - Créer et gérer des réplicas en lecture dans une instance d’Azure Database pour MySQL - Serveur flexible
description: Cet exemple de script Azure CLI montre comment créer et gérer des réplicas en lecture dans Azure Database pour MySQL – Serveur flexible.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 1155231ca8909352aee2354023a19ea66c7ffc4d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844092"
---
# <a name="create-and-manage-read-replicas-in-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Créer et gérer des réplicas en lecture dans une instance d’Azure Database pour MySQL - Serveur flexible avec Azure CLI

Cet exemple de script CLI montre comment créer et gérer des [réplicas en lecture](../concepts-read-replicas.md) dans Azure Database pour MySQL – Serveur flexible.

>[!IMPORTANT]
>Lorsque vous créez un réplica pour un serveur source qui n’en a pas, ce dernier commence par redémarrer afin de se préparer à la réplication. Tenez-en compte et effectuez ces opérations en période creuse.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="sample-script"></a>Exemple de script

Remplacez les lignes mises en surbrillance dans le script par vos valeurs de variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/read-replicas/create-manage-read-replicas.sh?highlight=7,10-12 "Create and manage Flexible Server Read Replicas.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois que l’exemple de script a été exécuté, vous pouvez utiliser l’extrait de code suivant pour nettoyer les ressources.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/read-replicas/clean-up-resources.sh?highlight=4-5 "Clean up resources.")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crée un groupe de ressources dans lequel toutes les ressources sont stockées|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crée un serveur flexible qui héberge les bases de données.|
|[az mysql flexible-server replica create](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_create)|Crée un réplica en lecture pour un serveur.|
|[az mysql flexible-server replica list](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_list)|Liste tous les réplicas en lecture d’un serveur donné.|
|[az mysql flexible-server replica stop-replication](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_stop_replication)|Arrête la réplication vers un réplica en lecture et en fait un serveur en lecture/écriture.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Supprime un serveur flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées.|

## <a name="next-steps"></a>Étapes suivantes

- Pour essayer d’autres scripts : [Exemples Azure CLI pour Azure Database pour MySQL - Serveur flexible](../sample-scripts-azure-cli.md)
- Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).