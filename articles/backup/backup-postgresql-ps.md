---
title: Sauvegarde d’Azure Database pour PostgreSQL avec rétention à long terme à l’aide d’Azure PowerShell
description: Découvrez comment sauvegarder une base de données Azure Database pour PostgreSQL avec Azure PowerShell.
ms.topic: conceptual
ms.author: v-amallick
ms.date: 10/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 47beb64c518bbd55090c9cf6cd50068635310d25
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098181"
---
# <a name="back-up-azure-postgresql-databases-using-azure-powershell"></a>Sauvegarde de bases de données Azure PostgreSQL avec Azure PowerShell

Cet article explique comment sauvegarder une [base de données Azure PostgreSQL](../postgresql/overview.md#azure-database-for-postgresql---single-server) avec Azure PowerShell.

Dans cet article, vous allez apprendre à :

- Créer un coffre de sauvegarde

- Créer une stratégie de sauvegarde

- Configurer une sauvegarde d’une base de données Azure PostgreSQL

- Exécuter un travail de sauvegarde à la demande

Pour plus d’informations sur les scénarios pris en charge et les limitations des bases de données Azure PostgreSQL, consultez la [matrice de prise en charge](backup-azure-database-postgresql-overview.md#support-matrix).

## <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde

Un coffre Sauvegarde est une entité de stockage dans Azure qui stocke les données de sauvegarde d’une série de nouvelles charges de travail prises en charge par la Sauvegarde Azure, notamment des serveurs Azure Database pour PostgreSQL, des disques Azure et des objets blob Azure. Il vous aide à organiser vos données de sauvegarde tout en réduisant la charge de gestion. Les coffres Sauvegarde sont basés sur le modèle Azure Resource Manager, qui fournit des fonctionnalités améliorées pour sécuriser les données de sauvegarde.

Avant de créer un coffre Sauvegarde, choisissez la redondance de stockage des données du coffre. Ensuite, procédez à la création du coffre de sauvegarde avec cette redondance de stockage et l’emplacement.

Dans cet article, nous allons créer un coffre Sauvegarde *TestBkpVault* dans la région *westus*, sous le groupe de ressources *testBkpVaultRG*. Utilisez la commande [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) pour créer un coffre Sauvegarde. Pour plus d’informations, consultez [Création d’un coffre Sauvegarde](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Maintenant que nous avons créé le coffre, nous allons élaborer une stratégie de sauvegarde pour protéger les bases de données Azure PostgreSQL.

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

### <a name="understand-postgresql-backup-policy"></a>Présentation de la stratégie de sauvegarde PostgreSQL

Tandis que la sauvegarde sur disque permet plusieurs sauvegardes par jour et que la sauvegarde d’objets blob constitue une sauvegarde *continue* sans déclencheur, la sauvegarde PostgreSQL offre une protection de l’archive. Les données de sauvegarde qui sont envoyées pour la première fois au coffre peuvent être déplacées vers le niveau *archive*, en fonction d’une règle définie ou d’un *cycle de vie*. Dans ce contexte, nous allons comprendre l’objet de stratégie de sauvegarde pour PostgreSQL.

-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (sauvegarde complète de base de données dans ce cas)
         -  Magasin de données initial (emplacement initial des sauvegardes)
         -  Déclencheur (mode de déclenchement de la sauvegarde)
            -  Suivant une planification
            -  Critères de marquage par défaut (« étiquette » par défaut pour toutes les sauvegardes planifiées, qui lie les sauvegardes à la règle de rétention)
   -  Règle de rétention par défaut (règle appliquée à toutes les sauvegardes, par défaut, sur le magasin de données initial)

Par conséquent, cet objet définit le type des sauvegardes déclenchées, leur mode de déclenchement (suivant une planification), leur marquage, leur emplacement (magasin de données) et le cycle de vie des données de sauvegarde dans un magasin de données. L’objet PowerShell par défaut pour PostgreSQL indique de déclencher une sauvegarde *complète* toutes les semaines. Les données arrivent dans le coffre, où elles sont stockées pendant trois mois.

Si vous souhaitez ajouter le niveau *archive* à la stratégie, vous devez décider du moment où les données sont déplacées du coffre vers le niveau archive, de la durée pendant laquelle elles sont conservées dans l’archive et des sauvegardes planifiées à *marquer* comme _archivables_. Par conséquent, vous devez ajouter une *règle de rétention*, dans laquelle le cycle de vie des données de sauvegarde est défini du magasin de données du coffre au magasin de données d’archivage. Elle spécifie également la durée pendant laquelle les données sont conservées dans le magasin de données *d’archivage*. Vous devez ensuite ajouter une *étiquette* qui marque les sauvegardes planifiées comme _admissibles à l’archivage_.

L’objet PowerShell qui en résulte est le suivant :


-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (sauvegarde complète de base de données dans ce cas)
         -  Magasin de données initial (emplacement initial des sauvegardes)
         -  Déclencheur (mode de déclenchement de la sauvegarde)
            -  Suivant une planification
            -  Critères de marquage par défaut (« étiquette » par défaut pour toutes les sauvegardes planifiées, qui lie les sauvegardes à la règle de rétention)
            -  Nouveaux critères de marquage de la nouvelle règle de rétention portant le même nom (« X »)
   -  Règle de rétention par défaut (règle appliquée à toutes les sauvegardes, par défaut, sur le magasin de données initial)
   -  Nouvelle règle de rétention nommée « X »
      -  Cycle de vie
         -  Magasin de données source
         -  Suppression après une certaine période dans le magasin de données source
         -  Copie dans le magasin de données cible

### <a name="retrieving-the-policy-template"></a>Récupération du modèle de stratégie

Pour comprendre les composants internes d’une stratégie de sauvegarde pour la sauvegarde de base de données Azure PostgreSQL, récupérez le modèle de stratégie avec la commande [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). Cette commande retourne un modèle de stratégie par défaut pour un type de source de données donné. Utilisez ce modèle de stratégie pour créer une nouvelle stratégie.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDatabaseForPostgreSQL
$policyDefn | fl


DatasourceType : {Microsoft.DBforPostgreSQL/servers/databases}
ObjectType     : BackupPolicy
PolicyRule     : {BackupWeekly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : VaultStore
Name                      : BackupWeekly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.ScheduleBasedTriggerCo
                            ntext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Le modèle de stratégie est constitué d’un déclencheur (qui détermine le déclenchement de la sauvegarde) et d’un cycle de vie (qui décide quand supprimer/copier/déplacer la sauvegarde). Dans la sauvegarde de base de données Azure PostgreSQL, la valeur par défaut du déclencheur est un déclencheur hebdomadaire planifié (une sauvegarde tous les 7 jours) et la rétention de chaque sauvegarde pendant trois mois.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2021-08-22T02:00:00+00:00/P1W}
ScheduleTimeZone              : UTC
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P3M
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : VaultStore
TargetDataStoreCopySetting : {}
```

### <a name="modify-the-policy-template"></a>Modification du modèle de stratégie

#### <a name="modify-the-schedule"></a>Modification de la planification

Le modèle de stratégie par défaut comporte une sauvegarde par semaine. Vous pouvez modifier la planification de la sauvegarde pour qu’elle se produise plusieurs jours par semaine. Pour cela, utilisez la commande [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-6.5.0&preserve-view=true).

Dans l’exemple suivant, la sauvegarde hebdomadaire est modifiée de façon à se produire le dimanche, le mercredi et le vendredi toutes les semaines. Le tableau des dates de planification mentionne les dates. Les jours de la semaine de ces dates sont pris comme jours de la semaine. Vous devez également spécifier que ces planifications se répètent chaque semaine. Par conséquent, l’intervalle de planification est « 1 », et le type d’intervalle « Weekly ».

```azurepowershell-interactive
$schDates = @(
    (
        (Get-Date -Year 2021 -Month 08 -Day 15 -Hour 22 -Minute 0 -Second 0)
    ), 
    (
        (Get-Date -Year 2021 -Month 08 -Day 18 -Hour 22 -Minute 0 -Second 0)
    ),
  (
        (Get-Date -Year 2021 -Month 08 -Day 20 -Hour 22 -Minute 0 -Second 0)
    )
)
$trigger = New-AzDataProtectionPolicyTriggerScheduleClientObject -ScheduleDays $schDates -IntervalType Weekly -IntervalCount 1 
Edit-AzDataProtectionPolicyTriggerClientObject -Schedule $trigger -Policy $policyDefn   
```

#### <a name="add-a-new-retention-rule"></a>Ajout d’une nouvelle règle de rétention

Par conséquent, si vous souhaitez ajouter la protection de _l’archive_, vous devez modifier le modèle de stratégie comme indiqué ci-dessous.

Le modèle par défaut comporte un cycle de vie pour le magasin de données initial sous la règle de rétention par défaut. Dans ce scénario, la règle indique de supprimer les données de sauvegarde au bout de trois mois. Ajoutez une nouvelle règle de rétention qui définit le moment où les données sont *déplacées* vers le magasin de données *d’archivage*. Autrement dit, les données de sauvegarde sont d’abord copiées vers le magasin de données d’archivage, puis supprimées dans le magasin de données du coffre. En outre, la règle doit définir la durée pendant laquelle les données sont conservées dans le magasin de données *d’archivage*. Utilisez la commande [New-AzDataProtectionRetentionLifeCycleClientObject](/powershell/module/az.dataprotection/new-azdataprotectionretentionlifecycleclientobject?view=azps-6.5.0&preserve-view=true) pour créer de nouveaux cycles de vie et la commande [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-6.5.0&preserve-view=true) pour les associer aux nouvelles règles ou aux règles existantes.

Dans l’exemple suivant, une règle de rétention nommée *Monthly* est créée : la première sauvegarde réussie de chaque mois doit être conservée dans le coffre pendant 6 mois, déplacée vers le niveau archive et conservée dans ce niveau pendant 24 mois.

```azurepowershell-interactive
$VaultToArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore VaultStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 6 -TargetDataStore ArchiveStore -CopyOption CopyOnExpiryOption

$OnArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore ArchiveStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 24

Edit-AzDataProtectionPolicyRetentionRuleClientObject -Policy $policyDefn -Name Monthly -LifeCycles $VaultToArchiveLifeCycle, $OnArchiveLifeCycleLifeCycle -IsDefault $false
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>Ajout d’une étiquette et des critères pertinents

Une fois qu’une règle de rétention est définie, vous devez créer une *étiquette* correspondante dans la propriété *Déclencheur* de la stratégie de sauvegarde. Utilisez la commande [New-AzDataProtectionPolicyTagCriteriaClientObject](/powershell/module/az.dataprotection/new-azdataprotectionpolicytagcriteriaclientobject?view=azps-6.5.0&preserve-view=true) pour créer un critère de marquage et la commande [Edit-AzDataProtectionPolicyTagClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytagclientobject?view=azps-6.5.0&preserve-view=true) pour mettre à jour l’étiquette existante ou en créer une.

Dans l’exemple suivant, une *étiquette* est créée avec les critères (première sauvegarde réussie du mois) sous le même nom que la règle de rétention correspondante à appliquer.

Dans cet exemple, les critères d’étiquette doivent être nommés *Monthly*.

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -AbsoluteCriteria FirstOfMonth
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

Si la planification se compose de plusieurs sauvegardes par semaine (tous les dimanches, mercredis et jeudis comme dans l’exemple ci-dessus) et que vous souhaitez archiver les sauvegardes du dimanche et du vendredi, vous pouvez modifier les critères de marquage comme suit :

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -DaysOfWeek @("Sunday", "Friday")
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

### <a name="create-a-new-postgresql-backup-policy"></a>Création d’une stratégie de sauvegarde PostgreSQL

Une fois le modèle modifié conformément aux exigences, utilisez la commande [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-6.5.0&preserve-view=true) pour créer une stratégie avec ce modèle.

```azurepowershell-interactive
$polOss = New-AzDataProtectionBackupPolicy -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Name "TestOSSPolicy" -Policy $policyDefn
```

## <a name="configure-backup"></a>Configurer une sauvegarde

Une fois le coffre et la stratégie créés, il existe trois points critiques à prendre en compte pour protéger une base de données Azure PostgreSQL.

### <a name="key-entities-involved"></a>Entités clés impliquées

#### <a name="postgresql-database-to-be-protected"></a>Base de données PostgreSQL à protéger

Récupérez l’ID Azure Resource Manager (ARM) de la base de données PostgreSQL à protéger. Il s’agit de l’identificateur de la base de données. Nous allons prendre un exemple de base de données nommé **empdb11** sous un serveur PostgreSQL **testposgresql**, qui est présent dans le groupe de ressources **ossrg** sous un autre abonnement.

```azurepowershell-interactive
$ossId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Coffre de clés Azure

Le service Sauvegarde Azure ne stocke pas le nom d’utilisateur ni le mot de passe permettant de se connecter à la base de données PostgreSQL. C’est l’administrateur de sauvegarde qui amorce les *clés* dans le coffre de clés. Ensuite, le service de sauvegarde accède au coffre de clés, lit les clés et accède à la base de données. Notez l’identificateur de secret de la clé concernée.

```azurepowershell-interactive
$keyURI = "https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Archivage de sauvegarde

Vous devez connecter le coffre Sauvegarde au serveur PostgreSQL, puis accéder à la base de données au moyen des clés présentes dans le coffre de clés. Il lui faut par conséquent un accès au serveur PostgreSQL et au coffre de clés. Cet accès est accordé au MSI du coffre Sauvegarde.

[Familiarisez-vous avec les autorisations](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-backup) que vous devez accorder au MSI du coffre Sauvegarde sur le serveur PostgreSQL et le coffre de clés Azure, où sont stockées les clés de la base de données.

### <a name="prepare-the-request"></a>Préparer la requête

Une fois toutes les autorisations nécessaires définies, la configuration de la sauvegarde s’effectue en deux étapes.

1. Nous préparons la demande appropriée en utilisant le coffre, la stratégie et la base de données PostgreSQL nécessaires avec la commande [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true).
1. Nous soumettons la demande pour protéger la base de données avec la commande [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDatabaseForPostgreSQL -DatasourceLocation $TestBkpvault.Location -PolicyId $polOss[0].Id -DatasourceId $ossId -SecretStoreURI $keyURI -SecretStoreType AzureKeyVault
ConvertTo-Json -InputObject $instance -Depth 4 
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                        Type                                         BackupInstanceName
----                        ----                                          ------------------
ossrg-empdb11       Microsoft.DataProtection/backupVaults/backupInstances ossrg-empdb11
```

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Récupérez l’instance de sauvegarde sur laquelle vous devez déclencher une sauvegarde avec la commande [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Vous pouvez spécifier une règle de rétention lors du déclenchement de la sauvegarde. Pour afficher les règles de rétention dans la stratégie, accédez à l’objet de stratégie pour les règles de rétention. Dans l’exemple suivant est affichée la règle portant le nom *default*. Nous l’utiliserons pour la sauvegarde à la demande.

```azurepowershell-interactive
$ossPol.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

## <a name="trigger-an-on-demand-backup"></a>Déclencher une sauvegarde à la demande

Pour déclencher une sauvegarde à la demande, utilisez la commande [Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="track-jobs"></a>Suivre les travaux

Effectuez le suivi de tous les travaux à l’aide de la commande [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true). Vous pouvez répertorier tous les travaux et extraire le détail d’un travail particulier.

Vous pouvez également utiliser la commande _Az.ResourceGraph_ pour effectuer le suivi de tous les travaux dans l’ensemble des coffres de sauvegarde. Utilisez la commande [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) pour récupérer les travaux appropriés parmi tous les coffres Sauvegarde.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Étapes suivantes

- [Restauration d’une base de données Azure PostgreSQL avec Azure PowerShell](restore-managed-disks-ps.md)
