---
title: Sauvegardes géoredondantes automatiques
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database et Azure SQL Managed Instance créent automatiquement une sauvegarde de base de données locale à des intervalles de quelques minutes et utilisent le stockage géoredondant avec accès en lecture Azure pour assurer la géoredondance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions, devx-track-azurepowershell
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma, wiassaf, danil
ms.date: 08/28/2021
ms.openlocfilehash: 884ae5d4677f33a4326ead8daae5d74b20d8596c
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555164"
---
# <a name="automated-backups---azure-sql-database--azure-sql-managed-instance"></a>Sauvegardes automatisées - Azure SQL Database et Azurer SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!div class="nextstepaction"]
> [Enquête pour améliorer Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="what-is-a-database-backup"></a>Qu’est-ce qu’une sauvegarde de base de données ?

Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions. Ces sauvegardes permettent de restaurer la base de données à un point dans le temps pendant la période de rétention configurée. Si vos règles de protection des données nécessitent que vos sauvegardes soient disponibles pendant une période prolongée (jusqu’à 10 ans), vous pouvez configurer une stratégie de [conservation à long terme](long-term-retention-overview.md) à la fois pour les bases de données uniques et mises en pool.

### <a name="backup-frequency"></a>Fréquence de sauvegarde

SQL Database et SQL Managed Instance utilisent la technologie SQL Server pour créer des sauvegardes [complètes](/sql/relational-databases/backup-restore/full-database-backups-sql-server) (chaque semaine), [différentielles](/sql/relational-databases/backup-restore/differential-backups-sql-server) (toutes les 12 à 24 heures) et du [journal des transactions](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) (toutes les 5 à 10 minutes). La fréquence des sauvegardes du journal des transactions est basée sur la taille de calcul et le volume d’activité de la base de données.

Quand vous restaurez une base de données, le service identifie les sauvegardes (complète, différentielle ou du journal des transactions) nécessitant une restauration.

### <a name="backup-storage-redundancy"></a>Redondance du stockage de sauvegarde

Par défaut, SQL Database et SQL Managed Instance stockent les données dans des [objets BLOB de stockage](../../storage/common/storage-redundancy.md) géoredondants qui sont répliqués dans une [région couplée](../../best-practices-availability-paired-regions.md). La géoredondance permet de se protéger contre les pannes impactant le stockage de sauvegarde dans la région primaire et vous permet de restaurer votre serveur dans une autre région en cas de sinistre. 

L’option de configuration de la redondance du stockage de sauvegarde offre la possibilité de choisir entre des objets blob de stockage localement redondant, redondant interzone ou géoredondant. Pour vous assurer que vos données restent dans la même région que celle où votre instance managée ou base de données SQL est déployée, vous pouvez modifier la redondance par défaut du stockage de sauvegarde géoredondant et configurer des blobs de stockage localement redondant ou redondant interzone pour les sauvegardes. La redondance de stockage stocke toujours plusieurs copies de vos données afin qu’elles soient protégées contre des événements planifiés ou non, notamment des défaillances matérielles temporaires, des pannes de réseau ou de courant et des catastrophes naturelles majeures. La redondance du stockage de sauvegarde configurée est appliquée aux paramètres de rétention de sauvegarde à court terme utilisés pour la récupération jusqu’à une date et heure (PITR) et les sauvegardes de rétention à long terme utilisées pour les sauvegardes à long terme (LTR). 

Pour SQL Database, la redondance du stockage de sauvegarde peut être configurée au moment de la création de la base de données ou peut être mise à jour pour une base de données existante ; les modifications apportées à une base de données existante s’appliquent uniquement aux sauvegardes ultérieures. Après la mise à jour de la redondance du stockage de sauvegarde d’une base de données existante, l’application des modifications peut prendre jusqu’à 48 heures. La géorestauration est désactivée dès qu’une base de données est mise à jour pour utiliser un stockage localement redondant ou redondant interzone. 


> [!IMPORTANT]
> La redondance du stockage de sauvegarde pour Hyperscale et SQL Managed Instance peut être définie uniquement durant la création de la base de données. Ce paramètre ne peut pas être modifié après le provisionnement de la ressource. Le processus de [copie de base de données](database-copy.md) peut être utilisé pour mettre à jour les paramètres de redondance du stockage de sauvegarde pour une base de données Hyperscale existante. 

> [!IMPORTANT]
> Pour le moment, le stockage redondant interzone est uniquement disponible dans [certaines régions](../../storage/common/storage-redundancy.md#zone-redundant-storage). 

> [!NOTE]
> La redondance configurable du stockage de sauvegarde pour Azure SQL Database est actuellement disponible uniquement en préversion publique dans toutes les régions Azure et mise à la disposition générale dans la région Azure Asie Sud-Est. 

### <a name="backup-usage"></a>Utilisation de la sauvegarde

Vous pouvez utiliser ces sauvegardes aux fins suivantes :

- **Restaurer une base de données existante** - [ à un point dans le temps situé dans le passé](recovery-using-backups.md#point-in-time-restore) pendant la période de rétention, à l’aide du portail Azure, d’Azure PowerShell, d’Azure CLI ou de l’API REST. Pour SQL Database, cette opération crée une nouvelle base de données sur le même serveur que la base de données d’origine, mais sous un nom différent pour éviter de remplacer la base de données d’origine. Une fois la restauration terminée, vous pouvez supprimer la base de données d’origine. Vous pouvez aussi [renommer](/sql/relational-databases/databases/rename-a-database) la base de données d’origine et renommer la base de données restaurée pour obtenir le nom de la base de données d’origine. De même, pour SQL Managed Instance, cette opération peut aussi créer une copie de la base de données sur une instance managée, identique ou non, dans le même abonnement et dans la même région.
- **Restaurer une base données à un instant dans le passé** - [Restaurer une base de données supprimée au moment de sa suppression](recovery-using-backups.md#deleted-database-restore) ou à tout point dans le temps pendant la période de rétention. La base de données supprimée ne peut être restaurée que sur le serveur ou la même instance gérée où la base de données d’origine a été créée. Lors de la suppression d’une base de données, le service effectue une sauvegarde finale du journal des transactions avant sa suppression, afin d’éviter toute perte de données.
- **Géo-restaurer** - [Restaurer une base de données dans une autre région géographique](recovery-using-backups.md#geo-restore). La géorestauration vous permet de procéder à la récupération après un sinistre géographique lorsque vous ne pouvez pas accéder à votre base de données ou aux sauvegardes dans la région principale. Cela crée une base de données sur un serveur ou une instance gérée existant(e), dans n’importe quelle région Azure.
   > [!IMPORTANT]
   > La géorestauration est disponible uniquement pour les bases de données SQL ou les instances gérées configurées avec un stockage de sauvegarde géoredondant.
- **Restaurer une base de données à partir d’une sauvegarde à long terme** - [Restaurer une base de données à partir d’une sauvegarde spécifique à long terme](long-term-retention-overview.md) d’une base de données unique ou mise en pool, si la base de données a été configurée avec une stratégie de conservation à long terme (LTR). La conservation à long terme (LTR) vous permet de [restaurer une ancienne version de la base de données](long-term-backup-retention-configure.md) à l’aide du portail Azure, d’Azure CLI ou d’Azure PowerShell pour répondre à une requête de conformité ou exécuter une ancienne version de l’application. Pour plus d’informations, consultez [Rétention à long terme](long-term-retention-overview.md).

> [!NOTE]
> Dans Stockage Azure, le terme *réplication* fait référence à la copie d’objets blob d’un emplacement à un autre. Dans SQL, la *réplication de base de données* fait référence aux diverses technologies utilisées pour conserver plusieurs bases de données secondaires synchronisées avec une base de données principale.

### <a name="restore-capabilities-and-features-of-azure-sql-database-and-azure-sql-managed-instance"></a><a id="restore-capabilities"></a>Capacités et fonctionnalités de restauration d’Azure SQL Database et Azure SQL Managed Instance

Ce tableau synthétise les capacités et fonctionnalités de la [récupération jusqu’à une date et heure](recovery-using-backups.md#point-in-time-restore), de la [géorestauration](recovery-using-backups.md#geo-restore) et des [sauvegardes avec conservation à long terme](long-term-retention-overview.md).

| **Propriétés de sauvegarde** | Récupération jusqu’à une date et heure | Géorestauration | Restauration de sauvegarde à long terme |           
|----|--|--|--|
| **Types de sauvegardes SQL** | Complète, différentielle, de fichier journal | Copies répliquées des sauvegardes avec récupération jusqu’à une date et heure | Uniquement les sauvegardes complètes | 
| **Objectif de point de récupération (RPO)** |  5-10 minutes selon la taille de calcul et le volume d’activité de la base de données. | Jusqu’à 1 heure en fonction de la géoréplication.\*  |  Une semaine (ou stratégie de l’utilisateur).|
| **Objectif de délai de récupération (RTO)** | La restauration prend généralement moins de 12 heures, mais elle peut prendre plus de temps selon la taille et l’activité. Consultez [Temps de récupération](recovery-using-backups.md#recovery-time). | La restauration prend généralement moins de 12 heures, mais elle peut prendre plus de temps selon la taille et l’activité. Consultez [Temps de récupération](recovery-using-backups.md#recovery-time). | La restauration prend généralement moins de 12 heures, mais elle peut prendre plus de temps selon la taille et l’activité. Consultez [Temps de récupération](recovery-using-backups.md#recovery-time). | 
| **Rétention** | 7 jours par défaut, jusqu’à 35 jours |  Activée par défaut, identique à la source.\*\* | Non activée par défaut, conservation jusqu’à 10 ans. |     
| **Azure Storage**  | Géoredondant par défaut. Possibilité de configuration d’un stockage localement redondant ou redondant interzone. | Disponible quand la redondance du stockage de sauvegarde avec récupération jusqu’à une date et heure est définie comme géoredondante. Non disponible quand le magasin de sauvegarde avec récupération jusqu’à une date et heure est un stockage localement redondant ou redondant interzone. | Géoredondant par défaut. Possibilité de configuration d’un stockage localement redondant ou redondant interzone. | 
| **Utilisation pour créer une base de données dans la même région** | Pris en charge | Prise en charge | Pris en charge |
| **Utilisation pour créer une base de données dans une autre région** | Non pris en charge | Prise en charge dans toutes les régions Azure | Prise en charge dans toutes les régions Azure |
| **Utilisation pour créer une base de données dans un autre abonnement** |  Non pris en charge  |  Non prise en charge\*\*\* | Non prise en charge\*\*\*  | 
| **Restauration par le biais du portail Azure**|Oui|Oui|Oui|
| **Restauration par le biais de PowerShell** |Oui|Oui|Oui| 
| **Restauration par le biais d’Azure CLI** |Oui|Oui|Oui| 
| | | | |

\*Pour les applications vitales pour l’entreprise qui nécessitent de grosses bases de données et doivent assurer la continuité de l’activité, utilisez des [groupes de basculement automatique](auto-failover-group-overview.md). 

\*\* Toutes les sauvegardes avec récupération jusqu’à une date et heure sont stockées sur un stockage géoredondant par défaut. La géorestauration est donc activée par défaut. 

\*\*\* La solution de contournement consiste à effectuer la restauration sur un nouveau serveur et à utiliser resource move pour déplacer le serveur vers un autre abonnement.

### <a name="restoring-a-database-from-backups"></a>Restauration d’une base de données à partir de sauvegardes 

Pour effectuer une restauration, consultez [Restaurer une base de données à partir de sauvegardes](recovery-using-backups.md). Vous pouvez essayer les opérations de configuration et de restauration de sauvegarde à l’aide des exemples suivants :

| Opération | Portail Azure | Azure CLI | Azure PowerShell |
|---|---|---|---|
| **Modifier la rétention des sauvegardes** | [Base de données SQL](#change-the-short-term-retention-policy-using-the-azure-portal) <br/> [SQL Managed Instance](#change-the-short-term-retention-policy-using-the-azure-portal) | [Base de données SQL](#change-the-short-term-retention-policy-using-azure-cli) <br/> [SQL Managed Instance](#change-the-short-term-retention-policy-using-azure-cli) | [Base de données SQL](#change-the-short-term-retention-policy-using-powershell) <br/>[SQL Managed Instance](#change-the-short-term-retention-policy-using-powershell) |
| **Modifier la rétention des sauvegardes à long terme** | [Base de données SQL](long-term-backup-retention-configure.md#create-long-term-retention-policies)<br/> [SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md) | [Base de données SQL](long-term-backup-retention-configure.md) <br/> [SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md) | [Base de données SQL](long-term-backup-retention-configure.md)<br/>[SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md)  |
| **Restaurer une base de données à partir d’un point dans le temps** | [Base de données SQL](recovery-using-backups.md#point-in-time-restore)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md) | [Base de données SQL](/cli/azure/sql/db#az_sql_db_restore) <br/> [SQL Managed Instance](/cli/azure/sql/midb#az_sql_midb_restore) | [Base de données SQL](/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL Managed Instance](/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Restaurer une base de données supprimée** | [Base de données SQL](recovery-using-backups.md)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [Base de données SQL](long-term-backup-retention-configure.md#restore-from-ltr-backups) <br/> [SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md#restore-from-ltr-backups) | [Base de données SQL](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL Managed Instance](/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Restaurer une base de données à partir d’un stockage Blob Azure** |  |  | <br/>[SQL Managed Instance](../managed-instance/restore-sample-database-quickstart.md) |

## <a name="backup-scheduling"></a>Planification de la sauvegarde

La première sauvegarde complète est planifiée immédiatement après la création ou la restauration d’une nouvelle base de données. Elle s’exécute généralement en 30 minutes, mais elle peut nécessiter davantage de temps si la base de données est volumineuse. Par exemple, la sauvegarde initiale peut prendre davantage de temps sur une base de données restaurée ou une copie de base de données, qui est généralement plus volumineuse qu’une nouvelle base de données. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées et gérées automatiquement. Le moment exact de toutes les sauvegardes de base de données est déterminé par le service SQL Database ou SQL Managed Instance en fonction de l’équilibrage de la charge de travail globale du système. Vous ne pouvez pas modifier la planification des travaux de sauvegarde ni les désactiver.

> [!IMPORTANT]
> Pour une base de données nouvelle, restaurée ou copiée, la fonction de restauration à un point dans le temps est disponible dès la création de la sauvegarde initiale du journal des transactions qui suit la sauvegarde complète initiale.

## <a name="backup-storage-consumption"></a>Consommation de stockage de sauvegarde

Avec la technologie de sauvegarde et de restauration SQL Server, la restauration d’une base de données à un point dans le temps requiert une chaîne de sauvegarde ininterrompue composée d’une sauvegarde complète, éventuellement d’une sauvegarde différentielle et d’une ou plusieurs sauvegardes du journal des transactions. La planification de sauvegarde SQL Database et SQL Managed Instance inclut une sauvegarde complète chaque semaine. Pour assurer la récupération jusqu’à une date et heure pour l’ensemble de la période de conservation, le système doit donc stocker des sauvegardes supplémentaires complètes, différentielles et du journal des transactions pendant une période plus longue d’une semaine que la période de conservation configurée. 

En d’autres termes, pour tout point dans le temps pendant la période de rétention, il doit y avoir une sauvegarde complète antérieure à la période de rétention la plus ancienne, ainsi qu’une chaîne ininterrompue de sauvegardes différentielles et du journal des transactions à partir de cette sauvegarde complète jusqu’à la prochaine sauvegarde complète.

> [!NOTE]
> Pour assurer la récupération jusqu’à une date et heure, les sauvegardes supplémentaires sont stockées pendant une période plus longue d’une semaine que la période de conservation configurée. Le stockage de sauvegarde est facturé au même tarif pour toutes les sauvegardes. 

Les sauvegardes qui ne sont plus nécessaires pour fournir la fonctionnalité PITR sont automatiquement supprimées. Étant donné que les sauvegardes différentielles et les sauvegardes de fichiers journaux requièrent une sauvegarde complète antérieure pour pouvoir être restaurées, les trois types de sauvegardes sont purgés par jeux hebdomadaires.

Pour toutes les bases de données, notamment les bases de données [chiffrées TDE](transparent-data-encryption-tde-overview.md), les sauvegardes sont compressées afin de réduire les coûts et la compression du stockage de sauvegarde. Le taux moyen de compression des sauvegardes est de 3-4 fois, mais il peut être nettement plus faible ou plus élevé selon la nature des données et de l’utilisation ou non de la compression des données dans la base de données.

SQL Database et SQL Managed Instance calculent votre stockage de sauvegarde total utilisé en tant que valeur cumulée. Toutes les heures, cette valeur est consignée dans le pipeline de facturation Azure, qui est responsable de l’agrégation de cette utilisation horaire afin de calculer votre consommation à la fin de chaque mois. Une fois la base de données supprimée, la consommation diminue à mesure que les sauvegardes vieillissent et sont supprimées. Une fois que toutes les sauvegardes ont été supprimées et que la récupération jusqu’à une date et heure (PITR) n’est plus possible, la facturation s’arrête.
   
> [!IMPORTANT]
> Les sauvegardes d’une base de données sont conservées pour assurer la récupération jusqu’à une date et heure même si la base de données a été supprimée. Si la suppression et la recréation d’une base de données peuvent réduire les coûts de stockage et de calcul, cela peut augmenter les coûts de stockage de sauvegarde, car le service conserve les sauvegardes pour chaque base de données supprimée, à chaque fois qu’elle est supprimée. 

### <a name="monitor-consumption"></a>Surveiller la consommation

Pour les bases de données vCore, le stockage consommé par chaque type de sauvegarde (complète, différentielle et de fichier journal) est signalé sur le volet de supervision de la base de données sous la forme d’une métrique distincte. Le diagramme suivant montre comment surveiller la consommation de stockage des sauvegardes pour une base de données unique. Cette fonctionnalité n’est pas disponible actuellement pour les instances gérées.

![Surveiller la consommation de sauvegarde de base de données dans le portail Azure](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Ajuster la consommation de stockage de sauvegarde

La consommation du stockage de sauvegarde jusqu’à la taille maximale des données pour une base de données n’est pas facturée. Une consommation de stockage de sauvegarde excessive dépend de la charge de travail et de la taille maximale des bases de données individuelles. Envisagez les diverses techniques d’ajustement suivantes pour réduire votre consommation de stockage de sauvegarde :

- Réduisez la [période de rétention de la sauvegarde](#change-the-short-term-retention-policy-using-the-azure-portal) au minimum possible pour vos besoins.
- Évitez d’effectuer des opérations d’écriture volumineuses telles que des reconstructions d’index plus qu’il n’est nécessaire.
- Pour les opérations de chargement de données volumineuses, envisagez d’utiliser des [index columnstore en cluster](/sql/relational-databases/indexes/columnstore-indexes-overview) et de suivre les [bonnes pratiques](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) connexes, et/ou de réduire le nombre d’index non en cluster.
- Au niveau de service Usage général, le stockage de données provisionné est moins onéreux que le prix du stockage de sauvegarde. Si vos coûts de stockage de sauvegarde sont sans cesse excessifs, vous pouvez envisager d’augmenter le stockage de données afin de réaliser des économies sur le stockage de sauvegarde.
- Utilisez TempDB au lieu de tables permanentes dans votre logique d’application pour le stockage des résultats et/ou des données temporaires.
- Utilisez le stockage de sauvegarde localement redondant chaque fois que cela est possible (par exemple, environnements de développement/test)

## <a name="backup-retention"></a>Rétention des sauvegardes

La Base de données SQL Azure et l’Instance managée SQL Azure fournissent toutes deux une rétention à court et long terme des sauvegardes. Les sauvegardes de rétention à long terme autorisent une limite de restauration dans le temps (PITR) avec la période de rétention de la base de données, tandis que la rétention à long terme fournit des sauvegardes pour différentes exigences de conformité.  

### <a name="short-term-retention"></a>Durée de rétention à court terme

Pour toutes les bases de données nouvelles, restaurées et copiées, Azure SQL Database et Azure SQL Managed Instance conservent des sauvegardes suffisantes pour autoriser la récupération jusqu’à une date et heure au cours des sept derniers jours par défaut. Des sauvegardes complètes, différentielles et de journal régulières sont effectuées pour garantir que les bases de données peuvent être restaurées à n’importe quel point dans le temps au cours de la période de rétention définie pour la base de données ou l’instance managée. En outre, pour les bases de données Azure SQL, les sauvegardes différentielles peuvent être configurées sur une fréquence de 12 heures ou 24 heures. 

> [!NOTE]
> Une fréquence de sauvegarde différentielle de 24 heures peut augmenter le temps nécessaire pour restaurer la base de données. 

À l’exception des bases de données des niveaux Hyperscale et De base, vous pouvez [modifier la période de conservation de la sauvegarde](#change-the-short-term-retention-policy) selon une plage comprise entre 1 et 35 jours pour chaque base de données active. Comme décrit dans [Consommation du stockage de sauvegarde](#backup-storage-consumption), les sauvegardes stockées pour activer la récupération jusqu’à une date et heure (PITR) peuvent être antérieures à la période de rétention. Pour Azure SQL Managed Instance uniquement, il est possible de définir le taux de rétention des sauvegardes PITR une fois qu’une base de données a été supprimée dans la plage de 0-35 jours. 

Si vous supprimez une base de données, le système conserve les sauvegardes de la même façon que pour une base de données en ligne avec sa période de rétention spécifique. Vous ne pouvez pas modifier la période de rétention de sauvegarde pour une base de données supprimée.

> [!IMPORTANT]
> Si vous supprimez un serveur ou une instance managée, toutes les bases de données sur ce serveur ou cette instance sont également supprimées et ne peuvent pas être récupérées. Vous ne pouvez pas restaurer un serveur ou une instance gérée supprimé(e). Toutefois, si vous avez configuré la rétention à long terme (LTR) pour une base de données ou une instance gérée, les sauvegardes de rétention à long terme ne sont pas supprimées et peuvent être utilisées pour restaurer des bases de données sur un(e) autre serveur ou instance gérée dans le même abonnement, à un point dans le temps où une sauvegarde de rétention à long terme a été effectuée.

La rétention des sauvegardes à des fins de récupération jusqu’à une date et heure (PITR) dans les 1-35 derniers jours est parfois appelée rétention des sauvegardes à court terme. Si vous avez besoin de conserver les sauvegardes pendant plus longtemps que la période de rétention à court terme maximale de 35 jours, vous pouvez activer la [rétention à long terme](long-term-retention-overview.md).

### <a name="long-term-retention"></a>Rétention à long terme

Pour SQL Database et SQL Managed Instance, vous pouvez configurer une conservation à long terme (LTR) des sauvegardes complètes allant jusqu’à 10 ans dans un stockage Blob Azure. Si la stratégie de conservation à long terme est activée, les sauvegardes complètes hebdomadaires sont automatiquement copiées vers un autre conteneur de stockage. Pour répondre aux diverses exigences de conformité, vous pouvez sélectionner plusieurs périodes de rétention pour les sauvegardes complètes hebdomadaires, mensuelles et/ou annuelles. La consommation du stockage dépend de la fréquence sélectionnée des sauvegardes LTR et des périodes de conservation. Vous pouvez utiliser la [calculatrice de prix LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) pour estimer le coût du stockage de conservation à long terme.

> [!IMPORTANT]
> La mise à jour de la redondance du stockage de sauvegarde pour une base de données Azure SQL existante s’applique uniquement aux sauvegardes ultérieures effectuées pour la base de données. Toutes les sauvegardes LTR existantes pour la base de données continueront de résider dans le blob de stockage existant et les nouvelles sauvegardes seront stockées sur le type de blob de stockage demandé. 

Pour plus d’informations sur la conservation à long terme, consultez [Conservation des sauvegardes à long terme](long-term-retention-overview.md).

## <a name="backup-storage-costs"></a>Coûts du stockage de sauvegarde

Le prix du stockage de sauvegarde varie et dépend de votre modèle d’achat (DTU ou vCore), de l’option de redondance de stockage de sauvegarde choisie et également de votre région. Le stockage de sauvegarde est facturé par Go/mois consommés. Pour connaître la tarification, consultez la page [Tarification Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) et la page [Tarification Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/). 

Pour plus d’informations sur les modèles d’achat, consultez [Choisir entre les modèles d’achat vCore et DTU](purchasing-models.md).

> [!NOTE]
> La facture Azure n’indiquera que l’excédent de stockage de sauvegarde consommé, et non la consommation totale de stockage de sauvegarde. Par exemple, dans un scénario hypothétique, si vous avez configuré 4 To de stockage de données, vous obtiendrez 4 To d’espace de stockage de sauvegarde gratuit. Si vous avez utilisé le total de 5,8 To d’espace de stockage de sauvegarde, la facture Azure n’indiquera que 1,8 To, car seul le stockage de sauvegarde excédentaire utilisé est facturé.

### <a name="dtu-model"></a>Modèle DTU

Dans le modèle DTU, aucuns frais supplémentaires ne sont facturés pour le stockage de sauvegarde des bases de données et des pools élastiques. Le prix du stockage de sauvegarde est inclus dans le prix de la base de données ou du pool.

### <a name="vcore-model"></a>Modèle vCore

Pour les bases de données uniques dans SQL Database, une quantité de stockage de sauvegarde égale à 100 % de la taille maximale de stockage des données pour la base de données est fournie sans frais supplémentaires. Pour les pools élastiques et les instances gérées, une quantité de stockage de sauvegarde égale à 100 % du stockage maximal de données pour le pool ou la taille maximale de stockage d’instance, respectivement, est fournie sans frais supplémentaires. 

Pour les bases de données uniques, cette équation est utilisée pour calculer l’utilisation totale du stockage de sauvegarde facturable :

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

Pour les bases de données mises en pool, la taille totale de stockage de sauvegarde facturable est agrégée au niveau du pool et calculée comme suit :

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

Pour les instances gérées, la taille totale de stockage de sauvegarde facturable est agrégée au niveau de l’instance et calculée comme suit :

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

Le stockage de sauvegarde total facturable, le cas échéant, sera facturé en Go/mois conformément au tarif de la redondance du stockage de sauvegarde utilisé. Cette consommation de stockage de sauvegarde dépend de la charge de travail et de la taille des bases de données, des pools élastiques et des instances gérées individuels. Les bases de données fortement modifiées ont des sauvegardes différentielles et de fichier journal plus volumineuses, car la taille de ces sauvegardes est proportionnelle à la quantité de données modifiées. Par conséquent, ces bases de données ont des frais de sauvegarde plus élevés.

SQL Database et SQL Managed Instance calculent votre stockage total de sauvegarde facturable en tant que valeur cumulée pour tous les fichiers de sauvegarde. Toutes les heures, cette valeur est consignée dans le pipeline de facturation Azure, qui agrège cette utilisation horaire afin d’obtenir votre consommation de stockage de sauvegarde à la fin de chaque mois. Si une base de données est supprimée, la consommation de stockage de sauvegarde diminue progressivement à mesure que les sauvegardes plus anciennes vieillissent et sont supprimées. Étant donné que les sauvegardes différentielles et les sauvegardes de fichiers journaux requièrent une sauvegarde complète antérieure pour pouvoir être restaurées, les trois types de sauvegardes sont purgés par jeux hebdomadaires. Une fois toutes les sauvegardes supprimées, la facturation s’arrête. 

À titre d’exemple simplifié, supposons qu’une base de données ait accumulé 744 Go de stockage de sauvegarde et que cette quantité reste constante pendant un mois entier, car la base de données est complètement inactive. Pour convertir cette consommation de stockage cumulée en une utilisation horaire, nous la divisons par 744,0 (31 jours par mois * 24 heures par jour). SQL Database signale au pipeline de facturation Azure que la base de données a consommé 1 Go de sauvegarde PITR chaque heure, à un taux constant. La facturation Azure agrègera cette consommation et affichera une utilisation de 744 Go pour le mois. Le coût sera basé sur le taux par quantité/Go/mois dans votre région.

À présent, un exemple plus complexe. Supposons que pour la même base de données inactive, la conservation est passée de 7 à 14 jours au milieu du mois. Cette augmentation entraîne un doublement du stockage total de sauvegarde, qui passe à 1 488 Go. SQL Database signale 1 Go d’utilisation pour les heures 1 à 372 (la première moitié du mois). Il signale l’utilisation de 2 Go pour les heures 373 à 744 (la deuxième moitié du mois). L’agrégation de cette utilisation aboutit à une facturation finale de 1,116 Go/mois.

Les scénarios de facturation de sauvegarde réels sont plus complexes. Étant donné que le taux de modifications dans la base de données dépend de la charge de travail et varie au fil du temps, la taille de chaque sauvegarde différentielle et de journal varie également, provoquant la fluctuation de la consommation de stockage de sauvegarde horaire. En outre, chaque sauvegarde différentielle contient toutes les modifications apportées dans la base de données depuis la dernière sauvegarde complète ; par conséquent, la taille totale de toutes les sauvegardes différentielles augmente progressivement au cours d’une semaine, puis chute brusquement une fois qu’un ensemble plus ancien de sauvegardes complètes, différentielles et de journaux arrive à expiration. Par exemple, si une activité d’écriture intensive telle que la reconstruction d’index a été exécutée juste après la fin d’une sauvegarde complète, alors les modifications apportées par la reconstruction de l’index seront incluses dans les sauvegardes du journal des transactions effectuées pendant la durée de la reconstruction, dans la prochaine sauvegarde différentielle et dans chaque sauvegarde différentielle effectuée jusqu’à la prochaine sauvegarde complète. Pour le dernier scénario dans les bases de données plus volumineuses, une optimisation du service crée une sauvegarde complète au lieu d’une sauvegarde différentielle si une sauvegarde différentielle devait être excessivement importante. Cela réduit la taille de toutes les sauvegardes différentielles jusqu’à la prochaine sauvegarde complète.

Vous pouvez surveiller la consommation totale du stockage de sauvegarde pour chaque type de sauvegarde (complète, différentielle, journal des transactions) au fil du temps, comme décrit dans [Surveiller la consommation](#monitor-consumption).

### <a name="backup-storage-redundancy"></a>Redondance du stockage de sauvegarde

La redondance du stockage de sauvegarde a un impact sur les coûts de sauvegarde de la façon suivante :
- prix localement redondant = x
- prix localement redondant = 1,25 x
- prix géoredondant = 2x

Pour connaître la tarification du stockage de sauvegarde, consultez la page [Tarification Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) et la page [Tarification Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

> [!IMPORTANT]
> La redondance configurable du stockage de sauvegarde pour SQL Managed Instance est disponible dans toutes les régions Azure et actuellement disponible dans la région Azure Asie Sud-Est uniquement pour SQL Database. Pour Managed Instance, elle ne peut être spécifiée que pendant le processus de création d’une instance gérée. Une fois que la ressource est approvisionné, vous ne pouvez pas modifier l’option de redondance du stockage de sauvegarde.

### <a name="monitor-costs"></a>Superviser les coûts

Pour comprendre les coûts de stockage des sauvegardes, accédez à **Gestion des coûts + Facturation** dans le portail Azure, sélectionnez **Gestion des coûts**, puis sélectionnez **Analyse du coût**. Sélectionnez l’abonnement souhaité comme **Étendue**, puis filtrez la période et le service qui vous intéressent.

Ajoutez un filtre pour **Nom de service**, puis choisissez **sql database** dans la liste déroulante. Utilisez le filtre **Meter subcategory** (Sous-catégorie du compteur) pour choisir le compteur de facturation pour votre service. Pour une base de données unique ou un pool de bases de données élastique, sélectionnez **stockage de sauvegarde PITR pour pool élastique/unique**. Pour une instance gérée, sélectionnez **stockage de sauvegarde PITR pour instance gérée**. Les sous-catégories **Stockage** et **Calcul** peuvent vous également intéresser, mais elles ne sont pas associées à des coûts de stockage de sauvegarde.

![Analyse du coût du stockage de sauvegarde](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > Les guichets sont visibles uniquement pour les compteurs en cours d’utilisation. Si un compteur n’est pas disponible, il est probable que la catégorie ne soit pas en cours d’utilisation. Par exemple, les compteurs d’instance managée ne seront pas présents pour les clients qui n’ont pas d’instance gérée déployée. De même, les compteurs de stockage ne seront pas visibles pour les ressources qui ne consomment pas de stockage. 

Pour plus d’informations, consultez [Gérer les coûts d’Azure SQL Database](cost-management.md).

## <a name="encrypted-backups"></a>Sauvegardes chiffrées

Si votre base de données est chiffrée à l’aide de TDE, les sauvegardes sont automatiquement chiffrées au repos, y compris les sauvegardes LTR. Par défaut, TDE est activé sur toutes les nouvelles bases de données dans Azure SQL. Pour plus d’informations sur TDE, consultez [Chiffrement transparent des données avec SQL Database et SQL Managed Instance](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Intégrité de la sauvegarde

L’équipe d’ingénieurs Azure SQL teste régulièrement et automatiquement la restauration des sauvegardes automatisées de bases de données. (Actuellement, ces tests ne sont pas disponibles dans SQL Managed Instance. Vous devez planifier DBCC CHECKDB sur vos bases de données dans SQL Managed Instance en fonction de votre charge de travail.)

Lors d’une restauration à un point dans le temps, les bases de données font également l’objet de vérifications d’intégrité DBCC CHECKDB.

Tout problème détecté lors de la vérification d’intégrité est traduit par une alerte envoyée à l’équipe d’ingénieurs. Pour plus d’informations, consultez [Intégrité des données dans SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

Toutes les sauvegardes de base de données sont effectuées avec l’option CHECKSUM pour fournir une intégrité de sauvegarde supplémentaire.

## <a name="compliance"></a>Conformité

Lorsque vous migrez votre base de données à partir d’un niveau de service basé sur DTU vers un niveau de service basé sur vCore, la conservation PITR est préservée pour garantir que la stratégie de récupération de données de votre application ne soit pas compromise. Si la conservation par défaut ne répond pas à vos exigences de conformité, vous pouvez modifier la période de conservation PITR. Pour plus d’informations, consultez [Modifier la période de rétention des sauvegardes PITR](#change-the-short-term-retention-policy).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-short-term-retention-policy"></a>Modifier la stratégie de rétention à court terme

Vous pouvez modifier la période de rétention des sauvegardes PITR par défaut et la fréquence de sauvegarde différentielle à l’aide du portail Azure, de PowerShell ou de l’API REST. Les exemples suivants montrent comment modifier la rétention PITR à 28 jours et les sauvegardes différentielles à un intervalle de 24 heures.

> [!WARNING]
> Si vous réduisez la période de rétention actuelle, vous perdez la possibilité de restaurer à des points dans le temps antérieurs à la nouvelle période de rétention. Les sauvegardes qui ne sont plus nécessaires pour fournir la fonctionnalité PITR dans la nouvelle période de rétention sont supprimées. Si vous augmentez la période de rétention actuelle, vous n’avez pas immédiatement la possibilité de restaurer à des points dans le temps antérieurs dans la nouvelle période de rétention. Vous obtenez cette possibilité dans le temps, car le système commence à conserver les sauvegardes plus longtemps.

> [!NOTE]
> Ces API impactent uniquement la période de rétention avec récupération jusqu’à une date et heure. Si vous avez configuré la conservation à long terme pour votre base de données, cette configuration n’est pas affectée. Pour plus d’informations sur la modification des périodes de conservation à long terme, consultez [Conservation à long terme](long-term-retention-overview.md).

### <a name="change-the-short-term-retention-policy-using-the-azure-portal"></a>Modifier la stratégie de rétention à court terme à l’aide du portail Azure

Pour modifier la période de rétention des sauvegardes PITR ou la fréquence de sauvegarde différentielle pour les bases de données actives via le Portail Azure, accédez au serveur ou à l’instance managée contenant les bases de données dont vous souhaitez modifier la période de rétention. Sélectionnez **Sauvegardes** dans le volet gauche, puis sélectionnez l’onglet **Stratégies de conservation**. Sélectionnez la ou les bases de données pour lesquelles vous souhaitez modifier la conservation de la sauvegarde PITR. Sélectionnez ensuite **Configurer la conservation** dans la barre d’action.

#### <a name="sql-database"></a>[Base de données SQL](#tab/single-database)

![Modifier la conservation des données avec récupération jusqu’à une date et heure au niveau serveur](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

![Modifier la conservation des données avec récupération jusqu’à une date et heure, instance gérée](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-short-term-retention-policy-using-azure-cli"></a>Modifier la stratégie de conservation à court terme à l’aide d’Azure CLI

Préparez votre environnement pour l’interface Azure CLI.

[!INCLUDE[azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

#### <a name="sql-database"></a>[Base de données SQL](#tab/single-database)

Modifiez la durée de conservation des sauvegardes PITR (limite de restauration dans le temps) et la fréquence des sauvegardes différentielles pour les bases de données Azure SQL actives à l’aide de l’exemple suivant.

```azurecli
# Set new PITR differential backup frequency on an active individual database
# Valid backup retention must be between 1 and 35 days
# Valid differential backup frequency must be ether 12 or 24
az sql db str-policy set \
    --resource-group myresourcegroup \
    --server myserver \
    --name mydb \
    --retention-days 28 \
    --diffbackup-hours 24
```

#### <a name="sql-database"></a>[Base de données SQL](#tab/managed-instance)

Utilisez l’exemple suivant pour changer la conservation des sauvegardes PITR d’une base de données **unique active** dans une instance managée SQL.

```azurecli
# Set new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
az sql midb short-term-retention-policy set \
    --resource-group myresourcegroup \
    --managed-instance myinstance \
    --name mymanageddb \
    --retention-days 1 \
```

Utilisez l’exemple suivant pour changer la conservation des sauvegardes PITR pour **toutes les bases de données actives** dans une instance managée SQL.

```azurecli
# Set new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
az sql midb short-term-retention-policy set \
    --resource-group myresourcegroup \
    --managed-instance myinstance \
    --retention-days 1 \
```

---

### <a name="change-the-short-term-retention-policy-using-powershell"></a>Modifier la stratégie de rétention à court terme à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell AzureRM est toujours pris en charge par SQL Database et SQL Managed Instance, mais tout développement futur concerne le module Az.Sql. Pour plus d’informations, consultez la page [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont essentiellement identiques à ceux utilisés dans les modules AzureRm.

#### <a name="sql-database"></a>[Base de données SQL](#tab/single-database)

Pour modifier la rétention de sauvegarde PITR et la fréquence de sauvegarde différentielle pour les bases de données SQL Azure actives, utilisez l’exemple PowerShell suivant.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

```powershell
# SET new PITR differential backup frequency on an active individual database
# Valid differential backup frequency must be ether 12 or 24. 
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28 -DiffBackupIntervalInHours 24
```

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Pour changer la conservation de sauvegarde avec récupération jusqu’à une date et heure pour **une seule base de données active** dans une instance SQL managée, utilisez l’exemple PowerShell suivant.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

Pour changer la conservation de sauvegarde avec récupération jusqu’à une date et heure pour **toutes les bases de données actives** dans une instance SQL managée, utilisez l’exemple PowerShell suivant.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

Pour changer la conservation de sauvegarde avec récupération jusqu’à une date et heure pour **une seule base de données supprimée** dans une instance SQL managée, utilisez l’exemple PowerShell suivant.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Pour changer la conservation de sauvegarde avec récupération jusqu’à une date et heure pour **toutes les bases de données supprimées** dans une instance SQL managée, utilisez l’exemple PowerShell suivant.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

La rétention des jours zéro (0) indique que la sauvegarde est immédiatement supprimée et n’est plus conservée pour une base de données supprimée.
Une fois la rétention de sauvegarde PITR réduite pour une base de données supprimée, elle ne peut plus être augmentée.

---

### <a name="change-the-short-term-retention-policy-using-the-rest-api"></a>Modifier la stratégie de rétention à court terme à l’aide de l’API REST

La demande ci-dessous met à jour la période de rétention à 28 jours et définit également la fréquence de sauvegarde différentielle sur 24 heures.


#### <a name="sql-database"></a>[Base de données SQL](#tab/single-database)

#### <a name="sample-request"></a>Exemple de demande

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2021-02-01-preview
```

#### <a name="request-body"></a>Corps de la demande

```json
{ 
    "properties":{
        "retentionDays":28
        "diffBackupIntervalInHours":24
  }
}
```

#### <a name="sample-response"></a>Exemple de réponse : 

```json
{ 
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
    "diffBackupIntervalInHours":24
  }
}
```


Pour plus d’informations, consultez [API REST de conservation des sauvegardes](/rest/api/sql/backupshorttermretentionpolicies).

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

#### <a name="sample-request"></a>Exemple de requête

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Corps de la demande

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Exemple de réponse

Code d’état : 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Pour plus d’informations, consultez [API REST de conservation des sauvegardes](/rest/api/sql/backupshorttermretentionpolicies).

---

## <a name="configure-backup-storage-redundancy"></a>Configuration de la redondance du stockage de sauvegarde

> [!NOTE]
> La redondance de stockage configurable pour les sauvegardes de SQL Managed Instance ne peut être spécifiée que pendant le processus de création d’une instance gérée. Une fois que la ressource est approvisionné, vous ne pouvez pas modifier l’option de redondance du stockage de sauvegarde. Pour SQL Database, la préversion publique de cette fonctionnalité est actuellement disponible dans toutes les régions Azure ; la version est en disponibilité générale dans la région Azure Asie Sud-Est. 

La redondance d’un stockage de sauvegarde d'une instance gérée ne peut être réglé que lors de la création de l'instance. Pour une base de données SQL, elle peut être définie lors de la création de la base de données ou être mise à jour pour une base de données existante. La valeur par défaut est le stockage géoredondant. Pour connaître les différences de prix entre le stockage de sauvegarde localement redondant, redondant interzone et géoredondant, consultez la page [Tarification Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Configurez la redondance du stockage de sauvegarde à l’aide du Portail Azure

#### <a name="sql-database"></a>[Base de données SQL](#tab/single-database)

Dans le portail Azure, vous pouvez configurer la redondance du stockage de sauvegarde dans le volet **Créer une base de données SQL**. L’option est disponible sous la section Redondance du stockage de sauvegarde. 
![Ouvrir le volet Créer une base de données SQL](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Dans le portail Azure, l’option de modification de la redondance du stockage de sauvegarde se trouve dans le volet **Calcul + Stockage**, accessible à partir de l’option **Configurer Managed Instance** de l’onglet **Informations de base** durant la création de votre instance SQL managée.
![Ouvrir le volet de configuration Calcul + Stockage](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

Recherchez l’option permettant de sélectionner la redondance du stockage de sauvegarde dans le volet **Calcul + Stockage**.
![Configurer la redondance du stockage de sauvegarde](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-the-azure-cli"></a>Configurer la redondance du stockage de sauvegarde à l’aide d’Azure CLI

#### <a name="sql-database"></a>[Base de données SQL](#tab/single-database)

Pour configurer la redondance du stockage de sauvegarde lors de la création d’une base de données, vous pouvez spécifier le paramètre `backup-storage-redundancy`. Les valeurs possibles sont Geo, Zone et Local. Par défaut, toutes les bases de données SQL utilisent le stockage géoredondant pour les sauvegardes. La géorestauration est désactivée si une base de données est créée ou mise à jour avec un stockage de sauvegarde localement redondant ou redondant interzone.

```azurecli
az sql db create \
    --resource-group myresourcegroup \
    --server myserver \
    --name mydb \
    --tier GeneralPurpose \
    --backup-storage-redundancy Local
```

Vous pouvez également mettre à jour une base de données existante avec le paramètre `backup-storage-redundancy`.

```azurecli
az sql db update \
    --resource-group myresourcegroup \
    --server myserver \
    --name mydb \
    --backup-storage-redundancy Local
```
Pour plus d’informations, consultez la page sur les commandes [az sql db create](/cli/azure/sql/db#az_sql_db_create) et [az sql db update](/cli/azure/sql/db#az_sql_db_update).

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

La configuration de la redondance du stockage de sauvegarde n’est pas disponible pour une instance managée SQL lors de l’utilisation d’Azure CLI. Pour plus d’informations, consultez les options [Portail Azure](#configure-backup-storage-redundancy-by-using-the-azure-portal) ou [PowerShell](#configure-backup-storage-redundancy-by-using-powershell).

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>Configurer la redondance du stockage de sauvegarde à l’aide de PowerShell

#### <a name="sql-database"></a>[Base de données SQL](#tab/single-database)

Pour configurer la redondance du stockage de sauvegarde quand vous créez une base de données, vous pouvez spécifier le paramètre -BackupStorageRedundancy. Les valeurs possibles sont Geo, Zone et Local. Par défaut, toutes les bases de données SQL utilisent le stockage géoredondant pour les sauvegardes. La géorestauration est désactivée si une base de données est créée avec un stockage de sauvegarde localement redondant ou redondant interzone. 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

Pour plus d’informations, consultez la page [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).

Pour mettre à jour la redondance du stockage de sauvegarde d’une base de données existante, vous pouvez utiliser le paramètre -BackupStorageRedundancy. Les valeurs possibles sont Geo, Zone et Local.
Il peut falloir jusqu’à 48 heures pour que les modifications soient appliquées à la base de données. Le passage d’un stockage de sauvegarde géoredondant à un stockage localement redondant ou redondant interzone désactive la géorestauration. 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

Pour plus d’informations, consultez la page [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase).

> [!NOTE]
> Pour utiliser le paramètre -BackupStorageRedundancy avec les opérations « database restore », « database copy » ou « create secondary », utilisez Azure PowerShell version Az.Sql 2.11.0. 


#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Pour configurer la redondance du stockage de sauvegarde quand vous créez une instance managée, vous pouvez spécifier le paramètre -BackupStorageRedundancy. Les valeurs possibles sont Geo, Zone et Local.

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

Pour plus d’informations, consultez [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance).

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>Utiliser Azure Policy pour appliquer la redondance du stockage de sauvegarde

Si vous avez des besoins de résidence des données qui vous obligent à conserver toutes vos données dans une seule région Azure, vous souhaiterez peut-être appliquer des sauvegardes redondantes interzones ou localement redondantes pour votre base de données SQL ou votre instance managée à l’aide d’Azure Policy. Azure Policy est un service que vous pouvez utiliser pour créer, attribuer et gérer des stratégies qui appliquent des règles à des ressources Azure. Lorsque vous utilisez Azure Policy, ces ressources restent conformes à vos normes d’entreprise et contrats de niveau de service. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Policy](../../governance/policy/overview.md). 

### <a name="built-in-backup-storage-redundancy-policies"></a>Stratégies intégrées de redondance du stockage de sauvegarde 

Les nouvelles stratégies intégrées suivantes sont ajoutées et peuvent être attribuées au niveau de l’abonnement ou du groupe de ressources pour bloquer la création de nouvelles bases de données ou instances avec un stockage de sauvegarde géoredondant. 

[SQL Database doit éviter d’utiliser la redondance de sauvegarde GRS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)


[Les instances managées SQL doivent éviter d’utiliser la redondance de sauvegarde GRS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

Vous trouverez une liste complète des définitions de stratégies intégrées pour SQL Database et Managed Instance [ici](./policy-reference.md).

Pour appliquer les exigences en matière de résidence des données au niveau de l’organisation, ces stratégies peuvent être attribuées à un abonnement. Une fois ces stratégies attribuées au niveau de l’abonnement, les utilisateurs de l’abonnement en question ne pourront pas créer de base de données ni d’instance managée avec un stockage de sauvegarde géoredondant via le portail Azure ou Azure PowerShell. 

> [!IMPORTANT]
> Les stratégies Azure ne sont pas appliquées lors de la création d’une base de données via T-SQL. Pour appliquer la résidence des données lors de la création d’une base de données à l’aide de T-SQL, [utilisez « LOCAL » ou « ZONE » comme entrée pour le paramètre BACKUP_STORAGE_REDUNDANCY dans l’instruction CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql#create-database-using-zone-redundancy-for-backups).

Découvrez comment attribuer des stratégies à l’aide du [portail Azure](../../governance/policy/assign-policy-portal.md) ou d’[Azure PowerShell](../../governance/policy/assign-policy-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

- Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Pour en savoir plus sur les autres solutions de continuité des activités SQL Database, consultez [Vue d’ensemble de la continuité des activités](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Pour plus d’informations sur la façon de configurer, gérer et restaurer depuis la conservation à long terme de sauvegardes automatisées dans Stockage Blob Azure avec le portail Azure, consultez [Gestion de la rétention des sauvegardes à long terme à l’aide du Portail Azure](long-term-backup-retention-configure.md).
- Pour plus d’informations sur la façon de configurer, gérer et restaurer des données à partir d’une conservation à long terme de sauvegardes automatisées dans Stockage Blob Azure avec PowerShell, voir [Gestion de la rétention des sauvegardes à long terme à l’aide de PowerShell](long-term-backup-retention-configure.md). 
- Apprenez-en davantage sur la [restauration d’une base de données à un point dans le temps à l’aide du portail Azure](recovery-using-backups.md).
- Apprenez-en davantage sur la [restauration d’une base de données à un point dans le temps à l’aide de PowerShell](scripts/restore-database-powershell.md).
- Pour tout savoir sur la consommation du stockage de sauvegarde sur Azure SQL Managed Instance, consultez [Explication de la consommation du stockage de sauvegarde sur Managed Instance](https://aka.ms/mi-backup-explained).
- Pour savoir comment ajuster la rétention et les coûts de stockage de sauvegarde pour Azure SQL Managed Instance, consultez [Réglage des coûts de stockage de sauvegarde sur Managed Instance](https://aka.ms/mi-backup-tuning).
