---
title: Journaux des requêtes lentes - Azure Database pour MySQL
description: Décrit les journaux des requêtes lentes disponibles dans Azure Database pour MySQL et les paramètres disponibles pour l’activation de différents niveaux de journalisation.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: d2ba1c751f2ed61ea583f1a1b6478bd0451bbea5
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641179"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Journaux des requêtes lentes dans Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
Dans Azure Database pour MySQL, le journal des requêtes lentes est disponible pour les utilisateurs. L’accès aux journaux des transactions n’est pas pris en charge. Le journal des requêtes lentes peut être utilisé pour identifier les goulots d’étranglement en matière de performances, afin de les faire disparaître.

Pour plus d’informations sur le journal des requêtes lentes MySQL, consultez la [section sur le journal des requêtes lentes](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) du manuel de référence MySQL.

Lorsque la fonctionnalité [Magasin des requêtes](concepts-query-store.md) est activée sur votre serveur, vous pouvez voir les requêtes telles que « `CALL mysql.az_procedure_collect_wait_stats (900, 30);` » journalisées dans les journaux de requêtes lentes. Ce comportement est attendu, car la fonctionnalité Magasin des requêtes collecte des statistiques sur vos requêtes. 

## <a name="configure-slow-query-logging"></a>Configurer la journalisation des requêtes lentes 
Par défaut, le journal des requêtes lentes est désactivé. Pour l’activer, affectez la valeur ON à `slow_query_log`. Pour ce faire, utilisez le portail Azure ou Azure CLI. 

Les autres paramètres que vous pouvez ajuster incluent :

- **long_query_time** : si une requête dure plus longtemps que long_query_time (en secondes), cette requête est enregistrée. La valeur par défaut est 10 secondes.
- **log_slow_admin_statements** : si ce paramètre est activé, inclut des instructions d’administration telles que ALTER_TABLE et ANALYZE_TABLE dans les instructions écrites dans le journal des requêtes lentes.
- **log_queries_not_using_indexes** : détermine si les requêtes qui n’utilisent pas les index sont enregistrées dans le journal des requêtes lentes.
- **log_throttle_queries_not_using_indexes** : Ce paramètre limite le nombre de requêtes hors index qui peuvent être écrites dans le journal des requêtes lentes. Ce paramètre prend effet lorsque log_queries_not_using_indexes est défini sur ON.
- **log_output** : si défini sur « File », permet au journal des requêtes lentes d’être écrit dans le stockage du serveur local et dans les journaux de diagnostic Azure Monitor. S’il est défini sur « None », le journal des requêtes lentes est uniquement écrit dans les journaux de diagnostics Azure Monitor. 

> [!IMPORTANT]
> Si vos tables ne sont pas indexées, la définition des paramètres `log_queries_not_using_indexes` et `log_throttle_queries_not_using_indexes` sur ON peut affecter les performances de MySQL, car toutes les requêtes exécutées sur ces tables non indexées sont écrites dans le journal des requêtes lentes.<br><br>
> Si vous envisagez de journaliser les requêtes lentes pendant une période prolongée, il est recommandé de définir `log_output` sur « None ». Si la valeur est « File », ces journaux sont écrits dans le stockage du serveur local, ce qui peut affecter les performances de MySQL. 

Consultez [la documentation MySQL consacrée au journal des requêtes lentes](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) pour obtenir une description complète des paramètres du journal des requêtes lentes.

## <a name="access-slow-query-logs"></a>Accéder aux journaux des requêtes lentes
Il existe deux options d’accès aux journaux de requêtes lentes dans Azure Database pour MySQL : le stockage sur le serveur local ou les journaux de diagnostic Azure Monitor. Ceci est défini à l’aide du paramètre `log_output`.

Pour le stockage sur le serveur local, vous pouvez lister et télécharger les journaux des requêtes lentes à l’aide du portail Azure ou de l’interface de ligne de commande Azure. Dans le portail Azure, accédez à votre serveur dans le portail Azure. Sous l’en-tête **Supervision**, sélectionnez la page **Journaux d’activité des serveurs**. Pour plus d’informations sur Azure CLI, consultez [Configurer et accéder aux journaux des requêtes lentes à l’aide d’Azure CLI](howto-configure-server-logs-in-cli.md). 

