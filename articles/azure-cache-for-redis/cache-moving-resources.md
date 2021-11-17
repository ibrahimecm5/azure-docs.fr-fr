---
title: Déplacer les instances Azure Cache pour Redis vers différentes régions
description: Comment déplacer les instances Azure Cache pour Redis vers une autre région Azure.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 8/27/2021
ms.openlocfilehash: 931c4aec854b2512c649570b5fe8fc24bfe11c81
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894946"
---
# <a name="move-azure-cache-for-redis-instances-to-different-regions"></a>Déplacer les instances Azure Cache pour Redis vers différentes régions

Dans cet article, vous allez découvrir comment déplacer les instances Azure Cache pour Redis vers une autre région Azure. Vous pouvez être amené à déplacer vos ressources vers une autre région pour diverses raisons :
- Pour tirer parti d’une nouvelle région Azure.
- Pour déployer des fonctionnalités ou des services disponibles uniquement dans des régions spécifiques.
- Pour respecter des exigences de gouvernance et de stratégie internes.
- Pour répondre à des exigences de planification de la capacité.

Le niveau d’Azure Cache pour Redis que vous utilisez détermine l’option qui vous convient le mieux.

| Niveau de cache | Options  | 
| ------------ |  ------- | 
| Premium | Géoréplication, créer un cache, effectuer la double écriture sur deux caches, exporter et importer des données via un fichier RDB| 
| De base ou Standard | Créer un cache ou effectuer la double-écriture dans deux caches| 
| Enterprise ou Enterprise Flash | Créer un cache ou exporter et importer des données avec un fichier RDB | 

## <a name="geo-replication-premium"></a>Géoréplication (Premium)

### <a name="prerequisites"></a>Prérequis 

Pour configurer la géoréplication entre deux caches, les conditions préalables suivantes doivent être remplies :

