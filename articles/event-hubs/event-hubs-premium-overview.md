---
title: Vue d’ensemble d’Event Hubs Premium
description: Cet article fournit une vue d’ensemble d’Azure Event Hubs Premium, un service permettant le déploiement multi-locataire de Event Hubs pour des besoins de diffusion en continu haut de gamme.
ms.topic: article
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8822b44f5f2a6d5cbc367da9a8ac5d397f87e9f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073013"
---
# <a name="overview-of-event-hubs-premium"></a>Vue d’ensemble d’Event Hubs Premium

Le niveau Premium de Event Hubs est conçu pour les scénarios de diffusion en continu haut de gamme qui requièrent des performances élastiques et supérieures avec une latence prévisible. Les performances sont obtenues en fournissant des ressources de calcul, de mémoire et de stockage réservées, ce qui réduit les interférences entre les locataires dans un environnement PaaS multi-locataire managé. 

Event Hubs Premium introduit un nouveau moteur de journalisation de code natif à deux niveaux qui fournit des latences d’envoi et de transfert plus prévisibles et nettement plus faibles que la génération précédente, sans faire de concession sur la durabilité. Event Hubs Premium réplique chaque événement sur trois réplicas, répartis, le cas échéant, entre les zones de disponibilité Azure. Puis tous les réplicas sont vidés de façon synchrone dans le stockage rapide sous-jacent avant que l’opération d’envoi soit signalée comme terminée. Les événements qui ne sont pas lus immédiatement ou qui doivent être relus ultérieurement peuvent être conservés jusqu’à 90 jours, de manière transparente dans un niveau de stockage redondant de zone de disponibilité. Les événements dans les niveaux de stockage rapide et de rétention sont chiffrés. Dans Event Hubs Premium, vous pouvez vous faire fournir les clés de chiffrement. 

Outre ces fonctionnalités de stockage, ainsi que toutes les capacités et la prise en charge des protocoles de l’offre Event Hubs Standard, le modèle d’isolation de Event Hubs Premium offre de nouvelles fonctionnalités telle que la mise à l’échelle dynamique des partitions, ainsi que des capacités à venir. Le niveau Premium vous permet aussi d’obtenir des allocations de quota beaucoup plus importantes. La fonctionnalité Capture de Event Hubs est incluse sans frais supplémentaires.

