---
title: Script CLI - Créer une instance d’Azure Database pour MySQL - Serveur flexible et activer la connectivité d’accès public
description: Cet exemple de script Azure CLI montre comment créer une instance d’Azure Database pour MySQL - Serveur flexible, comment configurer une règle de pare-feu au niveau du serveur (méthode de connectivité d’accès public) et comment se connecter au serveur.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 59df8d613e8753895fba77d3bfabefe476f49cf1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844190"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-and-enable-public-access-connectivity-using-azure-cli"></a>Créer une instance d’Azure Database pour MySQL - Serveur flexible et activer la connectivité d’accès public avec Azure CLI

Cet exemple de script CLI crée une instance d’Azure Database pour MySQL - Serveur flexible, configure une règle de pare-feu au niveau du serveur ([méthode de connectivité d’accès public](../concepts-networking-public.md)) et se connecte au serveur après sa création. 

Une fois que le script s’exécute, le serveur flexible MySQL devient accessible par tous les services Azure et par l’adresse IP configurée, et vous êtes connecté au serveur en mode interactif.

> [!NOTE] 
> Une fois le serveur créé, la méthode de connectivité ne peut pas être modifiée. Par exemple, si vous créez un serveur avec *Accès public (adresses IP autorisées)* , vous ne pourrez pas passer à *Accès privé (intégration au réseau virtuel)* à l’issue de cette création. Pour en savoir plus sur les méthodes de connectivité, consultez [Concepts relatifs au réseau](../concepts-networking.md).


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="sample-script"></a>Exemple de script

Remplacez les lignes mises en surbrillance dans le script par vos valeurs de variables.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-public-access/create-connect-burstable-server-public-access.sh?highlight=8,11-12 "Create Flexible Server and enable public access.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois que l’exemple de script a été exécuté, vous pouvez utiliser l’extrait de code suivant pour nettoyer les ressources.

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-public-access/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|Crée un groupe de ressources dans lequel toutes les ressources sont stockées|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|Crée un serveur flexible qui héberge les bases de données.|
|[az mysql flexible-server firewall-rule create](/cli/azure/mysql/flexible-server/firewall-rule#az_mysql_flexible_server_firewall_rule_create)|Crée une règle de pare-feu pour autoriser l’accès au serveur flexible et aux bases de données à partir de la plage d’adresses IP entrée.|
|[az mysql flexible-server connect](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_connect)|Se connecte à un serveur flexible pour effectuer des opérations de serveur ou de base de données.|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|Supprime un serveur flexible.|
|[az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées.|

## <a name="next-steps"></a>Étapes suivantes

- Pour essayer d’autres scripts : [Exemples Azure CLI pour Azure Database pour MySQL - Serveur flexible](../sample-scripts-azure-cli.md)
- Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).