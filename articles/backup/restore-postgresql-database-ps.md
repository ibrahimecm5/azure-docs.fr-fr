---
title: Restauration de bases de données Azure PostgreSQL avec Azure PowerShell
description: Découvrez comment restaurer des bases de données Azure PostgreSQL avec Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 6b271f823d8c0547a0fd48861e463bfcc1513e97
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097699"
---
# <a name="restore-azure-postgresql-databases-using-azure-powershell"></a>Restauration de bases de données Azure PostgreSQL avec Azure PowerShell

Cet article explique comment restaurer des [bases de données Azure PostgreSQL](/azure/postgresql/overview#azure-database-for-postgresql---single-server) sur un serveur Azure PostgreSQL sauvegardé par la Sauvegarde Azure.

Comme il s’agit d’une base de données PaaS, l’option Récupération à l’emplacement d’origine (OLR, Original Location Recovery), qui permet d’effectuer la restauration en remplaçant la base de données existante (d’où ont été effectuées les sauvegardes), n’est pas prise en charge. Vous pouvez partir d’un point de récupération pour créer une base de données, dans le même serveur Azure PostgreSQL ou non. C’est ce que l’on appelle la Récupération à un autre emplacement (ALR, Alternate Location Recovery), qui permet de conserver à la fois la base de données source et la (nouvelle) base de données restaurée.

Dans cet article, vous allez apprendre à :

- Effectuer une restauration pour créer une base de données PostgreSQL

- suivre l’état de l’opération de restauration.

Nous ferons référence au coffre de sauvegarde existant _TestBkpVault_ du groupe de ressources _testBkpVaultRG_ dans les exemples.

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-postgresql-database"></a>Effectuer une restauration pour créer une base de données PostgreSQL

### <a name="set-up-permissions"></a>Définir des autorisations

Le coffre de sauvegarde utilise une identité managée pour accéder aux autres ressources Azure. Pour effectuer la restauration à partir d’une sauvegarde, l’identité managée du coffre de sauvegarde a besoin d’un ensemble d’autorisations sur le serveur Azure PostgreSQL où la base de données doit être restaurée.

Pour attribuer les autorisations appropriées à l’identité managée affectée par le système du coffre sur le serveur PostgreSQL cible, consultez [l’ensemble d’autorisations nécessaires pour sauvegarder une base de données Azure PostgreSQL](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-restore).

Pour restaurer le point de récupération sous forme de fichiers dans un compte de stockage, [l’identité managée affectée par le système du coffre de sauvegarde a besoin d’accéder au compte de stockage cible](/azure/backup/restore-azure-database-postgresql#restore-permissions-on-the-target-storage-account).

### <a name="fetching-the-relevant-recovery-point"></a>Extraction du point de récupération approprié

Extrayez toutes les instances à l’aide de la commande [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true), puis identifiez l’instance appropriée.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Vous pouvez également utiliser **Az.Resourcegraph** et la commande [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) pour rechercher des points de récupération parmi les instances de nombreux coffres et abonnements.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured
```

Pour filtrer les critères de recherche, utilisez les fonctionnalités de recherche du client PowerShell :

```azurepowershell-interactive
Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured | Where-Object { $_.BackupInstanceName -match "empdb11"}
```

Une fois l’instance identifiée, extrayez le point de récupération approprié.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

Si vous avez besoin de récupérer le point de récupération à partir du niveau archive, ajoutez un filtre de client :

```azurepowershell-interactive
Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName | Where-Object {$_.Property.RecoveryPointDataStoresDetail[0].Type -match "Archive" }
```

### <a name="prepare-the-restore-request"></a>Préparer la demande de restauration

Il existe différentes options de restauration pour une base de données PostgreSQL. Vous pouvez restaurer le point de récupération sous la forme d’une autre base de données ou de fichiers. Il peut également se trouver dans le niveau archive.

#### <a name="restore-as-database"></a>Restaurer en tant que base de données

Construisez l’ID Azure Resource Manager (ARM) de la base de données PostgreSQL à créer (avec le serveur PostgreSQL cible qui a reçu les autorisations indiquées [ci-dessus](#set-up-permissions)) et le nom de base de données PostgreSQL nécessaire. Par exemple, une base de données PostgreSQL peut être nommée **emprestored21** sous un serveur PostgreSQL cible **targetossserver** dans le groupe de ressources **targetrg** avec un autre abonnement.

```azurepowershell-interactive
$targetOssId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21
```

Utilisez la commande [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) pour préparer la demande de restauration avec tous les détails appropriés.

```azurepowershell-interactive
$OssRestoreReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault
```

Pour un point de récupération basé sur une archive, vous devez effectuer les opérations suivantes :

1. Effectuer une réhydratation du magasin de données d’archive dans le magasin du coffre
1. Modifier le magasin de données source
1. Ajouter d’autres paramètres pour spécifier la priorité de réhydratation
1. Spécifier la durée pendant laquelle le point de récupération réhydraté doit être conservé dans le magasin de données du coffre
1. Effectuer la restauration sous forme de base de données à partir de ce point de récupération

Utilisez la commande suivante afin de préparer la demande pour toutes les opérations mentionnées ci-dessus en même temps.

```azurepowershell-interactive
$OssRestoreFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault -RehydrationDuration 12 -RehydrationPriority Standard
```

#### <a name="restore-as-files"></a>Restaurer sous forme de fichiers

Récupérez l’URI du conteneur dans le compte de stockage qui a reçu les autorisations indiquées [ci-dessus](#set-up-permissions), par exemple un conteneur nommé **testcontainerrestore** sous un compte de stockage **testossstorageaccount** avec un autre abonnement.

```azurepowershell-interactive
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

Utilisez la commande [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) pour préparer la demande de restauration avec tous les détails appropriés.

```azurepowershell-interactive
$OssRestoreAsFilesReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" 
```

Pour un point de récupération basé sur une archive, modifiez le magasin de données source, et ajoutez la priorité de réhydratation et la durée de conservation, en jours, du point de récupération réhydraté :

```azurepowershell-interactive
$OssRestoreAsFilesFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" -RehydrationDuration "14" -RehydrationPriority Standard
```

### <a name="trigger-the-restore"></a>Déclencher la restauration

Utilisez la commande [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) pour déclencher la restauration avec la demande préparée ci-dessus.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $OssRestoreReq
```

## <a name="tracking-job"></a>Suivi des travaux

Effectuez le suivi de tous les travaux à l’aide de la commande [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true). Vous pouvez répertorier tous les travaux et extraire le détail d’un travail particulier.

Vous pouvez également utiliser *Az.ResourceGraph* pour suivre tous les travaux dans l’ensemble des coffres de sauvegarde. Utilisez la commande [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) pour récupérer le travail approprié, quel que soit le coffre de sauvegarde.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -Operation OnDemandBackup
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la sauvegarde d’Azure PostgreSQL](backup-azure-database-postgresql-overview.md)
