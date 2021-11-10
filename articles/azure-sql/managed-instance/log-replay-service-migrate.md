---
title: Migrer des bases de données vers SQL Managed Instance à l’aide du service LRS
description: Découvrez comment migrer des bases de données depuis SQL Server vers SQL Managed Instance à l’aide du service LRS (Log Replay Service)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: mathoma
ms.date: 09/21/2021
ms.openlocfilehash: 8dbe12ec428820f14cce427e4780ec4d5d4fd5c8
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893307"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Migrer des bases de données depuis SQL Server vers SQL Managed Instance à l’aide du service LRS (préversion)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Cet article explique comment configurer manuellement la migration de la base de données de SQL Server 2008-2019 vers Azure SQL Managed Instance à l’aide du service LRS actuellement disponible en préversion publique. LRS est un service Cloud activé pour SQL Managed Instance. Il est basé sur la technologie d’envoi de journaux SQL Server. 

[Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) et LRS utilisent la même technologie de migration sous-jacente et les mêmes API. En publiant LRS, nous mettons encore en œuvre des migrations personnalisées complexes et une architecture hybride entre le SQL Server local et SQL Managed Instance.

## <a name="when-to-use-log-replay-service"></a>Quand utiliser le service LRS

Lorsque vous ne pouvez pas utiliser Azure Database Migration Service pour la migration, vous pouvez utiliser LRS directement avec PowerShell, les cmdlets Azure CLI ou les API pour créer et orchestrer manuellement des migrations de base de données vers SQL Managed Instance. 

Vous pouvez envisager d’utiliser LRS dans les cas suivants :
- Vous avez besoin de plus de contrôle pour votre projet de migration de base de données.
- Il y a une faible tolérance aux temps d’arrêt lors du basculement de la migration.
- Le fichier exécutable Database Migration Service ne peut pas être installé dans votre environnement.
- Le fichier exécutable Database Migration Service ne dispose pas d’accès aux fichiers de sauvegarde de base de données.
- Aucun accès au système d’exploitation hôte ou aucun privilège d’administrateur n’est disponible.
- Il est impossible d’ouvrir des ports de réseau à partir de votre environnement vers Azure.
- La limitation de bande passante ou les problèmes de blocage de proxy dans votre environnement.
- Les sauvegardes sont stockées directement dans le Stockage Blob Azure à l’aide de l’option `TO URL`.
- Vous devez utiliser des sauvegardes différentielles.

> [!NOTE]
> Nous vous recommandons d’automatiser la migration des bases de données de SQL Server vers SQL Managed Instance à l’aide de Database Migration Service. Ce service utilise le même service Cloud LRS sur le back-end avec la copie des journaux de transaction en mode `NORECOVERY`. Envisagez d’utiliser LRS manuellement pour orchestrer des migrations lorsque Database Migration Service ne prend pas entièrement en charge vos scénarios.

## <a name="how-it-works"></a>Fonctionnement

La création d’une solution personnalisée utilisant le service LRS pour migrer des bases de données vers le Cloud nécessite plusieurs étapes d’orchestration présentées dans le diagramme et décrites dans le tableau plus loin dans cette section.

La migration consiste à effectuer des sauvegardes complètes de la base de données sur SQL Server avec `CHECKSUM` activé, et à copier les fichiers de sauvegarde dans le Stockage Blob Azure. Le service LRS est utilisé pour restaurer les fichiers de sauvegarde à partir du Stockage Azure vers SQL Managed Instance. Le Stockage Blob est un stockage intermédiaire entre SQL Server et SQL Managed Instance.

LRS surveille le stockage d’objets BLOB pour toutes les nouvelles sauvegardes (différentielles ou de journaux) ajoutées après la restauration de la sauvegarde complète. LRS restaure ensuite automatiquement ces nouveaux fichiers. Vous pouvez utiliser le service pour surveiller la progression des fichiers de sauvegarde en cours de restauration sur SQL Managed Instance, et vous pouvez arrêter le processus si nécessaire.

