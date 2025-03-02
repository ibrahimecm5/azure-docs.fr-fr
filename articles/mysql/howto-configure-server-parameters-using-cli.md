---
title: Configurer les paramètres de serveur - Azure CLI - Azure Database pour MySQL
description: Cet article décrit comment configurer les paramètres de service dans Azure Database pour MySQL à l’aide de l’utilitaire en ligne de commande Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 08a8a26cd0420f5c856eebf2063b0ae4f14beb7d
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641149"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Configurer les paramètres de serveur dans Azure Database pour MySQL avec l’interface de ligne de commande Azure

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
Vous pouvez répertorier, afficher et mettre à jour les paramètres de configuration d’un serveur Azure Database pour MySQL à l’aide d’Azure CLI, l’utilitaire en ligne de commande Azure. Un sous-ensemble de configurations de moteur est exposé au niveau du serveur et peut être modifié. 

>[!Note]
> Les paramètres de serveur peuvent être mis à jour globalement au niveau du serveur à partir d’[Azure CLI](./howto-configure-server-parameters-using-cli.md), de [PowerShell](./howto-configure-server-parameters-using-powershell.md) ou du [portail Azure](./howto-server-parameters.md).

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Un serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- L’utilitaire en ligne de commande [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Répertorier les paramètres de configuration de serveur pour Azure Database pour MySQL
Pour répertorier tous les paramètres modifiables sur un serveur, ainsi que leurs valeurs, exécutez la commande [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list).

Vous pouvez répertorier les paramètres de configuration du serveur **mydemoserver.mysql.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Pour obtenir la définition de chacun des paramètres répertoriés, consultez la section de référence MySQL dans [Server System Variables (Variables système du serveur)](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Affichage des détails des paramètres de configuration du serveur
Pour afficher les détails d’un paramètre de configuration particulier pour un serveur, exécutez la commande [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show).

Cet exemple affiche les détails du paramètre de configuration de serveur **slow\_query\_log** pour le serveur **mydemoserver.mysql.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modifier une valeur de paramètre de configuration de serveur
Vous pouvez également modifier la valeur d’un paramètre de configuration de serveur, ce qui a pour effet de mettre à jour la valeur de configuration sous-jacente du moteur du serveur MySQL. Pour mettre à jour la configuration, exécutez la commande [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set). 

Pour mettre à jour le paramètre de configuration de serveur **slow\_query\_log** pour le serveur **mydemoserver.mysql.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Si vous souhaitez réinitialiser la valeur d’un paramètre de configuration, omettez le paramètre `--value` facultatif. Le service applique alors la valeur par défaut. Pour l’exemple ci-dessus, on aurait ce qui suit :
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ce code réinitialise la configuration **slow\_query\_log** à la valeur par défaut **OFF**. 

## <a name="setting-parameters-not-listed"></a>Configurer les paramètres non listés
Si le paramètre de serveur que vous souhaitez mettre à jour n’est pas listé dans le portail Azure, vous pouvez éventuellement définir le paramètre au niveau de la connexion avec `init_connect`. Cela définit les paramètres de serveur pour chaque client qui se connecte. 

Mettez à jour le paramètre **init\_connect** de configuration du serveur du serveur **mydemoserver.mysql.database.azure.com** sous le groupe de ressources **myresourcegroup** pour définir les valeurs comme le jeu de caractères.
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Utilisation avec le paramètre de fuseau horaire

### <a name="populating-the-time-zone-tables"></a>Remplissage des tables de fuseaux horaires

Les tables de fuseaux horaires sur votre serveur peuvent être remplies en appelant la procédure stockée `mysql.az_load_timezone` à partir d’un outil tel que la ligne de commande MySQL ou MySQL Workbench.

> [!NOTE]
> Si vous exécutez la commande `mysql.az_load_timezone` à partir de MySQL Workbench, vous devrez peut-être d’abord désactiver le mode de mise à jour sécurisée à l’aide de `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Vous devez redémarrer le serveur pour vous assurer que les tables des fuseaux horaires sont correctement remplies. Pour redémarrer le serveur, utilisez le [Portail Azure](howto-restart-server-portal.md) ou [CLI](howto-restart-server-cli.md).

Pour afficher les valeurs de fuseau horaire disponibles, exécutez la commande suivante :

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Définition du fuseau horaire global

Le fuseau horaire global peut être défini à l’aide de la commande [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set).

La commande suivante met à jour le paramètre de configuration de serveur **time\_zone** du serveur **mydemoserver.mysql.database.azure.com** dans le groupe de ressources **myresourcegroup** sur **US/Pacific**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Définition du fuseau horaire de la session

Le fuseau horaire de la session peut être défini en exécutant la commande `SET time_zone` à partir d’un outil tel que la ligne de commande MySQL ou MySQL Workbench. L’exemple ci-dessous définit le fuseau horaire **US/Pacific**.  

```sql
SET time_zone = 'US/Pacific';
```

Reportez-vous à la documentation de MySQL sur les [fonction de date et heure](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Étapes suivantes

- Guide pratique pour configurer des [paramètres de serveur dans le portail Azure](howto-server-parameters.md)
