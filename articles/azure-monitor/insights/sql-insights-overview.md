---
title: Surveiller vos déploiements SQL avec SQL Insights (préversion)
description: Vue d’ensemble de SQL Insights dans Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2021
ms.openlocfilehash: 2b66fe7d268fbb2ad044d0ecc4a5bf0fff5cd17e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297788"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Surveiller vos déploiements SQL avec SQL Insights (préversion)
SQL Insights est une solution complète de surveillance de tout produit de la [famille SQL Azure](../../azure-sql/index.yml). SQL Insights utilise des [vues de gestion dynamique](../../azure-sql/database/monitoring-with-dmvs.md) pour exposer les données dont vous avez besoin pour surveiller l’intégrité, diagnostiquer les problèmes et optimiser les performances.  

SQL Insights effectue toutes les analyses à distance. Les agents de surveillance sur les machines virtuelles dédiées se connectent à vos ressources SQL et recueillent à distance des données. Les données collectées sont stockées dans des [journaux Azure Monitor](../logs/data-platform-logs.md) pour faciliter l’agrégation, le filtrage et l’analyse de tendances. Vous pouvez afficher les données collectées à partir du [modèle de classeur](../visualize/workbooks-overview.md) SQL Insights, ou vous pouvez accéder directement aux données à l’aide de [requêtes de journal](../logs/get-started-queries.md).

## <a name="pricing"></a>Tarifs
Il n’existe aucun coût direct pour SQL Insights. Tous les coûts sont engendrés par les machines virtuelles qui recueillent les données, les espaces de travail Log Analytics qui stockent les données et toutes les règles d’alerte configurées sur les données. 

### <a name="virtual-machines"></a>Machines virtuelles

