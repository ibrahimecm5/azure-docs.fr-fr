---
title: Présentation du cache Azure pour Redis
description: Découvrez Azure Cache pour Redis et comment mettre en œuvre le modèle cache-aside, la mise en cache du contenu, la mise en cache des sessions utilisateur, la mise en file d’attente des travaux et des messages, et les transactions distribuées.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: overview
ms.date: 02/08/2021
ms.openlocfilehash: 8940744c2f857064699180c24bc3b766bc7995f2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440219"
---
# <a name="about-azure-cache-for-redis"></a>À propos du cache Azure pour Redis

Azure Cache pour Redis fournit un magasin de données en mémoire basé sur le logiciel [Redis](https://redis.io/). Redis améliore les performances et l’extensibilité d’une application qui utilise abondamment les magasins de données principaux. Il peut traiter des volumes conséquents de demandes d’applications en conservant les données fréquemment utilisées dans la mémoire du serveur, qui peut être écrite et lue rapidement. Redis constitue une solution de stockage de données à faible latence et à haut débit critique pour les applications modernes.

Azure Cache pour Redis fournit à la fois Redis open source (OSS Redis) et un produit commercial de Redis Labs (Redis Enterprise) sous la forme d’un service managé. Elle fournit une instance de serveur Redis sécurisée et dédiée, ainsi qu’une compatibilité complète avec l’API Redis. Le service est géré par Microsoft, hébergé sur Azure et peut être utilisé par toute application à l’intérieur ou à l’extérieur d’Azure.

Azure Cache pour Redis peut être utilisé comme cache de données ou de contenu distribué, magasin de sessions, répartiteur de messages, et bien plus encore. Il peut être déployé en tant que service autonome. Il peut également être déployé parallèlement à d’autres services de base de données Azure, comme Azure SQL ou Cosmos DB.

## <a name="key-scenarios"></a>Principaux scénarios

Azure Cache pour Redis améliore les performances des applications grâce à la prise en charge de modèles d’architecture d’application courants. Les modèles les plus courants sont les suivants :

| Modèle      | Description                                        |
| ------------ | -------------------------------------------------- |
| [Cache de données](cache-web-app-cache-aside-leaderboard.md) | Les bases de données sont souvent trop grandes pour être chargées directement dans un cache. Le modèle [cache-aside](/azure/architecture/patterns/cache-aside) est couramment utilisé pour charger des données dans le cache uniquement en cas de besoin. Quand le système modifie les données, il peut également mettre à jour le cache, qui est alors distribué à d’autres clients. En outre, le système peut définir un délai d’expiration pour les données ou utiliser une stratégie d’éviction pour déclencher les mises à jour de données dans le cache.|
| [Cache de contenu](cache-aspnet-output-cache-provider.md) | De nombreuses pages web sont générées à partir de modèles qui utilisent du contenu statique comme des en-têtes, des pieds de page ou des bannières. Ces éléments statiques ne doivent pas changer souvent. Par rapport aux magasins de données back-end, l’utilisation d’un cache en mémoire permet d’accéder rapidement au contenu statique. Ce modèle réduit le temps de traitement et la charge du serveur, améliorant la réactivité des serveurs web. Il peut vous permettre de réduire le nombre de serveurs nécessaires au traitement des charges. Azure Cache pour Redis propose le fournisseur de caches de sortie Redis pour la prise en charge de ce modèle avec ASP.NET.|
| [Magasin de sessions](cache-aspnet-session-state-provider.md) | Ce modèle est souvent utilisé avec les paniers d’achat et d’autres données d’historique utilisateur qu’une application web peut associer avec les cookies des utilisateurs. Le stockage de trop nombreuses informations dans un cookie peut avoir un effet négatif sur les performances à mesure que la taille du cookie augmente, ce dernier devant être transmis et validé à chaque requête. Une solution classique consiste à utiliser le cookie comme clé pour interroger les données dans une base de données. L’utilisation d’un cache en mémoire tel que le cache Azure pour Redis pour associer des informations à un utilisateur est beaucoup plus rapide que l’interaction avec une base de données relationnelle complète. |
| Mise en file d’attente des travaux et des messages | Les applications ajoutent fréquemment des tâches à une file d’attente quand l’exécution des opérations associées à la requête prend un certain temps. Les opérations dont l’exécution est plus longue sont mises en file d’attente pour être traitées en séquence, souvent par un autre serveur.  Cette méthode est appelée « mise en file d’attente des tâches ». Azure Cache pour Redis fournit une file d’attente distribuée pour activer ce modèle dans votre application.|
| Transactions distribuées | Les applications nécessitent parfois une série de commandes sur un magasin de données back-end pour s’exécuter comme opération atomique unique. Toutes les commandes doivent réussir, ou elles devront toutes être restaurées à leur état initial. Azure Cache pour Redis prend en charge l’exécution d’un lot de commandes comme [transaction](https://redis.io/topics/transactions) unique. |

## <a name="redis-versions"></a>Versions Redis

Azure Cache pour Redis prend en charge OSS Redis version 4.0.x et 6.0.x. Nous avons décidé d’ignorer Redis 5.0 pour vous apporter la version la plus récente. Auparavant, Azure Cache pour Redis ne gérait qu’une seule version de Redis. À l’avenir, il fournira une mise à niveau de version majeure plus récente et au moins une version stable plus ancienne. Vous pouvez [choisir la version](cache-how-to-version.md) qui convient le mieux à votre application.


## <a name="service-tiers"></a>Niveaux de service

Azure Cache pour Redis est disponible aux niveaux suivants :

| Niveau | Description |
|---|---|
| De base | Cache OSS Redis s’exécutant sur une seule machine virtuelle. Aucun contrat de niveau de service (SLA) ne couvre ce niveau qui est idéal pour les charges de travail de développement/test et non critiques. |
| Standard | Cache OSS Redis s’exécutant sur deux machines virtuelles dans une configuration répliquée. |
| Premium | Caches OSS Redis à hautes performances. Ce niveau offre un débit plus élevé, une latence plus faible, une meilleure disponibilité et d’autres fonctionnalités. Les caches Premium sont déployés sur des machines virtuelles plus puissantes que celles des caches De base ou Standard. |
| Enterprise | Les caches hautes performances alimentés par des logiciels Redis Enterprise de Redis Labs. Ce niveau prend en charge les modules Redis, notamment RediSearch, RedisBloom et RedisTimeSeries. De plus, il offre une disponibilité encore plus élevée que celle du niveau Premium. |
| Enterprise Flash | Grands caches économiques alimentés par des logiciels Redis Enterprise de Redis Labs. Ce niveau étend le stockage de données Redis à la mémoire non volatile, ce qui est moins coûteux qu’une mémoire DRAM, sur une machine virtuelle. Il réduit le coût de mémoire global par Go. |

### <a name="feature-comparison"></a>Comparaison des fonctionnalités

La page [Tarifs d’Azure Cache pour Redis](https://azure.microsoft.com/pricing/details/cache/) fournit une comparaison détaillée des différents niveaux. Le tableau suivant décrit quelques-unes des fonctionnalités prises en charge par niveau :

| Description de la fonctionnalité | De base | Standard | Premium | Enterprise | Enterprise Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| Chiffrement des données |✔|✔|✔|✔|✔|
| [Isolement réseau](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [Mise à l'échelle](cache-how-to-scale.md) |✔|✔|✔|-|-|
| [Clustering OSS](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [Persistance des données](cache-how-to-premium-persistence.md) |-|-|✔|PRÉVERSION|PRÉVERSION|
| [Redondance de zone](cache-how-to-zone-redundancy.md) |-|-|✔|✔|✔|
| [Géoréplication](cache-how-to-geo-replication.md) |-|-|✔|PRÉVERSION|PRÉVERSION|
| [Modules Redis](#choosing-the-right-tier) |-|-|-|✔|✔|
| [Import/Export](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Redémarrage](cache-administration.md#reboot) |✔|✔|✔|-|-|
| [Mises à jour planifiées](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>Choix du niveau approprié

Lors du choix d’un niveau Azure Cache pour Redis, vous devez prendre en compte les options suivantes :

* **Mémoire** : Les niveaux De base et Standard proposent de 250 Mo à 53 Go ; le niveau Premium de 6 Go à 1,2 To ; les niveaux Enterprise de 12 Go à 14 To.  Pour créer un cache de niveau Premium d’une taille supérieure à 120 Go, vous pouvez utiliser le clustering Redis OSS. Pour plus d’informations, consultez [Tarification du Cache Azure pour Redis](https://azure.microsoft.com/pricing/details/cache/). Pour plus d’informations, consultez la page [Comment configurer le clustering Redis pour un cache Azure pour Redis Premium](cache-how-to-premium-clustering.md).
* **Performances** : Les caches des niveaux Premium et Enterprise sont déployés sur du matériel qui dispose de processeurs plus rapides, offrant de meilleures performances que le niveau De base ou Standard. Les caches de niveau Premium ont un débit supérieur et des latences moindres. Pour plus d’informations, consultez [Performances d’Azure Cache pour Redis](./cache-planning-faq.yml#azure-cache-for-redis-performance).
* **Noyau dédié pour le serveur Redis** : Tous les caches sauf C0 exécutent des cœurs de machines virtuelles dédiées. Par conception, Redis n’utilise qu’un seul thread pour le traitement des commandes. Azure Cache pour Redis utilise d’autres cœurs pour le traitement des E/S. Le fait d’avoir plus de cœurs améliore les performances de débit, même ci cela risque de pas produire une mise à l’échelle linéaire. En outre, des tailles de machine virtuelle plus volumineuses sont généralement associées à des limites de bande passante supérieures. Cela vous permet d’éviter une saturation du réseau entraînant des délais d’attente dans votre application.
* **Performances du réseau** : Si vous avez une charge de travail qui nécessite un débit élevé, le niveau Premium ou Enterprise offre davantage de bande passante par rapport au niveau De base ou Standard. En outre, à chaque niveau les caches de taille supérieure offrent davantage de bande passante en raison de la machine virtuelle sous-jacente qui héberge le cache. Pour plus d’informations, consultez [Performances d’Azure Cache pour Redis](./cache-planning-faq.yml#azure-cache-for-redis-performance).
* **Nombre maximal de connexions client** : les niveaux Premium et Enterprise offrent le nombre maximal de clients pouvant se connecter à Redis, permettant un nombre de connexions plus élevé pour les caches de taille supérieure. Le clustering augmente la quantité totale de bande passante réseau disponible pour un cache en cluster.
* **Haute disponibilité** : Azure Cache pour Redis fournit plusieurs options de [haute disponibilité](cache-high-availability.md). Il garantit la disponibilité d’un cache Standard, Premium ou Enterprise conformément à notre [contrat SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Celui-ci couvre uniquement la connectivité aux points de terminaison du cache. Le contrat SLA ne couvre pas la protection contre la perte de données. Nous vous recommandons d’utiliser la fonctionnalité de persistance des données Redis disponible dans les niveaux Premium et Enterprise pour accroître la résilience contre la perte de données.
* **Persistance des données** : Les niveaux Premium et Enterprise vous permettent de rendre persistantes les données du cache sur un compte de stockage Azure et sur un disque managé, respectivement. Les problèmes d’infrastructure sous-jacents peuvent entraîner une éventuelle perte de données. Nous vous recommandons d’utiliser la fonctionnalité de persistance des données Redis disponible dans ces niveaux pour accroître la résilience contre la perte de données. Azure Cache pour Redis offre les options RDB et AOF (préversion). La persistance des données peut être activée sur le portail Azure et dans l’interface CLI. Pour plus d’informations, consultez [Comment configurer la persistance pour un Cache Azure pour Redis Premium](cache-how-to-premium-persistence.md).
* **Isolement réseau** : Les déploiements Azure Private Link et de réseaux virtuels offrent une sécurité améliorée et un isolement du trafic pour votre instance Azure Cache pour Redis. Le réseau virtuel vous permet de restreindre davantage l’accès par le biais de stratégies de contrôle d’accès réseau. Pour plus d’informations, consultez [Azure Cache pour Redis avec Azure Private Link](cache-private-link.md) et [Comment configurer la prise en charge de réseau virtuel pour une instance Premium d’Azure Cache pour Redis](cache-how-to-premium-vnet.md).
* **Modules Redis** : les niveaux Enterprise prennent en charge [RediSearch](https://docs.redislabs.com/latest/modules/redisearch/), [RedisBloom](https://docs.redislabs.com/latest/modules/redisbloom/) et [RedisTimeSeries](https://docs.redislabs.com/latest/modules/redistimeseries/). Ces modules ajoutent de nouveaux types de données et de nouvelles fonctionnalités à Redis.

Vous pouvez mettre à l’échelle votre cache du niveau De base jusqu’au niveau Premium une fois qu’il a été créé. Actuellement, la mise à l’échelle vers un niveau inférieur n’est pas prise en charge. Pour connaître la procédure de mise à l’échelle, consultez [Guide pratique pour mettre à l’échelle le cache Azure pour Redis](cache-how-to-scale.md) et [Guide pratique pour automatiser une opération de mise à l’échelle](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="special-considerations-for-enterprise-tiers"></a>Considérations spéciales relatives aux niveaux Enterprise

Les niveaux Enterprise s’appuient sur Redis Enterprise, une variante commerciale de Redis produite par Redis Labs. Les clients obtiennent et paient une licence pour ce logiciel par le biais d’une offre de la Place de marché Azure. Azure Cache pour Redis gère l’acquisition de licences afin que vous n’ayez pas à le faire séparément. Pour effectuer un achat dans la Place de marché Microsoft Azure, vous devez satisfaire les prérequis suivants :
* Votre abonnement Azure dispose d’un moyen de paiement valide. Les crédits Azure ou les abonnements MSDN gratuits ne sont pas pris en charge.
* Votre organisation autorise les [achats dans la Place de marché Azure](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases).
* Si vous utilisez une Place de marché privée, elle doit contenir l’offre Enterprise de Redis Labs.

> [!IMPORTANT]
> Azure Cache pour Redis Enterprise nécessite des équilibreurs de charge réseau standard qui sont facturés séparément des instances de cache. Pour plus d’informations, consultez [Facturation de Load balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
> Si un cache Enterprise est configuré pour plusieurs zones de disponibilité, le transfert de données sera facturé aux [taux de bande passante réseau standard](https://azure.microsoft.com/pricing/details/bandwidth/) à partir du 1er juillet 2021.
>
> En outre, la persistance des données ajoute des disques managés. L’utilisation de ces ressources sera gratuite dans la préversion publique de la persistance des données Enterprise. Cela pourra changer lorsque la fonctionnalité sera en disponibilité générale.
>
>

## <a name="next-steps"></a>Étapes suivantes

* [Créer un cache Redis open source](quickstart-create-redis.md)
* [Créer un cache Redis Enterprise](quickstart-create-redis-enterprise.md)
* [Utiliser Azure Cache pour Redis dans une application web ASP.NET](cache-web-app-howto.md)
* [Utiliser Azure Cache pour Redis dans .NET Core](cache-dotnet-core-quickstart.md)
* [Utiliser Azure Cache pour Redis dans .NET Framework](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Utiliser Azure Cache pour Redis dans Node.js](cache-nodejs-get-started.md)
* [Utiliser Azure Cache pour Redis dans Java](cache-java-get-started.md)
* [Utiliser Azure Cache pour Redis dans Python](cache-python-get-started.md)