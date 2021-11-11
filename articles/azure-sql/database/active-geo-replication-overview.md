---
title: La géoréplication active
description: Utilisez la géoréplication active pour créer des bases de données secondaires de bases de données individuelles accessibles en lecture dans Azure SQL Database dans des régions identiques ou différentes.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 10/25/2021
ms.openlocfilehash: ca958a3e7a43864caa673cd31736b1e661e7f608
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465361"
---
# <a name="active-geo-replication"></a>La géoréplication active
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

La géoréplication active est une fonctionnalité qui vous permet de créer une base de données secondaire lisible en permanence pour une base de données primaire. La base de données secondaire accessible en lecture peut être dans la même région Azure que la base de données primaire, ou, plus communément, dans une autre région. Ce type de base de données secondaire accessible en lecture est également appelé géo-secondaires ou géo-réplicas.

La géoréplication active est conçue comme une solution de continuité d’activité qui vous permet d’effectuer une reprise d’activité rapide au niveau des bases de données individuelles en cas de panne régionale ou à plus grande échelle. Une fois la géoréplication configurée, vous pouvez lancer un basculement géographique vers une zone géographique secondaire dans une autre région Azure. Le géo-basculement est initié par programme par l’application ou manuellement par l’utilisateur.

> [!NOTE]
> La fonctionnalité de géoréplication active pour Azure SQL Hyperscale [est désormais disponible en préversion publique](https://aka.ms/hsgeodr). Les limitations actuelles incluent : un seul géosecondaire dans la même région ou dans une autre région, basculement forcé et planifié non pris en charge pour le moment, restauration de base de données à partir d'un géosecondaire non prise en charge, utilisation d'un géosecondaire en tant que base de données source pour la copie de base de données ou en tant que principal pour un autre géosecondaire non prise en charge
> 
> Si vous devez faire du géosecondaire une zone géographique primaire (base de données accessible en écriture), procédez comme suit :
> 1. Rompez le lien de géoréplication à l’aide du cmdlet [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) dans PowerShell ou [az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete_link) pour Azure CLI. La base de données secondaire deviendra alors une base de données autonome accessible en écriture. Toutes les modifications de données validées sur la base de données primaire mais pas encore répliquées sur la base de données secondaire seront perdues. Ces modifications peuvent être récupérées lorsque l'ancienne base de données primaire est disponible ou, dans certains cas, en restaurant l'ancienne base de données primaire au dernier point disponible dans le temps.
> 2. Si l'ancienne base de données primaire est disponible, supprimez-la, puis configurez la géoréplication pour la nouvelle base de données primaire (une nouvelle base de données secondaire sera amorcée). 
> 3. Mettez à jour les chaînes de connexion de votre application en conséquence.

> [!NOTE]
> La géoréplication active n’est pas prise en charge par Azure SQL Managed Instance. Pour le basculement géographique d’instances de SQL Managed Instance, utilisez des [groupes de basculement automatique](auto-failover-group-overview.md).

> [!NOTE]
> Pour migrer des bases de données SQL depuis Azure Allemagne en utilisant la géoréplication active, consultez [Migrer une base de données SQL en utilisant la géoréplication active](../../germany/germany-migration-databases.md#migrate-sql-database-using-active-geo-replication).

Si votre application requiert un point de terminaison de connexion stable et une prise en charge du géo-basculement automatique en plus de la géoréplication, utilisez les [groupes de basculement automatique](auto-failover-group-overview.md).

Le diagramme suivant illustre la configuration standard d’une application cloud géoredondante avec la géoréplication active.

![Géoréplication active](./media/active-geo-replication-overview/geo-replication.png)

Si, pour une raison quelconque, votre base de données primaire échoue, vous pouvez lancer un basculement géographique vers l’une de vos bases de données secondaires. Quand une base de données secondaire est promue au rôle principal, tous les autres réplicas secondaires sont automatiquement liés à la nouvelle base de données primaire.

Vous pouvez gérer la géoréplication et initier un basculement géographique à l’aide des éléments suivants :

- [Portail Azure](active-geo-replication-configure-portal.md)
- [PowerShell : base de données unique](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell : pool élastique](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL : base de données unique ou pool élastique](/sql/t-sql/statements/alter-database-azure-sql-database)
- [API REST : base de données unique](/rest/api/sql/replicationlinks)

La géoréplication active tire parti de la technologie de [groupe de disponibilité Always on](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) pour répliquer de façon asynchrone le journal des transactions généré sur le réplica principal sur tous les géo-réplicas. À un moment donné, une base de données secondaire peut être légèrement en retard sur la base de données primaire, mais les données secondaires restent cohérentes au niveau transactionnel. En d’autres termes, les modifications effectuées par les transactions non validées ne sont pas visibles. 

> [!NOTE]
> La géoréplication active réplique les modifications en diffusant le journal des transactions de la base de données du réplica principal vers les réplicas secondaires. Elle n’est pas liée à la [réplication transactionnelle](/sql/relational-databases/replication/transactional/transactional-replication), qui réplique les modifications en exécutant des commandes DML (INSERT, UPDATE, DELETE) sur les abonnés.

La redondance entre régions, fournie par la géoréplication, permet aux applications de récupérer rapidement d’une perte permanente d’une région Azure entière, ou d’une partie d’une région, résultant de catastrophes naturelles, de graves erreurs humaines ou d’actes de malveillance. Le RPO de géoréplication se trouve dans la [Vue d’ensemble de la continuité des activités](business-continuity-high-availability-disaster-recover-hadr-overview.md).

La figure suivante présente un exemple de géoréplication active configurée avec une base de données primaire située dans la région USA Centre Nord, ainsi qu’une base de données géosecondaire située dans la région USA Centre Sud.

![Relation de géo-réplication](./media/active-geo-replication-overview/geo-replication-relationship.png)

En plus de la récupération d’urgence, la géoréplication active peut être utilisée dans les scénarios suivants :

- **Migration de base de données** : vous pouvez vous servir de la géoréplication active pour migrer une base de données d’un serveur vers un autre serveur avec un temps d’arrêt minimal.
- **Mises à niveau de l’application** : vous pouvez créer une base de données secondaire supplémentaire faisant office de copie de restauration automatique lors des mises à niveau de l’application.

Pour arriver à une continuité d’activité totale, l’ajout d’une redondance régionale de base de données n’est qu’une partie de la solution. La récupération d’une application (service) de bout en bout après une défaillance catastrophique implique la récupération de tous les composants constituant le service et tous les services dépendants. En voici quelques exemples : logiciel client (il peut s’agir par exemple d’un navigateur avec un code JavaScript personnalisé), serveurs web frontaux, ressources de stockage et DNS. Il est essentiel que tous les composants résistent aux mêmes défaillances et redeviennent disponibles dans l’objectif de délai de récupération (RTO) de votre application. Par conséquent, vous devez identifier tous les services dépendants et comprendre les garanties et les fonctionnalités qu’ils fournissent. Ensuite, vous devez prendre les mesures appropriées pour vous assurer que votre service fonctionne pendant le basculement des services dont il dépend. Pour plus d’informations sur la conception de solutions pour la récupération d’urgence, consultez la page [Designing Cloud Solutions for Disaster Recovery Using active geo-replication](designing-cloud-solutions-for-disaster-recovery.md) (Conception de solutions cloud pour la récupération d’urgence à l’aide de la géo-réplication active).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Terminologie et fonctionnalités associées à la géoréplication active

- **Réplication asynchrone automatique**

  Vous pouvez uniquement créer une zone géographique secondaire pour une base de données existante. La zone géosecondaire peut être créée sur n’importe quel serveur logique, autre que le serveur avec la base de données primaire. Une fois créé, le géo-réplica est rempli avec les données de la base de données primaire. Ce processus est appelé amorçage. Une fois la base de données géosecondaire créée et amorcée, les mises à jour de la base de données primaire sont automatiquement répliquées de manière asynchrone sur le géo-réplica. La réplication asynchrone signifie que les transactions sont validées sur la base de données primaire avant leur réplication.

- **Accès en lecture aux réplicas géosecondaires**

  Une application peut accéder à un géo-réplica pour exécuter des requêtes en lecture seule avec des principaux de sécurité identiques ou différents de ceux utilisés pour accéder à la base de données primaire. Pour plus d’informations, consultez [Utiliser des réplicas en lecture seule pour décharger des charges de travail de requêtes en lecture seule](read-scale-out.md).

   > [!IMPORTANT]
   > Vous pouvez utiliser la géoréplication pour créer des réplicas secondaires dans la même région que la base de données primaire. Vous pouvez utiliser ces réplicas secondaires pour répondre aux scénarios d’échelle horizontale en lecture dans la même région. Toutefois, un réplica secondaire dans la même région ne fournit pas de résilience supplémentaire aux défaillances catastrophiques ou aux pannes à grande échelle, et n’est donc pas une cible de basculement appropriée à des fins de récupération d’urgence. De même,il ne garantit en rien l’isolation de la zone de disponibilité. Utilisez le niveau de service Critique pour l’entreprise ou Premium avec une [configuration de zone redondante](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) ou le niveau de service Usage général avec une [configuration de zone redondante](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) pour isoler la zone de disponibilité.
   >

- **Basculement géographique planifié**

  Le géo-basculement planifié bascule les rôles des bases de données primaire et géo-secondaires après avoir effectué la synchronisation complète des données. Un basculement planifié n’entraîne pas de perte de données. La durée du basculement géographique planifié dépend de la taille du journal des transactions sur la base de données primaire qui doit être synchronisée avec la base géo-secondaire. Le géo-basculement planifié est conçu pour les scénarios suivants :

  - Simuler des récupérations d’urgence (DR) en production lorsque la perte de données n’est pas acceptable ; 
  - Déplacer les bases de données vers une autre région ; 
  - Renvoyer les bases de données vers la région primaire une fois la panne éliminée (restauration automatique).

- **Basculement géographique non planifié**

  Un géo-basculement non planifié ou forcé fait immédiatement basculer la base de données géosecondaire vers le rôle primaire sans synchronisation avec la base de données primaire. Toutes les transactions validées sur la base de données primaire qui ne sont pas répliquées sur la base de données secondaire sont perdues. Cette opération est conçue comme une méthode de récupération pendant les pannes lorsque le serveur principal n’est pas accessible, mais que la disponibilité de la base de données doit être restaurée rapidement. Lorsque le réplica principal d’origine est de nouveau en ligne, il est automatiquement reconnecté, réamorcé à l’aide des données principales actuelles et devient un nouveau réplica géosecondaire.

  > [!IMPORTANT]
  > Après un géo-basculement planifié ou non planifié, le point de terminaison de connexion de la base de données principale est modifié, car le nouveau réplica principal est maintenant situé sur un autre serveur logique.

- **Bases de données géosecondaires accessibles en lecture**

  Jusqu’à quatre zones géographiques secondaires peuvent être créées pour une base de données primaire. S’il n’existe qu’une seule base de données secondaire, en cas d’échec de celle-ci, l’application est exposée à un risque plus élevé jusqu’à la création d’une nouvelle base de données secondaire. S’il existe plusieurs bases de données secondaires, l’application reste protégée même en cas d’échec de l’une des bases de données secondaires. Des bases de données secondaires supplémentaires peuvent également être utilisées pour effectuer un scale-out des charges de travail en lecture seule.

  > [!TIP]
  > Si vous utilisez la géoréplication active pour créer une application mondialement distribuée et que vous avez besoin de fournir un accès en lecture seule aux données dans plus de quatre régions, vous pouvez créer une base de données secondaire pour une base de données secondaire (processus connu sous le nom de chaînage), afin de créer des géo-réplicas supplémentaires. Le décalage de réplication sur les géo-réplicas chaînés peut être plus élevé que sur les géo-réplicas connectés directement au serveur principal. La configuration des topologies de géoréplication chaînée est prise en charge uniquement par programme, et non à partir du Portail Azure.

- **Géoréplication des bases de données dans un pool élastique**

  Chaque base de données géosecondaire peut être une base de données autonome ou une base de données dans un pool élastique. Le choix du pool élastique pour chaque base de données géosecondaire est séparé et ne dépend pas de la configuration de tous les autres réplicas dans la topologie (primaire ou secondaire). Chaque pool élastique est contenu dans un serveur logique unique. Étant donné que les noms de bases de données sur un serveur logique doivent être uniques, plusieurs géo-réplicas de la même base de données primaire ne peuvent jamais partager un pool élastique.

- **Géo-basculement et restauration automatique contrôlés par l’utilisateur**

  Une base de données géosecondaire qui a terminé l’amorçage initial peut être explicitement basculée vers le rôle principal à tout moment par l’application ou par l’utilisateur. Pendant une panne où le réplica principal est inaccessible, seul un géo-basculement non planifié peut être utilisé. Qui promeut immédiatement une géo-secondaire en tant que nouvelle base de données primaire. Lorsque la panne est atténuée, le système définit automatiquement le serveur principal récupéré comme géo-secondaire et le met à jour avec le nouveau réplica principal. En raison de la nature asynchrone de la géoréplication, les transactions récentes peuvent être perdues lors de géo-basculements non planifiés si le réplica principal échoue avant que ces transactions ne soient répliquées sur un serveur géosecondaire. Quand une base de données primaire avec plusieurs géo-réplicas bascule, le système reconfigure automatiquement les relations de réplication et lie les bases de données géosecondaires restantes à la base de données nouvellement promue comme primaire sans aucune intervention de l’utilisateur. Une fois la panne à l’origine du géo-basculement résolue, il peut être judicieux de rétablir la base de données primaire dans sa région d’origine. Pour ce faire, appelez un géo-basculement planifié.

## <a name="prepare-for-geo-failover"></a><a name="preparing-secondary-database-for-failover"></a> Préparer le géo-basculement

Pour vous assurer que votre application peut accéder immédiatement au nouveau réplica principal après le géo-basculement, assurez-vous que l’authentification et l’accès réseau de votre serveur secondaire sont correctement configurés. Pour plus d’informations, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](active-geo-replication-security-configure.md). Vérifiez également que la stratégie de rétention de sauvegarde sur la base de données secondaire correspond à celle du réplica principal. Ce paramètre ne fait pas partie de la base de données et n’est pas répliqué à partir du serveur principal. Par défaut, le géo-réplica est configuré avec une période de rétention de récupération jusqu’à une date et heure par défaut de sept jours. Pour plus d’informations, consultez [Sauvegardes automatisées d’une base de données SQL](automated-backups-overview.md).

> [!IMPORTANT]
> Si votre base de données est membre d'un groupe de basculement, vous ne pouvez pas lancer son basculement à l'aide de la commande de basculement de la géoréplication. Utilisez la commande de basculement du groupe. Pour basculer une base de données individuelle, vous devez d'abord la supprimer du groupe de basculement. Pour plus d’informations, consultez [Groupes de basculement automatique](auto-failover-group-overview.md).

## <a name="configure-geo-secondary"></a><a name="configuring-secondary-database"></a> Configurer la base de données géosecondaire

Les bases de données primaire et géosecondaire doivent offrir le même niveau de service. Il est également vivement recommandé de configurer la base de données géosecondaire avec les mêmes redondance de stockage de sauvegarde et taille de calcul (DTU ou vCores) que la base de données primaire. En cas de charge de travail d’écriture importante de la base de données primaire, une base de données géosecondaire dotée d’une taille de calcul inférieure peut ne pas suivre. Cela entraînera un décalage de réplication sur le géo-réplica et peut entraîner une indisponibilité de la base de données géosecondaire. Pour atténuer ces risques, la géoréplication active réduit si nécessaire le taux de journalisation des transactions de la base de données primaire pour permettre à ses bases de données secondaires de rattraper le retard.

Après un basculement, une configuration de base de données géosecondaire déséquilibrée peut aussi altérer le niveau de performance de l’application en raison de la capacité de calcul insuffisante de la nouvelle base de données primaire. Dans ce cas, il est nécessaire d’effectuer un scale-up de la base de données, afin qu’elle puisse bénéficier des ressources suffisantes, ce qui peut nécessiter beaucoup de temps. Un basculement à [haute disponibilité](high-availability-sla.md) est également nécessaire à la fin du processus de scale-up, pouvant interrompre les charges de travail de l’application.

Si vous décidez de créer le géo-réplica avec une taille de calcul inférieure, vous devez surveiller le taux d’E/S de journal sur le serveur principal dans le temps. Cela vous permet d’estimer la taille de calcul minimale du géo-réplica requis pour supporter la charge de réplication. Par exemple, si votre base de données primaire est P6 (1 000 DTU) et si son taux d’E/S du journal est maintenu à 50 %, la base de données géosecondaire doit être au moins P4 (500 DTU). Pour récupérer les données d’E/S historiques du journal, utilisez la vue [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). Pour récupérer les données d’E/S récentes dans le journal avec une granularité plus élevée qui reflète mieux les pics à court terme, utilisez la vue [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).

> [!TIP]
> La limitation du nombre d’E/S de journalisation des transactions sur la base de données primaire en raison d’une réduction de la taille de calcul sur une base de données géosecondaire est signalée à l’aide du type d’attente HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO, visible dans les vues de base de données [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) et [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).
>
> Le nombre d’E/S de journalisation des transactions sur la base de données primaire peut être limité pour des raisons autres qu’une taille de calcul inférieure sur une base de données géosecondaire. Ce genre de limitation peut se produire même si la base de données géo-secondaire a une taille de calcul identique ou supérieure à celle de la base de données primaire. Pour plus d’informations, notamment sur les types d’attente pour les différents genres de limitation de nombre d’E/S de journalisation, consultez [Gouvernance relative au taux de journalisation des transactions](resource-limits-logical-server.md#transaction-log-rate-governance).

Par défaut, la redondance de stockage de sauvegarde de la base de données géosecondaire est identique à celle de la base de données primaire. Vous pouvez configurer la base de données géosecondaire avec une redondance de stockage de sauvegarde différente. Les sauvegardes sont toujours effectuées sur la base de données primaire. Si la base de données secondaire est configurée avec une redondance de stockage de sauvegarde différente, après le géo-basculement effectué lors de la promotion de la base de données secondaire en base de données primaire, les nouvelles sauvegardes sont stockées et facturées en fonction du type de stockage (RA-GRS, ZRS, LRS) sélectionné sur la nouvelle base de données primaire (ancienne secondaire). 

## <a name="cross-subscription-geo-replication"></a>Géoréplication entre abonnements

Pour créer un réplica géosecondaire dans un abonnement différent de celui de la base de données principale (qu’il soit sous le même locataire Azure Active Directory ou non), suivez les étapes de cette section.

1. Ajoutez l’adresse IP de l’ordinateur client exécutant les commandes T-SQL ci-dessous aux pare-feu de serveur **des serveurs principal et secondaire**. Vous pouvez confirmer cette adresse IP en exécutant la requête suivante tout en étant connecté au serveur principal à partir du même ordinateur client.
  
   ```sql
   select client_net_address from sys.dm_exec_connections where session_id = @@SPID;
   ``` 

   Pour plus d’informations, consultez [Configurer le pare-feu](firewall-configure.md).

2. Dans la base de données master sur le serveur **principal**, créez une connexion d’authentification SQL dédiée à la configuration de la géoréplication active. Ajustez le nom de connexion et le mot de passe si nécessaire.

   ```sql
   create login geodrsetup with password = 'ComplexPassword01';
   ```

3. Dans la même base de données, créez un utilisateur pour la connexion, puis ajoutez-le au rôle `dbmanager` :

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup;
   ```

4. Prenez note de la valeur SID de la nouvelle connexion. Obtenez la valeur SID à l’aide de la requête suivante.

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup';
   ```

5. Connectez-vous à la base de données **primaire** (et non à la base de données master) et créez un utilisateur pour la même connexion.

   ```sql
   create user geodrsetup for login geodrsetup;
   ```

6. Dans la même base de données, ajoutez l’utilisateur au rôle `db_owner`.

   ```sql
   alter role db_owner add member geodrsetup;
   ```

7. Dans la base de données master sur le serveur **secondaire**, créez la même connexion que sur le serveur principal, en utilisant le même nom, le même mot de passe et le même SID. Remplacez la valeur de SID hexadécimale dans l’exemple de commande ci-dessous par celle obtenue à l’étape 4.

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E;
   ```

8. Dans la même base de données, créez un utilisateur pour la connexion, puis ajoutez-le au rôle `dbmanager`.

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup;
   ```

9. Connectez-vous à la base de données master sur le serveur **principal** à l’aide de la nouvelle connexion`geodrsetup` et lancez la création géosecondaire sur le serveur secondaire. Ajustez le nom de la base de données et le nom du serveur secondaire au besoin. Une fois la commande exécutée, vous pouvez surveiller la création géosecondaire en interrogeant la vue [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) dans la base de données **primaire**, et la vue [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) dans la base de données master sur le serveur **principal**. Le temps nécessaire à la création d’un géo-réplica secondaire dépend de la taille de la base de données primaire.

   ```sql
   alter database [dbrep] add secondary on server [servername];
   ```

10. Une fois la base de données géo-secondaire créée, les utilisateurs, les connexions et les règles de pare-feu créés par cette procédure peuvent être supprimés.

> [!NOTE]
> Les opérations de géoréplication entre abonnements, y compris la configuration et le basculement, sont uniquement prises en charge via les commandes T-SQL.
> 
> L’ajout d’une base de données géosecondaire à l’aide de commandes T-SQL n’est pas pris en charge lorsque les serveurs primaire et/ou secondaires ont un [point de terminaison privé](private-endpoint-overview.md) configuré et que [l’accès au réseau public est refusé](connectivity-settings.md#deny-public-network-access). Si le point de terminaison privé est configuré mais que l’accès au réseau public est autorisé, l’ajout d’une base de données géosecondaire lors de la connexion au serveur principal à partir d’une adresse IP publique est pris en charge. Une fois la base de données géosecondaire ajoutée, un accès public peut être refusé.
> 
> La création d’un géo-réplica sur un serveur logique dans un autre locataire Azure n’est pas prise en charge lorsque seule l’authentification [Azure Active Directory](https://techcommunity.microsoft.com/t5/azure-sql/azure-active-directory-only-authentication-for-azure-sql/ba-p/2417673) pour Azure SQL est activée sur le serveur logique primaire ou secondaire.

## <a name="keep-credentials-and-firewall-rules-in-sync"></a><a name="keeping-credentials-and-firewall-rules-in-sync"></a> Synchronisation des informations d’identification et des règles de pare-feu

Lorsque vous utilisez un accès réseau public pour la connexion à la base de données, nous vous recommandons d’utiliser des [règles de pare-feu IP au niveau de la base de données](firewall-configure.md) pour les bases de données géo-répliquées. Ces règles sont répliquées avec la base de données, ce qui garantit que toutes les bases de données géosecondaires ont les mêmes règles de pare-feu IP que la base de données primaire. Cette approche évite aux clients de devoir configurer et tenir à jour manuellement les règles de pare-feu sur les serveurs hébergeant les bases de données primaire et secondaires. De même, l’utilisation des [utilisateurs de base de données autonome](logins-create-manage.md) pour l’accès aux données garantit que les bases de données primaires et secondaires ont toujours les mêmes informations d’identification d’authentification. Ainsi, après un géo-basculement, il n’y a aucune interruption due à des incompatibilités d’informations d’identification.

## <a name="scale-primary-database"></a><a name="upgrading-or-downgrading-primary-database"></a> Mettre à l’échelle une base de données primaire

Vous pouvez effectuer un scale-up/down sur une base de données primaire avec une taille de calcul différente (au sein du même niveau de service) sans déconnecter les bases de données géosecondaires. Lors du scale-up, nous vous recommandons de commencer par la base de données géosecondaire, puis de terminer avec la base de données primaire. Lors du scale-down, inversez l’ordre : commencez par la base de données primaire, puis terminez par la base de données secondaire.

> [!NOTE]
> Si vous avez créé une base de données géosecondaire dans le cadre de la configuration du groupe de basculement, il n’est pas recommandé d’effectuer un scale-down dessus. En effet, votre couche Données pourrait manquer de capacité pour traiter votre charge de travail normale après un géo-basculement.

> [!IMPORTANT]
> Pour permettre la mise à l’échelle vers le niveau de service supérieur (édition) d’une base de données primaire dans un groupe de basculement, la base de données secondaire doit avoir été préalablement mise à l’échelle vers le niveau supérieur. Par exemple, si vous souhaitez mettre à l’échelle la base de données principale de Usage général vers Critique pour l’entreprise, vous devez d’abord mettre à l’échelle la base de données géosecondaire vers Critique pour l’entreprise. Si vous essayez de mettre à l’échelle la base de données primaire ou géosecondaire d’une manière qui enfreint cette règle, vous recevez l’erreur suivante :
>
> `The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="prevent-loss-of-critical-data"></a><a name="preventing-the-loss-of-critical-data"></a> Empêcher la perte de données critiques

En raison de la latence élevée des réseaux étendus, la géoréplication utilise un mécanisme de réplication asynchrone. La réplication asynchrone rend la possibilité de perte de données inévitable en cas de défaillance de la base de données primaire. Pour protéger les transactions critiques d’une perte de données, le développeur d’applications peut appeler la procédure stockée [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) immédiatement après la validation de la transaction. L’appel de `sp_wait_for_database_copy_sync` bloque le thread appelant jusqu’à ce que la dernière transaction validée ait été transmise et renforcée dans le journal des transactions de la base de données secondaire. Toutefois, il n’attend pas que les transactions transmises soient relues (réeffectuées) sur la base de données secondaire. `sp_wait_for_database_copy_sync` est limité à un lien de géoréplication spécifique. Tout utilisateur disposant de droits de connexion à la base de données primaire peut appeler cette procédure.

> [!NOTE]
> `sp_wait_for_database_copy_sync` empêche la perte de données après un géo-basculement pour des transactions spécifiques, mais ne garantit pas une synchronisation complète pour l’accès en lecture. Le délai causé par un appel de procédure `sp_wait_for_database_copy_sync` peut être significatif et dépend de la taille du journal des transactions pas encore transmis à la base de données primaire au moment de l’appel.

## <a name="monitor-geo-replication-lag"></a><a name="monitoring-geo-replication-lag"></a> Supervision du décalage de la géoréplication

Pour surveiller le décalage par rapport au RPO, utilisez la colonne *replication_lag_sec* de [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) sur la base de données primaire. Elle affiche le décalage en secondes entre les transactions validées sur la base de données primaire et les transactions renforcées dans le journal des transactions sur la base de données secondaire. Par exemple, si le retard est d’une seconde, cela signifie que si la base de données primaire est affectée par une panne à ce moment et qu’un géo-basculement est initié, les transactions validées au cours de la dernière seconde seront perdues.

Pour mesurer le décalage ayant trait aux modifications de la base de données primaire renforcées sur la base de données géosecondaire, comparez la valeur *last_commit* sur la base de données secondaire avec la même valeur sur la base de données primaire.

> [!TIP]
> Sur la base de données primaire, si *replication_lag_sec* présente une valeur NULL, cela signifie que la base de données primaire ne connaît pas précisément l’état de retard de la base de données géosecondaire. Cela se produit généralement après le redémarrage du processus et relève d'une situation temporaire. Envisagez d’envoyer une alerte si *replication_lag_sec* présente une valeur NULL pendant une période prolongée. Cela peut indiquer que la base de données géosecondaire ne peut pas communiquer avec la base de données primaire en raison d’une défaillance de connectivité.
> 
> D’autres cas de figure peuvent aussi être à l’origine d’une importante différence de valeur *last_commit* entre les bases de données primaire et secondaire. Par exemple, si une validation intervient sur la base de données primaire après une longue période sans modifications, la différence augmente considérablement avant de redevenir rapidement nulle. Envisagez d’envoyer une alerte si la différence entre ces deux valeurs demeure importante pendant une période prolongée.

## <a name="programmatically-manage-active-geo-replication"></a><a name="programmatically-managing-active-geo-replication"></a> Gestion de la géoréplication active par programmation

Comme indiqué plus haut, la géoréplication active peut aussi être gérée par programmation à l’aide de T-SQL, d’Azure PowerShell et de l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles. La géoréplication active comprend un ensemble d’API Azure Resource Manager pour la gestion, notamment [l’API REST Azure SQL Database](/rest/api/sql/) et les [applets de commande Azure PowerShell](/powershell/azure/). Ces API prennent en charge le contrôle d’accès en fonction du rôle Azure (Azure RBAC). Pour plus d’informations sur l’implémentation de rôles d’accès, consultez la page sur le [contrôle d’accès en fonction du rôle Azure (RBAC Azure)](../../role-based-access-control/overview.md).

### <a name="t-sql-manage-geo-failover-of-single-and-pooled-databases"></a><a name="t-sql-manage-failover-of-single-and-pooled-databases"></a> T-SQL : Gérer le géo-basculement des bases de données uniques et mises en pool

> [!IMPORTANT]
> Ces commandes T-SQL s’appliquent uniquement à la géoréplication active et ne s’appliquent pas aux groupes de basculement. Par conséquent, elles ne s’appliquent pas non plus à SQL Managed Instance, qui prend uniquement en charge les groupes de basculement.

| Commande | Description |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Utilise l’argument **ADD SECONDARY ON SERVER** afin de créer une base de données secondaire pour une base de données existante puis lance la réplication des données |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Utilise l’argument **FAILOVER** ou **FORCE_FAILOVER_ALLOW_DATA_LOSS** pour basculer d’une base de données secondaire à une base de données principale afin de lancer le basculement |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Utilise l’argument **REMOVE SECONDARY ON SERVER** pour mettre fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifiée. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Retourne des informations concernant tous les liens de réplication existants pour chaque base de données sur un serveur. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtient l’heure de la dernière réplication, le dernier décalage de la réplication et d’autres informations sur le lien de réplication pour une base de données spécifique. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Affiche l’état de toutes les opérations de base de données, y compris les modifications apportées aux liens de réplication. |
| [sys.sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |Entraîne l’attente de l’application jusqu’à ce que toutes les transactions validées soient renforcées dans le journal des transactions d’un géo-réplica. |
|  | |

### <a name="powershell-manage-geo-failover-of-single-and-pooled-databases"></a><a name="powershell-manage-failover-of-single-and-pooled-databases"></a> PowerShell : Gérer le géo-basculement des bases de données uniques et mises en pool

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm.

| Applet de commande | Description |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtient une ou plusieurs bases de données. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |Crée une base de données secondaire pour une base de données existante puis lance la réplication des données. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |Bascule d’une base de données secondaire à une base de données principale afin de lancer le basculement. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |Met fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifiée. |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Obtient les liens de géoréplication pour une base de données. |
|  | |

> [!TIP]
> Pour plus d’exemples de scripts, consultez [Configurer et basculer une base de données unique à l’aide de la géoréplication active](scripts/setup-geodr-and-failover-database-powershell.md) et [Configurer et basculer une base de données mise en pool à l’aide de la géoréplication active](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).

### <a name="rest-api-manage-geo-failover-of-single-and-pooled-databases"></a><a name="rest-api-manage-failover-of-single-and-pooled-databases"></a> API REST : Gérer le géo-basculement des bases de données uniques et mises en pool

| API | Description |
| --- | --- |
| [.Créer ou mettre à jour la base de données (createMode=Restore)](/rest/api/sql/databases/createorupdate) |Crée, met à jour ou restaure une base de données principale ou secondaire. |
| [Créer ou mettre à jour l’état de la base de données](/rest/api/sql/databases/createorupdate) |Retourne l’état durant une opération de création. |
| [Définir la base de données secondaire comme principale (basculement planifié)](/rest/api/sql/replicationlinks/failover) |Définit la base de données secondaire principale via basculement à partir de la base de données primaire actuelle. **Cette option n’est pas prise en charge pour SQL Managed Instance.**|
| [Définir la base de données secondaire comme principale (basculement non planifié)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Définit la base de données secondaire principale via basculement à partir de la base de données primaire actuelle. Cette opération peut entraîner une perte de données. **Cette option n’est pas prise en charge pour SQL Managed Instance.**|
| [Obtenir un lien de réplication](/rest/api/sql/replicationlinks/get) |Obtient un liens de réplication spécifique pour une base de données particulière dans un partenariat de géo-réplication. Récupère les informations visibles dans la vue de catalogue sys.geo_replication_links. **Cette option n’est pas prise en charge pour SQL Managed Instance.**|
| [Liens de réplication - Liste par base de données](/rest/api/sql/replicationlinks/listbydatabase) | Obtient tous les liens de réplication pour une base de données donnée dans un partenariat de géo-réplication. Récupère les informations visibles dans la vue de catalogue sys.geo_replication_links. |
| [Supprimer un lien de réplication](/rest/api/sql/replicationlinks/delete) | Supprime un lien de réplication de base de données. Opération impossible pendant le basculement. |
|  | |

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des exemples de scripts, consultez :
  - [Configurer et basculer une seule base de données à l’aide de la géoréplication active](scripts/setup-geodr-and-failover-database-powershell.md).
  - [Configurer et basculer une base de données mise en pool à l’aide de la géoréplication active](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).
- SQL Database prend également en charge les groupes de basculement automatique. Pour plus d’informations, voir la section sur l’utilisation des [Groupes de basculement automatique](auto-failover-group-overview.md).
- Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Pour en savoir plus sur les sauvegardes automatisées d’une base de données Azure SQL, consultez [Sauvegardes automatisées d’une base de données SQL](automated-backups-overview.md).
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](recovery-using-backups.md).
- Pour en savoir plus sur les exigences d’authentification pour de nouveaux serveurs et bases de données primaires, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](active-geo-replication-security-configure.md).
