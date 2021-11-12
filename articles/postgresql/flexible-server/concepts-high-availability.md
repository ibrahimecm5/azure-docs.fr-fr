---
title: Vue d’ensemble de la haute disponibilité redondante interzone dans Azure Database pour PostgreSQL – Serveur flexible (préversion)
description: Apprenez-en davantage sur les concepts de haute disponibilité redondante interzone dans Azure Database pour PostgreSQL – Serveur flexible
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: 80b0d18eec8bbc37eda407d07873786f4155f4be
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422612"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Concepts de haute disponibilité dans Azure Database pour PostgreSQL – Serveur flexible



Azure Database pour PostgreSQL – Serveur flexible offre une configuration de haute disponibilité avec fonctionnalité de basculement automatique utilisant un déploiement de serveur **redondant interzone**. Déployé dans une configuration redondante interzone, le serveur flexible provisionne et gère automatiquement un réplica de secours dans une autre zone de disponibilité. À l’aide d’une réplication en streaming PostgreSQL, les données sont répliquées sur le serveur réplica de secours en mode **synchrone**. 

La configuration redondante interzone active une fonctionnalité de basculement automatique sans perte de données pendant des événements planifiés, tels qu’une opération de mise à l’échelle du calcul lancée par l’utilisateur, ainsi que pendant des événements non planifiés, tels que des erreurs matérielles et logicielles sous-jacentes, des échecs réseau et des défaillances de zone de disponibilité. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Haute disponibilité redondante interzone"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Architecture de haute disponibilité redondante interzone

Un déploiement redondant interzone permet à un serveur flexible de bénéficier d’une haute disponibilité. Vous pouvez choisir la région et la zone de disponibilité pour déployer votre serveur de base de données primaire. Un serveur réplica de secours est **automatiquement** provisionné et géré dans une zone de disponibilité différente de la même région, avec la même configuration de calcul, de stockage et réseau que le serveur primaire. Les fichiers de données et les fichiers journaux des transactions (journaux avec écriture anticipée, WAL) sont stockés sur un stockage localement redondant au sein de chaque zone de disponibilité, qui stocke automatiquement trois copies des données. Ceci permet d’isoler physiquement l’ensemble de la pile entre le serveur principal et le serveur de secours.  

Quand l’application effectue des écritures ou des validations, avec la réplication en streaming de PostgreSQL, les journaux des transactions (journaux avec écriture anticipée, WAL) sont écrits sur le disque local et sont également répliqués en mode *synchrone* sur le réplica de secours. Une fois que les journaux sont enregistrés sur le réplica de secours, l’application est informée des écritures ou des validations. Le serveur de secours est en mode de récupération, qui continue à appliquer les journaux, mais le serveur principal n’attend pas que l’application soit terminée.

Des sauvegardes automatiques sont effectuées régulièrement à partir du serveur de base de données primaire, tandis que les journaux de transactions sont archivés en permanence dans le stockage de sauvegarde à partir du réplica de secours.

L’intégrité du serveur principal et du serveur de secours est surveillée en permanence et les actions appropriées sont prises pour résoudre les problèmes, notamment le déclenchement d’un basculement vers le serveur de secours. Les états de haute disponibilité redondante interzone sont répertoriés ci-dessous :

| **État** | **Description** |
| ------- | ------ |
| <b> Initialisation | Dans le processus de création d’un serveur de secours |
| <b> Réplication des données | Une fois le serveur de secours créé, il rattrape le serveur principal. |
| <b> Intègre | La réplication est dans un état stable et intègre. |
| <b> Basculement | Le serveur de base de données est en cours de basculement vers le serveur de secours. |
| <b> Suppression du serveur de secours | Lors du processus de suppression du serveur de secours. | 
| <b> Non activée | La haute disponibilité redondante interzone n’est pas activée.  |

>[!NOTE]
> Vous pouvez activer la haute disponibilité lors de la création du serveur ou ultérieurement. Si vous activez ou que vous désactivez la haute disponibilité pendant la phase postérieure à la création, il est recommandé d’effectuer l’opération quand l’activité du serveur principal est faible.

## <a name="steady-state-operations"></a>Opérations à l’état stable

Les applications clientes PostgreSQL sont connectées au serveur primaire à l’aide du nom du serveur BDD. Les lectures de l’application sont traitées directement à partir du serveur principal, tandis que les validations et les écritures sont confirmées à l’application seulement une fois les données enregistrées à la fois sur le serveur principal et sur le réplica de secours. En raison de cet aller-retour supplémentaire, les applications peuvent s’attendre à une latence élevée pour les écritures et les validations. Vous pouvez surveiller l’intégrité de la haute disponibilité sur le portail.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="Haute disponibilité redondante interzone – État stable"::: 

