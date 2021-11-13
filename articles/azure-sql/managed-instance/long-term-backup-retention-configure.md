---
title: 'Azure SQL Managed Instance : conservation des sauvegardes à long terme'
description: Découvrez comment stocker et restaurer des sauvegardes automatisées sur des conteneurs de Stockage Blob Azure distincts pour une instance managée Azure SQL avec le portail Azure et PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: backup-restore
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 09/12/2021
ms.openlocfilehash: 25040c5017a4d0a990d9b1fddb342cab94c97e85
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239368"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention"></a>Gérer la conservation des sauvegardes à long terme Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dans Azure SQL Managed Instance, une fonctionnalité en préversion publique vous permet de configurer une stratégie de [rétention des sauvegardes à long terme](../database/long-term-retention-overview.md) (LTR). Cela vous permet de conserver automatiquement les sauvegardes de base de données dans des conteneurs Stockage Blob Azure distincts pendant 10 ans maximum. Vous pouvez ensuite récupérer une base de données à l’aide de ces sauvegardes avec le portail Azure et PowerShell.

   > [!IMPORTANT]
   > La LTR pour les instances gérées est actuellement disponible en préversion publique dans les régions publiques Azure. 

Les sections suivantes montrent comment utiliser le portail Azure, PowerShell et Azure CLI pour configurer la conservation des sauvegardes à long terme, voir les sauvegardes dans le stockage Azure SQL et effectuer une restauration à partir d’une sauvegarde dans le stockage Azure SQL.

## <a name="prerequisites"></a>Prérequis

# <a name="portal"></a>[Portail](#tab/portal)

Un abonnement Azure actif.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Préparez votre environnement pour l’interface Azure CLI.

