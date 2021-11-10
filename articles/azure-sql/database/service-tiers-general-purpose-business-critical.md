---
title: Niveaux de service Usage général et Critique pour l’entreprise
titleSuffix: Azure SQL Database & SQL Managed Instance
description: L’article aborde les niveaux de service Usage général et Critique pour l’entreprise dans le modèle d’achat vCore utilisé par Azure SQL Database et Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.openlocfilehash: 716c425958a457b45736835029b90567c090d4a9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446977"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Niveaux de service Azure SQL Database et Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

 Deux niveaux de service [vCore](service-tiers-vcore.md) sont disponibles dans Azure SQL Database et Azure SQL Managed Instance :

- Le niveau [Usage général](service-tier-general-purpose.md), le plus économique, est conçu pour la plupart des charges de travail présentant des exigences communes en matière de performances et de disponibilité.
- Le niveau [Critique pour l'entreprise](service-tier-business-critical.md) est conçu pour les charges de travail sensibles aux performances qui ont des exigences strictes en matière de disponibilité.

Azure SQL Database fournit également le niveau de service Hyperscale : 

- Conçu pour la plupart des charges de travail métier, le niveau [Hyperscale](service-tier-hyperscale.md) offre des fonctionnalités de stockage hautement évolutif, d'échelle horizontale en lecture, de mise à l'échelle rapide et de restauration rapide de base de données.

Pour obtenir une comparaison du modèle d’achat vCore avec le modèle d’achat DTU, consultez [Ressources et modèles d’achat](purchasing-models.md).

## <a name="service-tier-comparison"></a>Comparaison des niveaux de service

Le tableau suivant décrit les principales différences entre les niveaux de service.

|-| Type de ressource | Usage général | Hyperscale | Critique pour l’entreprise |
|:---:|:---:|:---:|:---:|:---:|
| **Idéal pour** | |  Offre des options de calcul et de stockage équilibrées et économiques. | La plupart des charges de travail d’entreprise. Mise à l’échelle automatique de la taille de stockage jusqu’à 100 To, mise à l’échelle verticale et horizontale du calcul, restauration rapide de la base de données. | Applications OLTP avec des débits de transactions élevés et une faible latence des E/S. Offre une meilleure résilience aux défaillances et des basculements rapides à l’aide de plusieurs réplicas mis à jour de façon synchrone.|
| **Disponible dans le type de ressource :** ||SQL Database / SQL Managed Instance | Base de données Azure SQL unique | SQL Database / SQL Managed Instance |
| **Taille de calcul**| SQL Database | 1 à 80 cœurs virtuels | 1 à 80 vCores | 1 à 128 vCores |
| | Instance managée SQL | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/A | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Pools SQL Managed Instance | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/A | N/A |
| **Type de stockage** | Tous | Stockage étendu | Stockage SSD distant et local hiérarchisé | Stockage SSD local |
| **Taille de la base de données** | SQL Database | 1 Go - 4 To | 40 Go - 100 To | 1 Go - 4 To |
| | Instance managée SQL  | 32 Go - 8 To </br> 16 To (préversion) en fonction du nombre de cœurs, Gen5 uniquement| N/A | 32 Go - 4 To |
| **Taille de stockage** | SQL Database | 1 Go - 4 To | 40 Go - 100 To | 1 Go - 4 To |
| | Instance managée SQL  | 32 Go - 8 To | N/A | 32 Go - 4 To |
| **Taille de TempDB** | SQL Database | [32 Go par vCore](resource-limits-vcore-single-databases.md) | [32 Go par vCore](resource-limits-vcore-single-databases.md) | [32 Go par vCore](resource-limits-vcore-single-databases.md) |
| | Instance managée SQL  | [24 Go par vCore](../managed-instance/resource-limits.md#service-tier-characteristics) | N/A | Jusqu’à 4 To - [limité par taille de stockage](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Débit d’écriture des journaux** | SQL Database | Bases de données uniques : [4,5 Mo/s par vCore (max 50 Mo/s)](resource-limits-vcore-single-databases.md) <br> Pools élastiques : [6 Mo/s par vCore (max 62,5 Mo/s)](resource-limits-vcore-elastic-pools.md)| 100 Mo/s | Bases de données uniques : [12 Mo/s par vCore (max 96 Mo/s)](resource-limits-vcore-single-databases.md) <br> Pools élastiques : [15 Mo/s par vCore (max 120 Mo/s)](resource-limits-vcore-elastic-pools.md)|
| | Instance managée SQL | [3 Mo/s par vCore (22 Mo/s max)](../managed-instance/resource-limits.md#service-tier-characteristics) | N/A | [4 Mo/s par vCore (48 Mo/s max)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Disponibilité**|SQL Database ([SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-database/))| 99,99 % | [99,95 % avec un réplica secondaire, 99,99 % avec d’autres réplicas](service-tier-hyperscale-frequently-asked-questions-faq.yml#what-slas-are-provided-for-a-hyperscale-database-) | 99,99 % <br/> [99,995 % avec base de données unique et redondante dans une zone](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
| |SQL Managed Instance ([SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance/))| 99,99 % |  [99,95 % avec un réplica secondaire, 99,99 % avec d’autres réplicas](service-tier-hyperscale-frequently-asked-questions-faq.yml#what-slas-are-provided-for-a-hyperscale-database-) | 99,99 % <br/> [99,995 % avec base de données unique et redondante dans une zone](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Sauvegardes**|Tous|RA-GRS, 1 à 35 jours (7 jours par défaut) | RA-GRS, 7 jours, récupération rapide jusqu'à une date et heure (PITR) | RA-GRS, 1 à 35 jours (7 jours par défaut) |
|**OLTP en mémoire** | | N/A | Prise en charge partielle. Les types de tables optimisées en mémoire, les variables de table et les modules compilés en mode natif sont pris en charge. | Disponible |
|**Réplicas en lecture seule**| | 0 intégré <br> 0 à 4 utilisant la [géoréplication](active-geo-replication-overview.md) | 0 à 4 intégrés | 1 intégré et inclus dans le prix <br> 0 à 4 utilisant la [géoréplication](active-geo-replication-overview.md) |
|**Tarification/facturation** | SQL Database | [vCore, stockage réservé et stockage de sauvegarde](https://azure.microsoft.com/pricing/details/sql-database/single/) sont facturés. <br/>Les IOPS ne sont pas facturées. | [vCore pour chaque réplica et le stockage utilisé](https://azure.microsoft.com/pricing/details/sql-database/single/) sont facturés. <br/>IOPS non encore facturées. | [vCore, stockage réservé et stockage de sauvegarde](https://azure.microsoft.com/pricing/details/sql-database/single/) sont facturés. <br/>Les IOPS ne sont pas facturées. |
|| Instance managée SQL | [vCore, stockage réservé et stockage de sauvegarde](https://azure.microsoft.com/pricing/details/sql-database/managed/) sont facturés. <br/>Les IOPS ne sont pas facturées.| N/A | [vCore, stockage réservé et stockage de sauvegarde](https://azure.microsoft.com/pricing/details/sql-database/managed/) sont facturés. <br/>Les IOPS ne sont pas facturées.| 
|**Modèles de remise**| | [Instances réservées](reserved-capacity-overview.md)<br/>[Azure Hybrid Benefit](../azure-hybrid-benefit.md) (non disponible avec les abonnements dev/test)<br/>Abonnements Dev/Test [Entreprise](https://azure.microsoft.com/offers/ms-azr-0148p/) et [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Azure Hybrid Benefit](../azure-hybrid-benefit.md) (non disponible avec les abonnements dev/test)<br/>Abonnements Dev/Test [Entreprise](https://azure.microsoft.com/offers/ms-azr-0148p/) et [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Instances réservées](reserved-capacity-overview.md)<br/>[Azure Hybrid Benefit](../azure-hybrid-benefit.md) (non disponible avec les abonnements dev/test)<br/>Abonnements Dev/Test [Entreprise](https://azure.microsoft.com/offers/ms-azr-0148p/) et [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/)|

> [!NOTE]
> Pour plus d’informations sur le Contrat de niveau de service (SLA), consultez [SLA pour Azure SQL Database](https://azure.microsoft.com/support/legal/sla/azure-sql-database/) ou [SLA pour Azure SQL Managed Instance](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance/).

### <a name="resource-limits"></a>Limites des ressources

Pour plus d’informations sur les limites de ressources, consultez :

 - [Azure SQL Database (vCore)](resource-limits-vcore-single-databases.md)
 - [Single Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md)
 - [Azure SQL Database en pool (DTU)](resource-limits-dtu-single-databases.md)
 - [Azure SQL Managed Instance](../managed-instance/resource-limits.md)

## <a name="data-and-log-storage"></a>Stockage des données et des journaux

Les facteurs suivants affectent la quantité de stockage utilisée pour les fichiers de données et les fichiers journaux, et s'appliquent aux niveaux Usage général et Critique pour l'entreprise. Pour plus d’informations sur le stockage des données et des journaux dans Hyperscale, consultez [Niveau de service Hyperscale](service-tier-hyperscale.md).

- Chaque taille de calcul accepte une taille de données maximale, qui correspond, par défaut, à 32 Go.
- Lorsque vous configurez la taille maximale des données, un espace de stockage supplémentaire de 30 % est automatiquement ajouté pour les fichiers journaux.
- Vous pouvez sélectionner une taille de données maximale comprise entre 1 Go et la taille de stockage maximale prise en charge, par incréments de 1 Go.
- Au niveau de service Usage général, `tempdb` utilise un disque SSD local et le coût de ce stockage est inclus dans le prix du modèle vCore.
- Au niveau de service Critique pour l'entreprise, `tempdb` partage le disque SSD local avec les données et les fichiers journaux, et le coût du stockage `tempdb` est inclus dans le prix du modèle vCore.
- La taille de stockage maximale d'une instance de SQL Managed Instance doit être spécifiée en multiples de 32 Go.

> [!IMPORTANT]
> Aux niveaux Usage général et Critique pour l'entreprise, vous êtes facturé en fonction de la taille de stockage maximale configurée pour une base de données, un pool élastique ou une instance gérée. Au niveau Hyperscale, vous êtes facturé en fonction du stockage de données alloué.

Pour surveiller la taille de stockage de données actuellement allouée et utilisée dans SQL Database, utilisez respectivement les [métriques](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) Azure Monitor *allocated_data_storage* et *storage*. Pour surveiller la taille totale de stockage d'instance consommée pour SQL Managed Instance, utilisez la [métrique](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlmanagedinstances) *storage_space_used_mb*. Pour surveiller la taille de stockage actuellement allouée et utilisée par les données individuelles et les fichiers journaux dans une base de données à l'aide de T-SQL, utilisez la vue [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) et la fonction [FILEPROPERTY(... , 'SpaceUsed')](/sql/t-sql/functions/fileproperty-transact-sql).

> [!TIP]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez [Gérer l’espace des fichiers dans Azure SQL Database](file-space-manage.md).

## <a name="backups-and-storage"></a>Sauvegardes et stockage

Du stockage est alloué pour les sauvegardes de base de données afin de prendre en charge les fonctionnalités de [récupération jusqu'à une date et heure (PITR)](recovery-using-backups.md) et de [conservation à long terme (LTR)](long-term-retention-overview.md) de SQL Database et SQL Managed Instance. Ce stockage, distinct du stockage des données et des fichiers journaux, est facturé séparément.

- **Récupération jusqu'à une date et heure (PITR)**  : aux niveaux Usage général et Critique pour l'entreprise, les sauvegardes de bases de données individuelles sont automatiquement copiées vers le [stockage géographiquement redondant avec accès en lecture (RA-GRS)](../../storage/common/geo-redundant-design.md). La taille de stockage augmente dynamiquement avec chaque nouvelle création de sauvegarde. Le service est utilisé par les sauvegardes complètes, les sauvegardes différentielles et les sauvegardes de fichiers journaux. La consommation du stockage dépend du taux de change de la base de données et de la période de rétention configurée pour les sauvegardes. Vous pouvez configurer une période de rétention distincte pour chaque base de données : de 1 à 35 jours pour SQL Database, et de 0 à 35 jours pour SQL Managed Instance. Une quantité de stockage de sauvegarde égale à la taille maximale des données configurées est fournie sans frais supplémentaires.
- **Conservation à long terme (LTR)**  : vous pouvez également configurer la conservation à long terme des sauvegardes complètes pendant 10 ans maximum. Si vous configurez une stratégie de conservation à long terme, ces sauvegardes sont stockées automatiquement dans le stockage RA-GRS. Toutefois, vous pouvez contrôler la fréquence à laquelle les sauvegardes sont copiées. Pour répondre aux différentes exigences de conformité, vous pouvez sélectionner plusieurs périodes de conservation pour les sauvegardes hebdomadaires, mensuelles ou annuelles. La configuration que vous choisissez détermine la quantité de stockage utilisée pour les sauvegardes de conservation à long terme. Pour plus d’informations, consultez [Conservation des sauvegardes à long terme](long-term-retention-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus de détails sur les tailles de calcul et de stockage spécifiques disponibles selon les niveaux de service vCore, consultez : 

- [Limites de ressources vCore pour Azure SQL Database](resource-limits-vcore-single-databases.md)
- [Limites de ressources vCore pour les bases de données mises en pool dans Azure SQL Database](resource-limits-vcore-elastic-pools.md).
- [Limites de ressources vCore pour Azure SQL Managed Instance](../managed-instance/resource-limits.md).