1. Les clients se connectent au serveur flexible et effectuent des opérations d’écriture.
2. Les modifications sont répliquées sur le site de secours.
3. Le serveur primaire reçoit un accusé de réception.
4. Les écritures/validations font l’objet d’un accusé de réception.

## <a name="failover-process---planned-downtimes"></a>Processus de basculement – Temps d’arrêt planifiés

Les événements de temps d’arrêt planifiés incluent des mises à jour logicielles périodiques planifiées Azure et des mises à niveau de version mineure. Configurées en mode haute disponibilité, ces opérations sont d’abord appliquées au réplica de secours, tandis que les applications continuent d’accéder au serveur primaire. Une fois le réplica de secours mis à jour, les connexions au serveur primaire sont purgées et un basculement est déclenché, qui a pour effet d’activer le serveur réplica de secours en tant que serveur principal portant le même nom de serveur de base de données. Les applications clientes doivent se reconnecter avec le même nom de serveur de base de données au nouveau serveur primaire, et peuvent reprendre leurs opérations. Un nouveau serveur de secours est établi dans la même zone que l’ancien serveur primaire. 

Pour les autres opérations initiées par l’utilisateur, telles que la mise à l’échelle du calcul ou du stockage, les modifications sont appliquées d’abord au serveur réplica de secours, puis au serveur primaire. Actuellement, le service n’est pas basculé sur le serveur de secours et par conséquent, pendant que l’opération de mise à l’échelle est effectuée sur le serveur principal, les applications vont subir un court temps d’arrêt.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Réduction des temps d’arrêt planifiés avec une fenêtre de maintenance gérée

Avec le serveur flexible, vous pouvez si vous le souhaitez planifier des activités de maintenance lancées par Azure en choisissant une fenêtre de 30 minutes un jour de votre choix, où les activités sur les bases de données devraient être faibles. Les tâches de maintenance Azure, comme les mises à jour correctives ou les mises à niveau vers des versions mineures, seront effectuées dans cette fenêtre de maintenance. Si vous ne choisissez pas une fenêtre personnalisée, une fenêtre d’une heure allouée par le système entre 23h00 et 7h00 (heure locale) est choisie pour votre serveur. 
 
Pour les serveurs flexibles configurés avec une haute disponibilité, ces activités de maintenance sont d’abord effectuées sur le réplica de secours, puis le service est basculé sur le serveur de secours auquel les applications peuvent se reconnecter.

## <a name="failover-process---unplanned-downtimes"></a>Processus de basculement – Temps d’arrêt non planifiés

Les interruptions non planifiées incluent les bogues logiciels ou des défaillances de composants d’infrastructure ayant un impact sur la disponibilité de la base de données. Dans le cas où le serveur principal devient indisponible, cet événement est détecté par le système de supervision, qui lance un processus de basculement.  Le processus inclut quelques secondes de temps d’attente pour vérifier qu’il ne s’agit pas d’un faux positif. La réplication sur le réplica de secours est interrompue, et le réplica de secours est activé et devient la base de données principale. Ceci comprend le secours permettant de récupérer tous les fichiers WAL résiduels. Une fois qu’il est entièrement récupéré, le DNS pour le même point de terminaison est mis à jour avec l’adresse IP du serveur de secours. Les clients peuvent alors tenter de se reconnecter au serveur de base de données en utilisant la même chaîne de connexion et reprendre leurs opérations. 

>[!NOTE]
> Les serveurs flexibles configurés avec une haute disponibilité redondante interzone fournissent un objectif de point de récupération (RPO) **égal à zéro** (aucune perte de données). L’objectif de délai de récupération (RTO) attendu est **inférieur à 120 s** dans les cas courants. Cependant, en fonction de l’activité sur le serveur de base de données principal au moment du basculement, le basculement peut prendre plus de temps. 

Après le basculement, lors du provisionnement d’un nouveau serveur de secours, les applications peuvent néanmoins toujours se connecter au serveur principal et effectuer leurs opérations de lecture/écriture. Une fois le serveur de secours établi, il commence à récupérer les journaux qui ont été générés après le basculement. 

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="Haute disponibilité redondante interzone – Basculement"::: 