LRS ne nécessite pas de convention d’affectation de noms spécifique pour les fichiers de sauvegarde. Il analyse tous les fichiers placés dans le stockage d’objets BLOB et construit la chaîne de sauvegarde à partir de la lecture des en-têtes de fichier uniquement. Les bases de données sont dans un État de « restauration » pendant le processus de migration. Les bases de données sont restaurées en mode [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery). Elles ne peuvent donc pas être utilisées pour la lecture ou l’écriture tant que le processus de migration n’est pas terminé. 

Si vous effectuez la migration de plusieurs bases de données, vous devez :
 
- Placer les sauvegardes de chaque base de données dans un dossier distinct du stockage d’objets BLOB.
- Démarrez LRS séparément pour chaque base de données.
- Indiquez des chemins d’accès différents pour séparer les dossiers de stockage Blob. 

Vous pouvez démarrer LRS en mode *autocomplétion* ou en mode *continu* . Lorsque vous le démarrez en mode autocomplétion, la migration se termine automatiquement lorsque le dernier fichier de sauvegarde spécifié a été restauré. Lorsque vous démarrez LRS en mode continu, le service restaure en continu les nouveaux fichiers de sauvegarde ajoutés et la migration se termine uniquement lors du basculement manuel. 

Nous vous recommandons de basculer manuellement après la sauvegarde de la fin du journal quand il est restauré sur SQL Managed Instance. Lors de l’étape finale du basculement, la base de données est mise en ligne et disponible à des fins de lecture et d’écriture dans SQL Managed Instance.

Une fois le service LRS arrêté, soit automatiquement avec l’autocomplétion, soit manuellement lors du basculement, vous ne pouvez pas reprendre le processus de restauration pour une base de données qui a été mise en ligne dans SQL Managed Instance. Pour restaurer des fichiers de sauvegarde supplémentaires une fois la migration terminée par le biais de l’autocomplétion ou du basculement, vous devez supprimer la base de données. Vous devez également restaurer entièrement la chaîne de sauvegarde en redémarrant LRS.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagramme qui explique les étapes de l’orchestration du service LRS pour SQL Managed Instance." border="false":::
    