Les journaux de diagnostics Azure Monitor vous permettent d’acheminer les journaux de requêtes lentes vers des journaux Azure Monitor (Log Analytics), le stockage Azure ou Event Hubs. Pour plus d’informations, voir [plus bas](concepts-server-logs.md#diagnostic-logs).

## <a name="local-server-storage-log-retention"></a>Rétention du journal de stockage du serveur local
Lorsque vous vous connectez au stockage local du serveur, les journaux sont disponibles jusqu’à sept jours après leur création. Si la taille totale des journaux disponibles est supérieure à 7 Go, les fichiers les plus anciens sont supprimés jusqu’à ce que de l’espace soit disponible. La limite de stockage de 7 Go pour les journaux de serveurs est disponible gratuitement et ne peut pas être étendue. 

Une rotation des journaux d’activité s’effectue toutes les 24 heures ou une fois les 7 Go atteints, selon ce qui se produit en premier.

> [!Note]
> La rétention du journal ci-dessus ne s’applique pas aux journaux qui sont acheminés à l’aide des journaux de diagnostic Azure Monitor. Vous pouvez modifier la période de rétention pour les récepteurs de données émises (par exemple, Stockage Azure).

## <a name="diagnostic-logs"></a>Journaux de diagnostic
Azure Database pour MySQL est intégré aux journaux de diagnostic Azure Monitor. Une fois que vous avez activé les journaux des requêtes lentes sur votre serveur MySQL, vous pouvez choisir qu’ils soient transmis vers des journaux Azure Monitor, Event Hubs ou le Stockage Azure. Pour en savoir plus sur l’activation des journaux de diagnostic, consultez la section des procédures de la [documentation des journaux de diagnostic](../azure-monitor/essentials/platform-logs-overview.md).

Le tableau suivant décrit ce que contient chaque journal. En fonction de la méthode de sortie, les champs inclus et l’ordre dans lequel ils apparaissent peuvent varier.

| **Propriété** | **Description** |
|---|---|
| `TenantId` | Votre ID d’abonné |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Horodatage du moment où le journal a été enregistré en UTC |
| `Type` | Type de journal. Toujours `AzureDiagnostics` |
| `SubscriptionId` | GUID de l’abonnement auquel appartient le serveur |
| `ResourceGroup` | Nom du groupe de ressources auquel le serveur appartient |
| `ResourceProvider` | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de ressource |
| `Resource` | Nom du serveur |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Nom du serveur |
| `start_time_t` [UTC] | Heure de début de la requête |
| `query_time_s` | Durée totale d’exécution de la requête en secondes |
| `lock_time_s` | Durée totale en secondes pendant laquelle la requête a été verrouillée |
| `user_host_s` | Nom d’utilisateur |
| `rows_sent_s` | Nombre de lignes envoyées |
| `rows_examined_s` | Nombre de lignes examinées |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | ID de l’insertion |
| `sql_text_s` | Requête complète |
| `server_id_s` | ID du serveur |
| `thread_id_s` | ID du thread |
| `\_ResourceId` | URI de ressource |

> [!Note]
> Pour `sql_text`, le journal est tronqué s’il dépasse 2 048 caractères.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analyser les journaux dans Azure Monitor

Une fois vos journaux des requêtes lentes canalisés vers des journaux Azure Monitor via des journaux de diagnostic, vous pouvez effectuer une analyse plus poussée de vos requêtes lentes. Voici quelques exemples de requêtes pour vous aider à commencer. Veillez à mettre à jour les exemples ci-dessous avec le nom de votre serveur.

- Requêtes de plus de 10 secondes sur un serveur particulier

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Lister les 5 requêtes les plus longues sur un serveur particulier

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Résumer les requêtes lentes par temps de requête minimal, maximal, moyen et d’écart type sur un serveur particulier

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Représenter sous forme de graphique la distribution de requête lente sur un serveur particulier

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Afficher les requêtes de plus de 10 secondes sur tous les serveurs MySQL avec les journaux de diagnostic activés

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Étapes suivantes
- [Configurer les journaux des requêtes lentes du portail Azure](howto-configure-server-logs-in-portal.md)
- [Configurer les journaux des requêtes lentes d’Azure CLI](howto-configure-server-logs-in-cli.md)
