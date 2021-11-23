---
title: Vue d’ensemble d’Azure Arc
description: Découvrez ce qu’est le service Azure Arc et comment il permet aux clients d’activer la gestion et la gouvernance de leurs ressources hybrides avec d’autres services et fonctionnalités Azure.
ms.date: 05/25/2021
ms.topic: overview
ms.openlocfilehash: 62395b53fea3f86e42726f1bb2d35b12d8a0bdca
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345735"
---
# <a name="azure-arc-overview"></a>Vue d’ensemble d’Azure Arc

Aujourd’hui, les entreprises éprouvent des difficultés à contrôler et à gérer des environnements de plus en plus complexes. Ces environnements s’étendent aux centres de données, à plusieurs clouds et à la périphérie. Chaque environnement et chaque cloud a son propre ensemble d’outils de gestion dont vous devez apprendre à vous servir.

En parallèle, il est difficile d’implémenter de nouveaux modèles opérationnels DevOps et ITOps, car les outils existants ne prennent pas en charge les nouveaux modèles cloud natifs.

Azure Arc simplifie la gouvernance et la gestion en proposant une plateforme de gestion multicloud et locale cohérente. Azure Arc vous permet de faire ce qui suit :
* Gérer l’ensemble de votre environnement, avec un volet unique, en projetant vos ressources non Azure, locales ou cloud existantes dans Azure Resource Manager. 
* Gérer les machines virtuelles, les clusters Kubernetes et les bases de données comme s’ils s’exécutaient dans Azure. 
* Utiliser des services et des fonctionnalités de gestion connus d’Azure quel que soit l’endroit où ils résident. 
* Continuer à utiliser des opérateurs informatiques (ITOps) traditionnels, tout en introduisant des pratiques DevOps pour prendre en charge de nouveaux modèles cloud natifs dans votre environnement.
* Configurer des emplacements personnalisés sous la forme d’une couche d’abstraction sur des extensions de cluster, une connexion au cluster et un cluster Kubernetes avec Azure Arc.  

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Diagramme du plan de contrôle de gestion d’Azure Arc" border="false":::

Aujourd’hui, Azure Arc vous permet de gérer les types de ressources suivants hébergés en dehors d’Azure :

* Serveurs : machines virtuelles et machines physiques exécutant Windows ou Linux.
* Clusters Kubernetes : prise en charge de plusieurs distributions Kubernetes.
* Services de données - Services Azure SQL Managed Instance et PostgreSQL Hyperscale.
* SQL Server - Inscrire des instances à partir de n’importe quel emplacement avec [SQL Server sur des serveurs avec Azure Arc](/sql/sql-server/azure-arc/overview).

## <a name="what-does-azure-arc-deliver"></a>Que propose Azure Arc ?

Les fonctionnalités clés d’Azure Arc sont les suivantes :

* Implémenter une cohérence au niveau inventaire, gestion, gouvernance et sécurité pour les serveurs dans votre environnement.

* Configurer des [extensions de machine virtuelle Azure](./servers/manage-vm-extensions.md) pour utiliser les services de gestion Azure afin de superviser, de sécuriser et de mettre à jour vos serveurs.

* Gérer et régir les clusters Kubernetes à grande échelle.

* Utilisez GitOps pour déployer la configuration sur un ou plusieurs clusters à partir de dépôts Git.

*  Conformité et configuration sans intervention pour vos clusters Kubernetes à l’aide d’Azure Policy.

* Exécuter les [services de données Azure](../azure-arc/kubernetes/custom-locations.md) sur n’importe quel environnement Kubernetes comme s’ils s’exécutaient dans Azure (en particulier Azure SQL Managed Instance et Azure Database pour PostgreSQL Hyperscale, avec des avantages tels que les mises à niveau et mises à jour, la sécurité et la supervision). Utiliser la mise à l’échelle élastique, appliquer des mises à jour sans temps d’arrêt de l’application, même sans connexion continue à Azure.

* Créer des [emplacements personnalisés](./kubernetes/custom-locations.md) en plus de vos clusters [Kubernetes avec Azure Arc](./kubernetes/overview.md), en les utilisant comme emplacements cibles pour le déploiement des instances de services Azure. Déployer vos extensions de cluster de service Azure pour [Data Services avec Azure Arc](./data/create-data-controller-direct-azure-portal.md), [App Services sur Azure Arc](../app-service/overview-arc-integration.md) (y compris les applications web, de fonction et logiques) et [Event Grid sur Kubernetes](../event-grid/kubernetes/overview.md).

* Une expérience unifiée de l’affichage de vos ressources avec Azure Arc, que vous utilisiez le portail Azure, l’interface Azure CLI, Azure PowerShell ou l’API REST Azure.

## <a name="how-much-does-azure-arc-cost"></a>Combien coûte Azure Arc ?

Voici les détails du tarif des fonctionnalités disponibles aujourd’hui avec Azure Arc.

### <a name="azure-arc-enabled-servers"></a>Serveurs avec Azure Arc

Les fonctionnalités suivantes du plan de contrôle Azure Arc sont proposées sans coût supplémentaire :

* Organisation des ressources avec les groupes d'administration et les étiquettes Azure

* Recherche et indexation avec Azure Resource Graph

* Accès et sécurité avec Azure RBAC et des abonnements

* Environnements et automatisation avec des modèles et des extensions

* Gestion des mises à jour.

Tout service Azure utilisé sur des serveurs avec Azure Arc, comme Microsoft Defender pour le cloud ou Azure Monitor, est facturé sur la base des tarifs de ce service. Pour plus d’informations, consultez la [page des tarifs Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes avec Azure Arc

Tout service Azure utilisé sur Kubernetes avec Azure Arc, comme Microsoft Defender pour le cloud ou Azure Monitor, est facturé sur la base des tarifs de ce service. Pour plus d’informations sur les tarifs des configurations Kubernetes avec Azure Arc, consultez la [page des tarifs Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-data-services"></a>Services de données avec Azure Arc

Pour plus d’informations, consultez la [page des tarifs Azure](https://azure.microsoft.com/pricing/).

## <a name="next-steps"></a>Étapes suivantes

* Pour en découvrir plus sur les serveurs avec Azure Arc, consultez cette [vue d’ensemble](./servers/overview.md)

* Pour en découvrir plus sur Kubernetes avec Azure Arc, consultez cette [vue d’ensemble](./kubernetes/overview.md)

* Pour plus d’informations sur les services de données avec Azure Arc, consultez cette [vue d’ensemble](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)

* Pour en découvrir plus sur SQL Server sur les serveurs avec Azure Arc, consultez cette [vue d’ensemble](/sql/sql-server/azure-arc/overview)

* Découvrez les services avec Azure Arc à partir de la [preuve de concept Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/)
