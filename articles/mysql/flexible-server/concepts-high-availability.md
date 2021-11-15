---
title: Haute disponibilité redondante interzone avec Azure Database pour MySQL - Serveur flexible
description: Obtenez une vue d’ensemble conceptuelle de la haute disponibilité redondante interzone dans Azure Database pour MySQL - Serveur flexible.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1d1ec1b6196dd543b9310a945fc9b07e26673c84
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472767"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server"></a>Concepts de haute disponibilité dans le serveur flexible Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database pour MySQL - Serveur flexible permet de configurer la haute disponibilité avec le basculement automatique. La solution haute disponibilité est conçue pour éviter toute perte de données en raison de défaillances, et que la base de données devienne un point de défaillance unique dans votre architecture logicielle. Quand la haute disponibilité est configurée, le serveur flexible approvisionne et gère automatiquement un réplica de secours. Deux modèles d'architecture à haute disponibilité sont disponibles :

* **Haute disponibilité redondante interzone**. Cette option est recommandée pour une isolation et une redondance complètes de l’infrastructure sur plusieurs zones de disponibilité. Elle offre le niveau de disponibilité le plus élevé, mais vous oblige à configurer la redondance des applications entre zones. La haute disponibilité redondante interzone est préférable quand vous voulez obtenir le niveau de disponibilité le plus élevé en cas de défaillance de l’infrastructure dans la zone de disponibilité et où la latence dans la zone de disponibilité est acceptable. Elle ne peut être activée que lors de la création du serveur. La haute disponibilité redondante interzone est disponible dans un [sous-ensemble de régions Azure](./overview.md#azure-regions), où chaque région prend en charge plusieurs [zones de disponibilité](../../availability-zones/az-overview.md) et où des [partages de fichiers Premium redondants interzone](../..//storage/common/storage-redundancy.md#zone-redundant-storage) sont disponibles.

* **Haute disponibilité dans la même zone**. Cette option est préférable pour la redondance de l’infrastructure avec une latence réseau inférieure, car le serveur principal et le serveur de secours se trouveront dans la même zone de disponibilité. Elle offre une haute disponibilité sans qu’il soit nécessaire de configurer la redondance des applications entre les zones. La haute disponibilité dans la même zone est préférable quand vous voulez obtenir le niveau de disponibilité le plus élevé au sein d’une même zone de disponibilité avec la latence réseau la plus faible. La haute disponibilité dans la même zone est disponible dans [toutes les régions Azure](./overview.md#azure-regions) où il est possible d’utiliser un serveur flexible Azure Database pour MySQL.  

## <a name="zone-redundant-ha-architecture"></a>Architecture à haute disponibilité redondante interzone
 
Lorsque vous déployez un serveur avec une haute disponibilité redondante interzone, deux serveurs sont créés : 
- Un serveur principal dans une zone de disponibilité
- Un serveur réplica de secours est approvisionné dans une zone de disponibilité Azure différente avec la même configuration que le serveur primaire, à savoir le niveau de calcul, la taille de calcul, la taille de stockage et la configuration réseau
 
Vous pouvez choisir la zone de disponibilité pour le réplica principal et le réplica de secours. Le fait de placer les serveurs de base de données de secours et les applications de secours dans la même zone réduit la latence. Cela vous permet également de mieux vous préparer à des situations de récupération d’urgence et des scénarios de « zone indisponible ».

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Diagramme qui montre l’architecture pour la haute disponibilité redondante interzone.":::

Les fichiers de données et les fichiers journaux sont hébergés dans un [stockage redondant interzone (ZRS)](../../storage/common/storage-redundancy.md#redundancy-in-the-primary-region). Ces fichiers sont répliqués sur le serveur de secours via la réplication de niveau stockage disponible avec le stockage redondant interzone. En cas de basculement : 
- Le réplica de secours est activé. 
- Les fichiers journaux binaires du serveur primaire continuent de s’appliquer au serveur de secours pour le mettre en ligne à la dernière transaction validée sur le serveur primaire. 

Les journaux dans le stockage redondant interzone sont accessibles même lorsque le serveur principal n’est pas disponible. Cette disponibilité permet de garantir qu’il n’y a aucune perte de données. Une fois le réplica de secours activé et les journaux binaires appliqués, le serveur réplica de secours actif prend les rôles du serveur principal. Le DNS est mis à jour afin que les connexions client soient dirigées vers le nouveau serveur principal quand le client se reconnecte. Le basculement est entièrement transparent depuis l’application cliente et ne nécessite aucune action de votre part. La solution haute disponibilité rétablit ensuite l’ancien serveur principal dès que possible et en fait le serveur de secours.
 
Le nom du serveur de base de données est utilisé pour connecter les applications au serveur primaire. Les informations du réplica de secours ne sont pas exposées à un accès direct. Les validations et les écritures sont acceptées après que les fichiers journaux ont été vidés sur le stockage redondant interzone du serveur principal. En raison de la technologie de réplication synchrone utilisée pour le stockage redondant interzone, vous pouvez vous attendre à une latence accrue de 5 à 10 % pour les écritures et les validations d’application.
 
Les sauvegardes automatiques, qu’il s’agisse d’instantanés ou de sauvegardes de fichiers journaux, sont effectuées sur un stockage redondant interzone depuis le serveur de base de données principal.

## <a name="same-zone-ha-architecture"></a>Architecture de haute disponibilité de zone identique
 
Lorsque vous déployez un serveur avec la haute disponibilité dans la même zone, deux serveurs sont créés dans la même zone : 
- Un serveur principal
- Un serveur réplica de secours ayant la même configuration que le serveur primaire (niveau de calcul, taille du calcul, taille du stockage et configuration du réseau) 

Le serveur de secours offre une redondance de l’infrastructure avec une machine virtuelle distincte (calcul). Cette redondance réduit le temps de basculement et la latence du réseau entre l’application et le serveur de base de données en raison de la colocalisation.

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Diagramme qui montre l’architecture pour la haute disponibilité dans la même zone.":::

Les fichiers de données et les fichiers journaux sont hébergés dans un [stockage localement redondant (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage). Ces fichiers sont répliqués sur le serveur de secours via la réplication de niveau stockage disponible avec le stockage localement redondant.

En cas de basculement : 
- Le réplica de secours est activé. 
- Les fichiers journaux binaires du serveur primaire continuent de s’appliquer au serveur de secours pour le mettre en ligne à la dernière transaction validée sur le serveur primaire. 

Les journaux dans le stockage localement redondant sont accessibles même lorsque le serveur principal n’est pas disponible. Cette disponibilité permet de garantir qu’il n’y a aucune perte de données. Une fois le réplica de secours activé et les journaux binaires appliqués, le réplica de secours actif prend les rôles du serveur principal. Le DNS est mis à jour pour rediriger les connexions vers le nouveau serveur principal lorsque le client se reconnecte. Le basculement est entièrement transparent depuis l’application cliente et ne nécessite aucune action de votre part. La solution haute disponibilité rétablit ensuite l’ancien serveur principal dès que possible et en fait le serveur de secours.
 
Le nom du serveur de base de données est utilisé pour connecter les applications au serveur primaire. Les informations du réplica de secours ne sont pas exposées à un accès direct. Les validations et les écritures sont acceptées après que les fichiers journaux ont été vidés sur le stockage localement redondant du serveur principal. Étant donné que les réplicas primaire et de secours se trouvent dans la même zone, il y a moins de décalage de réplication et une latence plus faible entre le serveur d’application et le serveur de base de données. La configuration de haute disponibilité dans la même zone n’offre pas de haute disponibilité quand des infrastructures dépendantes sont défaillantes pour la zone de disponibilité concernée. Il y aura un temps d’arrêt jusqu’à ce que tous les services dépendants reviennent en ligne pour cette zone de disponibilité.
 
Les sauvegardes automatiques, qu’il s’agisse d’instantanés ou de sauvegardes de fichiers journaux, sont effectuées sur un stockage localement redondant à partir du serveur de base de données principal.

>[!Note]
>Tant pour la haute disponibilité redondante interzone que pour la haute disponibilité dans la même zone :
>* En cas de défaillance, le temps nécessaire pour que le réplica de secours prenne le rôle de serveur principal dépend de l’application de journal binaire sur le serveur de secours. Nous vous recommandons donc d’utiliser des clés primaires sur toutes les tables pour réduire le temps de basculement. Les temps de basculement sont généralement compris entre 60 et 120 secondes. 
>* Le serveur de secours n’est pas disponible pour les opérations de lecture ou d’écriture. Il s’agit d’un remplacement passif pour permettre un basculement rapide.
>* Utilisez toujours un nom de domaine complet (FQDN) pour vous connecter à votre serveur primaire. Évitez d’utiliser une adresse IP pour vous connecter. En cas de basculement, une fois que les rôles de serveur primaire et de secours sont inversés, un enregistrement DNS A peut changer. Cette modification empêcherait l’application de se connecter au nouveau serveur primaire si une adresse IP est utilisée dans la chaîne de connexion.

## <a name="failover-process"></a>Processus de basculement 
 
### <a name="planned-forced-failover"></a>Planifié : Basculement forcé
 
Le basculement forcé d’Azure Database pour MySQL : Serveur flexible vous permet de forcer manuellement un basculement. Cette capacité vous permet de tester la fonctionnalité avec les scénarios de votre application et vous permet de vous préparer à des pannes. 

Le basculement forcé déclenche un basculement qui active le réplica en attente pour devenir le serveur primaire avec le même nom de serveur de base de données en mettant à jour l’enregistrement DNS. Le serveur principal d’origine est redémarré et devient le réplica de secours. Les connexions des clients sont déconnectées et doivent être reconnectées pour reprendre leurs opérations. 

Le temps de basculement global dépend de la charge de travail actuelle et du dernier point de contrôle. En général, il est supposé que le basculement prend entre 60 et 120 secondes.
 
### <a name="unplanned-automatic-failover"></a>Non planifié : Basculement automatique 
 
Les interruptions de service non planifiées peuvent être provoquées par des bogues logiciels ou des erreurs d’infrastructure, comme des défaillances de calcul, de réseau ou de stockage, ou des pannes de courant qui affectent la disponibilité de la base de données. En cas d’indisponibilité de la base de données, la réplication sur le réplica de secours est interrompue, et le réplica de secours est activé pour devenir la base de données primaire. Le système DNS est mis à jour et les clients se reconnectent au serveur de base de données pour reprendre leurs opérations. 

Le temps de basculement global doit être compris entre 60 et 120 secondes. Mais, en fonction de l’activité sur le serveur de base de données primaire au moment du basculement, par exemple si des transactions sont volumineuses et que le temps de récupération est long, le basculement peut prendre plus de temps.

## <a name="monitoring-for-high-availability"></a>Surveillance de la haute disponibilité
L’intégrité de votre haute disponibilité est supervisée et signalée en permanence dans la page Vue d’ensemble. Voici les statuts de réplication :

| **État** | **Description** |
| :----- | :------ |
| **NotEnabled** | La haute disponibilité redondante interzone n’est pas activée. |
| **ReplicatingData** | Le remplacement rattrape le serveur principal après sa création. |
| **FailingOver** | Le serveur de base de données est en cours de basculement, du serveur principal vers le réplica de secours. |
| **Healthy** | La haute disponibilité redondante interzone présente un état stable et sain. |
| **RemovingStandby** | Un utilisateur a supprimé le réplica de secours et la suppression est en cours.| 

##  <a name="limitations"></a>Limites 
 
Voici quelques réflexions à prendre en compte lorsque vous utilisez la haute disponibilité :
* La haute disponibilité redondante interzone peut être définie uniquement lors de la création du serveur flexible.
* La haute disponibilité n’est pas prise en charge au niveau du calcul expansible.
* Un redémarrage du serveur de base de données primaire pour récupérer les modifications de paramètres statiques a également pour effet de redémarrer le réplica de secours.
* Les réplicas de lecture ne sont pas pris en charge pour les serveurs haute disponibilité.
* La réplication des données entrantes n’est pas prise en charge pour les serveurs haute disponibilité.
* Le mode GTID est activé parce que la solution HA utilise un identificateur de transaction global (Global Transaction Identifier, GTID). Vérifiez si votre charge de travail présente des [restrictions ou des limitations en lien avec la réplication avec GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids-restrictions.html).  
 
## <a name="frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ)
 
**Puis-je utiliser le réplica de secours pour des opérations de lecture ou d’écriture ?** </br>
Le serveur de secours n’est pas disponible pour les opérations de lecture ou d’écriture. Il s’agit d’un remplacement passif pour permettre un basculement rapide.</br>
**Le basculement entraîne-t-il une perte de données ?**</br>
Les journaux dans le stockage redondant interzone sont accessibles même lorsque le serveur principal n’est pas disponible. Cette disponibilité permet de garantir qu’il n’y a aucune perte de données. Une fois le réplica de secours activé et les journaux binaires appliqués, le réplica prend les rôles du serveur principal. </br>
**Dois-je effectuer une action après un basculement ?**</br>
Les basculements sont entièrement transparents à partir de l’application cliente. Aucune action de votre part n’est nécessaire. L’application doit simplement utiliser la logique de nouvelle tentative pour ses connexions. </br>
**Que se passe-t-il si je ne choisis pas une zone spécifique pour mon réplica de secours ? Puis-je modifier la zone par la suite ?**</br>
Si vous ne choisissez pas de zone, l’une d’elles sera sélectionnée au hasard. Il ne s’agira pas de celle utilisée pour le serveur principal. Pour modifier la zone ultérieurement, vous pouvez définir **Haute disponibilité** sur **Désactivée** dans le volet **Haute disponibilité**, puis la redéfinir sur **Redondant interzone** et choisir une zone.</br>
**La réplication entre le serveur principal et le serveur réplica de secours est-elle synchrone ?**</br>
 La réplication entre le réplica primaire et celui de remplacement est similaire au [mode semisynchrone](https://dev.mysql.com/doc/refman/5.7/en/replication-semisync.html) de MySQL. Lorsqu’une transaction est validée, elle n’est pas nécessairement validée dans le réplica de remplacement. Toutefois, lorsque le serveur principal n’est pas disponible, celui de secours réplique toutes les modifications de données à partir du serveur principal pour s’assurer qu’il n’y a aucune perte de données.</br> 
**Un basculement est-il opéré vers le réplica de secours dans tous les cas de défaillance non planifiée ?**</br>
En cas de plantage d’une base de données ou de défaillance d’un nœud, la machine virtuelle du serveur flexible est redémarrée sur le même nœud. En même temps, un basculement automatique est déclenché. Si le redémarrage de la machine virtuelle du serveur flexible réussit avant la fin du basculement, l’opération de basculement est annulée. La détermination du serveur à utiliser comme réplica principal dépend du processus qui se termine en premier.</br>
**L’utilisation de la haute disponibilité a-t-elle un impact sur les performances ?**</br>
La haute disponibilité redondante interzone peut entraîner une chute de la latence de 5 à 10 % si l’application se connecte au serveur de base de données entre des zones de disponibilité où la latence du réseau est relativement supérieure (2 à 4 ms). Pour la haute disponibilité dans la même zone, étant donné que les réplicas primaire et de secours se trouvent dans la même zone, le décalage de réplication est plus faible. Il y a moins de latence entre le serveur d’application et le serveur de base de données lorsqu’ils se trouvent dans la même zone de disponibilité Azure.</br>
**Comment la maintenance de mon serveur haute disponibilité se produit-elle ?**</br>
Les événements planifiés, comme la mise à l’échelle du calcul et les mises à niveau de version mineures se produisent sur le serveur primaire et le serveur de secours en même temps. Vous pouvez définir la [fenêtre de maintenance planifiée](./concepts-maintenance.md) pour un serveur haute disponibilité de la même façon que pour un serveur flexible. Le temps d’arrêt sera le même que celui du serveur flexible Azure Database pour MySQL quand la haute disponibilité est désactivée. L’utilisation du mécanisme de basculement pour réduire les temps d’arrêt des serveurs haute disponibilité est sur notre feuille de route et sera bientôt disponible. </br>
**Puis-je effectuer une restauration à un instant dans le passé (PITR) de mon serveur haute disponibilité ?**</br>
Vous pouvez effectuer une [PITR](./concepts-backup-restore.md#point-in-time-restore) d’un serveur flexible Azure Database pour MySQL pour lequel la haute disponibilité est activée vers un nouveau serveur flexible Azure Database pour MySQL pour lequel la haute disponibilité est désactivée. Si le serveur source a été créé avec une haute disponibilité redondante interzone, vous pouvez par la suite activer la haute disponibilité redondante interzone ou la haute disponibilité dans la même zone sur le serveur restauré. Si le serveur source a été créé avec une haute disponibilité dans la même zone, vous pouvez uniquement activer la haute disponibilité dans la même zone sur le serveur restauré.</br>
**Puis-je activer la haute disponibilité sur un serveur après avoir créé le serveur ?**</br>
La haute disponibilité redondante interzone doit être activée lors de la création du serveur. Vous pouvez activer la haute disponibilité dans la même zone après avoir créé le serveur. Avant d’activer la haute disponibilité dans la même zone, assurez-vous que les paramètres de serveur « enforce_gtid_consistency » et [« gtid_mode »](./concepts-read-replicas.md#global-transaction-identifier-gtid) sont activés.</br> 
**Puis-je désactiver la haute disponibilité pour un serveur après sa création ?** </br>
Vous pouvez désactiver la haute disponibilité sur un serveur une fois que vous l’avez créé. La facturation s’arrête immédiatement.  </br>
**Comment puis-je réduire le temps d’arrêt ?**</br>
Vous devez être en mesure de réduire les temps d’arrêt de votre application, même si vous n’utilisez pas la haute disponibilité. Les interruptions de service, comme les correctifs planifiés, les mises à niveau de version mineures ou les opérations initiées par le client, comme la mise à l’échelle du calcul, peuvent être effectuées dans des fenêtres de maintenance planifiées. Pour limiter l’impact sur l’application pour les tâches de maintenance lancées par Azure, vous pouvez les planifier le jour de la semaine et à l’heure souhaités pour réduire l’impact sur l’application.</br>
**Puis-je utiliser un réplica en lecture pour un serveur à haute disponibilité ?**</br>
Les réplicas de lecture ne sont pas pris en charge pour les serveurs haute disponibilité. Cette fonctionnalité est présente dans notre feuille de route et nous faisons tout notre possible pour pouvoir la rendre disponible prochainement.</br>
**Puis-je utiliser la réplication des données entrantes pour des serveurs haute disponibilité ?**</br>
La réplication des données entrantes n’est pas prise en charge pour les serveurs haute disponibilité. Mais la réplication des données entrantes pour les serveurs haute disponibilité est sur notre feuille de route et sera bientôt disponible. Pour le moment, si vous souhaitez utiliser la réplication des données entrantes pour la migration, vous pouvez suivre ces étapes :
1. Créez le serveur avec la haute disponibilité redondante interzone.
1. Désactivez la haute disponibilité.
1. Suivez les étapes pour [configurer la réplication des données entrantes](./concepts-data-in-replication.md).  (Assurez-vous que `gtid_mode` a le même paramètre sur les serveurs source et cible.)
1. Après le basculement, supprimez les données dans la configuration de la réplication des données entrantes.
1. Activez la haute disponibilité.

**Puis-je basculer vers un serveur de secours pour réduire le temps d’arrêt pendant le redémarrage du serveur ou une mise à l’échelle ?** </br>
Actuellement, lorsque vous effectuez une opération de scale-up ou scale-down, le serveur de secours et le serveur primaire sont mis à l’échelle en même temps. Le basculement n’est donc pas utile. L’activation du scale-up sur le serveur de secours en premier, suivi du basculement et du scale-up du serveur primaire figure sur notre feuille de route, mais n’est pas encore prise en charge.</br>

**Peut-on modifier le mode de disponibilité (HA redondante interzone/dans la même zone) du serveur ?** </br>
Si vous créez le serveur avec le mode Haute disponibilité redondante interzone activé, vous pouvez passer de Haute disponibilité redondante interzone à la haute disponibilité dans la même zone et vice versa. Pour modifier le mode de disponibilité ultérieurement, vous pouvez définir **Haute disponibilité** sur **Désactivée** dans le volet **Haute disponibilité**, puis la redéfinir sur **Redondant interzone ou dans la même zone** et choisir **Mode Haute disponibilité**.</br>  

## <a name="next-steps"></a>Étapes suivantes

- Découvrir la [continuité de l’activité](./concepts-business-continuity.md).
- Découvrir la [haute disponibilité redondante interzone](./concepts-high-availability.md).
- Découvrez [la sauvegarde et la récupération](./concepts-backup-restore.md).
