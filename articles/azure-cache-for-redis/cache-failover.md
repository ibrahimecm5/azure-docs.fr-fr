---
title: Basculement et mise à jour corrective – Azure Cache pour Redis
description: Découvrez-en plus sur le basculement, la mise à jour corrective et le processus de mise à jour pour Azure Cache pour Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 11/3/2021
ms.openlocfilehash: 67b3ad49033a2fb4708b93c65e0d9f7110726cee
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850961"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Basculement et mise à jour corrective pour Azure Cache pour Redis

Pour générer des applications clientes résilientes et réussies, il est essentiel de comprendre ce qu’est un basculement dans le service Azure Cache pour Redis. Un basculement peut faire partie des opérations de gestion planifiées, ou il peut être dû à des défaillances matérielles ou réseau non planifiées. Une utilisation courante du basculement du cache est fournie lorsque le service de gestion corrige les fichiers binaires Azure Cache pour Redis.

Dans cet article, vous trouverez les informations suivantes :  

- Qu’est-ce qu’un basculement ?
- Comment le basculement se produit lors d’une mise à jour corrective.
- Comment créer une application cliente résiliente.

## <a name="what-is-a-failover"></a>Qu’est-ce qu’un basculement ?

Commençons par un aperçu du basculement pour Azure Cache pour Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Un rapide résumé de l’architecture du cache

Un cache se compose de plusieurs machines virtuelles avec des adresses IP privées et distinctes. Chaque machine virtuelle, également appelée nœud, est connectée à un équilibreur de charge partagé avec une seule adresse IP virtuelle. Chaque nœud exécute le processus serveur Redis et est accessible en utilisant le nom d’hôte et les ports Redis. Chaque nœud est considéré comme un nœud principal ou réplica. Lorsqu’une application cliente se connecte à un cache, son trafic passe par cet équilibreur de charge et est automatiquement routé au nœud principal.

Dans un cache De base, le nœud unique est toujours un nœud principal. Un cache Standard ou Premium comprend deux nœuds : l’un est choisi comme nœud principal et l’autre est le réplica. Étant donné que les caches Standard et Premium ont plusieurs nœuds, un nœud peut être indisponible pendant que l’autre continue à traiter les requêtes. Les caches en cluster sont constitués de plusieurs partitions, chacune ayant un nœud principal et un nœud réplica distincts. Une partition peut être en panne pendant que les autres restent disponibles.

> [!NOTE]
> Un cache de base n’a pas plusieurs nœuds et n’offre pas de contrat de niveau de service (SLA) en matière de disponibilité. Les caches de base sont recommandés uniquement à des fins de développement et de test. Utilisez un cache Standard ou Premium pour un déploiement sur plusieurs nœuds afin d’augmenter la disponibilité.

### <a name="explanation-of-a-failover"></a>Explication d’un basculement

Un basculement se produit lorsqu’un nœud réplica se promeut en nœud principal et que l’ancien nœud principal ferme des connexions existantes. Une fois le nœud principal rétabli, il remarque le changement de rôle et se rétrograde pour devenir un réplica. Il se connecte ensuite au nouveau nœud principal et synchronise les données. Un basculement peut être planifié ou non.

Un *basculement planifié* a lieu à deux moments différents :

- Mises à jour système, telles que les mises à niveau du système d’exploitation ou les mises à jour correctives Redis.  
- Opérations de gestion, telles que la mise à l’échelle et le redémarrage.

Étant donné que les nœuds sont avertis à l’avance de la mise à jour, ils peuvent échanger des rôles de manière coopérative et indiquer rapidement la modification à l’équilibreur de charge. Un basculement planifié se termine généralement en moins de 1 seconde.

Un *basculement non planifié* peut se produire en raison d’une défaillance matérielle, d’une défaillance de réseau ou d’autres pannes inattendues du nœud principal. Le nœud réplica se promeut en nœud principal, mais le processus prend plus de temps. Un nœud réplica doit tout d’abord détecter que son nœud principal n’est pas disponible avant de pouvoir démarrer le processus de basculement. Le nœud réplica doit également vérifier que cette défaillance non planifiée n’est ni temporaire ni locale, afin d’éviter un basculement inutile. Ce retard de détection signifie qu’un basculement non planifié se termine généralement dans un délai de 10 à 15 secondes.

## <a name="how-does-patching-occur"></a>Comment la mise à jour corrective a-t-elle lieu ?

Le service Azure Cache pour Redis met régulièrement à jour votre cache avec les fonctionnalités et les correctifs les plus récents de la plateforme. Pour corriger un cache, le service effectue les étapes suivantes :

1. Le service de gestion sélectionne un nœud à corriger.
1. Si le nœud sélectionné est un nœud principal, son nœud réplica se promeut de manière coopérative. Cette promotion est considérée comme un basculement planifié.
1. Le nœud sélectionné redémarre pour accepter les nouveaux changements, puis revient comme nœud réplica.
1. Le nœud réplica se connecte au nœud principal et synchronise les données.
1. Une fois la synchronisation des données terminée, le processus de mise à jour corrective se répète pour les nœuds restants.

