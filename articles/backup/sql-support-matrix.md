---
title: Tableau de prise en charge de Sauvegarde Azure pour la sauvegarde de SQL Server dans les machines virtuelles Azure
description: Propose un résumé des limitations et des paramètres de prise en charge de la sauvegarde de SQL Server dans les machines virtuelles Azure avec le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 10/22/2021
ms.custom: references_regions
ms.openlocfilehash: c0b46a1c75c47b85985946646bf3216c9ab719f5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219259"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Tableau de prise en charge de la sauvegarde de SQL Server dans les machines virtuelles Azure

Vous pouvez utiliser le service Sauvegarde Azure pour sauvegarder des bases de données SQL Server dans des machines virtuelles Azure hébergées sur la plateforme cloud Microsoft Azure. Cet article résume les limitations et les paramètres généraux de prise en charge des scénarios et des déploiements de sauvegarde de SQL Server dans des machines virtuelles Azure.

## <a name="scenario-support"></a>Prise en charge du scénario

**Support** | **Détails**
--- | ---
**Déploiements pris en charge** | Les machines virtuelles Azure de la Place de marché SQL et les machines virtuelles autres que celles de la Place de marché (sur lesquelles SQL Server est installé manuellement) sont prises en charge.
**Régions prises en charge** | Sauvegarde Azure pour les bases de données SQL Server est disponible dans toutes les régions, à l’exception de France Sud (FRS), Royaume-Uni Nord (UKN), Royaume-Uni Sud 2 (UKS2), UG IOWA (UGI) et Allemagne (Forêt noire).
**Systèmes d’exploitation pris en charge** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux n’est pas actuellement pris en charge.
**Versions de SQL Server prises en charge** | SQL Server 2019, SQL Server 2017 comme indiqué dans la [page Rechercher le cycle de vie des produits](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 et différents SP comme indiqué dans la [page Rechercher le cycle de vie des produits](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.<br><br>Les versions de base de données locales Express ne sont pas prises en charge.
**Versions .NET prises en charge** | .NET Framework 4.5.2 ou ultérieur installé sur la machine virtuelle
**Déploiements pris en charge** | Les machines virtuelles Azure de la Place de marché SQL et les machines virtuelles autres que celles de la Place de marché (sur lesquelles le Serveur SQL est installé manuellement) sont prises en charge. La prise en charge des instances autonomes existe toujours sur les [groupes de disponibilité](backup-sql-server-on-availability-groups.md).

## <a name="feature-considerations-and-limitations"></a>Considérations et limitations relatives aux fonctionnalités

|Paramètre  |Limite maximale |
|---------|---------|
|Nombre de bases de données pouvant être protégées sur un serveur (et dans un coffre)    |   2000      |
|Taille de la base de données prise en charge (au-delà, des problèmes de performances peuvent survenir)   |   6 To*      |
|Nombre de fichiers pris en charge dans une base de données    |   1 000      |
|Nombre de sauvegardes complètes prises en charge par jour    |    Une sauvegarde planifiée. <br><br> Trois sauvegardes à la demande. <br><br> Nous vous recommandons de ne pas déclencher plus de trois sauvegardes par jour. Toutefois, pour permettre à l’utilisateur de réaliser de nouvelles tentatives en cas d’échec, la limite matérielle pour les sauvegardes à la demande est définie à neuf tentatives. |

_* La taille limite de la base de données dépend du taux de transfert de données pris en charge et de la configuration de la limite de durée de sauvegarde. Il ne s'agit pas de la limite absolue. [En savoir plus](#backup-throughput-performance) sur les performances de débit de sauvegarde._

* La sauvegarde SQL Server peut être configurée dans le portail Azure ou **PowerShell**. CLI n’est pas pris en charge.
* La solution est prise en charge pour les deux types de [déploiements](../azure-resource-manager/management/deployment-models.md) : machines virtuelles Azure Resource Manager et machines virtuelles classiques.
* Tous les types de sauvegarde (complète/différentielle/journal) et les modes de récupération (simple/complet/journalisé en bloc) sont pris en charge.
* Pour les bases de données **en lecture seule** : les sauvegardes complètes et complètes de copie uniquement sont les seuls types de sauvegarde pris en charge.
* La compression native SQL est prise en charge si elle est explicitement activée par l’utilisateur dans la stratégie de sauvegarde. Sauvegarde Azure remplace les valeurs par défaut au niveau de l’instance par la clause COMPRESSION / NO_COMPRESSION en fonction de la valeur de ce contrôle, tel que défini par l’utilisateur.
* La sauvegarde des base de données compatibles avec TDE est prise en charge. Pour restaurer une base de données chiffrée avec TDE sur un autre serveur SQL Server, vous devez d’abord [restaurer le certificat sur le serveur de destination](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server). La compression de sauvegarde pour les bases de données compatibles TDE pour SQL Server 2016 et les versions plus récentes est disponible, mais à une taille de transfert inférieure, comme expliqué [ici](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593).
* Les opérations de sauvegarde et de restauration des bases de données miroirs et des instantanés de base de données ne sont pas prises en charge.
* L’**instance de cluster de basculement (FCI)** SQL Server n’est pas prise en charge.

## <a name="backup-throughput-performance"></a>Performances de débit de sauvegarde

Le service Sauvegarde Azure prend en charge un taux de transfert de données cohérent de 200 Mbits/s pour les sauvegardes complètes et différentielles de bases de données SQL volumineuses (de 500 Go). Pour des performances optimales, vérifiez que les conditions suivantes sont réunies :

- La machine virtuelle sous-jacente (contenant l'instance de SQL Server qui héberge la base de données) est configurée avec le débit réseau requis. Si le débit maximal de la machine virtuelle est inférieur à 200 Mbits/s, le service Sauvegarde Azure ne pourra pas transférer les données à la vitesse optimale.<br>En outre, le débit configuré pour le disque qui contient les fichiers de base de données doit être suffisant. [En savoir plus](../virtual-machines/disks-performance.md) sur le débit et les performances des disques des machines virtuelles Azure. 
- Les processus exécutés sur la machine virtuelle ne consomment pas la bande passante de celle-ci. 
- Les planifications de sauvegarde sont réparties sur un sous-ensemble de bases de données. Plusieurs sauvegardes exécutées simultanément sur une machine virtuelle se partagent le taux de consommation du réseau. [En savoir plus](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-) sur le contrôle du nombre de sauvegardes simultanées.

>[!NOTE]
> [Téléchargez le planificateur de ressources détaillé](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) pour calculer le nombre approximatif de bases de données protégées recommandé par serveur en fonction des ressources de machine virtuelle, de la bande passante et de la stratégie de sauvegarde.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [sauvegarder une base de données SQL Server](backup-azure-sql-database.md) s'exécutant sur une machine virtuelle Azure.