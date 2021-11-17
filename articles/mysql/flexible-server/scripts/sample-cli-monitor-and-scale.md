---
title: Script CLI - Monitorer et mettre à l’échelle une instance d’Azure Database pour MySQL - Serveur flexible
description: Cet exemple de script Azure CLI montre comment superviser et mettre à l’échelle une instance d’Azure Database pour MySQL - Serveur flexible (scale-up ou scale-down) afin de s’adapter aux besoins en performance.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 82661e10c2434541be89df6dc0e0871618a1a6b5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844183"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Monitorer et mettre à l’échelle une instance d’Azure Database pour MySQL - Serveur flexible avec Azure CLI

Cet exemple de script CLI met à l’échelle le calcul, le stockage et les IOPS d’une instance Azure Database pour MySQL - Serveur flexible après l’interrogation des métriques correspondantes. Le calcul et les IOPS peuvent faire l’objet d’un scale-up ou d’un scale-down, tandis que le stockage peut uniquement faire l’objet d’un scale-up. 

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="sample-script"></a>Exemple de script

Remplacez les lignes mises en surbrillance dans le script par vos valeurs de variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/monitor-and-scale/monitor-and-scale.sh?highlight=8,11-12 "Monitor your Flexible Server and scale Compute, Storage and IOPS.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois que l’exemple de script a été exécuté, vous pouvez utiliser l’extrait de code suivant pour nettoyer les ressources.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/monitor-and-scale/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crée un groupe de ressources dans lequel toutes les ressources sont stockées|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crée un serveur flexible qui héberge les bases de données.|
|[az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list)|Liste la valeur de la métrique Azure Monitor pour les ressources.|
|[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)|Met à jour les propriétés du serveur flexible.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Supprime un serveur flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées.|

## <a name="next-steps"></a>Étapes suivantes

- Pour essayer d’autres scripts : [Exemples Azure CLI pour Azure Database pour MySQL - Serveur flexible](../sample-scripts-azure-cli.md)
- Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).