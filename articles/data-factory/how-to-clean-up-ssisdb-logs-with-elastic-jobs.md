---
title: Comment nettoyer les journaux SSISDB automatiquement
description: Cet article explique comment nettoyer le déploiement de projet SSIS et les journaux d’exécution de package stockés dans SSISDB en appelant automatiquement la procédure stockée SSISDB appropriée via Azure Data Factory, Agent Azure SQL Managed Instance ou des travaux de base de données élastique.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78b6c754fe2b49ceabf940aa76b86ddd90dfc91e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843652"
---
# <a name="how-to-clean-up-ssisdb-logs-automatically"></a>Comment nettoyer les journaux SSISDB automatiquement

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Une fois que vous avez configuré un runtime d’intégration (IR) Azure-SQL Server Integration Services (SSIS) dans Azure Data Factory (ADF), vous pouvez l’utiliser pour exécuter des packages SSIS déployés dans :

- le catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database/Managed Instance (modèle de déploiement de projet) ;
- le système de fichiers, Azure Files ou une base de données SQL Server (MSDB) hébergée par Azure SQL Managed Instance (modèle de déploiement de package).

Dans le modèle de déploiement de projet, votre IR Azure-SSIS déploie des projets SSIS dans SSISDB, récupère les packages SSIS à exécuter à partir de SSISDB et écrit les journaux d’exécution de package dans SSISDB. SSISDB est également utilisé pour stocker les journaux des travaux SSIS et des opérations IR. Pour gérer les journaux accumulés, nous avons fourni des propriétés SSISDB et des procédures stockées appropriées qui peuvent être appelées automatiquement selon la planification par le biais d’ADF, Agent Azure SQL Managed Instance ou de travaux de base de données élastique.

