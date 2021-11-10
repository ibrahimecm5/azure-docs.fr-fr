---
title: Migrer avec Azure Data Studio
description: Découvrez comment utiliser l’extension de Migration Azure SQL dans Azure Data Studio pour migrer des bases de données avec Azure Database Migration Service.
services: database-migration
author: mokabiru
ms.author: mokabiru
manager: ''
ms.reviewer: cheryl.adams
ms.service: dms
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: d2840b5e8386801411a759d1b00f026bfbacad68
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027695"
---
# <a name="migrate-databases-with-azure-sql-migration-extension-for-azure-data-studio-preview"></a>Migrer des bases de données avec l’extension de migration Azure SQL pour Azure Data Studio (version préliminaire)

L’[extension de migration Azure SQL pour Azure Data Studio](/sql/azure-data-studio/extensions/azure-sql-migration-extension) vous permet d’utiliser la nouvelle fonctionnalité d’évaluation et de migration de SQL Server dans Azure Data Studio.

## <a name="architecture-of-azure-sql-migration-extension-for-azure-data-studio"></a>Architecture de l’extension de migration Azure SQL pour Azure Data Studio

Azure Database Migration Service (DMS) est l’un des principaux composants de l’architecture globale. DMS fournit un orchestrateur de migration fiable pour permettre la migration de bases de données vers Azure SQL. Créez ou réutilisez un DMS existant à l’aide de l’extension de migration Azure SQL dans Azure Data Studio (ADS).
DMS utilise le runtime d’intégration auto-hébergé d’Azure Data Factory pour accéder aux fichiers de sauvegarde valides et les charger à partir de votre partage réseau local ou de votre compte stockage Azure.

Le flux de travail du processus de migration est illustré ci-dessous.

:::image type="content" source="media/migration-using-azure-data-studio/architecture-ads-sql-migration.png" alt-text="Diagramme de l’architecture pour la migration de base de données à l’aide de Azure Data Studio avec DMS":::

