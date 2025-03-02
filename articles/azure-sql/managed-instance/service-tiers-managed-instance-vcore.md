---
title: Modèle d’achat vCore
description: Le modèle d’achat vCore permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances en local et d’optimiser les coûts pour Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 05/18/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d5a67a12d6dbdb72625ab0459767eb8be92a8241
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131035976"
---
# <a name="azure-sql-managed-instance---compute-hardware-in-the-vcore-service-tier"></a>Azure SQL Managed Instance - Matériel de calcul dans le niveau de Service vCore
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Cet article passe en revue le modèle d’achat vCore pour [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md). Pour plus d’informations sur le choix entre les modèles d’achat vCore et DTU, consultez [choisir entre les modèles d’achat vCore et DTU](../database/purchasing-models.md).

Le modèle d’achat vCore utilisé par Azure SQL Managed Instance présente les caractéristiques suivantes :

- contrôle de la génération du matériel pour mieux répondre aux besoins de calcul et de mémoire de la charge de travail ;
- remises sur le tarif [d’Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md) et de [l’Instance réservée (RI)](../database/reserved-capacity-overview.md) ;
- plus grande transparence des informations sur le matériel qui fait tourner les calculs, ce qui facilite la planification des migrations à partir de déploiements locaux.
- La [tarification des instances réservées](../database/reserved-capacity-overview.md) est disponible uniquement pour le modèle d’achat vCore. 

## <a name="service-tiers"></a><a id="compute-tiers"></a>Niveaux de service

Les options de niveau de service du modèle d’achat vCore sont les suivantes : usage général et critique pour l’entreprise. Le niveau de service définit généralement l’architecture de stockage, les limites d’espace et d’E/S et les options de continuité d’activité liées à la disponibilité et à la reprise d’activité.

|**Cas d’usage**|**Usage général**|**Critique pour l’entreprise**|
|---|---|---|
|Idéal pour|La plupart des charges de travail d’entreprise. Propose des options de calcul et de stockage équilibrées, évolutives et économiques. |Offre aux applications métier la résilience la plus élevée aux défaillances en utilisant plusieurs réplicas isolés et assure les meilleures performances d’E/S.|
|Stockage|Utilise le stockage à distance. 32 Go à 16 To selon le nombre de cœurs |Utilise le stockage SSD local. <BR>- **Série Standard (Gen5) :** 32 Go à 4 To <BR>- **Série Premium :** 32 Go à 5,5 To <BR>- **Série Premium à mémoire optimisée :** 32 Go à 16 To |
|IOPS et débit (approximatif)|Consultez [Vue d’ensemble des limites de ressources Azure SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics).|Consultez [Vue d’ensemble des limites de ressources Azure SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics).|
|Disponibilité|1 réplica, réplicas sans échelle lecture|4 réplicas au total, 1 [réplica avec échelle lecture](../database/read-scale-out.md),<br/> 2 réplicas haute disponibilité (HA)|
|Sauvegardes|[Stockage géoredondant avec accès en lecture (RA-GRS)](../../storage/common/geo-redundant-design.md), 1 à 35 jours (7 jours par défaut)|[RA-GRS](../../storage/common/geo-redundant-design.md), 1 à 35 jours (7 jours par défaut)|
|En mémoire|Non pris en charge|Pris en charge|
||||

### <a name="choosing-a-service-tier"></a>Choix d’un niveau de service

Pour plus d’informations sur la sélection d’un niveau de service pour une charge de travail spécifique, voir les articles suivants :