| Opération | Détails |
| :----------------------------- | :------------------------- |
| **1. Copier les sauvegardes des bases de données de SQL Server vers le Stockage Blob**. | Copiez les sauvegardes complètes, différentielles et de journal de SQL Server vers le conteneur Stockage Blob à l’aide de [AzCopy](../../storage/common/storage-use-azcopy-v10.md)ou de l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Utilisez n’importe quel nom de fichier. LRS ne nécessite pas une convention d’affectation de noms de fichiers.<br /><br />Lors de la migration de plusieurs bases de données, vous devez avoir un dossier distinct pour chaque base de données. |
| **2. Démarrez LRS dans le Cloud**. | Vous pouvez redémarrer le service avec un choix de cmdlets : PowerShell ([start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) ou Azure CLI ([cmdlet az_sql_midb_log_replay_start](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Démarrez LRS séparément pour chaque base de données qui pointe vers un dossier de sauvegarde dans le Stockage Blob. <br /><br /> Une fois démarré, le service prend les sauvegardes dans le conteneur Stockage Blob et commence à les restaurer dans SQL Managed Instance.<br /><br /> Si vous avez démarré LRS en mode continu, après la restauration de toutes les sauvegardes initialement téléchargées, le service surveille les nouveaux fichiers téléchargés dans le dossier. Le service applique continuellement les journaux en fonction de la chaîne du numéro séquentiel dans le journal (LSN) jusqu’à ce qu’il soit arrêté. |
| **2.1. Superviser la progression de l’opération**. | Vous pouvez surveiller la progression de l’opération de restauration avec un choix de cmdlets : PowerShell ([azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) ou Azure CLI ([cmdlet az_sql_midb_log_replay_show](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2.2. Arrêter l’opération si nécessaire**. | Si vous devez arrêter le processus de migration, vous avez le choix entre les cmdlets suivants : PowerShell ([stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) ou Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> L’arrêt de l’opération entraîne la suppression de la base de données que vous restaurez sur SQL Managed Instance. Après l’arrêt d’une opération, vous ne pouvez pas reprendre LRS pour une base de données. Vous devez redémarrer le processus de migration du début. |
| **3. Basculez le Cloud lorsque vous êtes prêt**. | Arrêtez l’application et la charge de travail. Chargez la dernière sauvegarde de la fin de journal dans le Stockage Blob Azure.<br /><br /> Terminez le basculement en lançant une opération LRS `complete` avec un choix de cmdlet : PowerShell ([complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) ou Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Cette opération arrêtera LRS et entraînera la mise en ligne de la base de données en vue d’une utilisation en mode lecture et écriture sur SQL Managed Instance.<br /><br /> Redirigez la chaîne de connexion de l’application de SQL Server vers SQL Managed Instance. Vous devez orchestrer cette étape vous-même, soit via une modification manuelle de la chaîne de connexion dans votre application, soit automatiquement (si votre application peut, par exemple, lire la chaîne de connexion à partir d’une propriété ou d’une base de données). |

## <a name="requirements-for-getting-started"></a>Conditions requises pour bien démarrer

### <a name="sql-server-side"></a>Côté SQL Server
- SQL Server 2008-2019
- Sauvegarde complète des bases de données (un ou plusieurs fichiers)
- Sauvegarde différentielle (un ou plusieurs fichiers)
- Sauvegarde de journal (non fractionnée pour un fichier journal des transactions)
- `CHECKSUM` activé pour les sauvegardes (obligatoire)

### <a name="azure-side"></a>Côté Azure
- Version 2.16.0 (ou ultérieure) du module PowerShell AZ.SQL ([installée](https://www.powershellgallery.com/packages/Az.Sql/) ou accessible via [Azure Cloud Shell](/azure/cloud-shell/))
- Version 2.19.0 (ou ultérieure) d’Azure CLI ([instalée](/cli/azure/install-azure-cli))
- Conteneur Stockage Blob Azure provisionné
- Jeton de sécurité SAS (signature d’accès partagé) avec uniquement les autorisations Lecture et Liste généré pour le conteneur Stockage Blob

### <a name="azure-rbac-permissions"></a>Autorisations Azure RBAC
L’exécution du service LRS via les clients fournis requiert l’un des rôles Azure suivants :
- Propriétaire de l’abonnement
- Rôle [Contributeur Managed Instance](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Rôle personnalisé avec l’autorisation suivante : `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Meilleures pratiques

Nous vous recommandons d'appliquer les méthodes conseillées ci-dessous :
- Exécutez l’[Assistant Migration de données](/sql/dma/dma-overview) pour confirmer que vos bases de données sont prêtes à être migrées vers SQL Managed Instance. 
- Fractionnez les sauvegardes complètes et différentielles en plusieurs fichiers, au lieu d’utiliser un seul fichier.
- Activez la compression de la sauvegarde.
- Utilisez Cloud Shell pour exécuter les scripts, car il dispose toujours des cmdlets les plus récents.
- Planifiez la fin de la migration dans les 36 heures suivant le démarrage du LRS. Il s’agit d’une période de grâce qui empêche l’installation de correctifs logiciels gérés par le système.
- Placez tous les fichiers de sauvegarde pour une base de données individuelle dans un dossier unique. N’utilisez pas de sous-dossiers pour la même base de données.

> [!IMPORTANT]
> - Vous ne pouvez pas utiliser la base de données en cours de restauration via LRS jusqu’à ce que le processus de migration se termine. 
> - L’accès en lecture seule aux bases de données pendant la migration n’est pas pris en charge par le service LRS.
> - Une fois la migration terminée, le processus de migration est finalisé, car LRS ne prend pas en charge la reprise du processus de restauration.

## <a name="steps-to-execute"></a>Étapes à exécuter

### <a name="make-backups-of-sql-server"></a>Effectuer des sauvegardes de SQL Server

Vous pouvez effectuer des sauvegardes de SQL Server avec l’une des options suivantes :

- Sauvegardez que le stockage du disque local, puis chargez les fichiers dans le Stockage Blob Azure, si votre environnement limite les sauvegardes directes au Stockage Blob.
- Sauvegardez directement dans le Stockage Blob avec l’option `TO URL` dans T-SQL, si votre environnement et vos procédures de sécurité l’autorisent. 

Configurez les bases de données que vous voulez migrer sur le mode de récupération complète pour autoriser les sauvegardes de journaux.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Pour effectuer manuellement des sauvegardes complètes, différentielles et de fichier journal de votre base de données sur le stockage local, utilisez l’exemple de script T-SQL suivant. Assurez-vous que l’option `CHECKSUM` est activée, car elle est obligatoire pour LRS.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Le Stockage Blob Azure est utilisé comme stockage intermédiaire pour les fichiers de sauvegarde entre SQL Server et SQL Managed Instance. Pour créer un compte de stockage et un conteneur Blob dans le compte de stockage, procédez comme suit :

1. [Créer un compte de stockage](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Créer un conteneur d’objets Blob](../../storage/blobs/storage-quickstart-blobs-portal.md) dans le compte de stockage.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Copier les sauvegardes de SQL Server vers le Stockage Blob

Lors de la migration de bases de données vers une instance gérée à l’aide de LRS, vous pouvez utiliser les méthodes suivantes pour charger les sauvegardes dans le Stockage Blob :
- Utiliser la fonction native [BACKUP TO URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) de SQL Server
- Utiliser [AzCopy](../../storage/common/storage-use-azcopy-v10.md) ou l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer) pour charger des sauvegardes dans un conteneur d’objets Blob
- Utiliser l’Explorateur Stockage dans le Portail Azure

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Effectuer des sauvegardes de SQL Server directement dans le Stockage Blob
Si vos stratégies d’entreprise et de réseau l’autorisent, une alternative consiste à effectuer des sauvegardes de SQL Server directement vers le Stockage Blob à l’aide de l’option native [BACKUP TO URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) de SQL Server. Si vous pouvez accéder à cette option, vous n’avez pas besoin d’effectuer des sauvegardes sur le stockage local et de les télécharger dans le Stockage Blob.

En tant que première étape, cette opération vous oblige à générer un jeton d’authentification SAS pour le Stockage Blob, puis à importer le jeton dans SQL Server. La deuxième étape consiste à effectuer des sauvegardes avec l’option `TO URL` dans T-SQL. Assurez-vous que toutes les sauvegardes sont effectuées avec l’option `CHEKSUM` activée.

Pour référence, l’exemple de code suivant effectue des sauvegardes dans le Stockage Blob. Cet exemple n’inclut pas d’instructions sur l’importation du jeton SAS. Vous trouverez des instructions détaillées, notamment sur la façon de générer et d’importer le jeton SAS dans SQL Server ans le tutoriel : [Utiliser le service Stockage Blob Azure avec SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Générer le jeton d’authentification SAS du Stockage Blob pour le service LRS

Le Stockage Blob Azure est utilisé comme stockage intermédiaire pour les fichiers de sauvegarde entre SQL Server et SQL Managed Instance. Vous devez générer un jeton d’authentification SAS, avec uniquement les autorisations de liste et de lecture, pour LRS. Ce jeton permet au service LRS d’accéder au Stockage Blob et d’utiliser les fichiers de sauvegarde pour les restaurer dans SQL Managed Instance. 

Effectuez ces étapes pour générer le jeton :

1. Ouvrez Explorateur Stockage à partir du Portail Azure.
2. Développez **Conteneurs d’objets blob**.
3. Cliquez-droit sur le conteneur Blob, puis sélectionnez **Obtenir une signature d’accès partagé**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Capture d’écran montrant les sélections permettant de générer un jeton d’authentification SAS.":::

4. Sélectionnez le délai d’expiration du jeton. Assurez-vous que le jeton est valide pour la durée de votre migration.
5. Sélectionnez le fuseau horaire pour le jeton : UTC ou votre heure locale.
    
   > [!IMPORTANT]
   > Le fuseau horaire du jeton et votre instance gérée peuvent ne pas correspondre. Assurez-vous que le jeton SAS a la validité de temps appropriée en tenant compte des fuseaux horaires. Si possible, définissez le fuseau horaire à une heure antérieure et ultérieure par rapport à celles de la fenêtre de votre migration planifiée.
6. Sélectionner uniquement les autorisations **Lecture** et **Liste**.

   > [!IMPORTANT]
   > Ne sélectionnez aucune autre autorisation. Sinon, LRS ne démarrera pas. Il s’agit d’une exigence de sécurité par défaut.
7. Sélectionnez **Create** (Créer).

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Capture d’écran montrant les sélections pour l’expiration du jeton SAS, le fuseau horaire et les autorisations, ainsi que le bouton Créer.":::

L’authentification SAS est générée avec la validité que vous avez indiquée. Vous avez besoin de la version d’URI du jeton, comme illustré dans la capture d’écran suivante.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Capture d’écran montrant un exemple de la version d’URI du jeton SAS.":::

### <a name="copy-parameters-from-the-sas-token"></a>Copier les paramètres à partir du jeton SAS

Avant d’utiliser le jeton SAS pour démarrer LRS, vous devez comprendre sa structure. L’URI du jeton SAS généré se compose de deux parties séparées par un point d’interrogation (`?`), comme illustré dans cet exemple :

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Exemple d’URI pour un jeton SAS généré pour le service LRS." border="false":::

La première partie commençant par `https://` jusqu’au point d’interrogation (`?`) est utilisée pour le paramètre `StorageContainerURI` qui est alimenté comme entrée au service LRS. Cela donne au service LRS des informations sur le dossier dans lequel les fichiers de sauvegarde de base de données sont stockés.

La deuxième partie, à partir du point d’interrogation (`?`) et jusqu’à la fin de la chaîne, est le paramètre `StorageContainerSasToken`. Il s’agit du jeton d’authentification signé lui-même, valide pour la durée indiquée. Cette partie ne doit pas nécessairement commencer par `sp=`, comme indiqué dans l’exemple. Votre cas est peut-être différent.

Copiez les paramètres comme suit :

1. Copiez la première partie du jeton, en commençant par `https://` jusqu’au point d’interrogation (`?`). Utilisez-le comme paramètre `StorageContainerUri` dans PowerShell ou Azure CLI pour démarrer LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Capture d’écran montrant la copie de la première partie du jeton.":::

2. Copiez la deuxième partie du jeton, en commençant par le point d’interrogation (`?`) jusqu’à la fin de la chaîne. Utilisez-le comme paramètre `StorageContainerSasToken` dans PowerShell ou Azure CLI pour démarrer LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Capture d’écran qui montre comment copier la deuxième partie du jeton.":::
   
> [!NOTE]
> N’incluez pas le point d’interrogation lorsque vous copiez l’une ou l’autre partie du jeton.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Se connecter à Azure et sélectionner un abonnement

Utilisez la cmdlet PowerShell suivante pour vous connecter à Azure :

```powershell
Login-AzAccount
```

Sélectionnez l’abonnement approprié dans lequel se trouve votre instance gérée à l’aide du cmdlet PowerShell suivant :

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Démarrer la migration

Pour démarrer la migration, démarrez LRS. Vous pouvez démarrer le service en mode autocomplétion ou en mode continu. 

Lorsque vous utilisez le mode autocomplétion, la migration se termine automatiquement lorsque le dernier fichier de sauvegarde spécifié a été restauré. Cette option requiert que la commande de démarrage spécifie le nom de fichier du dernier fichier de sauvegarde. 

Lorsque vous utilisez le mode continu, le service restaure continuellement les nouveaux fichiers de sauvegarde qui ont été ajoutés. La migration se terminera uniquement sur le basculement manuel. 

### <a name="start-lrs-in-autocomplete-mode"></a>Démarrer le service LRS en mode d’autocomplétion

Pour démarrer LRS en mode autocomplétion, utilisez les commandes PowerShell ou Azure CLI suivantes. Indiquez le nom du dernier fichier de sauvegarde à l’aide du paramètre `-LastBackupName`. Lors de la restauration des derniers fichiers de sauvegarde spécifiés, le service lance automatiquement le basculement.

Voici un exemple de démarrage de LRS en mode autocomplétion à l’aide de PowerShell :

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Voici un exemple de démarrage de LRS en mode autocomplétion à l’aide d’Azure CLI :

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Démarrer le service LRS en mode continu

Voici un exemple de démarrage de LRS en mode continu à l’aide de PowerShell :

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Voici un exemple de démarrage de LRS en mode continu à l’aide d’Azure CLI :

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Les clients PowerShell et CLI utilisés pour démarrer le service LRS en mode continu sont synchrones. Cela signifie que les clients attendent la réponse de l’API signalant la réussite ou l’échec du démarrage du travail. 

Pendant ce temps d’attente, la commande ne renvoie pas le contrôle à l’invite de commandes. Si vous créez un script pour l’expérience de migration et que vous avez besoin que la commande de démarrage du service LRS rende immédiatement le contrôle afin continuer avec le reste du script, vous pouvez exécuter PowerShell en tant que travail en arrière-plan avec le commutateur `-AsJob`. Par exemple :

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Quand vous démarrez un travail en arrière-plan, un objet de travail est immédiatement retourné, même si le travail prend plus de temps que prévu. Vous pouvez continuer à travailler dans la session sans interruption pendant l'exécution de la tâche. Pour plus d’informations sur l’exécution de PowerShell en tant que travail en arrière-plan, consultez la documentation [PowerShell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description).

De même, pour démarrer une commande Azure CLI sur Linux en tant que processus en arrière-plan, utilisez le signe esperluette (`&`) à la fin de la commande de démarrage du service LRS :

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Une fois que vous avez démarré le LRS, tous les patchs logiciels gérés par le système sont arrêtés pendant 36 heures. Après ce délai, le prochain correctif logiciel automatisé arrête automatiquement le service LRS. Si cela se produit, vous ne pouvez pas reprendre la migration et devez la redémarrer du début. 

## <a name="monitor-the-migration-progress"></a>Superviser la progression de la migration

Pour surveiller la progression de la migration via PowerShell, utilisez la commande suivante :

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Pour surveiller la progression de la migration via Azure CLI, utilisez la commande suivante :

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Arrêter la migration

Si vous devez arrêter la migration, utilisez les cmdlets suivants. L’arrêt de la migration entraîne la suppression de la base de données en cours de restauration sur SQL Managed Instance. Il n’est donc pas possible de reprendre la migration.

Pour arrêter le processus de migration par le biais de PowerShell, utilisez la commande suivante :

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Pour arrêter le processus de migration par le biais d’Azure CLI, utilisez la commande suivante :

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Terminer la migration (mode continu)

Si vous avez démarré LRS en mode continu, une fois que vous avez vérifié que toutes les sauvegardes ont été restaurées, le fait de lancer le basculement termine la migration. Après le basculement, la base de données est migrée et prête à des fins de lecture et d’écriture.

Pour terminer le processus de migration en mode continu du service LRS avec PowerShell, utilisez la commande suivante :

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Pour terminer le processus de migration en mode continu du service LRS avec Azure CLI, utilisez la commande suivante :

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

### <a name="migration-of-multiple-databases"></a>Migration de plusieurs bases de données
Vous devez placer les fichiers de sauvegarde de différentes bases dans des dossiers distincts dans le conteneur Stockage Blob Azure. Tous les fichiers de sauvegarde d’une base de données unique doivent être placés dans le même dossier, car il ne doit pas exister de sous-dossiers pour une base de données individuelle. LRS doit être démarré séparément pour chaque base de données qui pointe vers le chemin d’URI complet du conteneur Stockage Blob Azure et le dossier de la base de données individuelle.

Vous trouverez ci-dessous un exemple de la structure de dossiers et de la spécification d’URI requise lors de l’appel de LRS pour plusieurs bases de données. Démarrez LRS séparément pour chaque base de données, en spécifiant le chemin complet de l’URI vers le conteneur Stockage Blob Azure et le dossier de la base de données individuelle.

```URI
-- Place all backup files for database 1 in its own separate folder within a storage container. No further subfolders are allowed under database1 folder for this database.
https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/database1/<all database 1 backup files>

-- Place all backup files for database 2 in its own separate folder within a storage container. No further subfolders are allowed under database2 folder for this database.
https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/database2/<all database 2 backup files>

-- Place all backup files for database 2 in its own separate folder within a storage container. No further subfolders are allowed under database3 folder for this database.
https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/database3/<all database 3 backup files>
```

## <a name="functional-limitations"></a>Limitations fonctionnelles

Les limitations fonctionnelles de LRS sont les suivantes :
- La base de données que vous restaurez ne peut pas être utilisée pour l’accès en lecture seule pendant le processus de migration.
- Une fois que vous avez démarré le LRS, tous les patchs logiciels gérés par le système sont bloqués pendant 36 heures. Au-delà de ce délai, la mise à jour logicielle suivante arrête le service LRS. Vous devez ensuite recommencer le processus LRS du début.
- Le service LRS a besoin que les bases de données sur SQL Server soient sauvegardées avec l’option `CHECKSUM` activée.
- Le jeton SAS utilisé par LRS doit être généré pour l’ensemble du conteneur Stockage Blob Azure et doit avoir des autorisations de lecture et de liste uniquement.
- Les fichiers de sauvegarde de différentes bases de données doivent être placés dans des dossiers distincts dans le Stockage Blob.
- Les fichiers de sauvegarde dont le nom contient des caractères % et $ ne peuvent pas être utilisés par LRS. Vous devez renommer ces fichiers.
- Le placement de sauvegardes dans des sous-dossiers pour une base de données individuelle n’est pas pris en charge. Toutes les sauvegardes d’une base de données unique doivent être placées à la racine d’un dossier unique.
- Dans le cas de plusieurs bases de données, les fichiers de sauvegarde doivent être placés dans un dossier distinct pour chaque base de données. LRS doit être démarré séparément pour chaque base de données qui pointe vers le chemin d’URI complet contenant un dossier de base de données individuelle. 
- Le service LRS peut prendre en charge jusqu’à 100 processus de restauration simultanés pour une seule instance gérée.

## <a name="troubleshooting"></a>Dépannage

Une fois que vous avez démarré LRS, utilisez le cmdlet (`get-azsqlinstancedatabaselogreplay` ou `az_sql_midb_log_replay_show`) pour afficher l’état de l’opération. Si, après un certain temps, le service LRS ne parvient pas à démarrer et présente une erreur, recherchez les problèmes les plus courants :

- Une base de données existante sur SQL Managed Instance a-t-elle le même nom que celui que vous essayez de migrer à partir de SQL Server ? Résolvez ce conflit en renommant l’une des bases de données.
- La sauvegarde de base de données sur SQL Server a-t-elle été effectuée via l’option `CHECKSUM` ?
- Le jeton SAS a-t-il uniquement les autorisations de lecture et de liste pour LRS ?
- Avez-vous copié le jeton SAS pour LRS après le point d’interrogation (`?`), le contenu commençant comme suit : `sv=2020-02-10...` ? 
- Le délai de validité du jeton SAS est-il correct en fonction de la fenêtre de temps de démarrage et de fin de la migration ? Il peut y avoir des décalages en raison des différents fuseaux horaires utilisés pour SQL Managed Instance et le jeton SAS. Essayez de regénérer le jeton SAS en étendant la validité de la période de temps avant et après la date actuelle.
- Le nom de la base de données, le nom du groupe de ressources et le nom de l’instance gérée sont-ils correctement orthographiés ?
- Si vous avez démarré le service LRS en mode autocomplétion, le nom du dernier fichier de sauvegarde spécifié était-il valide ?

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur la [migration SQL Server vers SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- En savoir plus sur les [différences entre SQL Server et SQL Managed Instance](transact-sql-tsql-differences-sql-server.md).
- En savoir plus sur les [meilleures pratiques en termes de coût et de taille des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
