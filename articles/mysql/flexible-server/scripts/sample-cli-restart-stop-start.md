---
title: Script CLI - Redémarrer/Arrêter/Démarrer une instance d’Azure Database pour MySQL - Serveur flexible
description: Cet exemple de script Azure CLI montre comment redémarrer/arrêter/démarrer une instance d’Azure Database pour MySQL - Serveur flexible.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 12a375eb7195af9b95db85723efe7a52b53c5356
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844095"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Redémarrer/Arrêter/Démarrer un serveur flexible Azure Database pour MySQL avec Azure CLI

Cet exemple de script CLI effectue des opérations de redémarrage, de démarrage et d’arrêt sur une instance d’Azure Database pour MySQL - Serveur flexible. 

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]


> [!IMPORTANT]
> Lorsque vous **arrêtez** le serveur, il reste dans cet état durant les 7 jours suivants. Si vous ne le **démarrez** pas manuellement pendant cette période, le serveur sera automatiquement démarré à la fin des 7 jours. Vous pouvez choisir de l’**arrêter** de nouveau si vous n’utilisez pas le serveur.

Pendant l’arrêt du serveur, aucune opération de gestion ne peut être effectuée sur le serveur. Afin de modifier les paramètres de configuration sur le serveur, vous devez démarrer le serveur. 

Avant d’effectuer des opérations d’arrêt et de démarrage, consultez également les [limitations relatives aux arrêts et aux démarrages](../concepts-limitations.md#stopstart-operation).


[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="sample-script"></a>Exemple de script

Remplacez les lignes mises en surbrillance dans le script par vos valeurs de variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/restart-start-stop.sh?highlight=7,10-11 "Create a server, perform restart / start / stop operations.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois que l’exemple de script a été exécuté, vous pouvez utiliser l’extrait de code suivant pour nettoyer les ressources.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crée un groupe de ressources dans lequel toutes les ressources sont stockées|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crée un serveur flexible qui héberge les bases de données.|
|[az mysql flexible-server stop](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_stop)|Arrête un serveur flexible.|
|[az mysql flexible-server start](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_start)|Démarre un serveur flexible.|
|[az mysql flexible-server restart](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restart)|Redémarre un serveur flexible.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Supprime un serveur flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées.|

## <a name="next-steps"></a>Étapes suivantes

- Pour essayer d’autres scripts : [Exemples Azure CLI pour Azure Database pour MySQL - Serveur flexible](../sample-scripts-azure-cli.md)
- Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).