- Les deux caches sont de [niveau Premium](cache-overview.md#service-tiers).
- Les deux caches figurent dans le même abonnement Azure.
- La taille du cache lié secondaire est supérieure ou égale à celle du cache lié principal.
- Les deux caches existent déjà et sont en cours d’exécution.

### <a name="prepare"></a>Préparation

Pour déplacer votre instance de cache vers une autre région, vous devez [créer une deuxième instance de cache Premium](quickstart-create-redis.md) dans la région de votre choix. Une fois que les deux caches sont en cours d’exécution, vous pouvez configurer la géoréplication entre les deux instances de cache. 

> [!NOTE]
> Le transfert de données entre régions Azure est facturé aux [tarifs de bande passante](https://azure.microsoft.com/pricing/details/bandwidth/) standard.

Certaines fonctionnalités ne sont pas prises en charge par la géoréplication :

- La redondance de zone n’est pas prise en charge avec la géoréplication.
- La persistance n'est pas prise en charge par la géoréplication.

Conditions pour la prise en charge de la géoréplication :

- Le clustering est pris en charge s'il est activé pour les deux caches et si ceux-ci possèdent le même nombre de partitions.
- Les caches situés dans des réseaux virtuels différents sont pris en charge avec des mises en garde. Pour plus d’informations, consultez [Puis-je utiliser la géoréplication avec mes caches dans un réseau virtuel ?](cache-how-to-geo-replication.md#can-i-use-geo-replication-with-my-caches-in-a-vnet)

Une fois la géoréplication configurée, les restrictions suivantes s’appliquent à votre paire de caches liés :

- Le cache lié secondaire est en lecture seule. Vous pouvez y lire des données, mais vous ne pouvez pas y écrire de données. 
    - Si vous choisissez de lire à partir de l’instance géographique secondaire, à chaque fois qu’une synchronisation complète des données se produit entre les instances principale et secondaire, par exemple lors de la mise à jour de l’instance géographique principale ou secondaire, ou lors de certains scénarios de redémarrage, l’instance géographique secondaire lève des erreurs sur toute opération Redis, jusqu’à la fin de la synchronisation complète des données entre l’instance principale géographique et l’instance géographique secondaire.
    - Les applications qui lisent depuis une instance géographique secondaire doivent être générées pour revenir à l’instance géographique principale chaque fois que l’instance géographique secondaire génère de telles erreurs.
- Toutes les données présentes dans le cache lié secondaire avant l’ajout du lien sont supprimées. Toutefois, en cas de suppression ultérieure de la géoréplication, les données répliquées restent dans le cache lié secondaire.
- Vous ne pouvez pas procéder à la [mise à l'échelle](cache-how-to-scale.md) d'un seul des deux caches lorsque ceux-ci sont liés.
- Vous ne pouvez pas [modifier le nombre de partitions](cache-how-to-premium-clustering.md) si le clustering est activé pour le cache.
- Vous ne pouvez activer la persistance sur aucun des caches.
- Vous pouvez [exporter](cache-how-to-import-export-data.md#export) à partir de l'un ou l'autre des caches.
- Vous ne pouvez pas [importer](cache-how-to-import-export-data.md#import) dans le cache lié secondaire.
- Vous ne pouvez pas supprimer les caches liés ou le groupe de ressources qui les contient tant qu'ils n'ont pas été dissociés. Pour plus d’informations, consultez [Pourquoi ma tentative de suppression de mon cache lié a-t-elle échoué ?](cache-how-to-geo-replication.md#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Si les caches se trouvent dans des régions différentes, des frais de sortie de réseau s'appliquent aux données déplacées d'une région à l'autre. Pour plus d’informations, consultez [Combien coûte la réplication de mes données entre des régions Azure ?](cache-how-to-geo-replication.md#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Il n'y a pas de basculement automatique entre le cache lié principal et le cache lié secondaire. Pour plus d'informations et pour apprendre à basculer une application cliente, consultez [Comment fonctionne le basculement vers le cache lié secondaire ?](cache-how-to-geo-replication.md#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="move"></a>Déplacer

1. Pour lier deux caches à des fins de géoréplication, cliquez d'abord sur **Géoréplication** dans le menu Ressources du cache que vous souhaitez utiliser comme cache lié principal. Ensuite, dans le panneau **Géoréplication** sur la gauche, cliquez sur **Ajouter une liaison de réplication de cache**.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-menu.png" alt-text="Ajouter un lien":::

1. Dans la liste **Caches compatibles**, cliquez sur le nom du cache secondaire souhaité. Si le cache secondaire ne figure pas dans la liste, vérifiez que les [conditions préalables à la géoréplication](#prerequisites) du cache secondaire sont remplies. Pour filtrer les caches par région, sélectionnez la région dans la carte afin de n’afficher que les caches figurant dans la liste **Caches compatibles**.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link.png" alt-text="Caches compatibles avec la géoréplication":::

    Vous pouvez également lancer le processus de liaison ou afficher des détails sur le cache secondaire à l'aide du menu contextuel.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png" alt-text="Menu contextuel de la géoréplication":::

1. Sélectionnez **Lier** pour lier les deux caches et commencer le processus de réplication.
   
    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png" alt-text="Lier des caches":::

### <a name="verify"></a>Vérification

1. Vous pouvez voir la progression du processus de réplication dans le panneau **Géoréplication** sur la gauche.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-linking.png" alt-text="État du lien":::

    Vous pouvez également voir l’état de la liaison sur la gauche, en utilisant la **Vue d’ensemble** pour les caches principal et secondaire.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-status.png" alt-text="Capture d’écran montrant comment afficher l’état de liaison pour les caches principal et secondaire.":::

    Une fois le processus de réplication terminé, l’**État du lien** devient **Réussi**.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-successful.png" alt-text="État du cache":::

    Le cache lié principal reste disponible pour une utilisation pendant le processus de liaison. Le cache lié secondaire n'est pas disponible tant que le processus de liaison n'est pas terminé.

### <a name="clean-up-source-resources"></a>Nettoyer les ressources sources 

Une fois que votre nouveau cache dans la région ciblée est rempli avec toutes les données nécessaires, supprimez le lien entre les deux caches et supprimez l’instance d’origine.

1. Pour supprimer la liaison entre deux caches et arrêter la géoréplication, dans **Géoréplication** à la gauche, cliquez sur **Dissocier les caches**.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-unlink.png" alt-text="Dissocier les caches":::

    Une fois le processus de dissociation terminé, le cache secondaire est disponible tant en lecture qu’en écriture.

>[!NOTE]
>En cas de suppression du lien de géoréplication, les données répliquées à partir du cache lié principal restent dans le cache secondaire.
>
>

2. Supprimez l'instance d'origine.

## <a name="create-a-new-cache-all-tiers"></a>Créer un cache (tous les niveaux)

### <a name="prerequisites"></a>Prérequis
- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)

### <a name="prepare"></a>Préparation
Si vous n’avez pas besoin de gérer vos données pendant le déplacement, le moyen le plus simple de déplacer des régions consiste à créer une nouvelle instance de cache dans la région ciblée et à y connecter votre application. Par exemple, si vous utilisez Redis comme cache de recherche d’enregistrements de base de données, vous pouvez facilement reconstruire le cache à partir de zéro.

### <a name="move"></a>Déplacer

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

Enfin, mettez à jour votre application pour utiliser les nouvelles instances. 

### <a name="clean-up-source-resources"></a>Nettoyer les ressources sources 
Une fois que votre nouveau cache dans la région ciblée est en cours d’exécution, supprimez l’instance d’origine.


## <a name="export-and-import-data-with-an-rdb-file-premium-enterprise-enterprise-flash"></a>Exporter et importer des données avec un fichier RDB (Premium, Enterprise, Enterprise Flash)
Redis open source définit un mécanisme standard pour la capture d’un instantané du jeu de données en mémoire d’un cache et son enregistrement dans un fichier. Ce fichier, appelé RDB, peut être lu par un autre cache Redis. [Les niveaux Premium et Enterprise d’Azure Cache pour Redis](cache-overview.md#service-tiers) prennent en charge l’importation de données dans une instance de cache avec des fichiers RDB. Vous pouvez utiliser un fichier RDB pour transférer des données à partir d’un cache existant vers Azure Cache pour Redis.

> [!IMPORTANT]
> Le format de fichier RDB peut changer entre les versions de Redis et risque de ne pas assurer la compatibilité descendante. La version de Redis du cache que vous exportez doit être identique ou inférieure à la version de votre nouvelle instance de cache.
>

### <a name="prerequisites"></a>Prérequis
- Les deux caches sont de [niveau Premium ou niveau Enterprise](cache-overview.md#service-tiers).
- Le deuxième cache a soit la même taille de cache, soit une taille de cache supérieure à celle du cache d’origine.
- La version de Redis du cache que vous exportez doit être identique ou inférieure à la version de votre nouvelle instance de cache.

### <a name="prepare"></a>Préparation
Pour déplacer votre instance de cache vers une autre région, vous devez créer [une deuxième instance de cache Premium](quickstart-create-redis.md) ou [une deuxième instance de cache Enterprise](quickstart-create-redis-enterprise.md) dans la région de votre choix.

### <a name="move"></a>Déplacer
1. Pour plus d’informations sur la manière d’importer et d’exporter des données dans Azure Cache pour Redis, consultez la section [ici](cache-how-to-import-export-data.md).

2. Mettez à jour votre application pour utiliser la nouvelle instance de cache.

### <a name="verify"></a>Vérification
Vous pouvez surveiller la progression de l’importation grâce aux notifications du portail Azure ou aux événements dans le [journal d’audit](../azure-monitor/essentials/activity-log.md).

### <a name="clean-up-source-resources"></a>Nettoyer les ressources sources 
Une fois que votre nouveau cache dans la région ciblée est en cours d’exécution, supprimez l’instance d’origine.

## <a name="dual-write-to-two-caches-basic-standard-and-premium"></a>Double écriture dans deux caches (De base, Standard et Premium)
Au lieu de déplacer les données directement entre les caches, vous pouvez utiliser votre application pour écrire des données dans un cache existant et un nouveau cache que vous configurez. L’application lit initialement les données du cache existant. Lorsque le nouveau cache disposera des données nécessaires, vous basculerez l’application vers ce cache et mettrez l’ancien hors service. Supposons, par exemple, que vous utilisez Redis comme magasin de sessions et que les sessions d’application sont valides pendant sept jours. Après avoir écrit dans les deux caches pendant une semaine, vous avez la certitude que le nouveau cache contient toutes les informations de session non expirées. Vous pouvez, en toute sécurité, l’utiliser à partir de ce point, sans vous soucier des pertes de données.

### <a name="prerequisites"></a>Prérequis
- Le deuxième cache a soit la même taille de cache, soit une taille de cache supérieure à celle du cache d’origine.

### <a name="prepare"></a>Préparation
Pour déplacer votre instance de cache vers une autre région, vous devez [créer une deuxième instance de cache](quickstart-create-redis.md) dans la région de votre choix.

### <a name="move"></a>Déplacer
Les étapes générales pour implémenter cette option sont les suivantes :

1. Modifiez le code de l’application pour écrire à la fois dans la nouvelle instance et celle d’origine.

2. Continuez la lecture des données à partir de l’instance d’origine jusqu’à ce que la nouvelle instance soit suffisamment remplie avec des données.

3. Mettez à jour le code de l’application pour qu’elle lise et écrive à partir de la nouvelle instance uniquement.

### <a name="clean-up-source-resources"></a>Nettoyer les ressources sources 
Une fois que votre nouveau cache dans la région ciblée est en cours d’exécution, supprimez l’instance d’origine.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les fonctionnalités d’Azure Cache pour Redis.
- [FAQ concernant la géoréplication](cache-how-to-geo-replication.md#geo-replication-faq)
- [Niveaux de service Azure Cache pour Redis](cache-overview.md#service-tiers)
- [Haute disponibilité pour Azure Cache pour Redis](cache-high-availability.md)