Pour les machines virtuelles, vous êtes facturé en fonction de la tarification publiée sur la [page de tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Le nombre de machines virtuelles requises varie en fonction du nombre de chaînes de connexion que vous souhaitez analyser. Nous vous recommandons d’allouer une machine virtuelle de taille Standard_B2s pour 100 chaînes de connexion. Pour plus d’informations, consultez [Configuration requise pour les machines virtuelles Azure](sql-insights-enable.md#azure-virtual-machine-requirements).

### <a name="log-analytics-workspaces"></a>Espaces de travail Log Analytics

Pour les espaces de travail Log Analytics, vous êtes facturé en fonction de la tarification publiée sur la [page de tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Les espaces de travail Log Analytics que SQL Insights utilise entraînent des coûts d’ingestion de données, de conservation de données et d’exportation de données (éventuellement). 

Les frais exacts varient en fonction de la quantité de données ingérées, conservées et exportées. La quantité de ces données varie en fonction de l’activité de votre base de données et des paramètres de collecte définis dans vos [profils d’analyse](sql-insights-enable.md#create-sql-monitoring-profile).

### <a name="alert-rules"></a>Règles d'alerte

Pour les règles d'alerte dans Azure Monitor, vous êtes facturé en fonction de la tarification publiée sur la [page de tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Si vous choisissez de [créer des alertes avec SQL Insights](sql-insights-alerts.md), toutes les règles d’alerte créées et toutes les notifications envoyées vous sont facturées.

## <a name="supported-versions"></a>Versions prises en charge 
SQL Insights prend en charge les versions suivantes de SQL Server :
- SQL Server 2012 et versions plus récentes

SQL Insights prend en charge SQL Server s’exécutant dans les environnements suivants :
- Azure SQL Database
- Azure SQL Managed Instance
- SQL Server sur des machines virtuelles Azure (SQL Server s’exécutant sur des machines virtuelles inscrites auprès du fournisseur de [machines virtuelles SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md))
- Machines virtuelles Azure (SQL Server s’exécutant sur des machines virtuelles non inscrites auprès du fournisseur de [machines virtuelles SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md))

SQL Insights ne prend pas en charge ou prend en charge de manière limitée les éléments suivants :
- **Instances non Azure** : SQL Server s’exécutant sur des machines virtuelles en dehors d’Azure n’est pas pris en charge.
- **Pools élastiques Azure SQL Database** : les métriques ne peuvent pas être collectées pour des pools élastiques ou des bases de données à l’intérieur de pools élastiques.
- **Niveaux de service faible Azure SQL Database** : les métriques ne peuvent pas être collectées pour les bases de données sur les [niveaux de service](../../azure-sql/database/resource-limits-dtu-single-databases.md) de base, S0, S1 et S2.
- **Niveau serverless Azure SQL Database** : les métriques peuvent être collectées pour les bases de données via le niveau de calcul serverless. Toutefois, le processus de collecte des métriques réinitialise le minuteur de délai de pause automatique, ce qui empêche la base de données d’entrer dans un état de pause automatique.
- **Réplicas secondaires** : les métriques ne peuvent être collectées que pour un réplica secondaire unique par base de données. Si une base de données a plus d’un réplica secondaire, un seul peut être surveillé.
- **Authentification avec Azure Active Directory** : La seule méthode d'[authentification](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) prise en charge pour la surveillance est l’authentification SQL. Pour SQL Server sur des machines virtuelles Azure, l’authentification via Active Directory sur un contrôleur de domaine personnalisé n’est pas prise en charge.  

## <a name="regional-availability"></a>Disponibilité régionale

SQL Insights est disponible dans toutes les régions Azure où Azure Monitor est [disponible](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), à l’exception des clouds Azure Government et nationaux.

## <a name="opening-sql-insights"></a>Ouverture de SQL Insights

Pour ouvrir SQL Insights :

1. Dans le portail Azure, accédez au menu **Azure Monitor**.
1. Dans la section **Insights**, sélectionnez **SQL (préversion)** . 
1. Sélectionnez une vignette pour charger l’expérience de la ressource SQL que vous surveillez.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Capture d’écran montrant SQL Insights dans le portail Azure.":::

Pour plus d’informations, consultez [Activer SQL insights](sql-insights-enable.md) et [Résoudre des problèmes liés à SQL Insights](sql-insights-troubleshoot.md).

## <a name="collected-data"></a>Données collectées
SQL Insights effectue toutes les analyses à distance. Aucun agent n’est installé sur les machines virtuelles exécutant SQL Server. 

SQL Insights utilise des machines virtuelles de surveillance dédiées pour collecter des données à distance à partir de vos ressources SQL. Chaque machine virtuelle de surveillance dispose de l’[agent Azure Monitor](../agents/azure-monitor-agent-overview.md) et de l’extension WLI (Workload Insights). 

L’extension WLI comprend l’[agent Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) open source. SQL Insights utilise des [règles de collecte de données](../agents/data-collection-rule-overview.md) pour spécifier les paramètres de collecte de données pour le [plug-in SQL Server](https://www.influxdata.com/integration/microsoft-sql-server/)de Telegraf.

Des jeux de données différents sont disponibles pour Azure SQL Database, Azure SQL Managed Instance et SQL Server. Les tableaux suivants décrivent les données disponibles. Vous pouvez personnaliser les jeux de données à collecter et la fréquence de la collecte lorsque vous [créez un profil de surveillance](sql-insights-enable.md#create-sql-monitoring-profile).

Les tableaux contiennent les colonnes suivantes :
- **Nom convivial** : nom de la requête, comme indiqué sur la Portail Azure lors de la création d’un profil de surveillance.
- **Nom de configuration** : nom de la requête, comme indiqué sur la Portail Azure lors de la modification d’un profil de surveillance.
- **Espace de noms** : nom de la requête tel qu’il se trouve dans un espace de travail Log Analytics. Cet identificateur apparaît dans la table **InsighstMetrics** de la propriété `Namespace` dans la colonne `Tags`.
- **DMV** : vues de gestion dynamiques utilisées pour générer le jeu de données.
- **Activé par défaut** : indique si les données sont collectées par défaut.
- **Fréquence de collecte par défaut** : fréquence de collecte des données par défaut.

### <a name="data-for-azure-sql-database"></a>Données pour Azure SQL Database 
| Nom convivial | Nom de la configuration | Espace de noms | DMV | Activée par défaut | Fréquence de collecte par défaut |
|:---|:---|:---|:---|:---|:---|
| Statistiques d’attente de la base de données | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | Non | Non applicable |
| Statistiques d’attente DBO | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Oui | 60 secondes |
| Régisseurs de mémoire | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Oui | 60 secondes |
| E/S de la base de données | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys.database_files | Oui | 60 secondes |
| Propriétés du serveur | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sys.[databases]<br>sys.[database_service_objectives] | Oui | 60 secondes |
| Compteurs de performance | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Oui | 60 secondes |
| Statistiques des ressources | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Oui | 60 secondes |
| Gouvernance des ressources | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Oui | 60 secondes |
| Demandes | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Non | Non applicable |
| Planificateurs| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Non | Non applicable  |

### <a name="data-for-azure-sql-managed-instance"></a>Données pour Azure SQL Managed Instance 

| Nom convivial | Nom de la configuration | Espace de noms | DMV | Activée par défaut | Fréquence de collecte par défaut |
|:---|:---|:---|:---|:---|:---|
| Statistiques d’attente | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Oui | 60 secondes |
| Régisseurs de mémoire | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Oui | 60 secondes |
| E/S de la base de données | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Oui | 60 secondes |
| Propriétés du serveur | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Oui | 60 secondes |
| Compteurs de performance | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Oui | 60 secondes |
| Statistiques des ressources | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Oui | 60 secondes |
| Gouvernance des ressources | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Oui | 60 secondes |
| Demandes | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Non | N/D |
| Planificateurs | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Non | Non applicable |

### <a name="data-for-sql-server"></a>Données pour SQL Server

| Nom convivial | Nom de la configuration | Espace de noms | DMV | Activée par défaut | Fréquence de collecte par défaut |
|:---|:---|:---|:---|:---|:---|
| Statistiques d’attente | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Oui | 60 secondes | 
| Régisseurs de mémoire | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Oui | 60 secondes |
| E/S de la base de données | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Oui | 60 secondes |
| Propriétés du serveur | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Oui | 60 secondes |
| Compteurs de performance | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Oui | 60 secondes |
| Espace sur le volume | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Oui | 60 secondes |
| Processeur SQL Server | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Oui | 60 secondes |
| Planificateurs | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Non | Non applicable |
| Demandes | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Non | Non applicable |
| États du réplica de disponibilité | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | Non | 60 secondes |
| Réplica de base de données de disponibilité | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | Non | 60 secondes |

## <a name="next-steps"></a>Étapes suivantes

- Pour consulter les questions fréquemment posées sur SQL Insights, Consultez le [Forum aux questions](../faq.yml).