1. **SQL Server Source** : L’instance SQL Server en local, sur un cloud privé ou sur n’importe quelle machine virtuelle de cloud public. SQL Server 2008 et les versions ultérieures sont pris en charge.
1. **Azure SQL cible** : les cibles Azure SQL prises en charge sont Azure SQL Managed Instance ou SQL Server sur des machines virtuelles Azure (inscrites avec l’extension de l’agent SQL IaaS en [mode de gestion complète](../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md#management-modes))
1. **Partage de fichiers réseau** : partage de fichiers réseau SMB (Server Message Block) dans lequel les fichiers de sauvegarde sont stockés pour la ou les bases de données à migrer. Les conteneurs d’objets blob Stockage Azure et la partage de fichiers Stockage Azure sont également pris en charge.
1. **Azure Data Studio** : téléchargez et installez l'[extension de migration Azure SQL dans Azure Data Studio](/sql/azure-data-studio/extensions/azure-sql-migration-extension).
1. **Azure DMS** : service Azure qui orchestre les pipelines de migration pour effectuer des activités de déplacement de données d’un emplacement local vers Azure. DMS est associé à l’IR auto-hébergé d’Azure Data Factory (ADF) et offre la possibilité d’enregistrer et de surveiller l’IR auto-hébergé.
1. **IR auto-hébergé** : l’IR auto-hébergé doit être installé sur un ordinateur qui peut se connecter au SQL Server source et à l’emplacement des fichiers de sauvegarde. DMS fournit les clés d’authentification et inscrit l’IR auto-hébergé.
1. **Chargement des fichiers de sauvegarde Stockage Azure** : DMS utilise l’IR auto-hébergé pour charger les fichiers de sauvegarde valides à partir de l’emplacement de sauvegarde local vers votre compte Stockage Azure approvisionné. Les activités et pipelines de déplacement des données sont automatiquement créés dans le flux de travail de migration pour charger les fichiers de sauvegarde.
1. **Restaurer les sauvegardes sur Azure SQL cible** : DMS restaure les fichiers de sauvegarde à partir de votre compte Stockage Azure sur l’Azure SQL cible pris en charge. 
    > [!IMPORTANT]
    > Avec le mode de migration en ligne, DMS charge continuellement les fichiers de sauvegarde source vers Stockage Azure et les restaure à la cible jusqu’à ce que vous ayez terminé l’étape finale de la découpe de la cible.
    >
    > En mode migration hors connexion, DMS charge les fichiers de sauvegarde source dans Stockage Azure et les restaure sur la cible sans que vous ayez besoin d’effectuer un basculement.

## <a name="prerequisites"></a>Prérequis

Les conditions préalables associées à Azure Database Migration Service communes à tous les scénarios de migration pris en charge incluent le besoin de :

* [Télécharger et installer Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Installer l’extension de migration Azure SQL](/sql/azure-data-studio/extensions/azure-sql-migration-extension) à partir de la place de marché Azure Data Studio
* Disposer d’un compte Azure affecté à l’un des rôles intégrés listés ci-dessous :
    - Contributeur pour Azure SQL Managed Instance cible (et compte Stockage pour charger vos fichiers de sauvegarde de base de données à partir d’un partage réseau SMB).
    - Rôle de propriétaire ou de contributeur pour les groupes de ressources Azure contenant Azure SQL Managed Instance cible ou le compte de stockage Azure.
    - Rôle de propriétaire ou de contributeur pour l’abonnement Azure.
* Créez une [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md) ou un [SQL Server on Azure Virtual Machine](../azure-sql/virtual-machines/windows/create-sql-vm-portal.md) cible.
* Vérifiez que les comptes de connexion utilisés pour connecter l’instance SQL Server source sont membres du rôle serveur *sysadmin* ou ont l’autorisation `CONTROL SERVER`. 
* Utilisez l’une des options de stockage suivantes pour la base de données complète et les fichiers de sauvegarde du journal des transactions : 
    - Partage réseau SMB 
    - Partage de fichiers de compte de stockage Azure ou conteneur d’objets blob 

    > [!IMPORTANT]
    > - Si vos fichiers de sauvegarde de base de données sont fournis dans un partage réseau SMB, [Créez un compte de stockage Azure](../storage/common/storage-account-create.md) qui permet au service DMS de charger les fichiers de sauvegarde de la base de données.  Veillez à créer le compte de stockage Azure dans la même région que celle où l’instance d’Azure Database Migration Service est créée.
    > - Azure Database Migration Service ne lance pas les sauvegardes. Il utilise les sauvegardes existantes (qui peuvent déjà faire partie de votre plan de récupération d'urgence) pour la migration.
    > - Vous devez effectuer des [sauvegardes à l’aide de l' `WITH CHECKSUM` option](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server). 
    > - Chaque sauvegarde peut être enregistrée dans un fichier de sauvegarde distinct ou dans plusieurs fichiers de sauvegarde. Toutefois, l’ajout de plusieurs sauvegardes (par exemple, full et t-log) sur un seul support de sauvegarde n’est pas pris en charge. 
    > - Utilisez des sauvegardes compressées pour réduire le risque de problèmes liés à la migration de sauvegardes volumineuses.
* Assurez-vous que le compte de service exécutant l’instance source SQL Server dispose des autorisations en lecture et en écriture sur le partage réseau SMB qui contient les fichiers de sauvegarde de base de données.
* Le certificat d’instance source SQL Server d’une base de données protégée par Transparent Data Encryption (TDE) doit être migré vers Azure SQL Managed Instance ou SQL Server cible sur une machine virtuelle Azure avant de migrer les données. Pour plus d’informations, consultez [Migrer un certificat d’une base de données protégée par TDE vers Azure SQL Managed Instance](../azure-sql/managed-instance/tde-certificate-migrate.md) et [Déplacer une base de données protégée TDE vers une autre SQL Server](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
    > [!TIP]
    > Si votre base de données contient des données sensibles qui sont protégées par [Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio), le processus de migration à l’aide d’Azure Data Studio avec DMS migre automatiquement vos clés Always Encrypted vers votre Azure SQL Managed Instance ou SQL Server cible sur une machine virtuelle Azure.

* Si vos sauvegardes de base de données se trouvent dans un partage de fichiers réseau, fournissez un ordinateur pour installer l’[IR auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md) pour accéder et migrer les sauvegardes de base de données. L’Assistant Migration fournit le lien de téléchargement et les clés d’authentification pour télécharger et installer votre IR auto-hébergé. Pour préparer la migration, assurez-vous que l’ordinateur sur lequel vous prévoyez d’installer l’IR auto-hébergé possède les règles de pare-feu sortantes et les noms de domaine suivants activés :

    | Noms de domaine                                          | Ports sortants | Description                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | Cloud public : `{datafactory}.{region}.datafactory.azure.net`<br> ou `*.frontend.clouddatahub.net` <br> Azure Government : `{datafactory}.{region}.datafactory.azure.us` <br> Chine : `{datafactory}.{region}.datafactory.azure.cn` | 443            | Requis par l’IR auto-hébergé pour se connecter au service de migration des données. <br>Pour les nouvelles Data Factory créées dans le cloud public, localisez le nom de domaine complet à partir de votre clé de runtime d'intégration auto-hébergée, qui est au format `{datafactory}.{region}.datafactory.azure.net`. Pour une ancienne fabrique de données, si vous ne voyez pas le nom de domaine complet dans votre clé d’intégration auto-hébergée, utilisez *.frontend.clouddatahub.net à la place. |
    | `download.microsoft.com`    | 443            | Exigé par le runtime d’intégration auto-hébergé pour télécharger les mises à jour. Si vous avez désactivé la mise à jour automatique, vous pouvez ignorer la configuration de ce domaine. |
    | `*.core.windows.net`          | 443            | Utilisé par l’IR auto-hébergé qui se connecte au compte de stockage Azure pour charger les sauvegardes de base de données à partir de votre partage réseau |

    > [!TIP]
    > Si vos fichiers de sauvegarde de base de données sont déjà fournis dans un compte de stockage Azure, l’IR auto-hébergé n’est pas requis pendant le processus de migration.

* Lorsque vous utilisez l’IR auto-hébergé, assurez-vous que l’ordinateur sur lequel le runtime est installé peut se connecter à l’instance SQL Server et au partage de fichiers réseau source où se trouvent les fichiers de sauvegarde. Le port de sortie 445 doit être activé pour autoriser l’accès au partage de fichiers réseau.
* Si vous utilisez l’Azure Database Migration Service pour la première fois, assurez-vous que le fournisseur de ressources Microsoft.DataMigration est inscrit dans votre abonnement. Vous pouvez suivre les étapes pour [inscrire le fournisseur de ressources](./quickstart-create-data-migration-service-portal.md#register-the-resource-provider)

### <a name="recommendations-for-using-self-hosted-integration-runtime-for-database-migrations"></a>Recommandations pour l’utilisation de l’IR auto-hébergé pour les migrations de base de données
- Utilisez un seul IR auto-hébergé pour plusieurs bases de données de SQL Server sources.
- Installez une seule instance d’IR auto-hébergé sur une machine.
- Associer un seul runtime d’intégration auto-hébergé à un seul DMS.
- Le runtime d’intégration auto-hébergé utilise des ressources (mémoire/UC) sur l’ordinateur sur lequel il est installé. Installez le runtime d’intégration auto-hébergé sur un ordinateur différent de votre SQL Server source. Cela étant, la présence du runtime d’intégration auto-hébergé à proximité de la source de données réduit le temps de connexion du runtime d’intégration auto-hébergé à la source de données. 
- Utilisez le runtime d’intégration auto-hébergé uniquement lorsque vous avez vos sauvegardes de base de données dans un partage réseau SMB local. Le runtime d’intégration auto-hébergé n’est pas requis pour les migrations de base de données si vos sauvegardes de base de données source se trouvent déjà dans le conteneur d’objets blob Stockage Azure.
- Nous vous recommandons de configurer jusqu’à 10 migrations simultanées de bases de données par runtime d’intégration auto-hébergé sur un seul ordinateur. Pour augmenter le nombre de migrations de base de données simultanées, effectuez un scale-out du runtime auto-hébergé jusqu’à quatre nœuds ou créez un runtime d’intégration auto-hébergé distinct sur des ordinateurs différents.
- Configurez le runtime d’intégration auto-hébergé pour la mise à jour automatique afin d’appliquer automatiquement les nouvelles fonctionnalités, les correctifs de bogues et les améliorations publiées. Pour en savoir plus, consultez [Mise à jour automatique du runtime d'intégration auto-hébergé](../data-factory/self-hosted-integration-runtime-auto-update.md).

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations
- Le remplacement des bases de données existantes à l’aide de DMS dans votre Azure SQL Managed Instance ou SQL Server cible sur une machine virtuelle Azure n’est pas pris en charge.
- La configuration de la haute disponibilité et de la récupération d’urgence sur votre cible pour qu’elle corresponde à la topologie source n’est pas prise en charge par DMS.
- Les objets serveur suivants ne sont pas pris en charge :
    - Connexions
    - travaux de l'Agent SQL Server
    - Informations d'identification
    - Packages SSIS
    - Rôles serveur
    - Audit de serveur
- L’automatisation des migrations avec Azure Data Studio à l’aide de PowerShell/CLI n’est pas prise en charge.
- La migration vers Azure SQL Database n’est pas prise en charge.
- Les comptes de stockage Azure sécurisés par des règles de pare-feu spécifiques ou configurés avec un point de terminaison privé ne sont pas pris en charge pour les migrations.
- Vous ne pouvez pas utiliser un runtime d’intégration auto-hébergé existant créé à partir d’Azure Data Factory pour les migrations de base de données avec DMS. Au départ, le runtime d’intégration auto-hébergé doit être créé à l’aide de l’extension de migration Azure SQL dans Azure Data Studio et peut être réutilisé pour des migrations de base de données supplémentaires.
> [!IMPORTANT]
> **Problème connu lors de la migration de plusieurs bases de données vers SQL Server sur une machine virtuelle Azure :** la migration simultanée de plusieurs bases de données vers le même SQL Server sur une machine virtuelle Azure entraîne des échecs de migration pour la plupart des bases de données. Veillez à ne migrer qu’une seule base de données vers un SQL Server sur une machine virtuelle Azure à tout moment.  

## <a name="pricing"></a>Tarifs
- Azure Database Migration Service est libre d’utilisation avec l’extension de migration Azure SQL dans Azure Data Studio. Vous pouvez migrer plusieurs bases de données SQL Server à l’aide d’Azure Database Migration Service gratuitement pour l’utilisation du service ou de l’extension de migration Azure SQL.
- Il n’y a aucun déplacement de données ou coût d’entrée de données pour la migration de vos bases de données locales vers Azure. Si la base de données source est déplacée à partir d’une autre région ou d’une machine virtuelle Azure, vous pouvez avoir des [frais de bande passante](https://azure.microsoft.com/pricing/details/bandwidth/) basés sur votre fournisseur de bande passante et votre scénario de routage.
- Fournissez votre propre ordinateur ou serveur local pour installer Azure Data Studio.
- Un runtime d’intégration auto-hébergé est nécessaire pour accéder aux sauvegardes de base de données à partir de votre partage réseau local.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble et l’installation de l’extension de migration Azure SQL, consultez [Extension de migration Azure SQL pour Azure Data Studio](/sql/azure-data-studio/extensions/azure-sql-migration-extension).