1. Le serveur de base de données primaire est à l’arrêt et les clients perdent la connexion à la base de données. 
2. Le serveur de secours est activé pour devenir le nouveau serveur primaire. Le client se connecte au nouveau serveur primaire en utilisant la même chaîne de connexion. Le fait que l’application cliente se trouve dans la même zone que le serveur de base de données primaire réduit la latence et améliore les performances.
3. Le serveur de secours est établi dans la même zone que l’ancien serveur primaire, et la réplication en streaming est lancée. 
4. Une fois la réplication à l’état stable, les validations et les écritures de l’application cliente font l’objet d’un accusé de réception quand les données sont conservées sur les deux sites.

## <a name="on-demand-failover"></a>Basculement à la demande

Le serveur flexible fournit deux méthodes pour vous permettre d’effectuer un basculement à la demande vers le serveur de secours. Elles sont utiles si vous voulez tester l’impact la durée et le temps d’arrêt du basculement pour vos applications et si vous voulez basculer vers la zone de disponibilité préférée. 

### <a name="forced-failover"></a>basculement forcé

Vous pouvez utiliser cette fonctionnalité pour simuler un scénario de panne non planifiée tout en exécutant votre charge de travail de production et pour observer le temps d’arrêt de votre application. Dans de rares cas, si votre serveur principal ne répond plus pour une raison quelconque, vous pouvez utiliser cette fonctionnalité. 

Ces déclencheurs de la fonctionnalité arrête le serveur principal et lance le flux de travail de basculement dans lequel l’opération de promotion du serveur de secours est effectuée. Une fois que le serveur de secours a terminé le processus de récupération jusqu’aux dernières données validées, il est promu en tant que serveur principal. Les enregistrements DNS sont mis à jour et votre application peut se connecter au serveur principal promu. Votre application peut continuer à écrire sur le serveur principal, tandis qu’un nouveau serveur de secours est établi en arrière-plan, sans que cela ait un impact sur la durée de bon fonctionnement. 

Voici les étapes à suivre lors du basculement forcé :

  | **Étape** | **Description** | **Temps d’arrêt de l’application attendu ?** |
  | ------- | ------ | ----- |
  | 1 | Le serveur principal est arrêté peu après la réception de la demande de basculement. | Oui |
  | 2 | L’application subit un temps d’arrêt au moment où le serveur principal est arrêté. | Oui |
  | 3 | Le système de surveillance interne détecte la défaillance et lance un basculement vers le serveur de secours. | Oui |
  | 4 | Le serveur de secours passe en mode de récupération avant d’être promu entièrement en tant que serveur indépendant. | Oui |
  | 5 | Le processus de basculement attend la fin de la récupération de secours. | Oui |
  | 6 | Une fois le serveur opérationnel, l’enregistrement DNS est mis à jour avec le même nom d’hôte, mais en utilisant l’adresse IP du serveur de secours. | Oui |
  | 7 | L’application peut se reconnecter au nouveau serveur principal et reprendre son fonctionnement. | Non |
  | 8 | Un serveur de secours est étable dans la zone préférée. | Non |
  | 9 | Le serveur de secours commence à récupérer les journaux (à partir du Stockage Blob Azure) qu’il a manqués pendant la période nécessaire à son établissement. | Non |
  | 10 | Un état stable entre le serveur principal et le serveur de secours est établi. | Non |
  | 11 | Le processus de basculement forcé est terminé. | Non |

Le temps d’arrêt de l’application doit normalement commencer après l’étape #1 et persister jusqu’à ce que l’étape #6 soit terminée. Les autres étapes se produisent en arrière-plan sans impact sur les écritures et les validations de l’application.

>[!Important]
>Le processus de basculement de bout en bout comprend (a) le basculement vers le serveur de secours après défaillance du serveur principal et (b) l’établissement d’un nouveau serveur de secours dans un état stable. Étant donné que votre application subit un temps d’arrêt uniquement jusqu’à la fin du basculement vers le serveur de secours, **veuillez mesurer le temps d’arrêt du point de vue de votre application/client** au lieu du processus de basculement global de bout en bout. 

### <a name="planned-failover"></a>Basculement planifié

Vous pouvez utiliser cette fonctionnalité pour basculer vers le serveur de secours avec un temps d’arrêt réduit. Par exemple, après un basculement non planifié, votre serveur principal peut se trouver dans une zone de disponibilité différente de celle de l’application, et vous voulez ramener le serveur principal dans la zone précédente afin qu’il se trouve au même endroit que votre application.

