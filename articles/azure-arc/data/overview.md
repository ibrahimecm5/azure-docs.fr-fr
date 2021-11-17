---
title: Que sont les services de données avec Azure Arc ?
description: Présente les services de données avec Azure Arc
ms.custom: references_regions
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 642448cab1fa6511d9d12eb4a3de894f3e5c1251
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056755"
---
# <a name="what-are-azure-arc-enabled-data-services"></a>Que sont les services de données activés pour Azure Arc ?

Azure Arc vous permet d'exécuter des services de données Azure localement, à la périphérie et dans des clouds publics à l'aide de Kubernetes et de l'infrastructure de votre choix.

Actuellement, les services de données avec Azure Arc suivants sont disponibles : 

- Instance managée SQL
- PostgreSQL Hyperscale (préversion)

Pour obtenir une présentation de la façon dont les services de données avec Azure Arc prennent en charge votre environnement de travail hybride, consultez cette vidéo d’introduction :

> [!VIDEO https://channel9.msdn.com/Shows//Inside-Azure-for-IT/Choose-the-right-data-solution-for-your-hybrid-environment/player?format=ny]

## <a name="always-current"></a>Toujours à jour

Les services de données avec Azure Arc, tels que SQL Managed Instance avec Azure Arc et PostgreSQL Hyperscale avec Azure Arc, bénéficient de mises à jour fréquentes contenant notamment des patchs de maintenance et de nouvelles fonctionnalités semblables à l’expérience offerte par Azure. Des mises à jour de Microsoft Container Registry vous sont fournies et vous pouvez définir les cadences de déploiement conformément à vos stratégies. Ainsi, les bases de données locales restent à jour tandis que vous gardez le contrôle. Dans la mesure où les services de données avec Azure Arc sont fournis sur abonnement, vos bases de données ne seront plus confrontées à des situations de fin de support.

## <a name="elastic-scale"></a>Mise à l’échelle élastique

L'élasticité de type cloud disponible localement vous permet d'effectuer dynamiquement un scale-up ou un scale-down de vos bases de données en fonction de la capacité de votre infrastructure, de la même manière que dans Azure. Cette fonctionnalité peut convenir aux scénarios d'augmentation du trafic qui se caractérisent par des besoins volatils, notamment aux scénarios nécessitant l'ingestion et l'interrogation de données en temps réel, quelle que soit l'échelle, avec un temps de réponse inférieur à la seconde. En outre, vous pouvez effectuer un scale-out des instances de base de données à l'aide de l'option de déploiement hyperscale unique d'Azure Database pour PostgreSQL Hyperscale. Cette fonctionnalité permet aux charges de travail de données de bénéficier d'une optimisation supplémentaire en termes de capacité grâce à des lectures et à des écritures de scale-*out* uniques.

## <a name="self-service-provisioning"></a>Approvisionnement en libre-service

Azure Arc offre également d'autres avantages liés au cloud, tels qu'un déploiement rapide et une automatisation à grande échelle. Grâce à l'orchestration basée sur Kubernetes, vous pouvez déployer une base de données en quelques secondes via l'interface graphique utilisateur (GUI) ou l'interface de ligne de commande (CLI).

## <a name="unified-management"></a>Gestion unifiée

À l’aide d’outils que vous connaissez déjà, comme le portail Azure, Azure Data Studio et l’interface de ligne de commande Azure (`az`) avec l’extension `arcdata`, vous pouvez désormais profiter d’une vue unifiée sur toutes les ressources de données déployées avec Azure Arc. Vous pouvez non seulement visualiser et gérer différentes bases de données relationnelles au sein de votre environnement et d’Azure, mais également obtenir des journaux et des données de télémétrie à partir d’API Kubernetes pour analyser la capacité et l’intégrité de l’infrastructure sous-jacente. Outre les fonctionnalités localisées d'analytique des journaux d'activité et d'analyse des performances, vous pouvez désormais tirer parti d'Azure Monitor pour obtenir des insights opérationnels complets sur l'ensemble de votre patrimoine.

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="disconnected-scenario-support"></a>Prise en charge de scénarios déconnectés

De nombreux services tels que l'approvisionnement en libre-service, les sauvegardes/restaurations automatisées et la supervision peuvent être exécutés localement dans votre infrastructure, avec ou sans connexion directe à Azure. La connexion directe à Azure ouvre la voie à des options supplémentaires d’intégration à d’autres services Azure tels qu’Azure Monitor et la possibilité d’utiliser le portail Azure et les API Azure Resource Manager n’importe où dans le monde pour gérer vos services de données avec Azure Arc.

## <a name="supported-regions"></a>Régions prises en charge

Le tableau suivant décrit les scénarios actuellement pris en charge pour les services de données avec Azure Arc.

|Régions Azure  |Mode de connexion directe  |Mode de connexion indirecte  |
|---------|---------|---------|
|USA Est|Disponible|Disponible
|USA Est 2|Disponible|Disponible
|USA Ouest|Disponible|Disponible
|USA Ouest 2|Disponible|Disponible
|USA Ouest 3|Disponible|Disponible
|Centre-Nord des États-Unis | Disponible | Disponible
|USA Centre|Non disponible|Disponible
|États-Unis - partie centrale méridionale|Disponible|Disponible
|Sud du Royaume-Uni|Disponible|Disponible
|France Centre|Disponible|Disponible
|Europe Ouest |Disponible |Disponible
|Europe Nord|Disponible|Disponible
|Japon Est|Non disponible|Disponible
|Centre de la Corée|Non disponible|Disponible
|Asie Est|Non disponible|Disponible
|Asie Sud-Est|Disponible|Disponible
|Australie Est|Disponible|Disponible

## <a name="next-steps"></a>Étapes suivantes

> **Vous voulez juste essayer ?**  
> Démarrez rapidement avec [Démarrage rapide d'Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) sur Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure.
>
>En outre, déployez [JumpStart ArcBox](https://azurearcjumpstart.io/azure_jumpstart_arcbox/), un bac à sable facile à déployer pour tout ce qui concerne Azure Arc. ArcBox est conçu pour être entièrement autonome au sein d’un seul abonnement Azure et d’un seul groupe de ressources, ce qui vous permet de vous familiariser facilement avec toutes les technologies avec Azure Arc grâce à un abonnement Azure.

[Installer les outils clients](install-client-tools.md)

[Planifier le déploiement des services de données Azure Arc](plan-azure-arc-data-services.md) (nécessite d’abord l’installation des outils clients)

[Créer une instance gérée Azure SQL sur Azure Arc](create-sql-managed-instance.md) (nécessite la création préalable d’un contrôleur de données Azure Arc)

[Créer un groupe de serveurs Azure Database pour PostgreSQL Hyperscale sur Azure Arc](create-postgresql-hyperscale-server-group.md) (nécessite la création préalable d’un contrôleur de données Azure Arc)