La mise à jour corrective étant un basculement planifié, le nœud réplica se promeut rapidement en nœud principal. Ensuite, le nœud commence à traiter les requêtes et les nouvelles connexions. Les caches de base n’ont pas de nœud réplica et sont indisponibles tant que la mise à jour n’est pas terminée. Chaque partition d’un cache en cluster est corrigée séparément et ne ferme pas les connexions à une autre partition.

> [!IMPORTANT]
> Les nœuds sont corrigés un à un pour éviter toute perte de données. Les caches de base subissent une perte de données. Les caches en cluster sont corrigés une partition à la fois.

Si plusieurs caches se trouvent dans le même groupe de ressources et la même région, ils sont également corrigés un à un.  Les caches qui se trouvent dans des groupes de ressources différents ou des régions différentes peuvent être corrigés simultanément.

Étant donné que la synchronisation complète des données se produit avant la répétition du processus, il est peu probable que des données soient perdues lorsque vous utilisez un cache Standard ou Premium. Vous pouvez vous prémunir davantage contre la perte de données en [exportant](cache-how-to-import-export-data.md#export) les données et en activant la [persistance](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Charge de cache supplémentaire

Chaque fois qu’un basculement se produit, les caches Standard et Premium doivent répliquer les données d’un nœud à l’autre. Cette réplication entraîne une augmentation de la charge au niveau de la mémoire et du processeur du serveur. Si l’instance de cache est déjà très chargée, les applications clientes peuvent subir une latence accrue. Dans des cas extrêmes, les applications clientes peuvent recevoir des exceptions de délai d’expiration. Pour aider à atténuer l’effet d’une charge plus grande, [configurez](cache-configure.md#memory-policies) le paramètre `maxmemory-reserved` du cache.

## <a name="how-does-a-failover-affect-my-client-application"></a>Quel est l’impact d’un basculement sur mon application cliente ?

Des applications clientes pourraient recevoir des erreurs de leur Azure Cache pour Redis. Le nombre d’erreurs détectées par une application cliente dépend du nombre d’opérations en attente sur cette connexion au moment du basculement. Toute connexion acheminée via le nœud ayant fermé ses connexions rencontre des erreurs.

De nombreuses bibliothèques clientes peuvent lever différents types d’erreurs en cas d’interruption des connexions, à savoir :

- Exceptions de délai d’attente
- Exceptions de connexion
- Exceptions de socket

Le nombre et le type d’exceptions dépendent de l’emplacement de la demande dans le chemin du code quand le cache ferme ses connexions. Par exemple, une opération qui envoie une requête mais qui ne reçoit pas de réponse quand le basculement se produit peut obtenir une exception de délai d’expiration. Les nouvelles requêtes sur l’objet de connexion fermé reçoivent des exceptions de connexion jusqu’à ce que la connexion soit rétablie.

La plupart des bibliothèques clientes tentent de se reconnecter au cache si elles sont configurées pour ce faire. Toutefois, des bogues imprévus peuvent parfois placer les objets de bibliothèque dans un état irrécupérable. Si les erreurs persistent plus longtemps qu’une durée préconfigurée, l’objet de connexion doit être recréé. Dans Microsoft.NET et d’autres langages orientés objet, il est possible de recréer la connexion sans redémarrer l’application à l’aide d’un [modèle ForceReconnect](cache-best-practices-connection.md#using-forcereconnect-with-stackexchangeredis).

### <a name="can-i-be-notified-in-advance-of-planned-maintenance"></a>Puis-je être notifié à l’avance d’une maintenance planifiée ?

Azure Cache pour Redis publie des notifications via un canal de publication et d’abonnement (Pub/Sub) nommé [AzureRedisEvents](https://github.com/Azure/AzureCacheForRedis/blob/main/AzureRedisEvents.md), environ 30 secondes avant les mises à jour planifiées. Les notifications sont des notifications de runtime.

Les notifications sont destinées aux applications qui utilisent des disjoncteurs pour contourner le cache, ou aux applications qui mettent en mémoire les commandes. Par exemple, le cache peut être contourné lors des mises à jour planifiées.

Le canal `AzureRedisEvents` n’est pas un mécanisme capable de vous avertir plusieurs jours ou heures à l’avance. Le canal peut informer les clients des événements de maintenance de serveur planifiés à venir, susceptibles d’affecter la disponibilité du serveur.

De nombreuses bibliothèques clientes Redis populaires prennent en charge l’abonnement à des canaux Pub/Sub. La réception de notifications à partir du canal `AzureRedisEvents` est généralement un ajout simple à votre application cliente.

Une fois votre application abonnée à `AzureRedisEvents`, elle reçoit une notification 30 secondes avant qu’un événement de maintenance affecte un nœud. La notification contient des détails sur l’événement à venir et indique s’il affecte un nœud principal ou de réplica.

Une autre notification est envoyée quelques minutes plus tard, une fois l’opération de maintenance accomplie.

Votre application utilise le contenu de la notification pour prendre des mesures afin d’éviter d’utiliser le cache pendant la maintenance. Un cache peut implémenter un modèle de disjoncteur ayant pour effet d’acheminer le trafic à l’extérieur du cache pendant l’opération de maintenance. Au lieu de cela, le trafic est envoyé directement à un magasin persistant. La notification ne vise pas à alerter une personne en lui laissant du temps pour prendre des mesures manuelles.

Dans la plupart des cas, votre application n’a pas besoin de s’abonner aux notifications `AzureRedisEvents` ou d’y répondre. Au lieu de cela, nous vous recommandons d’implémenter une [génération dans la résilience](#build-in-resiliency).

Avec un niveau de résilience suffisant, les applications gèrent correctement tout épisode bref de perte de connexion ou d’indisponibilité du cache, comme lors d’une maintenance de nœud. Il peut également arriver que votre application perde inopinément sa connexion au cache sans avertissement de `AzureRedisEvents`, en raison d’erreurs réseau ou d’autres événements.

Nous ne vous recommandons de vous abonner à `AzureRedisEvents` que dans quelques cas notables :

- Applications sujettes à des exigences de performances extrêmes, où même des retards mineurs doivent être évités. Dans de tels scénarios, le trafic peut être réacheminé sans heurt vers un cache auxiliaire avant le début de la maintenance sur le cache actif.
- Applications qui lisent explicitement des données à partir du réplica plutôt qu’à partir des nœuds principaux. Pendant la maintenance sur un nœud de réplica, l’application peut basculer temporairement vers la lecture de données à partir des nœuds principaux.
- Applications qui ne peuvent pas courir le risque que des opérations d’écriture échouent silencieusement ou réussissent sans confirmation, ce qui peut se produire lorsque des connexions sont fermées à des fins de maintenance. Si ces situations risquent d’entraîner une perte de données dangereuse, l’application peut suspendre ou rediriger de manière proactive les commandes d’écriture avant le début planifié de la maintenance.

### <a name="client-network-configuration-changes"></a>Modifications de la configuration réseau du client

Certaines modifications de la configuration réseau côté client peuvent déclencher des erreurs de type « Aucune connexion disponible ». Ces modifications peuvent inclure :

- Le remplacement de l’adresse IP virtuelle d’une application cliente entre les emplacements intermédiaires et de production.
- Mise à l’échelle de la taille ou du nombre d’instances de votre application.

De telles modifications peuvent entraîner un problème de connectivité qui dure moins d’une minute. Il est probable que votre application cliente perde sa connexion à d’autres ressources réseau externes, mais également au service Azure Cache pour Redis.

## <a name="build-in-resiliency"></a>Intégrer la résilience 

Vous ne pouvez pas éviter complètement les basculements. Au lieu de cela, écrivez vos applications clientes pour qu’elles soient résilientes aux interruptions de connexion et aux demandes ayant échoué. La plupart des bibliothèques de clients se reconnectent automatiquement au point de terminaison de cache, mais seules quelques-unes retentent les demandes ayant échoué. Selon le scénario de l’application, il peut être utile d’utiliser la logique de nouvelle tentative avec retrait.

### <a name="how-do-i-make-my-application-resilient"></a>Comment faire rendre mon application résiliente ?

Reportez-vous aux modèles de conception ci-dessous pour créer des clients résilients, en particulier aux modèles de disjoncteur et de nouvelle tentative :

- [Modèles de fiabilité – Modèles de conception cloud](/azure/architecture/framework/resiliency/reliability-patterns#resiliency)
- [Guide de nouvelle tentative pour les services Azure – Bonnes pratiques pour les applications cloud](/azure/architecture/best-practices/retry-service-specific)
- [Implémenter de nouvelles tentatives avec interruption exponentielle](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)

Pour tester la résilience d’une application cliente, utilisez un [redémarrage](cache-administration.md#reboot) comme déclencheur manuel pour les interruptions de connexion.

En outre, nous vous recommandons de [planifier des mises à jour](cache-administration.md#schedule-updates) sur un cache pour appliquer des correctifs de runtime Redis pendant des fenêtres hebdomadaires spécifiques. Ces fenêtres correspondent généralement à des périodes où le trafic de l’application cliente est faible pour éviter les incidents potentiels.

Pour plus d’informations, consultez [Résilience des connexions](cache-best-practices-connection.md).

## <a name="next-steps"></a>Étapes suivantes

- [Planifiez les mises à jour](cache-administration.md#schedule-updates) pour votre cache.
- Testez la résilience des applications à l’aide d’un [redémarrage](cache-administration.md#reboot).
- [Configurez](cache-configure.md#memory-policies) des réservations et des stratégies de mémoire.
