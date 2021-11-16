---
title: Meilleures pratiques pour la résilience des connexions
titleSuffix: Azure Cache for Redis
description: Découvrez comment rendre vos connexions Azure Cache pour Redis résilientes.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 11/3/2021
ms.author: shpathak
ms.openlocfilehash: d8e5f95e78db7c46ad1c52401b938acc37af6b4f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850942"
---
# <a name="connection-resilience"></a>Résilience des connexions

## <a name="retry-commands"></a>Commandes de nouvelle tentative

Configurez vos connexions client pour effectuer de nouvelles tentatives avec un backoff exponentiel. Pour plus d’informations, consultez les [Instructions relatives aux nouvelles tentatives](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis).

## <a name="test-resiliency"></a>Tester la résilience

Testez la résilience de votre système aux interruptions de connexion en effectuant un [redémarrage](cache-administration.md#reboot) pour simuler un correctif. Pour plus d’informations sur le tests de performance, consultez [Test de performance](cache-best-practices-performance.md).

## <a name="tcp-settings-for-linux-hosted-client-applications"></a>Paramètres TCP pour les applications clientes hébergées par Linux

Certaines versions de Linux utilisent des paramètres TCP optimistes par défaut. Les paramètres TCP peuvent créer une situation dans laquelle une connexion cliente à un cache ne peut pas être rétablie pendant une longue période quand un serveur Redis ne répond plus avant de fermer la connexion correctement. Le rétablissement d’une connexion peut échouer si le nœud principal de votre Azure Cache pour Redis devient indisponible, par exemple, pour une maintenance non planifiée.

Nous vous recommandons d’utiliser les paramètres TCP suivants :

|Paramètre  |Valeur |
|---------|---------|
| *net.ipv4.tcp_retries2*   | 5 |

Pour plus d’informations sur ce scénario, consultez [La connexion ne se rétablit pas pendant 15 minutes lors de l’exécution sous Linux](https://github.com/StackExchange/StackExchange.Redis/issues/1848#issuecomment-913064646). Bien que cette discussion concerne la bibliothèque StackExchange.Redis, d’autres bibliothèques clientes s’exécutant sur Linux sont également affectées. L’explication reste néanmoins utile et vous pouvez la généraliser à d’autres bibliothèques.

## <a name="using-forcereconnect-with-stackexchangeredis"></a>Utilisation de ForceReconnect avec StackExchange.Redis

Dans de rares cas, StackExchange.Redims ne parvient pas à se reconnecter après l’interruption d’une connexion. Dans ce cas, le redémarrage du client ou la création d’un nouveau `ConnectionMultiplexer` corrige le problème. Nous vous recommandons d’utiliser un modèle `ConnectionMultiplexer` singleton tout en permettant aux applications de forcer régulièrement une reconnexion. Jetez un coup d’œil à l’exemple de projet de démarrage rapide qui correspond le mieux à l’infrastructure et à la plateforme que votre application utilise. Vous pouvez voir des exemples de ce modèle de code dans nos [démarrages rapide](https://github.com/Azure-Samples/azure-cache-redis-samples).

Les utilisateurs du `ConnectionMultiplexer` doivent gérer toutes les erreurs `ObjectDisposedException` susceptibles de se produire suite à la suppression de l’ancien modèle.

Appelez `ForceReconnectAsync()` pour `RedisConnectionExceptions` et `RedisSocketExceptions`. Vous pouvez également appeler `ForceReconnectAsync()` pour `RedisTimeoutExceptions`, mais uniquement si vous utilisez des valeurs `ReconnectMinInterval` et `ReconnectErrorThreshold` généreuses. Dans le cas contraire, l’établissement de nouvelles connexions peut provoquer une défaillance en cascade sur un serveur qui a dépassé le délai d’attente parce qu’il est déjà surchargé.

## <a name="configure-appropriate-timeouts"></a>Configurer les délais d’attente appropriés

Deux valeurs de délai d’attente doivent impérativement être prises en compte dans la résilience de la connexion : le [délai de connexion](#connect-timeout) et le [délai d’expiration de commande](#command-timeout).

### <a name="connect-timeout"></a>Connect timeout

Le `connect timeout` est le temps pendant lequel votre client attend l’établissement d’une connexion avec un serveur Redis. Configurez votre bibliothèque de client pour qu’elle observe un délai de connexion (`connect timeout`) de 5 secondes, afin de laisser au système le temps de se connecter même dans des conditions de sollicitation plus importante du processeur.

Un `connection timeout` court ne garantit pas que la connexion puisse être établie dans ce laps de temps. Si un problème se produit (par exemple, une forte sollicitation du processeur du client ou du serveur), une valeur de délai de connexion (`connection timeout`) faible entraîne l’échec de la tentative de connexion. Ce comportement aggrave souvent une situation déjà détériorée. Au lieu d’améliorer la situation, la diminution des délais d’attente aggrave le problème en forçant le système à redémarrer le processus de tentative de reconnexion, avec le risque de générer au final une boucle *connexion -> échec -> nouvelle tentative*.

### <a name="command-timeout"></a>Délai d’expiration de la commande

La plupart des bibliothèques de client ont une autre configuration de délai d’attente pour les `command timeouts`, qui est le temps pendant lequel le client attend une réponse du serveur Redis. Même si nous recommandons un paramètre initial inférieur à cinq secondes, envisagez de définir une valeur `command timeout` plus élevée ou plus faible en fonction de votre scénario et de la taille des valeurs stockées dans votre cache.

Si le `command timeout` est trop court, il se peut que la connexion semble instable. Toutefois, si le délai de commande (`command timeout`) est trop long, il se peut que votre application doive attendre longtemps avant de pouvoir déterminer si la commande va expirer ou non.

## <a name="avoid-client-connection-spikes"></a>Éviter les pics de connexion client

Évitez de créer de nombreuses connexions en même temps lors de la reconnexion après une perte de connexion. De la même façon que des [délais de connexion courts](#configure-appropriate-timeouts) peuvent entraîner des interruptions plus longues, le démarrage simultané de nombreuses tentatives de reconnexion peut augmenter la charge du serveur et allonger le temps nécessaire pour la reconnexion de tous les clients.

Si vous reconnectez de nombreuses instances clientes, envisagez d’échelonner les nouvelles connexions afin d’éviter un pic abrupt du nombre de clients connectés.

> [!NOTE]
> Si vous utilisez la bibliothèque de client `StackExchange.Redis`, définissez `abortConnect` sur `false` dans votre chaîne de connexion.  Nous vous recommandons de laisser le `ConnectionMultiplexer` gérer la reconnexion. Pour plus d’informations, consultez [Meilleures pratiques StackExchange.Redis](./cache-management-faq.yml#stackexchangeredis-best-practices).

## <a name="avoid-leftover-connections"></a>Éviter les connexions inutilisées

Les caches sont limités quant au nombre de connexions clientes par niveau de cache. Assurez-vous que, lorsque votre application cliente recrée des connexions, elle ferme et supprime les anciennes connexions.

## <a name="advance-maintenance-notification"></a>Notification de maintenance à l’avance

Utilisez des notifications pour être informé de la maintenance à venir. Pour plus d’informations, consultez [Puis-je être notifié à l’avance d’une maintenance planifiée ?](cache-failover.md#can-i-be-notified-in-advance-of-planned-maintenance).

## <a name="schedule-maintenance-window"></a>Fenêtre Planifier la maintenance

Ajustez vos paramètres de cache à la maintenance. Pour plus d’informations sur la création d’une fenêtre de maintenance afin de réduire les effets négatifs sur votre cache, consultez [Planifier les mises à jour](cache-administration.md#schedule-updates).

## <a name="more-design-patterns-for-resilience"></a>Autres modèles de conception pour la résilience

Appliquez des modèles de conception pour la résilience. Pour plus d’informations, consultez [Comment rendre mon application résiliente](cache-failover.md#how-do-i-make-my-application-resilient).

## <a name="idle-timeout"></a>Délai d’inactivité

Azure Cache pour Redis ayant actuellement un délai d’inactivité de 10 minutes pour les connexions. le paramètre de délai d’inactivité dans votre application cliente doit être inférieur à 10 minutes. La plupart des bibliothèques clientes courantes disposent d’un paramètre de configuration qui permet aux bibliothèques clientes d’envoyer des commandes Redis `PING` à un serveur Redis automatiquement et périodiquement. Toutefois, lors de l’utilisation de bibliothèques clientes sans ce type de paramètre, les applications clientes sont elles-mêmes responsables du maintien de la connexion active.

## <a name="next-steps"></a>Étapes suivantes

- [Meilleures pratiques relatives pour le développement](cache-best-practices-development.md)
- [Questions fréquentes (FAQ) sur le développement d’Azure Cache pour Redis](cache-development-faq.yml)
- [Basculement et correctif](cache-failover.md)