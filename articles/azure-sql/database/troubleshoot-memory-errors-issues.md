---
title: Résoudre les problèmes de mémoire
titleSuffix: Azure SQL Database
description: Fournit des étapes pour résoudre des problèmes de mémoire insuffisante dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 11/03/2021
ms.openlocfilehash: 07ff08303cc68934d5d7191952536b9101e12cf6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494707"
---
# <a name="troubleshoot-out-of-memory-errors-with-azure-sql-database"></a>Résoudre les erreurs de mémoire insuffisante avec Azure SQL Database  
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Des messages d’erreur peuvent s’afficher lorsque le moteur de base de données SQL n’a pas pu allouer suffisamment de mémoire pour exécuter la requête. Cela peut être dû à différentes raisons, notamment aux limites de l’objectif de service sélectionné, aux demandes totales de mémoire de charge de travail et aux demandes de mémoire par la requête. Pour plus d’informations sur la limite des ressources mémoire pour les bases de données Azure SQL, consultez [Gestion des ressources dans Azure SQL Database](resource-limits-logical-server.md#memory).

> [!NOTE]
> **Cet article est axé sur Azure SQL Database.** Pour plus d’informations sur la résolution des problèmes de mémoire insuffisante dans SQL Server, consultez [MSSQLSERVER_701](/sql/relational-databases/errors-events/mssqlserver-701-database-engine-error).

Essayez les voies d’investigation suivantes en réponse au :

- Code d’erreur 701 avec le message d’erreur « Mémoire système insuffisante dans le pool de ressources « %ls » pour exécuter cette requête ».
- Code d’erreur 802 avec le message d’erreur « Mémoire disponible insuffisante dans le pool de mémoires tampons ».

## <a name="investigate-memory-allocation"></a>Investiguer l’allocation de mémoire

Si les erreurs de mémoire insuffisante persistent dans Azure SQL Database, élevez au moins temporairement l’objectif de niveau de service de la base de données dans le portail Azure. Si les erreurs de mémoire insuffisante persistent, utilisez les requêtes suivantes pour rechercher des allocations de mémoire exceptionnellement élevées qui peuvent contribuer à une condition de mémoire insuffisante. Exécutez les exemples de requêtes suivants dans la base de données qui a rencontré l’erreur (pas dans la base de données `master` du serveur logique Azure SQL).  

### <a name="use-dmvs-to-view-memory-clerks"></a>Utiliser des vues de gestion dynamique pour afficher les régisseurs de mémoire

Commencez par une investigation générale, si l’erreur de mémoire insuffisante s’est produite récemment, en affichant l’allocation de mémoire aux régisseurs de mémoire. Les régisseurs de mémoire sont internes au moteur de base de données pour cette base de données Azure SQL. Les principaux régisseurs de mémoire en termes de pages allouées peuvent indiquer le type de requête ou de fonctionnalité de SQL Server qui consomme le plus de mémoire. 


```sql
SELECT [type], [name], pages_kb, virtual_memory_committed_kb
FROM sys.dm_os_memory_clerks
WHERE memory_node_id <> 64 -- ignore Dedicated Admin Connection (DAC) node
ORDER BY pages_kb DESC;
GO
SELECT [type], [name], pages_kb, virtual_memory_committed_kb
FROM sys.dm_os_memory_clerks
WHERE memory_node_id <> 64 -- ignore Dedicated Admin Connection (DAC) node
ORDER BY virtual_memory_committed_kb DESC;
```

 - Certains régisseurs de mémoire courants, tels que MEMORYCLERK_SQLQERESERVATIONS, sont mieux traités en identifiant les requêtes avec des allocations de mémoire importantes et en améliorant leurs performances avec une meilleure indexation et un paramétrage de l’index. 
 - Bien qu’OBJECTSTORE_LOCK_MANAGER ne soit pas lié aux allocations de mémoire, il est supposé être élevé par exemple lorsque des requêtes revendiquent de nombreux verrous, en raison de l’escalade de verrous désactivée ou de transactions très volumineuses. 
 - Certains régisseurs présenteront normalement une utilisation plus élevée : MEMORYCLERK_SQLBUFFERPOOL est presque toujours le principal régisseur, tandis que CACHESTORE_COLUMNSTOREOBJECTPOOL est élevé lorsque des index columnstore sont utilisés. Il est normal et attendu que ces régisseurs présentent l’utilisation la plus élevée. 

 Pour plus d’informations sur les types de régisseurs de mémoire, consultez [sys.dm_os_memory_clerks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql). 

### <a name="use-dmvs-to-investigate-active-queries"></a>Utiliser des vues de gestion dynamique pour investiguer les requêtes actives 

Dans la plupart des cas, la requête qui a échoué n’est pas la cause de cette erreur. 

L’exemple de requête suivant pour Azure SQL Database retourne des informations importantes sur les transactions qui sont en train de détenir ou d’attendre des allocations de mémoire. Ciblez les principales requêtes identifiées pour l’examen et le réglage des performances, et évaluez si elles s’exécutent comme prévu. Tenez compte du timing des requêtes de rapport ou des opérations de maintenance gourmandes en mémoire.

```sql
--Active requests with memory grants
SELECT
--Session data 
  s.[session_id], s.open_transaction_count
--Memory usage
, r.granted_query_memory, mg.grant_time, mg.requested_memory_kb, mg.granted_memory_kb, mg.required_memory_kb, mg.used_memory_kb, mg.max_used_memory_kb     
--Query 
, query_text = t.text, input_buffer = ib.event_info, query_plan_xml = qp.query_plan, request_row_count = r.row_count, session_row_count = s.row_count
--Session history and status
, s.last_request_start_time, s.last_request_end_time, s.reads, s.writes, s.logical_reads, session_status = s.[status], request_status = r.status
--Session connection information
, s.host_name, s.program_name, s.login_name, s.client_interface_name, s.is_user_process
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests AS r 
    ON r.[session_id] = s.[session_id]
LEFT OUTER JOIN sys.dm_exec_query_memory_grants AS mg 
    ON mg.[session_id] = s.[session_id]
OUTER APPLY sys.dm_exec_sql_text (r.[sql_handle]) AS t
OUTER APPLY sys.dm_exec_input_buffer(s.[session_id], NULL) AS ib 
OUTER APPLY sys.dm_exec_query_plan (r.[plan_handle]) AS qp 
WHERE mg.granted_memory_kb > 0
ORDER BY mg.granted_memory_kb desc, mg.requested_memory_kb desc;
```

Vous pouvez décider d’utiliser l’instruction KILL pour arrêter une requête en cours d’exécution qui détient ou attend une allocation de mémoire importante. Utilisez cette instruction avec précaution, en particulier lorsque des processus critiques sont en cours d’exécution. Pour plus d’informations, consultez [KILL &#40;Transact-SQL&#41;](/sql/t-sql/language-elements/kill-transact-sql). 


### <a name="use-query-store-to-investigate-past-query-memory-usage"></a>Utiliser le Magasin des requêtes pour investiguer l’utilisation passée de la mémoire par les requêtes

Alors que l’exemple de requête précédent fournit uniquement les résultats des requêtes actives, la requête suivante utilise le [Magasin des requêtes](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) pour retourner des informations sur l’exécution passée des requêtes. Cela peut être utile pour investiguer une erreur de mémoire insuffisante qui s’est produite dans le passé.

L’exemple de requête suivant pour Azure SQL Database retourne des informations importantes sur les exécutions de requêtes enregistrées par le Magasin des requêtes. Ciblez les principales requêtes identifiées pour l’examen et le réglage des performances, et évaluez si elles s’exécutent comme prévu. Notez le filtre de temps sur `qsp.last_execution_time` pour limiter les résultats à l’historique récent. Vous pouvez ajuster la clause TOP pour générer plus ou moins de résultats en fonction de votre environnement.

```sql
SELECT TOP 10 PERCENT --limit results
  a.plan_id, query_id, plan_group_id, query_sql_text
, query_plan = TRY_CAST(query_plan as XML)
, avg_query_max_used_memory
, min_query_max_used_memory
, max_query_max_used_memory
, last_query_max_used_memory 
, last_execution_time  
, query_count_executions
    FROM (
    SELECT 
      qsp.plan_id, qsp.query_id, qsp.plan_group_id, qsp.query_plan, qsqt.query_sql_text
    , last_execution_time = MAX(qsp.last_execution_time)
    , query_count_executions = SUM(qsrs.count_executions) 
    , avg_query_max_used_memory = AVG(qsrs.avg_query_max_used_memory)
    , min_query_max_used_memory = MIN(qsrs.min_query_max_used_memory)
    , max_query_max_used_memory = MAX(qsrs.max_query_max_used_memory)
    , last_query_max_used_memory = MAX(qsrs_latest.last_query_max_used_memory) --only from latest result
    FROM sys.query_store_plan AS qsp 
    INNER JOIN sys.query_store_query AS qsq
        ON qsp.query_id = qsq.query_id
    INNER JOIN sys.query_store_query_text AS qsqt
        ON qsq.query_text_id = qsqt.query_text_id 
    INNER JOIN sys.query_store_runtime_stats AS qsrs
        ON qsp.plan_id = qsrs.plan_id 
    INNER JOIN (SELECT plan_id
            , last_query_max_used_memory 
            , rownum = ROW_NUMBER() OVER (PARTITION BY plan_id ORDER BY last_execution_time DESC)
            FROM sys.query_store_runtime_stats qsrs) AS qsrs_latest
        ON qsrs_latest.plan_id = qsp.plan_id
        AND qsrs_latest.rownum = 1 --use latest last_query_max_used_memory per plan_id
    WHERE DATEADD(hour, -24, sysdatetime()) < qsp.last_execution_time --past 24 hours only
    AND qsrs_latest.last_query_max_used_memory > 0
    GROUP BY qsp.plan_id, qsp.query_id, qsp.plan_group_id, qsp.query_plan, qsqt.query_sql_text
    ) AS a
ORDER BY max_query_max_used_memory DESC, avg_query_max_used_memory DESC;
```

### <a name="extended-events"></a>Événements étendus
Outre les informations ci-dessus, il peut être utile de capturer une trace des activités sur le serveur pour investiguer minutieusement un problème de mémoire insuffisante dans Azure SQL Database. 

Il existe deux façons de capturer des traces dans SQL Server : les événements étendus (XEvents) et les traces du profileur. Toutefois, la technologie de trace [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) est déconseillée et n’est pas prise en charge pour Azure SQL Database. Les [événements étendus](/sql/relational-databases/extended-events/extended-events) constituent la nouvelle technologie de suivi qui permet une plus grande polyvalence et un impact moindre sur le système observé, et son interface est intégrée à SQL Server Management Studio (SSMS). 

Reportez-vous au document qui explique comment utiliser l’[Assistant Nouvelle session d’événements étendus](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) dans SSMS. Toutefois, pour les bases de données Azure SQL, SSMS fournit un sous-dossier Événements étendus sous chaque base de données dans Explorateur d’objets. Utilisez une session d’événements étendus pour capturer ces événements utiles et identifier les requêtes qui les génèrent : 

-   Catégorie Erreurs :
    - error_reported
    - exchange_spill
    - hash_spill_details
    
-   Catégorie Exécution :
    - excessive_non_grant_memory_used

-   Catégorie Mémoire :
    - query_memory_grant_blocking
    - query_memory_grant_usage

La capture des blocs d’allocation de mémoire, des dépassements d’allocation de mémoire ou des allocations de mémoire excessives peut être un indice potentiel indiquant qu’une requête occupe soudainement davantage de mémoire qu’auparavant, et une explication potentielle d’une erreur de mémoire insuffisante émergente dans une charge de travail existante.

### <a name="in-memory-oltp-out-of-memory"></a>Mémoire insuffisante de l’OLTP en mémoire 

Vous pouvez rencontrer `Error code 41805: There is insufficient memory in the resource pool '%ls' to run this operation` lors de l’utilisation de l’OLTP en mémoire. Réduisez la quantité de données dans les tables à mémoire optimisée et les paramètres table à mémoire optimisée, ou effectuez un scale-up de la base de données à un objectif de service supérieur pour disposer de plus de mémoire. Pour plus d’informations sur les problèmes de mémoire insuffisante avec l’OLTP en mémoire SQL Server, consultez [Résoudre les problèmes de mémoire insuffisante](/sql/relational-databases/in-memory-oltp/resolve-out-of-memory-issues).

### <a name="get-azure-sql-db-support"></a>Bénéficier d’une assistance pour Azure SQL DB 

Si les problèmes de mémoire insuffisante persistent dans Azure SQL Database, créez une demande de support Azure en sélectionnant **Obtenir de l’aide** sur le site du [support Azure](https://azure.microsoft.com/support/options).

## <a name="next-steps"></a>Étapes suivantes

- [Traitement de requêtes intelligent dans les bases de données SQL](/sql/relational-databases/performance/intelligent-query-processing)
- [Guide d’architecture de traitement des requêtes](/sql/relational-databases/query-processing-architecture-guide)    
- [Centre de performances pour le moteur de base de données SQL Server et Azure SQL Database](/sql/relational-databases/performance/performance-center-for-sql-server-database-engine-and-azure-sql-database)  
- [Résolution des problèmes de connectivité et autres erreurs avec Azure SQL Database et Azure SQL Managed Instance](troubleshoot-common-errors-issues.md)
- [Résoudre les erreurs de connexion temporaires dans SQL Database et SQL Managed Instance](troubleshoot-common-connectivity-issues.md)
- [Illustration du traitement de requêtes intelligent](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/intelligent-query-processing)   
- [Gestion des ressources dans Azure SQL Database](resource-limits-logical-server.md#memory).
