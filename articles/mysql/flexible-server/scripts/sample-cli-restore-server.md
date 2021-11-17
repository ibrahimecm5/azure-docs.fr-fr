---
title: Script CLI - Restaurer une instance d’Azure Database pour MySQL - Serveur flexible
description: Cet exemple de script Azure CLI montre comment restaurer une instance d’Azure Database pour MySQL - Serveur flexible vers un point antérieur dans le temps.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: f109da4924f04cd97fbe9cd816c2da45ce5c0e2d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844147"
---
# <a name="restore-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Restaurer une instance d’Azure Database pour MySQL - Serveur flexible avec Azure CLI

Azure Database pour MySQL - Serveur flexible crée automatiquement des sauvegardes de serveur et les stocke de manière sécurisée dans un stockage localement redondant au sein de la région.

Cet exemple de script CLI effectue une [restauration à un instant dans le passé](../concepts-backup-restore.md) et crée un nouveau serveur à partir des sauvegardes de votre serveur flexible. 

Le nouveau serveur flexible est créé avec la configuration du serveur d’origine, et hérite également des étiquettes et des paramètres du serveur source comme ceux relatifs au réseau virtuel et au pare-feu. Le niveau de calcul et de stockage du serveur restauré, la configuration et les paramètres de sécurité peuvent être modifiés une fois la restauration terminée.

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="sample-script"></a>Exemple de script

Remplacez les lignes mises en surbrillance dans le script par vos valeurs de variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/restore-server.sh?highlight=7,10-12 "Perform point-in-time-restore of a source server to a new server.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois que l’exemple de script a été exécuté, vous pouvez utiliser l’extrait de code suivant pour nettoyer les ressources.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/clean-up-resources.sh?highlight=4-5 "Clean up resources.")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crée un groupe de ressources dans lequel toutes les ressources sont stockées|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crée un serveur flexible qui héberge les bases de données.|
|[az mysql flexible-server restore](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restore)|Restaure un serveur flexible à partir d’une sauvegarde.|
|[az mysql flexible-server show](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_show)|Récupère les détails d’un serveur flexible.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Supprime un serveur flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées.|

## <a name="next-steps"></a>Étapes suivantes

- Pour essayer d’autres scripts : [Exemples Azure CLI pour Azure Database pour MySQL - Serveur flexible](../sample-scripts-azure-cli.md)
- Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).