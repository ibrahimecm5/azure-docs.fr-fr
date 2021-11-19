---
title: Modèle d’achat vCore
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Le modèle d’achat vCore permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances en local et d’optimiser les coûts pour Azure SQL Database et Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 05/18/2021
ROBOTS: NOINDEX
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 67e79d8b307de6327f3bacf2bfd57f95080b50d1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553189"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Vue d’ensemble du modèle vCore – Azure SQL Database et SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Le modèle d’achat vCore (« cœur virtuel ») utilisé par Azure SQL Database et Azure SQL Managed Instance offre plusieurs avantages :

- contrôle de la génération du matériel pour mieux répondre aux besoins de calcul et de mémoire de la charge de travail ;
- remises sur le tarif [d’Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md) et de [l’Instance réservée (RI)](reserved-capacity-overview.md) ;
- plus grande transparence des informations sur le matériel qui fait tourner les calculs, ce qui facilite la planification des migrations à partir de déploiements locaux.
- Dans le cas d’Azure SQL Database, le modèle d’achat vCore fournit des limites de calcul, de mémoire, d’e/s et de stockage plus élevées que le modèle DTU.

Pour plus d’informations sur le choix entre les modèles d’achat vCore et DTU, consultez [choisir entre les modèles d’achat vCore et DTU](purchasing-models.md).

> [!div class="nextstepaction"]
> [Enquête pour améliorer Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="service-tiers"></a>Niveaux de service

Les articles suivants fournissent des informations spécifiques sur le modèle d’achat vCore dans chaque produit.

- Pour plus d’informations sur les niveaux de service Azure SQL Database pour le modèle vCore, consultez [Vue d’ensemble du modèle vCore - Azure SQL Database](service-tiers-sql-database-vcore.md).
- Pour plus d’informations sur les niveaux de service Azure SQL Managed Instance pour le modèle vCore, consultez [Vue d’ensemble du modèle vCore - Azure SQL Managed Instance](../managed-instance/service-tiers-managed-instance-vcore.md).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, consultez : 
- [Création d’une base de données SQL à l’aide du portail Azure](single-database-create-quickstart.md)
- [Création d’une instance SQL Managed Instance à l’aide du portail Azure](../managed-instance/instance-create-quickstart.md)

- Pour les détails de la tarification, consultez 
    - [Page de tarification d’Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)
    - [Page de tarification d’Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Page de tarification des pools d’Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
    
Pour plus d’informations sur les tailles de calcul et de stockage spécifiques disponibles dans les niveaux de service Usage général et Critique pour l’entreprise, consultez :

- [Limites de ressources vCore pour Azure SQL Database](resource-limits-vcore-single-databases.md).
- [Limites de ressources vCore pour une instance Azure SQL Database mise en pool](resource-limits-vcore-elastic-pools.md).
- [Limites de ressources vCore pour Azure SQL Managed Instance](../managed-instance/resource-limits.md).
