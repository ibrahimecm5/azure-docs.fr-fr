---
title: Meilleures pratiques relatives pour le développement
titleSuffix: Azure Cache for Redis
description: Découvrez comment développer du code pour Azure Cache pour Redis.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 11/3/2021
ms.author: shpathak
ms.openlocfilehash: 1c0c6b5f79e38dbd400dc048e2cd14cd2f52d35a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850980"
---
# <a name="development"></a>Développement

## <a name="connection-resilience-and-server-load"></a>Résilience de la connexion et charge du serveur

Lorsque vous développez des applications clientes, veillez à prendre en compte les meilleures pratiques pertinentes pour la [résilience des connexions](cache-best-practices-connection.md) et la [gestion de la charge du serveur](cache-best-practices-server-load.md).

## <a name="consider-more-keys-and-smaller-values"></a>Prendre en compte d’autres clés et des valeurs plus petites

Azure Cache pour Redis fonctionne mieux avec les valeurs plus petites. Envisagez de diviser les plus grandes portions de données en plus petits segments pour répartir les données sur plusieurs clés. Pour plus d’informations sur la taille idéale des valeurs, consultez cet [article](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/).

## <a name="large-request-or-response-size"></a>Taille importante de la demande ou de la réponse

Une demande/réponse volumineuse peut entraîner des délais d’expiration. Par exemple, supposons que la durée du délai d’expiration que vous avez configurée sur votre client soit de 1 seconde. Votre application demande deux clés (par exemple, « A » et « B ») en même temps (à l’aide de la même connexion réseau physique). La plupart des clients prennent en charge le traitement en pipeline des requêtes, de sorte que les deux requêtes A et B sont envoyées l’une après l’autre sans attendre les réponses. Le serveur renvoie les réponses dans le même ordre. Si la réponse A est volumineuse, elle peut consommer la majeure partie du délai d’expiration des requêtes suivantes.

Dans l’exemple suivant, les requêtes A et B sont envoyées rapidement au serveur. Le serveur commence rapidement à envoyer les réponses A et B. En raison des temps de transfert de données, la réponse B doit attendre l’expiration de la réponse A, même si le serveur a répondu rapidement.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

Cette demande/réponse est difficile à mesurer. Vous pouvez instrumenter votre code client pour suivre les requêtes et les réponses volumineuses.

Les solutions possibles pour la gestion des réponses volumineuses sont variées, et incluent notamment :

- Optimiser votre application pour prendre en charge un grand nombre de petites valeurs plutôt qu’un petit nombre de grandes valeurs
    - La meilleure solution consiste à diviser vos données en valeurs plus petites.
    - Lisez le billet [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) pour savoir pourquoi il est recommandé d’utiliser des valeurs moins élevées.
- Augmenter la taille de votre machine virtuelle pour obtenir des capacités de bande passante plus élevées
    - Une plus grande quantité de bande passante sur votre machine virtuelle cliente ou serveur peut réduire le temps de transfert des données pour les réponses volumineuses.
    - Comparez l’utilisation actuelle du réseau par les deux ordinateurs aux limites associées à la taille de votre machine virtuelle. L’augmentation de la bande passante sur le serveur ou le client uniquement peut ne pas suffire.
- Augmenter le nombre d’objets de connexion qu’utilise votre application
    - Utilisez un tourniquet (round-robin) pour envoyer des requêtes vers différents objets de connexion.

## <a name="key-distribution"></a>Distribution de clés

Si vous envisagez d’utiliser le clustering Redis, lisez [Bonnes pratiques concernant le clustering Redis avec des clés](https://redislabs.com/blog/redis-clustering-best-practices-with-keys/).

## <a name="use-pipelining"></a>Utilisez le traitement en pipeline

Essayez de choisir un client Redis qui prend en charge le [traitement en pipeline Redis](https://redis.io/topics/pipelining). Le traitement en pipeline permet d’utiliser efficacement le réseau et d’obtenir le meilleur débit possible.

## <a name="avoid-expensive-operations"></a>Éviter les opérations coûteuses

Certaines opérations Redis, comme la commande [KEYS](https://redis.io/commands/keys), sont très coûteuses à exécuter et sont donc à proscrire. Pour plus d’informations sur les commandes à longue durée, consultez [Commandes de longue durée](cache-troubleshoot-server.md#long-running-commands)

## <a name="choose-an-appropriate-tier"></a>Choisir un niveau approprié

Utilisez le niveau Standard ou Premium pour les systèmes de production.  N’utilisez pas le niveau de base en production. Le niveau De base est un système à nœud unique, sans réplication des données ni contrat SLA. En outre, utilisez au moins un cache C1. Les caches C0 s’appliquent uniquement aux scénarios de développement/test simples, car :

- ils partagent un cœur de processeur
- ils utilisent peu de mémoire
- sont sujets à des problèmes de *voisinage bruyant*

Nous vous recommandons de tester les performances pour choisir le niveau approprié et valider les paramètres de connexion. Pour plus d’informations, consultez [Test de performances](cache-best-practices-performance.md).

## <a name="client-in-same-region-as-cache"></a>Client dans la même région que le cache

Placez votre instance de cache et votre application dans la même région. La connexion à un cache dans une autre région peut considérablement augmenter la latence et réduire la fiabilité.  

Vous pouvez vous connecter en dehors d’Azure, mais cela est déconseillé, *en particulier si vous utilisez Redis comme cache*.  Si vous utilisez le serveur Redis simplement comme magasin de clés/valeurs, la latence n’est sans doute pas votre première préoccupation.

## <a name="use-tls-encryption"></a>Utiliser le chiffrement TLS

Azure Cache pour Redis nécessite des communications chiffrées avec le protocole TLS par défaut. Les versions 1.0, 1.1 et 1.2 de TLS sont actuellement prises en charge. Toutefois, TLS 1.0 et 1.1 étant en passe de dépréciation dans l’ensemble du secteur, utilisez TLS 1.2 dans la mesure du possible.

Si votre outil ou bibliothèque de client ne prend pas en charge TLS, vous pouvez activer des connexions non chiffrées par le biais du [portail Azure](cache-configure.md#access-ports) ou des [API de gestion](/rest/api/redis/redis/update). Dans les cas où il est impossible d’établir des connexions chiffrées, nous vous recommandons de placer votre cache et votre application cliente dans un réseau virtuel. Pour plus d’informations sur les ports utilisés dans le scénario de mise en cache du réseau virtuel, consultez [ce tableau](cache-how-to-premium-vnet.md#outbound-port-requirements).

## <a name="client-library-specific-guidance"></a>Conseils pour la bibliothèque cliente

- [StackExchange.Redis (.NET)](cache-best-practices-connection.md#using-forcereconnect-with-stackexchangeredis)
- [Java - Quel client dois-je utiliser ?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
- [Lettuce (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
- [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
- [Node.JS](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
- [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
- [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
- [Fournisseur d’état de session ASP.NET](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)

## <a name="next-steps"></a>Étapes suivantes

- [FAQ sur le développement d’Azure Cache pour Redis](cache-development-faq.yml)
- [Tests de performances](cache-best-practices-performance.md)
- [Basculement et mise à jour corrective pour Azure Cache pour Redis](cache-failover.md)
