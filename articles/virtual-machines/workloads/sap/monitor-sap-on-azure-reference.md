---
title: Surveiller la référence de données SAP sur Azure
description: Matériel de référence important nécessaire pour surveiller SaP sur Azure.
author: mamccrea
ms.topic: reference
ms.author: mamccrea
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.custom: subject-monitoring
ms.date: 08/27/2021
ms.openlocfilehash: 36d90dc91a37d3881f7fb74637b74759904bf3c7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060638"
---
# <a name="monitor-sap-on-azure-preview-data-reference"></a>Surveiller la référence de données SAP sur Azure (version préliminaire)

Cet article fournit des informations de référence sur les données de journal collectées pour analyser les performances et la disponibilité d’Azure Monitor pour SAP Solutions. Pour plus d’informations sur la collecte et l’analyse des données de supervision concernant pour SaP sur Azure, consultez [Surveiller SaP sur Azure (version préliminaire)](monitor-sap-on-azure.md).

## <a name="metrics"></a>Mesures

Azure Monitor pour SAP Solutions ne prend pas en charge les métriques.

## <a name="azure-monitor-logs-tables"></a>Tables de journaux Azure Monitor

Cette section fait référence à toutes les tables Kusto de journaux Azure Monitor qui sont pertinentes pour Azure Monitor pour SAP Solutions et qui peuvent être interrogées par Log Analytics. Azure Monitor pour SAP Solutions utilise des journaux personnalisés. Les schémas de certaines tables sont définis par des fournisseurs tiers, tels que SAP. Voici les journaux personnalisés actuels pour Azure Monitor pour SAP Solutions avec des liens vers les sources pour plus d’informations.

### <a name="saphana_hostconfig_cl"></a>SapHana_HostConfig_CL

Pour plus d’informations, consultez [Vue système M_LANDSCAPE_HOST_CONFIGURATION](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/20b1d83875191014b028e076c874e9d7.html) dans le SQL SAP HANA et informations de référence sur les vues système.

### <a name="saphana_hostinformation_cl"></a>SapHana_HostInformation_CL

Pour plus d’informations, consultez [Vue système M_HOST_INFORMATION](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20b10028751910148c1c9de602d771de.html) dans le SQL SAP HANA et informations de référence sur les vues système.

### <a name="saphana_systemoverview_cl"></a>SapHana_SystemOverview_CL

Pour plus d’informations, consultez [Vue système M_SYSTEM_OVERVIEW](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20c61f0675191014a1d5f96d9668855b.html) dans le SQL SAP HANA et informations de référence sur les vues système.

### <a name="saphana_loadhistory_cl"></a>SapHana_LoadHistory_CL

Pour plus d’informations, consultez [Vue système M_LOAD_HISTORY_HOST](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/3fa52abf1d854edbb7342a69364bcb0e.html) dans le SQL SAP HANA et informations de référence sur les vues système.

### <a name="saphana_disks_cl"></a>SapHana_Disks_CL

Pour plus d’informations, consultez [Vue système M_DISKS](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20aef7a275191014b37acbc35b4f20a4.html) dans le SQL SAP HANA et informations de référence sur les vues système.

### <a name="saphana_systemavailability_cl"></a>SapHana_SystemAvailability_CL

Pour plus d’informations, consultez [Vue système M_SYSTEM_AVAILABILITY](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/1ef9723a03214bd889c4fb8947765aa4.html) dans le SQL SAP HANA et informations de référence sur les vues système.

### <a name="saphana_backupcatalog_cl"></a>SapHana_BackupCatalog_CL

Pour plus d'informations, consultez les pages suivantes :
- [Vue système M_BACKUP_CATALOG_FILES](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20a8437d7519101495a3fa7ad9961cf6.html?q=M_BACKUP_CATALOG)
- [Vue système M_BACKUP_CATALOG](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/1.0.12/en-US/20a8100e75191014870ecf908b5d2abf.html)

### <a name="saphana_systemreplication_cl"></a>SapHana_SystemReplication_CL

Pour plus d’informations, consultez [Vue système M_SERVICE_REPLICATION](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/2021_2_QRC/en-US/20c43fc975191014b0ece11b47a86c10.html) dans le SQL SAP HANA et informations de référence sur les vues système.
 