Lors de l’exécution de cette fonctionnalité, le serveur de secours est d’abord préparé de façon à intégrer les transactions récentes, ce qui permet à l’application de continuer à effectuer des opérations de lecture/écriture. Le serveur de secours est ensuite promu et les connexions au serveur principal sont interrompues. Votre application peut continuer à écrire sur le serveur principal, un nouveau serveur de secours étant établi en arrière-plan. Voici les étapes impliquées dans le basculement planifié.

| **Étape** | **Description** | **Temps d’arrêt de l’application attendu ?** |
  | ------- | ------ | ----- |
  | 1 | Attendre que le serveur de secours se mette au niveau du serveur principal. | Non |
  | 2 | Le système de surveillance interne lance le flux de travail de basculement. | Non |
  | 3 | Les écritures de l’application sont bloquées quand le serveur de secours est proche du numéro séquentiel dans le journal (LSN) du serveur principal. | Oui |
  | 4 | Le serveur de secours est promu en tant que serveur indépendant. | Oui |
  | 5 | L’enregistrement DNS est mis à jour avec l’adresse IP du nouveau serveur de secours. | Oui |
  | 6 | L’application se reconnecte et reprend ses lectures/écritures sur le nouveau serveur principal. | Non |
  | 7 | Un nouveau serveur de secours est établi dans une autre zone. | Non |
  | 8 | Le serveur de secours commence à récupérer les journaux (à partir du Stockage Blob Azure) qu’il a manqués pendant la période nécessaire à son établissement. | Non |
  | 9 | Un état stable entre le serveur principal et le serveur de secours est établi. | Non |
  | 10 |  Le processus de basculement planifié est terminé. | Non |

Le temps d’arrêt de l’application commence à l’étape #3 et son fonctionnement peut reprendre après l’étape #5. Les autres étapes se produisent en arrière-plan sans impact sur les écritures et les validations de l’application. 

### <a name="considerations-while-performing-on-demand-failovers"></a>Considérations sur l’exécution de basculements à la demande

* La durée d’ensemble de l’opération peut être plus longue que le temps d’arrêt réel subi par l’application. **Mesurez le temps d’arrêt du point de vue de l’application**.
* N’effectuez pas de basculements consécutifs sans pause. Attendez au moins 15-20 minutes entre chaque basculement, ce qui permettra également au nouveau serveur de secours d’être entièrement établi.
* Pour le basculement planifié avec un temps d’arrêt réduit, il est recommandé de l’exécuter pendant une période d’activité faible.

Pour la gestion de la haute disponibilité, consultez [ce guide](how-to-manage-high-availability-portal.md).


## <a name="point-in-time-restore-of-ha-servers"></a>Restauration à un instant dans le passé de serveurs à haute disponibilité

