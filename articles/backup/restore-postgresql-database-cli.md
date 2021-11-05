---
title: Restaurer des bases de données Azure PostgreSQL avec Azure CLI
description: Découvrez comment restaurer des bases de données Azure PostgreSQL avec Azure CLI.
ms.topic: conceptual
ms.date: 10/25/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: e3b7260e58b738a2a15dae174adfefbc007644a2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097700"
---
# <a name="restore-azure-postgresql-databases-using-azure-cli"></a>Restaurer des bases de données Azure PostgreSQL avec Azure CLI

Cet article décrit comment restaurer des [bases de données Azure PostgreSQL](/azure/postgresql/overview#azure-database-for-postgresql---single-server) sur un serveur Azure PostgreSQL sauvegardé par la Sauvegarde Azure.

Comme il s’agit d’une base de données PaaS, l’option Récupération à l’emplacement d’origine (OLR) pour effectuer la restauration en remplaçant la base de données existante (d’où ont été effectuées les sauvegardes) n’est pas prise en charge. Vous pouvez effectuer une restauration à partir d’un point de récupération pour créer une base de données dans le même serveur Azure PostgreSQL ou un autre. C’est ce que l’on appelle la Récupération dans un autre emplacement (ALR) qui permet de conserver à la fois la base de données source et la (nouvelle) base de données restaurée.

Dans cet article, vous allez apprendre à :

- Effectuer une restauration pour créer une base de données PostgreSQL

- suivre l’état de l’opération de restauration.

Nous allons faire référence au coffre de sauvegarde existant _TestBkpVault_ du groupe de ressources _testBkpVaultRG_ dans les exemples.

## <a name="restore-a-backed-up-postgresql-database"></a>Restaurer une base de données PostgreSQL sauvegardée

### <a name="set-up-permissions"></a>Définir des autorisations

Le coffre de sauvegarde utilise une identité managée pour accéder aux autres ressources Azure. Pour effectuer la restauration à partir d’une sauvegarde, l’identité managée du coffre de sauvegarde nécessite un ensemble d’autorisations sur le serveur Azure PostgreSQL où la base de données doit être restaurée.

Pour attribuer les autorisations appropriées à l’identité managée affectée par le système du coffre sur le serveur PostgreSQL cible, consultez l’[ensemble d’autorisations nécessaires pour sauvegarder une base de données Azure PostgreSQL](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-restore).

Pour restaurer le point de récupération sous forme de fichiers dans un compte de stockage, l’[identité managée affectée par le système du coffre de sauvegarde a besoin d’accéder au compte de stockage cible](/azure/backup/restore-azure-database-postgresql#restore-permissions-on-the-target-storage-account).

### <a name="fetch-the-relevant-recovery-point"></a>Récupérer le point de récupération approprié

Pour lister toutes les instances de sauvegarde dans un coffre, utilisez la commande [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list), puis récupérez l’instance appropriée avec la commande [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show). Pour les scénarios [à grande échelle](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph), vous pouvez également lister les instances de sauvegarde de plusieurs coffres et abonnements en utilisant la commande _az dataprotection backup-instance list-from-resourcegraph_.

```azurecli
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL -subscriptions "xxxxxxxx-xxxx-xxxx-xxxx"

  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
        "resourceLocation": "westus",
        "resourceName": "postgres",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceUri": ""
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
        "resourceLocation": "westus",
        "resourceName": "testpostgresql",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceUri": ""
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "friendlyName": "testpostgresql\\empdb11",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
        "policyParameters": null,
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded",
      "validationType": null
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "testBkpVault",
    "zones": null
  }
.
.
.
.
.
```

Une fois l’instance identifiée, extrayez le point de récupération approprié à l’aide de la commande [az dataprotection recovery-point list](/cli/azure/dataprotection/recovery-point?view=azure-cli-latest&preserve-view=true#az_dataprotection_recovery_point_list).

```azurecli
az dataprotection recovery-point list --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 -g testBkpVaultRG --vault-name TestBkpVault

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/recoveryPoints/9da55e757af94261afa009b43cd3222a",
  "name": "9da55e757af94261afa009b43cd3222a",
  "properties": {
    "friendlyName": "2031fdb43a914114b6ce644eb6fcb5ce",
    "objectType": "AzureBackupDiscreteRecoveryPoint",
    "policyName": "oss-clitest-policy",
    "policyVersion": null,
    "recoveryPointDataStoresDetails": [
      {
        "creationTime": "2021-09-13T15:17:41.209845+00:00",
        "expiryTime": null,
        "id": "beddea84-7b30-42a5-a752-7c75baf96a52",
        "metaData": "{\"objectType\":\"PostgresBackupMetadata\",\"version\":\"1.0\",\"postgresVersion\":\"11\",\"dbName\":\"postgres\",\"serverName\":\"testpostgresql\",\"serverFQDN\":\"testpostgresql.postgres.database.azure.com\",\"usernameUsed\":\"backupadmin@testpostgresql\",\"backupToolPath\":\"postgresql-11.6-1\\\\bin\\\\pg_dump.exe\",\"backupType\":\"Full\",\"backupDumpFormat\":\"CUSTOM\",\"backupToolArgsFormat\":\"--no-acl --no-owner --serializable-deferrable --no-tablespaces --quote-all-identifiers -Fc -d postgres://{0}:{1}@{2}:5432/{3}?sslmode=verify-full&sslrootcert=E:\\\\approot\\\\Plugins\\\\Postgres\\\\..\\\\..\\\\postgres-root.crt\",\"storageUnits\":{\"1\":\"DbBackupDumpData\"},\"streamNamesInFirstStorageUnit\":[\"dbbkpdmpdatastream-1631546260050\"],\"pitId\":\"2031fdb43a914114b6ce644eb6fcb5ce\",\"bytesTransferred\":2063,\"dataSourceSize\":8442527,\"backupToolVersion\":\"11\"}",
        "rehydrationExpiryTime": null,
        "rehydrationStatus": null,
        "state": "COMMITTED",
        "type": "VaultStore",
        "visible": true
      }
    ],
    "recoveryPointId": "9da55e757af94261afa009b43cd3222a",
    "recoveryPointTime": "2021-09-13T15:17:41.209845+00:00",
    "recoveryPointType": "Full",
    "retentionTagName": "default",
    "retentionTagVersion": "637671427933449525"
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
```

Si vous avez besoin de récupérer le point de récupération dans le niveau archive, la variable _type_ dans _recoveryPointDataStoreDetails_ est _ArchiveStore_.

### <a name="prepare-the-restore-request"></a>Préparer la demande de restauration

Il existe différentes options de restauration pour une base de données PostgreSQL. Vous pouvez restaurer le point de récupération sous la forme d’une autre base de données ou de fichiers. Le point de récupération peut également se trouver dans le niveau archive.

#### <a name="restore-as-database"></a>Restaurer en tant que base de données

Construisez l’ID Azure Resource Manager (ARM) de la base de données PostgreSQL à créer (avec le serveur PostgreSQL cible qui a reçu des autorisations comme indiqué [ci-dessus](#set-up-permissions)) et le nom de base de données PostgreSQL nécessaire. Par exemple, une base de données PostgreSQL peut être nommée **emprestored21** sous un serveur PostgreSQL cible **targetossserver** dans le groupe de ressources **targetrg** avec un autre abonnement.

```azurecli
$targetOssId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

Utilisez la commande [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery) pour préparer la demande de restauration avec tous les détails appropriés.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" > OssRestoreReq.JSON
```

Pour un point de récupération basé sur une archive, vous devez :

1. Réhydrater la base de données du magasin de données d’archive dans le magasin du coffre
1. Modifier le magasin de données source.
1. Ajouter d’autres paramètres pour spécifier la priorité de réhydratation.
1. Spécifier la durée pendant laquelle le point de récupération réhydraté doit être conservé dans le magasin de données du coffre.
1. Effectuer la restauration sous forme de base de données à partir de ce point de récupération.

Utilisez la commande suivante afin de préparer la demande pour toutes les opérations mentionnées ci-dessus, en même temps.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" --rehydration-priority Standard --rehydration-duration 12 > OssRestoreFromArchiveReq.JSON
```

#### <a name="restore-as-files"></a>Restaurer sous forme de fichiers

Récupérez l’URI du conteneur dans le compte de stockage qui a reçu les autorisations, comme indiqué [ci-dessus](#set-up-permissions). Par exemple, un conteneur nommé **testcontainerrestore** sous un compte de stockage **testossstorageaccount** avec un autre abonnement.

```azurecli
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

Utilisez la commande [az dataprotection backup-instance restore initialize-for-data-recovery-as-files](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery_as_files) pour préparer la demande de restauration avec tous les détails appropriés.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a > OssRestoreAsFilesReq.JSON
```

Pour un point de récupération basé sur une archive, modifiez le magasin de données source, et ajoutez la priorité de réhydratation et la durée de conservation, en jours, du point de récupération réhydraté, comme indiqué ci-dessous :

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a --rehydration-priority Standard --rehydration-duration 12 > OssRestoreAsFilesReq.JSON
```

Vous pouvez également vérifier si le fichier JSON peut créer des ressources en utilisant la commande [az dataprotection backup-instance validate-for-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore).

### <a name="trigger-the-restore"></a>Déclencher la restauration

Utilisez la commande [az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) pour déclencher l’opération de restauration avec la demande préparée ci-dessus.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 --parameters OssRestoreReq.JSON
```

## <a name="tracking-job"></a>Suivi des travaux

Effectuez le suivi de tous les travaux à l’aide de la commande [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list). Vous pouvez répertorier tous les travaux et extraire le détail d’un travail particulier.

Vous pouvez également utiliser _Az.ResourceGraph_ pour suivre tous les travaux dans l’ensemble des coffres de sauvegarde. Utilisez la commande [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) pour obtenir le travail approprié sur l’ensemble des coffres de sauvegarde.

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --operation Restore
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la sauvegarde d’Azure PostgreSQL](backup-azure-database-postgresql-overview.md)