### <a name="prometheus_osexporter_cl"></a>Prometheus_OSExporter_CL

Pour plus d’informations, consultez [prometheus / node_exporter sur GitHub](https://github.com/prometheus/node_exporter).

### <a name="prometheus_haclusterexporter_cl"></a>Prometheus_HaClusterExporter_CL

Pour plus d’informations, consultez [ClusterLabs/ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md).

### <a name="mssql_dbconnections_cl"></a>MSSQL_DBConnections_CL

Pour plus d'informations, consultez les pages suivantes :
- [sys.dm_exec_sessions (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 
- [sys.databases (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)

### <a name="mssql_systemprops_cl"></a>MSSQL_SystemProps_CL

Pour plus d'informations, consultez les pages suivantes : 
- [sys.dm_os_windows_info (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-windows-info-transact-sql) 
- [sys.database_files (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)
- [sys.dm_exec_sql_text (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)
- [sys.dm_io_virtual_file_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)
- [sys.dm_db_partition_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)
- [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)
- [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [Vues de gestion dynamique SQL Server liées au système d'exploitation (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql)
- [sys.availability_groups (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql)
- [sys.dm_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)
- [sys.dm_xe_session_targets (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [backupset (Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql)
- [sys.sysprocesses (Transact-SQL)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql)

### <a name="mssql_fileoverview_cl"></a>MSSQL_FileOverview_CL

Pour plus d’informations, consultez [sys.database_files (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

### <a name="mssql_memoryoverview_cl"></a>MSSQL_MemoryOverview_CL

Pour plus d’informations, consultez [sys.dm_os_memory_clerks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql).

### <a name="mssql_top10statements_cl"></a>MSSQL_Top10Statements_CL

Pour plus d'informations, consultez les pages suivantes :
- [sys.dm_exec_sql_text (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)

### <a name="mssql_ioperformance_cl"></a>MSSQL_IOPerformance_CL

Pour plus d’informations, consultez [sys.dm_io_virtual_file_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql).

### <a name="mssql_tablesizes_cl"></a>MSSQL_TableSizes_CL

Pour plus d’informations, consultez [sys.dm_db_partition_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql).

### <a name="mssql_batchrequests_cl"></a>MSSQL_BatchRequests_CL

Pour plus d’informations, consultez [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql).

### <a name="mssql_waitpercs_cl"></a>MSSQL_WaitPercs_CL

Pour plus d’informations, consultez [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="mssql_pagelifeexpectancy2_cl"></a>MSSQL_PageLifeExpectancy2_CL

Pour plus d’informations, consultez [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql).

### <a name="mssql_error_cl"></a>MSSQL_Error_CL

Pour plus d’informations, consultez [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql).

### <a name="mssql_cpuusage_cl"></a>MSSQL_CPUUsage_CL

Pour plus d’informations, consultez [Vues de gestion dynamique SQL Server liées au système d'exploitation (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql).

### <a name="mssql_aooverview_cl"></a>MSSQL_AOOverview_CL

Pour plus d’informations, consultez [sys.availability_groups (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql).

### <a name="mssql_aowaiter_cl"></a>MSSQL_AOWaiter_CL

Pour plus d’informations, consultez [sys.dm_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql).

### <a name="mssql_aowaitstats_cl"></a>MSSQL_AOWaitstats_CL

Pour plus d’informations, consultez [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="mssql_aofailovers_cl"></a>MSSQL_AOFailovers_CL

Pour plus d'informations, consultez les pages suivantes :
- [sys.dm_xe_session_targets (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

### <a name="mssql_bckbackups2_cl"></a>MSSQL_BckBackups2_CL

Pour plus d’informations, consultez [backupset (Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql).

### <a name="mssql_blockingprocesses_cl"></a>MSSQL_BlockingProcesses_CL

Pour plus d’informations, consultez [sys.sysprocesses (Transact-SQL)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation d’Azure Monitor pour SAP Solutions, consultez [Surveiller SAP sur Azure](monitor-sap-on-azure.md).
- Pour plus d’informations sur Azure Monitor, consultez [Supervision de ressources Azure avec Azure Monitor](../../../azure-monitor/essentials/monitor-azure-resource.md).