Pour les serveurs flexibles configurés avec une haute disponibilité, les données des journaux sont répliquées en temps réel sur le serveur de secours. Toute erreur d’un utilisateur sur le serveur principal, par exemple la suppression accidentelle d’une table ou une mise à jour incorrecte des données, est répliquée sur le réplica de secours. Par conséquent, vous ne pouvez pas utiliser le serveur de secours pour récupérer de telles erreurs logiques. Pour récupérer de telles erreurs, vous devez effectuer une restauration à un instant dans le passé à partir de la sauvegarde.  La fonctionnalité de restauration à un instant dans le passé du serveur flexible vous permet d’opérer une récupération jusqu`à une date et heure antérieures à la survenance de l’erreur. Pour les bases de données configurées avec une haute disponibilité, un nouveau serveur de base de données est restauré en tant que serveur flexible dans une seule zone avec un nom de serveur fourni par l’utilisateur. Vous pouvez utiliser le serveur restauré dans quelques cas d’usage :

  1. Vous pouvez utiliser le serveur restauré pour une utilisation en production et éventuellement activer la haute disponibilité redondante interzone. 
  2. Si vous voulez simplement restaurer un objet, vous pouvez exporter l’objet à partir du serveur de base de données restauré et l’importer sur votre serveur de base de données de production. 
  3. Si vous voulez cloner votre serveur de base de données à des fins de test et de développement, ou si vous voulez effectuer une restauration pour n’importe quelle autre raison, vous pouvez effectuer une restauration à un instant dans le passé.

## <a name="zone-redundant-high-availability---features"></a>Haute disponibilité redondante interzone – Fonctionnalités

* Le réplica de secours sera déployé dans une configuration de machine virtuelle exacte identique à celle du serveur primaire, incluant les vCores, le stockage, les paramètres réseau (VNET, pare-feu), etc.

* Vous pouvez ajouter la haute disponibilité pour un serveur de base de données existant.

* Vous pouvez supprimer le réplica de secours en désactivant la haute disponibilité.

* Vous pouvez choisir votre zone de disponibilité pour votre serveur de base de données principal. La zone de secours est sélectionnée automatiquement.

* Les opérations comme l’arrêt, le démarrage et le redémarrage sont effectuées simultanément sur le serveur de base de données principal et sur le serveur de base de données de secours.

* Des sauvegardes automatiques sont effectuées à partir du serveur de base de données primaire, et stockées dans un stockage redondant interzone.

* Les clients se connectent toujours au nom d’hôte du serveur de base de données principal.

* Toute modification apportée aux paramètres du serveur sont également appliquées au réplica de secours.

* Possibilité de redémarrer le serveur pour intégrer toute modification de paramètre du serveur statique.
  
* Les activités de maintenance périodiques telles que les mises à niveau de version mineure se déroulent sur le serveur de secours, puis le service est basculé pour réduire le temps d’arrêt.  

## <a name="zone-redundant-high-availability---limitations"></a>Haute disponibilité redondante interzone – Limitations

* La haute disponibilité n’est pas prise en charge avec le niveau de calcul par rafale.
* La haute disponibilité est prise en charge uniquement dans les régions où plusieurs zones sont disponibles.
* En raison de la réplication synchrone vers une autre zone de disponibilité, les applications peuvent rencontrer une latence d’écriture et de validation élevée.

* Un réplica de secours ne peut pas être utilisé pour des requêtes en lecture.

* En fonction de la charge de travail et de l’activité sur le serveur principal, le processus de basculement peut prendre plus de 120 secondes en raison de la récupération nécessaire au niveau du réplica de secours avant que celui-ci puisse être promu. 
* Le serveur de secours récupère généralement les fichiers WAL à un débit de 40 Mo/s. Si votre charge de travail dépasse cette limite, il se peut que la récupération soit plus longue pendant le basculement ou après avoir établi un nouveau serveur de secours. 

* Le redémarrage du serveur de base de données primaire redémarre également le réplica de secours. 

* La configuration de réplicas en lecture supplémentaires n’est pas prise en charge.

* La configuration des tâches de gestion lancées par le client ne peut pas être planifiée pendant la fenêtre de maintenance gérée.

* Des événements planifiés, tels que la mise à l’échelle du calcul et du stockage, se produisent d’abord sur le serveur de secours, puis sur le serveur primaire. Actuellement, le serveur n’a pas basculé pour ces opérations planifiées. 

* Si le décodage logique ou la réplication logique sont configurés avec un serveur flexible configuré pour la haute disponibilité, en cas de basculement vers le serveur de secours, les emplacements de réplication logique ne sont pas copiés sur le serveur de secours.  

## <a name="availability-without-zone-redundant-ha"></a>Disponibilité sans haute disponibilité redondante interzone

Pour les serveurs flexibles configurés **sans** haute disponibilité redondante interzone, le service fournit toujours une disponibilité, une redondance et une résilience du stockage intégrées pour faciliter la récupération après un événement de temps d’arrêt planifié ou non.

### <a name="planned-downtime"></a>Temps d’arrêt planifié 

Voici quelques scénarios de maintenance planifiée :

| **Scénario** | **Description**|
| ------------ | ----------- |
| <b>Mise à l’échelle du calcul | Quand l’utilisateur effectue une opération de mise à l’échelle du calcul, un nouveau serveur de base de données est approvisionné à l’aide de la configuration de calcul mise à l’échelle. Sur l’ancien serveur de base de données, les points de contrôle actifs sont autorisés à cesser d’opérer, les connexions clientes sont vidées, toutes les transactions non validées sont annulées, puis le serveur est arrêté. Le stockage est ensuite détaché de l’ancien serveur de base de données et attaché au nouveau. Il est alors opérationnel pour accepter toutes les connexions.|
| <b>Augmentation du stockage | L’augmentation du stockage est actuellement une opération effectuée hors connexion qui implique un temps d’arrêt réduit.|
| <b>Déploiement de nouveaux logiciels (Azure) | Le déploiement de nouvelles fonctionnalités ou la corrections de bogues se produisent automatiquement dans le cadre de la maintenance planifiée du service. Pour plus d’informations, consultez la [documentation](./concepts-maintenance.md) et visitez votre [portail](https://aka.ms/servicehealthpm).|
| <b>Mises à niveau de version mineure | Le service Azure Database pour PostgreSQL opère automatiquement la mise à niveau des serveurs de base de données vers la version mineure déterminée par Azure. Cela se produit dans le cadre de la maintenance planifiée du service. L’opération entraîne un bref temps d’arrêt de quelques secondes, et le serveur de base de données est automatiquement redémarré avec la nouvelle version mineure. Pour plus d’informations, consultez la [documentation](./concepts-maintenance.md) et visitez votre [portail](https://aka.ms/servicehealthpm).|


###  <a name="unplanned-downtime"></a>Temps d’arrêt non planifié 

Des temps d’arrêt non planifiés peuvent se produire suite à des défaillances imprévues telles qu’un échec matériel sous-jacent, des problèmes de mise en réseau et des bogues logiciels. Si le serveur de base de données tombe en panne de façon inattendue, un nouveau serveur de base de données est automatiquement approvisionné en quelques secondes. Le stockage étendu est automatiquement attaché au nouveau serveur de base de données. Le moteur PostgreSQL effectue l’opération de récupération à l’aide des fichiers WAL et de base de données, puis ouvre le serveur de base de données pour permettre aux clients de se connecter. Les transactions non validées sont perdues et doivent être retentées par l’application. Alors qu’il n’est pas possible d’éviter un temps d’arrêt non planifié, le serveur flexible réduit ce temps d’arrêt en effectuant automatiquement des opérations de récupération au niveau du serveur de base de données et des couches de stockage, sans intervention humaine. 

Voici quelques scénarios d’échec et comment le serveur flexible récupère automatiquement :

| **Scénario** | **Récupération automatique** |
| ---------- | ---------- |
| <B>Échec du serveur de base de données | En cas d’arrêt du serveur de base de données en raison d’une erreur matérielle sous-jacente, les connexions actives sont abandonnées et toutes les transactions en cours interrompues. Un nouveau serveur de base de données est déployé automatiquement, et le stockage de données étendu est attaché au nouveau serveur de base de données. Une fois la récupération de la base de données terminée, les clients peuvent se connecter au nouveau serveur de base de données via le même point de terminaison. <br /> <br /> Le temps de récupération (RTO) dépend de différents facteurs, dont l’activité au moment de l’erreur, telle qu’une transaction de grande ampleur, et le volume de la récupération à effectuer pendant le processus de démarrage du serveur de base de données. <br /> <br /> Des applications utilisant les bases de données PostgreSQL doivent être créées de manière à détecter et à retenter les connexions abandonnées et les transactions ayant échoué.  |
| <B>Échec de stockage | Les applications ne détectent aucun impact des problèmes liés au stockage, tels qu’une défaillance de disque ou une corruption de bloc physique. Les données étant stockées dans 3 copies, la copie des données est servie par le stockage survivant. Les altérations de bloc sont corrigées automatiquement. En cas de perte d’une copie des données, une nouvelle est automatiquement créée. |

Voici quelques scénarios d’échec qui nécessitent une action de l’utilisateur pour la récupération :

| **Scénario** | **Plan de récupération** |
| ---------- | ---------- |
| <b>Défaillance de zone de disponibilité | Si la région prend en charge plusieurs zones de disponibilité, les sauvegardes sont automatiquement stockées dans un stockage de sauvegarde redondant interzone. En cas de défaillance d’une zone, vous pouvez effectuer une restauration à partir de la sauvegarde vers une autre zone de disponibilité. Cette opération offre une résilience au niveau des zones. Elle implique toutefois un délai pour la restauration et la récupération. Elle peut occasionner des pertes de données, car tous les enregistrements WAL n’ont peut-être pas été copiés dans le stockage de sauvegarde. <br> <br> Si vous préférez réduire le temps d’arrêt et bénéficier d’une durée de bon fonctionnement élevée, nous vous recommandons de configurer votre serveur avec une haute disponibilité redondante interzone. |
| <b> Erreurs logiques/de l’utilisateur | La récupération d’erreurs de l’utilisateur, telles qu’une suppression accidentelle de tables ou une mise à jour incorrecte de données, implique l’exécution d’une [récupération jusqu’à une date et heure](./concepts-backup-restore.md) (PITR), en restaurant et récupérant les données jusqu’au moment où l’erreur s’est produite.<br> <br>  Si vous ne souhaitez restaurer qu’un sous-ensemble de bases de données ou de tables spécifiques plutôt que toutes les bases de données du serveur de base de données, vous pouvez restaurer celui-ci dans une nouvelle instance, exporter les tables via l’utilitaire [pg_dump](https://www.postgresql.org/docs/13/app-pgdump.html), puis vous servir de l’utilitaire [pg_restore](https://www.postgresql.org/docs/13/app-pgrestore.html) pour restaurer ces tables dans votre base de données. |

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="ha-configuration-questions"></a>Questions sur la configuration de la haute disponibilité

* **Ai-je besoin d’une haute disponibilité redondante interzone pour protéger mon serveur contre les interruptions non planifiées ?** <br>
    Non. Le serveur flexible offre un stockage redondant local avec 3 copies des données, une sauvegarde redondante interzone (dans les régions où celle-ci est prise en charge), ainsi qu’une résilience de serveur intégrée pour redémarrer automatiquement un serveur défaillant et même déplacer le serveur vers un autre nœud physique. La haute disponibilité redondante interzone offre une durée de bon fonctionnement plus élevée en effectuant un basculement automatique vers un autre serveur (de secours) exécuté dans une autre zone. Elle fournit ainsi une haute disponibilité résiliente aux zones sans perte de données.

* **La haute disponibilité redondante interzone est-elle disponible dans toutes les régions ?** <br>
    La haute disponibilité redondante interzone est disponible dans les régions prenant en charge plusieurs zones de disponibilité . Pour obtenir les informations les plus récentes sur la prise en charge des régions, consultez [cette documentation](overview.md#azure-regions). Nous ajoutons des régions et activons des zones de disponibilité en permanence. 

* **Quel est le mode de réplication entre les serveurs principal et de secours ?** <br>
    Le mode de réplication synchrone est établi entre le serveur principal et le serveur de secours. Les écritures et validations d’application sont reconnues uniquement lorsque les données du journal WAL (write-ahead log) sont conservées sur le site de secours. Cela permet d’éviter toute perte de données en cas de basculement.

* **Le mode synchrone entraîne une latence. À quel type d’impact sur les performances de mon application puis-je m’attendre ?** <br>
    La configuration de la haute disponibilité entraîne une certaine latence des écritures et des validations. Aucun impact sur les requêtes de lecture. L’impact sur les performances varie en fonction de votre charge de travail. En règle générale, l’impact des écritures et des validations peut être d’environ 20 à 30 %. 

* **La haute disponibilité redondante interzone offre-t-elle une protection contre les interruptions planifiées et non planifiées ?** <br>
    Oui. L’objectif principal de la haute disponibilité est d’offrir une durée de bon fonctionnement plus élevée pour atténuer les interruptions. En cas d’interruption non planifiée, y compris une erreur dans une base de données, une machine virtuelle, un nœud physique, un centre de données ou au niveau de la zone de disponibilité, le système de surveillance bascule automatiquement vers le serveur de secours. De même, pendant des interruptions planifiées, telles que des mises à jour de versions mineures ou d’infrastructure pendant une fenêtre de maintenance planifiée, les mises à jour sont appliquées au serveur de secours, puis le service est basculé vers celui-ci pendant le processus de mise à jour du serveur principal. Cela réduit le temps d’arrêt global. 

* **Puis-je activer ou désactiver la haute disponibilité à tout moment ?** <br>

    Oui. Vous pouvez activer ou désactiver la haute disponibilité redondante interzone à tout moment, sauf quand le serveur se trouve dans certains états, par exemple s’il est arrêté, en cours de redémarrage ou déjà engagé dans le processus de basculement. 

* **Puis-je choisir la zone de disponibilité du serveur de secours ?** <br>
    Non. Actuellement, vous ne pouvez pas choisir la zone de disponibilité du serveur de secours. Nous prévoyons d’ajouter cette fonctionnalité à l’avenir.

* **Puis-je configurer la haute disponibilité entre accès privé (réseau virtuel) et accès public ?** <br>
    Non. Vous pouvez configurer soit la haute disponibilité à l’intérieur d’un réseau virtuel (s’étendant aux zones de disponibilité dans une région), ou un accès public. 

* **Puis-je configurer la haute disponibilité à travers plusieurs régions ?** <br>
    Non. La haute disponibilité est configurée à l’intérieur d’une région, mais à travers plusieurs zones de disponibilité. Dans le futur, nous prévoyons de proposer des réplicas en lecture configurables dans plusieurs régions à des fins de reprise d’activité. Nous fournirons plus de détails lorsque la fonctionnalité sera activée. 

* **Puis-je utiliser une réplication logique avec des serveurs configurés pour la haute disponibilité ?** <br>
    Vous pouvez configurer une réplication logique avec haute disponibilité. Toutefois, après un basculement, les détails d’emplacement logique ne sont pas copiés vers le serveur de secours. Par conséquent, la prise en charge de cette configuration est actuellement limitée.

### <a name="replication-and-failover-related-questions"></a>Questions relatives à la réplication et au basculement

* **Comment un serveur flexible fournit-il la haute disponibilité dans le cas d’une panne, par exemple la défaillance d’une zone de disponibilité ?** <br>
    Lorsque vous activez votre serveur avec une haute disponibilité redondante interzone, un réplica de secours physique offrant la même configuration de calcul et de stockage que le serveur principal est déployé automatiquement dans une autre zone de disponibilité. La réplication de diffusion en continu PostgreSQL est établie entre les serveurs principal et de secours. 

* **Quel est le processus de basculement classique en cas d’interruption ?** <br>
    Quand le système de surveillance détecte l’erreur, il initie un flux de travail de basculement qui implique de s’assurer que le serveur de secours a appliqué tous les fichiers WAL résiduels et a complètement rattrapé son retard avant de l’ouvrir pour les opérations de lecture et d’écriture. Ensuite, le DNS est mis à jour avec l’adresse IP du serveur de secours afin que les clients puissent se reconnecter au serveur en utilisant le même point de terminaison (nom d’hôte). Un nouveau serveur de secours est instancié pour conserver la configuration en mode haute disponibilité.

* **Quel sont généralement le temps de basculement et la perte de données attendue pendant une interruption ?** <br>
    En règle générale, le temps de basculement ou d’arrêt du point de vue de l’application est compris entre 60 et 120 secondes. Ce peut être plus long si la défaillance survient pendant des transactions longues, la création d’un index ou des activités intenses en écriture, car le serveur de secours peut prendre plus de temps pour effectuer le processus de récupération.

    Étant donné que la réplication se produit en mode synchrone, elle ne devrait occasionner aucune perte de données.

* **Offrez-vous un contrat SLA pour le temps de basculement ?** <br>
    Concernant le basculement, nous fournissons des instructions sur le temps que prend généralement l’opération. Le contrat de niveau de service (SLA) officiel sera fourni pour la durée globale de bon fonctionnement quand le service sera en disponibilité générale. Aucun SLA n’est proposé pendant la période de préversion publique.

* **L’application se connecte-t-elle automatiquement au serveur après le basculement ?** <br>
    Non. Les applications doivent disposer d’un mécanisme de nouvelle tentative pour se reconnecter au même point de terminaison (nom d’hôte).

* **Comment faire pour tester le basculement ?** <br>
    Vous pouvez utiliser la fonctionnalité **Basculement forcé** ou **Basculement planifié** pour tester le basculement. Pour plus d’informations, consultez la section **Basculement à la demande** de ce document.

* **Comment faire pour vérifier l’état de la réplication ?** <br>
    Sur le portail, la page de vue d’ensemble du serveur montre l’état de la haute disponibilité redondante interzone et l’état du serveur. Vous pouvez également vérifier l’état et les zones de disponibilité des serveurs principal et de secours dans le panneau Haute disponibilité du portail du serveur. 

    À partir de psql, vous pouvez exécuter `select * from pg_stat_replication;`, qui montre l’état du streaming, parmi d’autres informations détaillées.

* **Prenez-vous en charge les requêtes de lecture sur le réplica de secours ?** <br>
    Non. Nous ne prenons pas en charge les requêtes de lecture sur le réplica de secours.

* **Lorsque j’opère une restauration à un instant dans le passé, celle-ci a-t-elle pour effet de configurer automatiquement le serveur restauré en haute disponibilité ?** <br>
    Non. Le serveur de restauration à un instant dans le passé est restauré en tant que serveur autonome. Si vous souhaitez activer la haute disponibilité, vous pouvez le faire une fois la restauration terminée.


## <a name="next-steps"></a>Étapes suivantes

-   Découvrir la [continuité de l’activité](./concepts-business-continuity.md)
-   Découvrez comment [gérer la haute disponibilité](./how-to-manage-high-availability-portal.md)
-   Découvrir [la sauvegarde et la récupération](./concepts-backup-restore.md)