- [Quand choisir le niveau de service Usage général](../database/service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quand choisir le niveau de service Critique pour l’entreprise](../database/service-tier-business-critical.md#when-to-choose-this-service-tier)

## <a name="compute"></a>Compute

Le calcul SQL Managed Instance fournit une quantité spécifique de ressources de calcul provisionnées en continu, indépendamment de l’activité de la charge de travail, la quantité de ressources provisionnées étant facturée à un tarif horaire fixe.

## <a name="hardware-generations"></a>Génération du matériel

Les options de génération de matériel du modèle vCore incluent la série Standard (Gen5), la série Premium et la série Premium à mémoire optimisée. La génération du matériel définit généralement les limites de calcul et de mémoire, ainsi que d’autres caractéristiques qui ont un impact sur les performances de la charge de travail.

Pour plus d’informations sur les spécificités et les limitations des générations de matériel, consultez [Caractéristiques des générations de matériel](resource-limits.md#hardware-generation-characteristics).

 Dans la vue de gestion dynamique [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database), la génération de matériel pour les instances utilisant des processeurs Intel&reg; SP-8160 (Skylake) apparaît comme Gen6, tandis que la génération de matériel pour les instances utilisant des processeurs Intel&reg; 8272CL (Cascade Lake) apparaît comme Gen7. Les processeurs Intel&reg; 8370C (Ice Lake) utilisés par les générations de matériel des séries Premium et Premium à mémoire optimisée apparaissent sous le nom de Gen8. Les limites de ressources pour toutes les instances de la série Standard (Gen5) sont identiques, quel que soit le type de processeur (Broadwell, Skylake ou Cascade Lake).

### <a name="selecting-a-hardware-generation"></a>Choisir une génération de matériel

Dans le portail Azure, vous pouvez sélectionner la génération de matériel au moment de la création, ou bien la modifier pour une instance managée SQL existante.

**Pour sélectionner une génération de matériel lors de la création d’une instance SQL Managed Instance**

Pour plus d’informations, consultez [Créer une instance SQL Managed Instance](../managed-instance/instance-create-quickstart.md).

Sous l'onglet **Informations de base**, sélectionnez le lien **Configurer la base de données** dans la section **Calcul + Stockage**, puis sélectionnez la génération de matériel souhaitée :

:::image type="content" source="../database/media/service-tiers-vcore/configure-managed-instance.png" alt-text="Configurer une instance SQL Managed Instance"  loc-scope="azure-portal":::
  
**Pour changer la génération du matériel d’une instance SQL Managed Instance existante**

#### <a name="the-azure-portal"></a>[Le portail Azure](#tab/azure-portal)

Dans la page SQL Managed Instance, sélectionnez le lien **Niveau tarifaire** situé sous la section Paramètres

:::image type="content" source="../database/media/service-tiers-vcore/change-managed-instance-hardware.png" alt-text="Modifier le matériel d’une instance SQL Managed Instance"  loc-scope="azure-portal":::

Dans la page Niveau tarifaire, vous pouvez modifier la génération du matériel comme décrit dans les étapes précédentes.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez le script PowerShell suivant :

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Pour plus d’informations, consultez la rubrique relative à la commande [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance).

#### <a name="the-azure-cli"></a>[L’interface de ligne de commande Microsoft Azure](#tab/azure-cli)

Utilisez la commande CLI suivante :

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Pour plus d’informations, consultez la rubrique relative à la commande [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update).

---

### <a name="hardware-availability"></a>Disponibilité matérielle

#### <a name="gen4"></a><a id="gen4gen5-1"></a> Gen4

Le matériel Gen4 est [en cours de retrait](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) et n’est plus disponible pour les nouveaux déploiements. Toutes les nouvelles instances doivent être déployées sur des générations de matériel plus récentes.

#### <a name="standard-series-gen5-and-premium-series"></a>Série Standard (Gen5) et série Premium

Le matériel de la série Standard (Gen5) est disponible dans toutes les régions publiques du monde.
  
Le matériel des séries Premium et Premium à mémoire optimisée est en préversion, et sa disponibilité régionale est limitée. Pour plus d’informations, consultez [Limites des ressources d’Azure SQL Managed Instance](../managed-instance/resource-limits.md#hardware-generation-characteristics).

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer, consultez [Créer une instance managée SQL à l’aide du portail Azure](instance-create-quickstart.md).
- Pour les détails de la tarification, consultez 
    - [Page de tarification d’Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Page de tarification des pools d’Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
- Pour obtenir des informations détaillées sur les tailles de calcul et de stockage spécifiques disponibles dans les niveaux de service usage général et critique pour l’entreprise, consultez [Limites de ressources vCore pour Azure SQL Managed Instance](resource-limits.md).