L’offre Premium est facturée en fonction du nombre d’[unités de traitement (ou « PU », pour « Processing Units »)](event-hubs-scalability.md#processing-units) qui correspondent à une part de ressources isolées (UC, mémoire et stockage) dans l’infrastructure sous-jacente. 

Dans la mesure où Event Hubs Premium offre une isolation dans un environnement multi-locataire de très grande taille qui peut déplacer rapidement les ressources, elle peut évoluer de manière beaucoup plus souple et plus rapide que l’offre Dedicated. De plus, sa quantité d’unités de traitement peut être ajustée dynamiquement. Par conséquent, Event Hubs Premium est souvent une option plus économique que Event Hubs Dedicated pour les besoins en débit moyen (< 120 Mo/s), en particulier grâce à la modification des charges tout au long de la journée ou de la semaine. 
> [!NOTE]
> Notez que Event Hubs Premium prend uniquement en charge TLS 1.2 ou une version supérieure. 

Pour accroître la robustesse grâce à la prise en charge des zones de disponibilité, la mise à l’échelle de déploiement minimale pour Event Hubs Dedicated doit monter à 8 unités de capacité (ou « CU », pour « Capacity Units »). Cependant, avec Event Hubs Premium, vous bénéficiez d’une prise en charge des zones de disponibilité dès la première unité de capacité, dans toutes les régions de zone de disponibilité. 

Vous pouvez acheter 1, 2, 4, 8 et 16 unités de traitement pour chaque espace de noms. Étant donné que Event Hubs Premium est une offre basée sur la capacité, le débit réalisable n’est pas défini par une limitation comme c’est le cas dans Event Hubs Standard, mais dépend du travail que vous demandez à Event Hubs de faire, tout comme pour Event Hubs Dedicated. Le débit d’ingestion et de diffusion effectif pour chaque zone de disponibilité dépend de différents facteurs, notamment :

* Nombre de producteurs et consommateurs
* Taille de charge utile 
* Nombre de partitions
* Taux de requêtes de sortie 
* Utilisation de la fonctionnalité Capture de Event Hubs, du registre de schéma et d’autres fonctionnalités avancées

Si vous souhaitez en savoir plus, veuillez consulter notre [comparaison entre les références SKU Event hubs](event-hubs-quotas.md) .


> [!NOTE]
> Tous les espaces de noms Event Hubs activés pour le protocole RPC Apache Kafka par défaut peuvent être utilisés par vos applications Kafka existantes. L’activation de Kafka sur votre cluster n'affecte pas vos cas d'utilisation non-Kafka ; il n'y a aucune option ou besoin de désactiver Kafka sur un cluster.

## <a name="why-premium"></a>Pourquoi opter pour la version Premium ?

Premium Event Hubs offre trois avantages intéressants pour les clients qui souhaitent renforcer leur isolation dans un environnement mutualisé, tout en ayant besoin d’une faible latence et d’une ingestion de données à débit élevé.

#### <a name="superior-performance-with-the-new-two-tier-storage-engine"></a>Des performances supérieures avec le nouveau moteur de stockage à deux niveaux

Event Hubs Premium utilise un nouveau moteur de stockage de journal à deux niveaux, qui améliore considérablement les performances d’entrée de données avec une latence globale et une instabilité de latence réduites, sans compromettre les garanties de durabilité. 

#### <a name="better-isolation-and-predictability"></a>Une amélioration de l’isolation et de la prévisibilité

Event Hubs Premium offre une capacité d’isolation de la mémoire et des calculs pour obtenir une latence plus prévisible et réduire le risque d’impact des *voisins bruyants* réduit dans un déploiement multi-locataire.

Event Hubs Premium implémente un modèle de *clusters imbriqués (ou « Cluster in Cluster »)* dans ses clusters multi-locataire, pour fournir une prévisibilité et un niveau de performances de qualité tout en conservant tous les avantages d’un environnement PaaS multi-locataire managé. 


#### <a name="cost-savings-and-scalability"></a>Une réduction des coûts et une scalabilité
Comme Event Hubs Premium est une offre multi-locataire, elle peut être mise à l’échelle de façon dynamique de façon plus flexible, très rapidement. La capacité est allouée dans les unités de traitement qui allouent des pods isolés du processeur et/ou de la mémoire à l’intérieur du cluster. Le nombre de ces pods peut être augmenté/réduit pour chaque espace de noms. Par conséquent, Event Hubs Premium est une option à faible coût pour les scénarios de messagerie. Sa plage de débit globale est inférieure à 120 Mo/s, mais reste supérieure à ce que vous pouvez obtenir avec la référence SKU standard.  

## <a name="quotas-and-limits"></a>Quotas et limites
Le niveau Premium offre toutes les fonctionnalités du plan Standard, améliore les performances ainsi que l’isolation, et propose des quotas plus importants. Pour augmenter les quotas et limites, consultez [Quotas et limites d’Azure Event Hubs](event-hubs-quotas.md)


## <a name="faqs"></a>FAQ

[!INCLUDE [event-hubs-dedicated-clusters-faq](./includes/event-hubs-premium-faq.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez commencer à utiliser Event Hubs Premium via le [portail Azure](https://portal.azure.com/#create/Microsoft.EventHub). Vous pouvez consulter la page [Tarification Event Hubs Premium](https://azure.microsoft.com/pricing/details/event-hubs/) pour en savoir plus sur la tarification, ou la page de [FAQ sur Event Hubs](event-hubs-faq.yml) pour connaître nos réponses aux questions les plus fréquemment posées sur Event Hubs. 