## <a name="ssisdb-log-clean-up-properties-and-stored-procedures"></a>Propriétés et procédures stockées de nettoyage des journaux SSISDB
Pour gérer les journaux d’exécution de package SSIS, vous pouvez configurer des propriétés de nettoyage des journaux SSISDB en vous connectant à SSISDB hébergé par votre serveur Azure SQL Database/Managed Instance à l’aide de SQL Server Management Studio (SSMS). Consultez [Connexion à SSISDB](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial?view=sql-server-ver15&preserve-view=true#connect-to-the-ssisdb-database). Une fois connecté, dans la fenêtre **Explorateur d’objets** de SSMS, vous pouvez développer le nœud **Catalogues Integration Services**, cliquer avec le bouton droit sur le sous-nœud **SSISDB**, puis sélectionner l’élément de menu **Propriétés** pour ouvrir la boîte de dialogue **Propriétés du catalogue**. Dans la boîte de dialogue **Propriétés du catalogue**, vous trouverez les propriétés de nettoyage des journaux SSISDB suivantes :

- **Nettoyer les journaux régulièrement** : active le nettoyage des journaux d’exécution de package, défini par défaut sur *True*.
- **Période de rétention (jours)** : spécifie l’ancienneté maximale des journaux retenus (en jours), défini par défaut sur *365*. Les journaux plus anciens sont supprimés lorsque la procédure stockée SSISDB appropriée est appelée.
- **Supprimer régulièrement les anciennes versions** : active le nettoyage des versions de projet stockées, défini par défaut sur *True*.
- **Nombre maximal de versions par projet** : spécifie le nombre maximal de versions de projet stockées, défini par défaut sur *10*. Les versions plus anciennes sont supprimées lorsque la procédure stockée SSISDB appropriée est appelée.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-ssisdb-properties.png" alt-text="Propriétés de nettoyage des journaux SSISDB":::

Une fois les propriétés de nettoyage des journaux SSISDB configurées, vous pouvez appeler la procédure stockée SSISDB appropriée, `[internal].[cleanup_server_retention_window_exclusive]`, pour nettoyer les journaux d’exécution de package SSIS.

Pour nettoyer les journaux des travaux SSIS, vous pouvez appeler la procédure stockée SSISDB appropriée, `[internal].[cleanup_completed_jobs_exclusive]`. La période de rétention est définie par défaut sur 60 minutes et les anciens journaux sont supprimés lorsque la procédure stockée est appelée.

Pour nettoyer les journaux des opérations IR SSIS, vous pouvez appeler la procédure stockée SSISDB appropriée, `[internal].[cleanup_expired_worker]`. La période de rétention est définie par défaut sur 168 heures et les anciens journaux sont supprimés lorsque la procédure stockée est appelée.

Ces procédures stockées SSISDB nettoient différentes tables SSISDB :

| Procédures stockées SSISDB | Tables SSISDB à nettoyer |
|--------------------------|---------------------------|
| `[internal].[cleanup_server_retention_window_exclusive]` | `[internal].[event_message_context_scaleout]`<br/>`[internal].[event_messages_scaleout]`<br/>`[internal].[executable_statistics]`<br/>`[internal].[execution_component_phases]`<br/>`[internal].[execution_data_statistics]`<br/>`[internal].[execution_data_taps]`<br/>`[internal].[execution_parameter_values]`<br/>`[internal].[execution_parameter_values_noncatalog]`<br/>`[internal].[execution_property_override_values]`<br/>`[internal].[execution_property_override_values_noncatalog]`<br/>`[internal].[executions]`<br/>`[internal].[executions_noncatalog]`<br/>`[internal].[extended_operation_info]`<br/>`[internal].[operation_messages]`<br/>`[internal].[operation_messages_scaleout]`<br/>`[internal].[operation_permissions]`<br/>`[internal].[operations]`<br/>`[internal].[validations]` |
| `[internal].[cleanup_completed_jobs_exclusive]` | `[internal].[job_worker_agents]`<br/>`[internal].[jobs]`<br/>`[internal].[tasks]` |
| `[internal].[cleanup_expired_worker]` | `[internal].[worker_agents]` |

Ces procédures stockées SSISDB peuvent également être appelées automatiquement selon la planification par le biais d’ADF, Agent Azure SQL Managed Instance ou de travaux de base de données élastique.

## <a name="clean-up-ssisdb-logs-automatically-via-adf"></a>Nettoyer les journaux SSISDB automatiquement par le biais d’ADF
Que vous utilisiez le serveur de base de données Azure SQL/Managed Instance pour héberger des SSISDB, vous pouvez toujours utiliser ADF pour nettoyer automatiquement les journaux SSISDB selon une planification. Pour ce faire, vous pouvez préparer une activité Exécuter le package SSIS dans le pipeline ADF avec un package incorporé contenant une seule tâche Exécuter SQL qui appelle les procédures stockées SSISDB appropriées. Consultez l’exemple 4 dans notre blog : [Exécutez n’importe quelle requête SQL en 3 étapes simples à l’aide du service SSIS dans Azure Data Factory](https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244).

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/run-sql-ssis-activity-ssis-parameters-ssisdb-clean-up.png" alt-text="Nettoyage des journaux SSISDB par le biais d’ADF":::

Pour le paramètre **SQLStatementSource**, vous pouvez entrer `EXEC internal.cleanup_server_retention_window_exclusive` pour nettoyer les journaux d’exécution de package SSIS. 

Pour nettoyer les journaux des travaux SSIS, vous pouvez ajouter `EXEC internal.cleanup_completed_jobs_exclusive [@minutesToKeep=’Number of minutes to set as retention period’]`. 

Pour nettoyer les journaux des opérations IR SSIS, vous pouvez ajouter `EXEC internal.cleanup_expired_worker [@hoursToKeep=’Number of hours to set as retention period’] `.

Une fois que votre pipeline ADF est préparé, vous pouvez associer un déclencheur de planification pour l’exécuter régulièrement. Consultez [Comment déclencher un pipeline ADF selon une planification](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="clean-up-ssisdb-logs-automatically-via-azure-sql-managed-instance-agent"></a>Nettoyer les journaux SSISDB automatiquement par le biais de l’Agent Azure SQL Managed Instance
Si vous utilisez Azure SQL Managed Instance pour héberger SSISDB, vous pouvez également utiliser son orchestrateur/planificateur de travaux intégré, l’Agent Azure SQL Managed Instance, pour nettoyer automatiquement les journaux SSISDB selon une planification. Si SSISDB a été créé récemment dans votre Azure SQL Managed Instance, nous avons également créé un travail T-SQL nommé **Travail de maintenance de serveur SSIS** sous l’Agent Azure SQL Managed Instance pour nettoyer plus particulièrement les journaux d’exécution de package SSIS. Il est désactivé et configuré par défaut avec une planification d’exécution quotidienne.  Si vous souhaitez l’activer et ou reconfigurer sa planification, vous pouvez le faire en vous connectant à votre Azure SQL Managed Instance à l’aide de SSMS. Une fois connecté, dans la fenêtre **Explorateur d'objets** de SSMS, vous pouvez développer le nœud **Agent SQL Server**, développer le sous-nœud **Travaux**, puis double-cliquer sur **Travail de maintenance de serveur SSIS** pour l’activer/le reconfigurer.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-maintenance-job.png" alt-text="Nettoyage des journaux SSISDB par le biais de l’Agent Azure SQL Managed Instance":::

Si le **Travail de maintenance de serveur SSIS** n’est pas encore créé sous votre Agent Azure SQL Managed Instance, vous pouvez l’ajouter manuellement en exécutant le script T-SQL suivant sur votre Azure SQL Managed Instance.

```sql
USE msdb
IF EXISTS(SELECT * FROM sys.server_principals where name = '##MS_SSISServerCleanupJobLogin##')
   DROP LOGIN ##MS_SSISServerCleanupJobLogin##

DECLARE @loginPassword nvarchar(256)
SELECT @loginPassword = REPLACE (CONVERT( nvarchar(256), CRYPT_GEN_RANDOM( 64 )), N'''', N'''''')
EXEC ('CREATE LOGIN ##MS_SSISServerCleanupJobLogin## WITH PASSWORD =''' +@loginPassword + ''', CHECK_POLICY = OFF')
ALTER LOGIN ##MS_SSISServerCleanupJobLogin## DISABLE

USE master
GRANT VIEW SERVER STATE TO ##MS_SSISServerCleanupJobLogin##

USE SSISDB
IF EXISTS (SELECT name FROM sys.database_principals WHERE name = '##MS_SSISServerCleanupJobUser##')
    DROP USER ##MS_SSISServerCleanupJobUser##
CREATE USER ##MS_SSISServerCleanupJobUser## FOR LOGIN ##MS_SSISServerCleanupJobLogin##
GRANT EXECUTE ON [internal].[cleanup_server_retention_window_exclusive] TO ##MS_SSISServerCleanupJobUser##
GRANT EXECUTE ON [internal].[cleanup_server_project_version] TO ##MS_SSISServerCleanupJobUser##

USE msdb
EXEC dbo.sp_add_job
    @job_name = N'SSIS Server Maintenance Job', 
    @enabled = 0,
    @owner_login_name = '##MS_SSISServerCleanupJobLogin##',
    @description = N'Runs every day. The job removes operation records from the database that are outside the retention period and maintains a maximum number of versions per project.'

DECLARE @IS_server_name NVARCHAR(30)
SELECT @IS_server_name = CONVERT(NVARCHAR, SERVERPROPERTY('ServerName'))
EXEC sp_add_jobserver  @job_name = N'SSIS Server Maintenance Job',
                       @server_name = @IS_server_name

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Operation Records Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_retention_window_exclusive]',
    @database_name = N'msdb',
    @on_success_action = 3,
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Max Version Per Project Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_project_version]',
    @database_name = N'msdb',
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobschedule
    @job_name = N'SSIS Server Maintenance Job',
    @name = 'SSISDB Scheduler',
    @enabled = 1,
    @freq_type = 4, /*daily*/
    @freq_interval = 1,/*every day*/
    @freq_subday_type = 0x1,
    @active_start_date = 20001231,
    @active_end_date = 99991231,
    @active_start_time = 0,
    @active_end_time = 120000
```

Vous pouvez également configurer le **Travail de maintenance de serveur SSIS** existante ou modifier le script T-SQL ci-dessus pour nettoyer également les journaux des travaux SSIS/opérations IR en appelant les procédures stockées SSISDB appropriées.

## <a name="clean-up-ssisdb-logs-automatically-via-elastic-database-jobs"></a>Nettoyer les journaux SSISDB automatiquement par le biais de travaux de base de données élastique
Si vous utilisez le serveur Azure SQL Database pour héberger SSISDB, il n’a pas d’orchestrateur/de planificateur de travaux intégré. Vous devez donc utiliser un composant externe, par exemple ADF (voir ci-dessus) ou des travaux de base de données élastique (voir le reste de cette section) pour nettoyer les journaux SSISDB automatiquement selon une planification.

Les travaux de base de données élastique constituent un service Azure qui permet d’automatiser et d’exécuter des travaux par rapport à une base de données ou un groupe de bases de données. Vous pouvez planifier, exécuter et surveiller ces travaux à l’aide du portail Azure, Azure PowerShell, T-SQL ou des API REST. Utilisez des travaux de base de données élastique pour appeler les procédures stockées SSISDB appropriées pour le nettoyage des journaux une fois ou selon une planification. Vous pouvez choisir l’intervalle de planification en fonction de l’utilisation des ressources SSISDB afin d’éviter une lourde charge de base de données.

Pour plus d’informations, consultez [Gérer des groupes de bases de données avec des travaux de base de données élastique](../azure-sql/database/elastic-jobs-overview.md).

Les sections suivantes décrivent comment appeler les procédures stockées SSISDB appropriées, `[internal].[cleanup_server_retention_window_exclusive]`/`[internal].[cleanup_completed_jobs_exclusive]`/`[internal].[cleanup_expired_worker]`, qui suppriment les journaux SSISDB qui se trouvent en dehors de leurs périodes de rétention spécifiques.

### <a name="configure-elastic-database-jobs-using-azure-powershell"></a>Configurer des travaux de base de données élastique à l’aide d’Azure PowerShell

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Les scripts Azure PowerShell suivants créent un nouveau travail élastique qui appelle la procédure stockée de nettoyage des journaux SSISDB sélectionnée. Pour plus d’informations, consultez [Créer un agent de travail élastique à l’aide de PowerShell](../azure-sql/database/elastic-jobs-powershell-create.md).

#### <a name="create-parameters"></a>Créer des paramètres

``` powershell
# Parameters needed to create your job database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing Azure SQL Database server, for example myjobserver, to hold your job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for your job database to be created in the given Azure SQL Database server"),
$StoredProcName = $(Read-Host "Please enter the name of SSISDB log clean-up stored procedure to be invoked by your job (internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker)"), 

# Your job database should be a clean, empty S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create your Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your Elastic Job agent"),

# Parameters needed to create credentials in your job database for connecting to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for the log clean-up job user to connect to SSISDB"),

# Parameters needed to create the login and user for SSISDB
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of target Azure SQL Database server that contains SSISDB, for example myssisdbserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set the job schedule for invoking SSISDB log clean-up stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run your job that cleans up SSISDB logs outside their retention period immediately (Y/N). Make sure the specific retention period is set properly before running the following scripts as deleted logs cannot be recovered."),
$IntervalType = $(Read-Host "Please enter the interval type for SSISDB log clean-up schedule: Year, Month, Day, Hour, Minute, Second are supported."),
$IntervalCount = $(Read-Host "Please enter the count of interval type for SSISDB log clean-up schedule."),

# The start time for SSISDB log clean-up schedule is set to current time by default. 
$StartTime = (Get-Date)
```

#### <a name="invoke-ssisdb-log-clean-up-stored-procedure"></a>Appeler la procédure stockée de nettoyage des journaux SSISDB

```powershell
# Install the latest PowerShell PackageManagement module that PowerShellGet v1.6.5 depends on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# You may need to restart your PowerShell session
# Install the latest PowerShellGet module that adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Install AzureRM.Sql preview cmdlets side by side with the existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create your job database for defining SSISDB log clean-up job and tracking the job history
Write-Output "Creating a blank SQL database to be used as your job database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable Elastic Database Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create your Elastic Job agent
Write-Output "Creating your Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create job credentials in your job database for connecting to SSISDB in target server
Write-Output "Creating job credentials for connecting to SSISDB..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# Create the job user login in master database of target server
Write-Output "Grant permissions on the master database of target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# Create SSISDB log clean-up user from login in SSISDB and grant it permissions to invoke SSISDB log clean-up stored procedure
Write-Output "Grant appropriate permissions on SSISDB..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON " + $StoredProcName + " TO SSISDBLogCleanupUser" 

$TargetDatabase | ForEach-Object -Process {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create your target group that includes only SSISDB to clean up
Write-Output "Creating your target group that includes only SSISDB to clean up..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create your job to invoke SSISDB log clean-up stored procedure
Write-Output "Creating your job to invoke SSISDB log clean-up stored procedure..."
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add your job step to invoke SSISDB log clean-up stored procedure
Write-Output "Adding your job step to invoke SSISDB log clean-up stored procedure..."
$SqlText = "EXEC " + $StoredProcName 
$Job | Add-AzureRmSqlElasticJobStep -Name "Step to invoke SSISDB log clean-up stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run your job to immediately invoke SSISDB log clean-up stored procedure once
if ($RunJobOrNot -eq 'Y')
{
Write-Output "Invoking SSISDB log clean-up stored procedure immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule your job to invoke SSISDB log clean-up stored procedure periodically, deleting SSISDB logs outside their retention period
Write-Output "Starting your schedule to invoke SSISDB log clean-up stored procedure periodically..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

### <a name="configure-elastic-database-jobs-using-t-sql"></a>Configurer des travaux de base de données élastique à l’aide de T-SQL

Les scripts T-SQL suivants créent un nouveau travail élastique qui appelle la procédure stockée de nettoyage des journaux SSISDB sélectionnée. Pour plus d’informations, consultez [Utiliser T-SQL pour créer et gérer des travaux de base de données élastique](../azure-sql/database/elastic-jobs-tsql-create-manage.md).

1. Identifiez un niveau de service Azure SQL Database S0/supérieur vide ou créez-en un nouveau pour votre base de données de travaux. Créez ensuite un agent de travail élastique dans le [portail Azure](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent).

2. Dans votre base de données de travaux, créez les informations d’identification pour la connexion à SSISDB sur votre serveur cible.

   ```sql
   -- Connect to the job database specified when creating your job agent.
   -- Create a database master key if one doesn't already exist, using your own password.
   CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';

   -- Create credentials for SSISDB log clean-up.
   CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
   ```

3. Définissez votre groupe cible qui comprend SSISDB à nettoyer uniquement.

   ```sql
   -- Connect to your job database.
   -- Add your target group.
   EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

   -- Add SSISDB to your target group
   EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
   @target_type = 'SqlDatabase',
   @server_name = '<EnterSSISDBTargetServerName>',
   @database_name = 'SSISDB'

   -- View your recently created target group and its members.
   SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
   SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
   ```

4. Créez un utilisateur de nettoyage des journaux SSISDB à partir de la connexion dans SSISDB et accordez-lui les autorisations nécessaires pour appeler la procédure stockée de nettoyage des journaux SSISDB. Pour obtenir des instructions détaillées, consultez [Gérer les connexions](../azure-sql/database/logins-create-manage.md).

   ```sql
   -- Connect to the master database of target server that hosts SSISDB 
   CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

   -- Connect to SSISDB
   CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
   GRANT EXECUTE ON '<internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker>' TO SSISDBLogCleanupUser 
   ```

5. Créez votre travail et ajoutez votre étape de travail pour appeler la procédure stockée de nettoyage des journaux SSISDB.

   ```sql
   -- Connect to your job database.
   -- Add your job to invoke the relevant SSISDB log clean-up stored procedure.
   EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs outside their specific retention period'

   -- Add your job step to invoke the relevant SSISDB log clean-up stored procedure
   EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
   @command=N'<EXEC internal.cleanup_server_retention_window_exclusive/EXEC internal.cleanup_completed_jobs_exclusive/EXEC internal.cleanup_expired_worker>',
   @credential_name='SSISDBLogCleanupCred',
   @target_group_name='SSISDBTargetGroup'
   ```

6. Avant de continuer, veillez à définir correctement la période de rétention spécifique. Les journaux SSISDB en dehors de cette période seront supprimés et ne pourront pas être récupérés. Vous pouvez ensuite exécuter votre travail immédiatement pour démarrer le nettoyage des journaux SSISDB.

   ```sql
   -- Connect to your job database.
   -- Run your job immediately to invoke SSISDB log clean-up stored procedure.
   declare @je uniqueidentifier
   exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

   -- Watch SSISDB log clean-up results 
   select @je
   select * from jobs.job_executions where job_execution_id = @je
   ```

7. Si vous le souhaitez, vous pouvez supprimer les journaux SSISDB en dehors de leur période de rétention selon une planification. Configurez vos paramètres de travail comme suit.

   ```sql
   -- Connect to your job database.
   EXEC jobs.sp_update_job
   @job_name='CleanupSSISDBLog',
   @enabled=1,
   @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
   @schedule_interval_count='<EnterDetailedIntervalValue>',
   @schedule_start_time='<EnterProperStartTimeForSchedule>',
   @schedule_end_time='<EnterProperEndTimeForSchedule>'
   ```

### <a name="monitor-ssisdb-log-clean-up-job-using-azure-portal"></a>Surveiller le travail de nettoyage des journaux SSISDB à l’aide de portail Azure

Vous pouvez surveiller le travail de nettoyage des journaux SSISDB dans le portail Azure. Pour chaque exécution, vous voyez son état, heure de début et heure de fin.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png" alt-text="Surveiller le travail de nettoyage des journaux SSISDB dans le portail Azure":::

### <a name="monitor-ssisdb-log-clean-up-job-using-t-sql"></a>Surveiller le travail de nettoyage des journaux SSISDB à l’aide de T-SQL

Vous pouvez également utiliser T-SQL pour afficher l’historique d’exécution du travail de nettoyage des journaux SSISDB.

```sql
-- Connect to your job database.
-- View all SSISDB log clean-up job executions.
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions.
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>Étapes suivantes

Pour gérer et surveiller vos IR Azure-SSIS, consultez les articles suivants.

- [Reconfigurer le runtime d’intégration Azure-SSIS](manage-azure-ssis-integration-runtime.md)

- [Surveiller le runtime d’intégration Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