[!INCLUDE[azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Préparez votre environnement pour PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais le module Az.Sql fera l’objet d’un développement futur. Pour ces cmdlets, voir [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm.

Pour **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** et **Restore-AzSqlInstanceDatabase**, vous devez avoir l’un des rôles suivants :

- Rôle Propriétaire de l’abonnement
- Rôle Collaborateur Managed Instance
- Rôle personnalisé avec les autorisations suivantes :
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Pour **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup**, vous devez avoir l’un des rôles suivants :

- Rôle Propriétaire de l’abonnement
- Rôle personnalisé avec l’autorisation suivante :
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Le rôle Collaborateur Managed Instance n’a pas l’autorisation de supprimer les sauvegardes LTR.

Les autorisations Azure RBAC peuvent être accordées dans l’étendue de l’*abonnement* ou du *groupe de ressources*. Toutefois, pour accéder aux sauvegardes LTR appartenant à une instance supprimée, il faut accorder l’autorisation dans l’étendue de *l’abonnement* de cette instance.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

---

## <a name="create-long-term-retention-policies"></a>Créer des stratégies de conservation à long terme

Vous pouvez configurer SQL Managed Instance pour [conserver des sauvegardes automatisées](../database/long-term-retention-overview.md) sur une période plus longue que la période de rétention associée à votre niveau de service.

# <a name="portal"></a>[Portail](#tab/portal)

1. Dans le portail Azure, sélectionnez votre instance gérée, puis cliquez sur **Sauvegardes**. Sous l’onglet **Stratégies de conservation**, sélectionnez les bases de données sur lesquelles vous souhaitez définir ou modifier des stratégies de rétention des sauvegardes à long terme. Les modifications ne s’appliquent pas aux bases de données non sélectionnées. 

   ![lien gérer les sauvegardes](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. Dans le volet **Configurer les stratégies**, spécifiez la période de conservation souhaitée pour les sauvegardes hebdomadaires, mensuelles ou annuelles. Choisissez une période de conservation de « 0 » pour indiquer qu’aucune conservation de sauvegarde à long terme ne doit être définie.

   ![configurer des stratégies](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Lorsque vous avez terminé, cliquez sur **Appliquer**.

> [!IMPORTANT]
> Lorsque vous activez une stratégie de rétention des sauvegardes à long terme, la première sauvegarde peut ne devenir visible et disponible pour une restauration qu’au bout de 7 jours. Pour en savoir plus sur la cadence des sauvegardes LTR, consultez la section relative à la [conservation des sauvegardes à long terme](../database/long-term-retention-overview.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Exécutez la commande [az sql midb show](/cli/azure/sql/midb#az_sql_midb_show) pour obtenir les détails de la base de données Managed Instance.

    ```azurecli
    az sql midb show /
    --resource-group mygroup /
    --managed-instance myinstance /
    --name mymanageddb /
    --subscription mysubscription
    ```

2. Exécutez la commande [az sql midb ltr-policy set](/cli/azure/sql/midb/ltr-policy#az_sql_midb_ltr_policy_set) pour créer une stratégie LTR. L’exemple suivant définit une stratégie de conservation à long terme pour 12 semaines pour la sauvegarde hebdomadaire.

    ```azurecli
    az sql midb ltr-policy set /
    --resource-group mygroup /
    --managed-instance myinstance /
    --name mymanageddb /
    --weekly-retention "P12W"
    ```

    Cet exemple définit une stratégie de conservation pour 12 semaines pour la sauvegarde hebdomadaire, 5 ans pour la sauvegarde annuelle, et la semaine du 15 avril pour l’exécution de la sauvegarde LTR annuelle.

    ```azurecli
    az sql midb ltr-policy set /
    --resource-group mygroup /
    --managed-instance myinstance /
    --name mymanageddb /
    --weekly-retention "P12W" /
    --yearly-retention "P5Y" /
    --week-of-year 16
    ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

---

## <a name="view-backups-and-restore-from-a-backup"></a>Afficher des sauvegardes et restaurer à partir d’une sauvegarde

# <a name="portal"></a>[Portail](#tab/portal)

Affichez les sauvegardes qui sont conservées pour une base de données spécifique avec une stratégie de conservation à long terme et restaurez à partir de ces sauvegardes.

1. Dans le portail Azure, sélectionnez votre instance gérée, puis cliquez sur **Sauvegardes**. Dans l’onglet **Sauvegardes disponibles**, sélectionnez la base de données pour laquelle vous souhaitez afficher les sauvegardes disponibles. Cliquez sur **Gérer**.

   ![sélectionner la base de données](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. Dans le volet **Gérer les sauvegardes**, passez en revue les sauvegardes disponibles.

   ![afficher les sauvegardes](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Sélectionnez la sauvegarde à partir de laquelle vous souhaitez restaurer, cliquez sur **Restaurer**, puis, dans la page Restaurer, spécifiez le nouveau nom de la base de données. La sauvegarde et la source sont préremplies sur cette page. 

   ![sélectionner une sauvegarde pour restauration](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![restauration](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Cliquez sur **Vérifier + créer** pour examiner les détails de votre restauration. Cliquez ensuite sur **Créer** pour restaurer votre base de données à partir de la sauvegarde choisie.

1. Dans la barre d’outils, cliquez sur l’icône de notification pour visualiser l’état du travail de restauration.

   ![progression du travail de restauration](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Une fois le travail de restauration terminé, ouvrez la page **Vue d’ensemble de l’instance gérée** pour voir la base de données nouvellement restaurée.

> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour [extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante](../database/recovery-using-backups.md#point-in-time-restore).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-ltr-policies"></a>Afficher des stratégies de rétention à long terme

Exécutez la commande [az sql midb ltr-policy show](/cli/azure/sql/midb/ltr-policy#az_sql_midb_ltr_policy_show) pour afficher la stratégie LTR pour une base de données unique dans une instance unique.

```azurecli
az sql midb ltr-policy show \
    --resource-group mygroup \
    --managed-instance myinstance \
    --name mymanageddb
```

### <a name="view-ltr-backups"></a>Afficher des sauvegardes de rétention à long terme

Utilisez la commande [az sql midb ltr-backup list](/cli/azure/sql/midb/ltr-backup#az_sql_midb_ltr_backup_list) pour afficher les stratégies LTR dans une instance.

```azurecli
az sql midb ltr-backup list \
    --resource-group mygroup \
    --location eastus2 \
    --managed-instance myinstance
```

### <a name="delete-ltr-backups"></a>Supprimer des sauvegardes de rétention à long terme

Exécutez la commande [az sql midb ltr-backup delete](/cli/azure/sql/midb/ltr-backup#az_sql_midb_ltr_backup_delete) pour supprimer une stratégie LTR. Vous pouvez exécuter [az sql midb ltr-backup list](/cli/azure/sql/midb/ltr-backup#az_sql_midb_ltr_backup_list) pour obtenir le `name` de la sauvegarde.

```azurecli
az sql midb ltr-backup delete \
    --location eastus2 \
    --managed-instance myinstance \
    --database mymanageddb \
    --name "3214b3fb-fba9-43e7-96a3-09e35ffcb336;132292152080000000"
```

> [!IMPORTANT]
> La suppression de sauvegardes de rétention à long terme n’est pas réversible. Pour supprimer une sauvegarde LTR une fois l’instance supprimée, vous devez disposer d’une autorisation étendue à l’abonnement. Vous pouvez configurer des notifications sur chaque suppression dans Azure Monitor en filtrant sur l’opération « Supprime une sauvegarde de conservation à long terme ». Le journal d’activité contient des informations sur la personne qui a effectué la requête et quand. Consultez [Créer des alertes de journal d’activité](../../azure-monitor/alerts/alerts-activity-log.md) pour obtenir des instructions détaillées.

### <a name="restore-from-ltr-backups"></a>Restaurer à partir de sauvegardes de rétention à long terme

Exécutez la commande [az sql midb ltr-backup restore](/cli/azure/sql/midb/ltr-backup#az_sql_midb_ltr_backup_restore) pour restaurer votre stratégie à partir d’une sauvegarde LTR. Vous pouvez exécuter [az sql midb ltr-backup show](/cli/azure/sql/midb/ltr-backup#az_sql_midb_ltr_backup_show) pour obtenir le `backup-id`.

1. Créez une variable pour le `backup-id` avec la commande `az sql db ltr-backup show` pour une utilisation ultérieure.

   ```azurecli
    get_backup_id=$(az sql midb ltr-backup show
       --location eastus2 \
       --managed-instance myinstance \
       --database mydb \
       --name "3214b3fb-fba9-43e7-96a3-09e35ffcb336;132292152080000000" \
       --query 'id' \
       --output tsv)
   ```
2. Restaurer votre base de données à partir d’une sauvegarde LTR

    ```azurecli
    az sql midb ltr-backup restore \
        --dest-database targetmidb \
        --dest-mi myinstance \
        --dest-resource-group mygroup \
        --backup-id $get_backup_id
    ```

> [!IMPORTANT]
> Pour effectuer une restauration à partir d’une sauvegarde LTR après la suppression de l’instance, vous devez disposer d’autorisations étendues à l’abonnement de l’instance, cet abonnement devant être actif.

> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante. Consultez [Restauration dans le temps](../database/recovery-using-backups.md#point-in-time-restore).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

### <a name="view-ltr-policies"></a>Afficher des stratégies de rétention à long terme

Cet exemple montre comment lister les stratégies LTR au sein d’une instance pour une base de données unique.

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

Cet exemple montre comment lister les stratégies LTR pour toutes les bases de données sur une instance.

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>Effacer une stratégie de rétention à long terme

Cet exemple montre comment effacer une stratégie de conservation à long terme d’une base de données.

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>Afficher des sauvegardes de rétention à long terme

Cet exemple montre comment lister les sauvegardes LTR au sein d’une instance.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>Supprimer des sauvegardes de rétention à long terme

Cet exemple montre comment supprimer une sauvegarde de rétention à long terme de la liste des sauvegardes.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> La suppression de sauvegardes de rétention à long terme n’est pas réversible. Pour supprimer une sauvegarde LTR une fois l’instance supprimée, vous devez disposer d’une autorisation étendue à l’abonnement. Vous pouvez configurer des notifications sur chaque suppression dans Azure Monitor en filtrant sur l’opération « Supprime une sauvegarde de conservation à long terme ». Le journal d’activité contient des informations sur la personne qui a effectué la requête et quand. Consultez [Créer des alertes de journal d’activité](../../azure-monitor/alerts/alerts-activity-log.md) pour obtenir des instructions détaillées.

### <a name="restore-from-ltr-backups"></a>Restaurer à partir de sauvegardes de rétention à long terme

Cet exemple montre comment restaurer à partir d’une sauvegarde de rétention à long terme. Notez que cette interface n’a pas changé, mais que le paramètre d’ID de ressource requiert désormais l’ID de ressource de sauvegarde LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Pour effectuer une restauration à partir d’une sauvegarde LTR après la suppression de l’instance, vous devez disposer d’autorisations étendues à l’abonnement de l’instance, cet abonnement devant être actif. Vous devez également omettre le paramètre facultatif -ResourceGroupName.

> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante. Consultez [Restauration dans le temps](../database/recovery-using-backups.md#point-in-time-restore).

---

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez la page [Sauvegardes automatiques](../database/automated-backups-overview.md).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](../database/long-term-retention-overview.md).
