---
title: Réplicas en lecture - Azure Database for MariaDB
description: 'Découvrez les réplicas en lecture dans Azure Database for MariaDB : choix des régions, création de réplicas, connexion à des réplicas, supervision de la réplication et arrêt de la réplication.'
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.custom: references_regions
ms.openlocfilehash: 3b7306555e59759f79ffad8e984f0688820247de
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641291"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Réplicas en lecture dans Azure Database for MariaDB

La fonctionnalité de réplica en lecture vous permet de répliquer les données d’un serveur Azure Database for MariaDB sur un serveur en lecture seule. Vous pouvez effectuer la réplication à partir du serveur source vers cinq réplicas au maximum. Les réplicas sont mis à jour de manière asynchrone à l’aide de la technologie de réplication selon la position du fichier journal binaire (binlog) du moteur MariaDB avec ID de transaction global (GTID). Pour découvrir plus en détail la réplication binlog, consultez la [vue d’ensemble de la réplication binlog](https://mariadb.com/kb/en/library/replication-overview/).

Les réplicas sont de nouveaux serveurs que vous gérez de manière similaire aux serveurs Azure Database for MariaDB classiques. Pour chaque réplica en lecture, vous êtes facturé en fonction de la capacité de calcul provisionnée dans les vCores et du stockage provisionné en Go/mois.

Pour en savoir plus sur la réplication GTID, consultez la [documentation sur la réplication MariaDB](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Cet article contient des références au terme *esclave*, un terme que Microsoft n’utilise plus. Lorsque le terme sera supprimé du logiciel, nous le supprimerons de cet article.

## <a name="when-to-use-a-read-replica"></a>Quand utiliser un réplica en lecture

La fonctionnalité de réplica en lecture contribue à améliorer les performances et la scalabilité des charges de travail nécessitant une lecture intensive. Les charges de travail de lecture peuvent être isolées sur les réplicas, tandis que les charges de travail d’écriture peuvent être dirigées vers le serveur principal.

Un scénario courant est d’avoir les charges de travail décisionnelles et analytiques qui utilisent le réplica en lecture comme source de données pour la création de rapports.

Dans la mesure où les réplicas sont en lecture seule, ils ne réduisent pas directement les charges relatives à la capacité d’écriture sur le serveur principal. Cette fonctionnalité ne cible pas les charges de travail nécessitant une écriture intensive.

La fonctionnalité de réplica en lecture utilise la réplication asynchrone. La fonctionnalité n’est pas destinée aux scénarios de réplication synchrone. Il y aura un délai mesurable entre la source et le réplica. Les données du réplica finissent par devenir cohérentes avec les données du serveur principal. Utilisez cette fonctionnalité pour les charges de travail pouvant s’adapter à ce délai.

## <a name="cross-region-replication"></a>Réplication entre régions

Vous pouvez créer un réplica en lecture dans une autre région à partir de votre serveur source. La réplication entre régions peut être utile pour des scénarios tels que la planification de la récupération d’urgence ou le rapprochement des données de vos utilisateurs.

Vous pouvez disposer d’un serveur source dans toute [région Azure Database for MariaDB](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  Un serveur source peut avoir un réplica dans sa région jumelée ou dans les régions de réplica universelles. L’image ci-dessous montre les régions de réplica disponibles en fonction de votre région source.

[![Régions des réplicas en lecture](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Régions de réplica universelles

Vous pouvez créer un réplica en lecture dans les régions suivantes, quel que soit l’emplacement de votre serveur source. Les régions de réplica universelles prises en charge sont les suivantes :

Australie Est, Australie Sud-Est, Brésil Sud, Canada Centre, Canada Est, USA Centre, Asie Est, USA Est, USA Est 2, Japon Est, Japon Ouest, Corée Centre, Corée Sud, USA Centre Nord, Europe Nord, USA Centre Sud, Asie Sud-Est, Royaume-Uni Sud, Royaume-Uni Ouest, Europe Ouest, USA Ouest, USA Ouest 2, USA Centre-Ouest.

### <a name="paired-regions"></a>Régions jumelées

Outre les régions de réplica universelles, vous pouvez créer un réplica en lecture dans la région Azure jumelée de votre serveur source. Si vous ne connaissez pas la région jumelée de votre région, vous trouverez plus d’informations dans l’[article sur les régions jumelées Azure](../best-practices-availability-paired-regions.md).

Si vous utilisez des réplicas entre régions pour la planification de la récupération d’urgence, nous vous recommandons de créer le réplica dans la région jumelée plutôt que dans une autre région. Les régions jumelées évitent les mises à jour simultanées et hiérarchisent l’isolement physique et la résidence des données.  

Il existe toutefois quelques limitations à prendre en compte : 

* Disponibilité régionale : Azure Database for MariaDB est disponible dans les régions France Centre, Émirats arabes unis Nord et Allemagne Centre. Toutefois, leurs régions jumelées ne sont pas disponibles.

* Paires unidirectionnelles : Certaines régions Azure sont jumelées dans une seule direction. Ces régions incluent Inde Ouest, Brésil Sud et US Gov Virginie. 
   Cela signifie qu’un serveur source dans la région Inde Ouest peut créer un réplica dans la région Inde Sud. Toutefois, un serveur source dans la région Inde Sud ne peut pas créer de réplica dans la région Inde Ouest. En effet, la région secondaire de la région Inde Ouest est Inde Sud, mais la région secondaire de la région Inde Sud n’est pas Inde Ouest.

## <a name="create-a-replica"></a>Créer un réplica

> [!IMPORTANT]
> La fonctionnalité de réplica en lecture est disponible uniquement pour les serveurs Azure Database for MariaDB dans les niveaux tarifaires Usage général ou Mémoire optimisée. Vérifiez que le serveur source se trouve dans l’un de ces niveaux tarifaires.

Si un serveur source ne dispose d’aucun serveur réplica, le serveur source redémarre tout d’abord afin de se préparer pour la réplication.

Quand vous démarrez le flux de travail de création de réplica, un serveur Azure Database for MariaDB vide est créé. Le nouveau serveur est rempli avec les données qui se trouvaient sur le serveur source. Le temps de création dépend de la quantité de données présentes sur le serveur source et du temps écoulé depuis la dernière sauvegarde complète hebdomadaire. Le temps nécessaire peut aller de quelques minutes à plusieurs heures.

> [!NOTE]
> Si aucune alerte de stockage n’est configurée sur vos serveurs, nous vous recommandons de le faire. L’alerte vous informe quand un serveur approche de sa limite de stockage, ce qui affecte la réplication.

Découvrez comment [créer un réplica en lecture dans le portail Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Se connecter à un réplica

Au moment de sa création, un réplica hérite des règles de pare-feu du serveur source. Ensuite, ces règles sont indépendantes du serveur source.

Le réplica hérite du compte Administrateur du serveur source. Tous les comptes d’utilisateur sur le serveur source sont répliqués sur les réplicas en lecture. Vous pouvez uniquement vous connecter à un réplica en lecture à l’aide des comptes d’utilisateur disponibles sur le serveur source.

Vous pouvez vous connecter au réplica à l’aide de son nom d’hôte et d’un compte d’utilisateur valide, comme vous le faites sur un serveur Azure Database for MariaDB classique. Sur un serveur nommé **myreplica**, à l’aide du nom d’utilisateur administrateur **myadmin**, vous pouvez vous connecter au réplica via l’interface de ligne de commande mysql :

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

À l’invite, entrez le mot de passe du compte d’utilisateur.

## <a name="monitor-replication"></a>Superviser la réplication

Azure Database for MariaDB fournit la métrique **Décalage de la réplication en secondes** dans Azure Monitor. Cette métrique est disponible pour les réplicas uniquement.

Cette métrique est calculée à l’aide de la métrique `seconds_behind_master` disponible dans la commande `SHOW SLAVE STATUS` de MariaDB.

Définissez une alerte qui vous informe quand le décalage de la réplication atteint une valeur inacceptable pour votre charge de travail.

## <a name="stop-replication"></a>Arrêter la réplication

Vous pouvez arrêter la réplication entre un serveur source et un réplica. Une fois la réplication arrêtée entre un serveur source et un réplica en lecture, celui-ci devient un serveur autonome. Les données du serveur autonome sont les données disponibles sur le réplica au lancement de la commande d’arrêt de la réplication. Le serveur autonome ne se met pas au même niveau que le serveur source.

Lorsque vous décidez d’arrêter la réplication pour un réplica, celui-ci perd tous les liens avec son serveur source précédent et d’autres réplicas. Il n’existe aucun basculement automatique entre un serveur source et son réplica.

> [!IMPORTANT]
> Le serveur autonome ne peut pas être retransformé en réplica.
> Avant d’arrêter la réplication sur un réplica en lecture, vérifiez que celui-ci contient toutes les données nécessaires.

Découvrez comment [arrêter la réplication sur un réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Basculement

Il n’existe aucun basculement automatique entre les serveurs source et réplica.

Étant donné que la réplication est asynchrone, il existe un décalage entre le serveur source et le réplica. Le niveau de décalage dépend d’un certain nombre de facteurs, comme la charge de travail exécutée sur le serveur source et la latence qui existe entre les centres de données. Dans la plupart des cas, le décalage du réplica va de quelques secondes à quelques minutes. Pour connaître le décalage d’un réplica, consultez la métrique *Décalage de la réplication*, qui est disponible pour chaque réplica. Cette métrique indique le temps écoulé depuis la dernière transaction réexécutée. Il est recommandé d’observer votre réplica sur une période donnée afin de déterminer le décalage moyen. Vous pouvez configurer une alerte afin d’être averti lorsque le décalage d’un réplica sort de la plage définie et prendre les mesures nécessaires.

> [!Tip]
> Si vous basculez vers le réplica, le décalage qui existe au moment où vous supprimez la liaison entre le réplica et le serveur source indiquera la quantité de données perdues.

Une fois que vous avez décidé de basculer vers un réplica :

1. Arrêtez la réplication vers le réplica.

   Cette étape est nécessaire pour que le serveur de réplication puisse accepter les écritures. Dans le cadre de ce processus, le serveur de réplication est dissocié du serveur principal. En général, une fois que le processus d’arrêt de la réplication est lancé, le processus back-end prend environ 2 minutes. Pour comprendre les implications d’une telle action, consultez la section [Arrêter la réplication](#stop-replication).

2. Faites pointer votre application vers l’ancien réplica.

   Chaque serveur est associé à une chaîne de connexion unique. Mettez à jour votre application pour qu’elle pointe vers l’ancien réplica au lieu du serveur principal.

Une fois que votre application est en mesure de traiter les lectures et les écritures, le basculement est terminé. Le temps d’arrêt de votre application dépend du moment où vous détectez le problème et où vous effectuez les étapes 1 et 2 ci-dessus.

## <a name="considerations-and-limitations"></a>Observations et limitations

### <a name="pricing-tiers"></a>Niveaux de tarification

Les réplicas en lecture ne sont actuellement disponibles que dans les niveaux tarifaires Usage général et Mémoire optimisée.

> [!NOTE]
> Le coût d’exécution du serveur de réplication est basé sur la région où le serveur de réplication est en cours d’exécution.

### <a name="source-server-restart"></a>Redémarrage du serveur source

Lorsque vous créez un réplica pour un serveur source qui n’en a pas, ce dernier commence par redémarrer afin de se préparer à la réplication. Tenez-en compte et effectuez ces opérations en période creuse.

### <a name="new-replicas"></a>Nouveaux réplicas

Un réplica en lecture est créé en tant que serveur Azure Database for MariaDB. Un serveur existant ne peut pas être transformé en réplica. Vous ne pouvez pas créer un réplica d’un autre réplica en lecture.

### <a name="replica-configuration"></a>Configuration du réplica

Un réplica est créé à partir de la même configuration que celle du serveur principal. Une fois le réplica créé, vous pouvez changer plusieurs paramètres indépendamment du serveur source : génération de calcul, vCores, stockage, période de rétention de sauvegarde et version du moteur MariaDB. Le niveau tarifaire peut également être changé indépendamment, sauf vers ou depuis le niveau De base.

> [!IMPORTANT]
> Avant de mettre à jour une configuration de serveur source avec de nouvelles valeurs, mettez à jour la configuration du réplica avec des valeurs égales ou supérieures. Ainsi, vous aurez la garantie que le réplica peut suivre les changements apportés au serveur principal.

Les règles de pare-feu et les paramètres sont transmis du serveur source au réplica lorsque le réplica est créé. Par la suite, les règles du réplica sont indépendantes.

### <a name="stopped-replicas"></a>Réplicas arrêtés

Si vous arrêtez la réplication entre un serveur source et un réplica en lecture, le réplica arrêté devient un serveur autonome qui accepte aussi bien les lectures que les écritures. Le serveur autonome ne peut pas être retransformé en réplica.

### <a name="deleted-source-and-standalone-servers"></a>Serveurs sources et autonomes supprimés

Lorsqu’un serveur source est supprimé, la réplication est arrêtée sur tous les réplicas en lecture. Ces réplicas deviennent automatiquement des serveurs autonomes pouvant accepter des lectures et des écritures. Le serveur source lui-même est supprimé.

### <a name="user-accounts"></a>Comptes d'utilisateurs

Les utilisateurs sur le serveur source sont répliqués sur les réplicas en lecture. Vous ne pouvez vous connecter à un réplica en lecture qu’avec des comptes d’utilisateur disponibles sur le serveur source.

### <a name="server-parameters"></a>Paramètres de serveur

Pour empêcher les données de se désynchroniser et pour éviter leur perte ou leur endommagement potentiels, certains paramètres de serveur sont verrouillés et ne peuvent pas être mis à jour lors de l’utilisation des réplicas en lecture.

Les paramètres de serveur suivants sont verrouillés sur les serveurs sources et réplicas :

* [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
* [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

Le paramètre [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) est verrouillé sur les serveurs réplicas.

Pour mettre à jour l’un des paramètres ci-dessus sur le serveur source, supprimez les serveurs réplicas, mettez à jour la valeur du paramètre sur le serveur principal, puis recréez les réplicas.

### <a name="other"></a>Autres

* La création d’un réplica d’un réplica n’est pas prise en charge.
* Les tables en mémoire peuvent entraîner la désynchronisation des réplicas. Il s’agit d’une limitation de la technologie de réplication MariaDB.
* Vérifiez que les tables du serveur source possèdent des clés primaires. L’absence de clés primaires peut entraîner une latence de réplication entre la source et les réplicas.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [créer et gérer des réplicas en lecture clusters avec le portail Azure](howto-read-replicas-portal.md)
* Découvrir comment [créer et gérer des réplicas en lecture avec l’interface de ligne de commande Azure et l’API REST](howto-read-replicas-cli.md)
