---
title: Vue d’ensemble - Azure Database pour MySQL - Serveur flexible
description: Découvrez Azure Database pour MySQL - Serveur flexible, un service de base de données relationnelle du cloud Microsoft qui repose sur MySQL Community Edition.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, references_regions
ms.topic: overview
ms.date: 08/10/2021
ms.openlocfilehash: 13d220dc0b168ade0bd6493025d858e01772a980
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893952"
---
# <a name="azure-database-for-mysql---flexible-server"></a>Serveurs flexibles Azure Database pour MySQL 

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Deux modes de déploiement sont disponibles pour Azure Database pour MySQL optimisé par MySQL Community Edition :

- Serveur flexible
- Serveur unique

Cet article contient une vue d'ensemble et une présentation des concepts de base du modèle de déploiement de serveur flexible. Pour plus d’informations sur la façon de déterminer l’option de déploiement qui convient à votre charge de travail, consultez [choix de l’option de serveur MySQL appropriée dans Azure](./../select-right-deployment-type.md).

## <a name="overview"></a>Vue d’ensemble

Azure Database pour MySQL - Serveur flexible est un service de base de données prêt pour la production entièrement géré et conçu pour offrir un contrôle et une flexibilité plus granulaires des fonctions de gestion de base de données et des paramètres de configuration. L’architecture de serveur flexible permet aux utilisateurs d’opter pour une haute disponibilité au sein d’une même zone de disponibilité et dans plusieurs zones de disponibilité. Les serveurs flexibles offrent de meilleurs contrôles d’optimisation des coûts grâce à la possibilité de d’arrêter/de démarrer le serveur et un niveau de calcul expansible, ce qui est idéal pour les charges de travail ne nécessitant pas en permanence une capacité de calcul complète. Un serveur flexible prenant également en charge des instances réservées, il vous permet d’économiser jusqu’à 63 % sur les coûts, ce qui est idéal pour des charges de travail de production dont les exigences en matière de capacité de calcul sont prévisibles. Le service prend en charge la version de la communauté de MySQL 5.7 et 8.0. Le service est aujourd'hui généralement disponible dans un grand nombre de [régions Azure](overview.md#azure-regions).

L’option de déploiement Serveur flexible offre trois niveaux de calcul : Expansible, Usage général et Mémoire optimisée. Chaque niveau offre des capacités de calcul et de mémoire différentes pour prendre en charge les charges de travail de votre base de données. Vous pouvez créer votre première application sur un niveau Expansible pour un faible coût mensuel, puis adapter l’échelle aux besoins de votre solution. L’évolutivité dynamique permet de répondre en toute transparence à l’évolution rapide des besoins en ressources de votre base de données. Vous payez uniquement pour les ressources dont vous avez besoin et seulement quand vous en avez besoin. Pour plus d’informations, consultez [Calcul et stockage](concepts-compute-storage.md).

Les serveurs flexibles sont idéalement adaptés pour ce qui suit :
- Facilité de déploiement, mise à l’échelle simplifiée et faible surcharge de gestion de base de données pour les fonctions telles que les sauvegardes, la haute disponibilité, la sécurité et le monitoring
- Développements d’applications nécessitant la version de la communauté de MySQL avec un meilleur contrôle et des personnalisations
- Charges de travail de production avec la haute disponibilité dans la même zone, redondante interzone et les fenêtres de maintenance managées
- Expérience de développement simplifiée 
- Sécurité de qualité professionnelle, conformité et confidentialité

Pour les dernières mises à jour apportées au serveur flexible, consultez [Nouveautés d’Azure Database pour MySQL - Serveur flexible](whats-new.md).

![Schéma conceptuel du serveur flexible](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="free-12-month-offer"></a>Offre gratuite de 12 mois

Avec un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez utiliser un serveur flexible gratuitement pendant 12 mois avec les limites mensuelles suivantes :
* **750 heures d’instance Burstable B1MS**, soit un nombre d’heures suffisant pour exécuter une instance de base de données en continu chaque mois.
* **32 Go** de stockage et **32 Go** de stockage de sauvegarde. 

Vous pouvez profiter de cette offre si vous souhaitez développer et déployer des applications qui utilisent Azure Database pour MySQL - Serveur flexible. Pour découvrir comment créer et utiliser un serveur flexible gratuitement avec un compte gratuit Azure, accédez à [ce tutoriel](how-to-deploy-on-azure-free-account.md). 

## <a name="high-availability-within-and-across-availability-zones"></a>Haute disponibilité à l’intérieur de zones de disponibilité et entre elles

Azure Database pour MySQL - Serveur flexible permet de configurer la haute disponibilité avec le basculement automatique. Cette solution de haute disponibilité est conçue pour empêcher toute perte de données validées provoquée par des défaillances et améliorer la durée de bon fonctionnement globale de votre application.Quand la haute disponibilité est configurée, le serveur flexible provisionne et gère automatiquement un réplica de secours. Deux modèles d'architecture à haute disponibilité sont disponibles : 

- **Haute disponibilité redondante interzone** : cette option est recommandée pour une isolation et une redondance complètes de l’infrastructure sur plusieurs zones de disponibilité. Elle offre le niveau de disponibilité le plus élevé, mais vous oblige à configurer la redondance des applications entre zones. La haute disponibilité redondante interzone est préférable quand vous voulez obtenir le niveau de disponibilité le plus élevé en cas de défaillance de l’infrastructure dans la zone de disponibilité et où la latence dans la zone de disponibilité est acceptable. La haute disponibilité redondante interzone est disponible dans un  [sous-ensemble de régions Azure](overview.md#azure-regions)  où chaque région prend en charge plusieurs zones de disponibilité et où des partages de fichiers Premium redondants interzones sont disponibles. 

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Haute disponibilité redondante interzone":::

- **Haute disponibilité dans la même zone** : cette option est préférable pour la redondance de l’infrastructure avec une latence réseau inférieure, car le serveur principal et le serveur de secours se trouvent dans la même zone de disponibilité. Elle offre une haute disponibilité sans qu’il soit nécessaire de configurer la redondance des applications entre les zones. La haute disponibilité dans la même zone est préférable quand vous voulez obtenir le niveau de disponibilité le plus élevé au sein d’une même zone de disponibilité avec la latence réseau la plus faible. La haute disponibilité dans la même zone est disponible dans [toutes les régions Azure](overview.md#azure-regions) où il est possible de créer un serveur flexible Azure Database pour MySQL. 

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Haute disponibilité redondante dans la même zone":::

Pour plus d’informations, consultez [Concepts de haute disponibilité](concepts-high-availability.md).

## <a name="automated-patching-with-managed-maintenance-window"></a>Mise à jour corrective automatisée avec fenêtre de maintenance gérée

Le service effectue une mise à jour corrective automatisée du matériel, du système d’exploitation et du moteur de base de données sous-jacents. Le correctif comprend les mises à jour de sécurité et de logiciel. Pour le moteur MySQL, les mises à niveau de version mineure sont également incluses dans le cadre de la publication de maintenance planifiée. Les utilisateurs peuvent configurer la planification de la mise à jour corrective pour qu’elle soit gérée par le système, ou définir leur planification personnalisés. Pendant la planification de la maintenance, le correctif est appliqué et le serveur peut nécessiter un redémarrage dans le cadre du processus de mise à jour corrective pour achever la mise à jour. Avec la planification personnalisée, les utilisateurs peuvent rendre leur cycle de mise à jour prévisible, et choisir une fenêtre de maintenance avec un impact minimal sur l’activité. En général, le service suit un calendrier de publication mensuel dans le cadre de l’intégration et de la publication continues.

Pour plus d’informations, consultez [Maintenance planifiée](concepts-maintenance.md). 

## <a name="automatic-backups"></a>Sauvegardes automatiques

Le service à serveur flexible crée automatiquement des sauvegardes de serveur et les conserve sur un stockage géoredondant ou redondant localement configuré par l'utilisateur. Les sauvegardes peuvent être utilisées pour restaurer votre serveur à n'importe quel point dans le temps au cours de la période de rétention des sauvegardes. La période de rétention de sauvegarde par défaut est de sept jours. La conservation peut éventuellement être configurée sur une durée de 1 à 35 jours. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES de 256 bits.

Pour plus d’informations, consultez [Concepts de sauvegarde](concepts-backup-restore.md) .

## <a name="network-isolation"></a>Isolement réseau

Vous avez deux possibilités de mise en réseau pour connecter votre serveur flexible Azure Database pour MySQL. Il s’agit de l’**accès privé (intégration au réseau virtuel)** et de l’**accès public (adresses IP autorisées)** . 

- **Accès privé (intégration au réseau virtuel)**  : vous pouvez déployer votre serveur flexible sur votre [réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md). Les réseaux virtuels Azure offrent des communications réseau privées et sécurisées. Les ressources incluses sur un réseau virtuel peuvent communiquer par le biais d’adresses IP privées.

  Choisissez l’option d’intégration au réseau virtuel si vous voulez avoir les capacités suivantes :

  - Connexion à partir de ressources Azure du même réseau virtuel à votre serveur flexible à l’aide d’adresses IP privées
  - Utilisation d’un VPN ou du service ExpressRoute pour vous connecter à partir de ressources non-Azure à votre serveur flexible
  - Aucun point de terminaison public

- **Accès public (adresses IP autorisées)**  : vous pouvez déployer votre serveur flexible avec un point de terminaison public. Le point de terminaison public est une adresse DNS résolvable publiquement. L’expression « adresses IP autorisées » fait référence à une plage d’adresses IP que vous choisissez d’autoriser à accéder à votre serveur. Ces autorisations sont appelées **règles de pare-feu**.

Pour en savoir plus, consultez [Concepts de mise en réseau](concepts-networking.md).

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustez les performances et la mise à l’échelle en quelques secondes

Trois références SKU sont disponibles pour le service à serveur flexible : Expansible, Usage général et À mémoire optimisée. Le niveau Burstable est idéalement adapté aux charges de travail de développement à faible coût et faible concurrence ne nécessitant pas en permanence une capacité de calcul complète. Les niveaux Usage général et À mémoire optimisée conviendront quant à eux aux charges de travail de production nécessitant une simultanéité et une mise à l'échelle de haut niveau, ainsi que des performances prévisibles. Vous pouvez créer votre première application sur une petite base de données pour un faible coût mensuel, puis adapter l’échelle en toute transparence aux besoins de votre solution. La mise à l'échelle du stockage s'effectue en ligne et prend en charge la croissance automatique du stockage. Le serveur flexible vous permet de provisionner jusqu’à 20 000 IOPS supplémentaires par rapport à la limite d’IOPS gratuites indépendamment du stockage. À l’aide de cette fonctionnalité, vous pouvez augmenter ou diminuer à tout moment le nombre d’IOPS approvisionnées en fonction des exigences de votre charge de travail. L’évolutivité dynamique permet de répondre en toute transparence à l’évolution rapide des besoins en ressources de votre base de données. Vous ne payez que pour les ressources que vous consommez. 

Pour plus d’informations, consultez [Concepts de calcul et de stockage](concepts-compute-storage.md).

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>Effectuer un scale-out de la charge de travail en lecture pour utiliser jusqu’à dix réplicas en lecture

MySQL est l’un des moteurs de base de données couramment utilisés pour exécuter des applications web et mobiles à l’échelle d’Internet. La plupart de nos clients s’en servent pour leurs services de formation en ligne, services de diffusion vidéo, solutions de paiement numérique, plateformes de commerce électronique, services de jeux, portails d’actualité, administrations publiques et sites web de santé. Ces services sont requis à des fins de mise à l’échelle à mesure que le trafic sur l’application web ou mobile augmente.

Du côté des applications, l’application est généralement développée en Java ou PHP et migrée pour s’exécuter sur des  [groupes de machines virtuelles identiques Azure](../../virtual-machine-scale-sets/overview.md)  ou  [Azure App Services](../../app-service/overview.md) , ou est conteneurisée pour s’exécuter sur  [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md). Avec un groupe de machines virtuelles identiques, App Service ou AKS en tant qu’infrastructure sous-jacente, la mise à l’échelle des applications est simplifiée grâce à l’approvisionnement instantané de nouvelles machines virtuelles et à la réplication des composants sans état des applications pour répondre aux demandes, mais souvent, la base de données finit par constituer un goulot d’étranglement comme composant avec état centralisé.

La fonctionnalité de réplica en lecture vous permet de répliquer les données d’un serveur flexible Azure Database pour MySQL sur un serveur en lecture seule. Vous pouvez effectuer la réplication à partir du serveur source vers **dix réplicas au maximum**. Les réplicas sont mis à jour de manière asynchrone à l’aide de la [technologie de réplication selon la position du fichier journal binaire (binlog)](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) native au moteur MySQL. Vous pouvez utiliser une solution de proxy d’équilibrage de charge comme [ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) pour faire un scale-out transparent de la charge de travail de votre application vers des réplicas en lecture sans coût de refactorisation de l’application. 

Pour en savoir plus, consultez [Concepts de réplicas en lecture](concepts-read-replicas.md).

## <a name="setup-hybrid-or-multi-cloud-data-synchronization-with-data-in-replication"></a>Configurer la synchronisation de données hybride ou multicloud avec la réplication de données

La réplication des données entrantes vous permet de synchroniser les données d’un serveur MySQL externe avec le service Azure Database pour MySQL Flexible. Le serveur externe peut être hébergé localement, dans des machines virtuelles, Azure Database pour MySQL Single Server, ou il peut s'agir d'un service de base de données hébergé par d'autres fournisseurs de services cloud. La réplication des données est basée sur la position du fichier journal binaire (binlog). Voici les principaux scénarios à prendre en compte concernant l’utilisation de la réplication des données entrantes :
* Synchronisation de données hybride
* Synchronisation de plusieurs clouds
* [Migration minimale des temps d’arrêt vers un serveur flexible](../../mysql/howto-migrate-single-flexible-minimum-downtime.md)

Pour plus d’informations, consultez [Concepts de réplication de données](concepts-data-in-replication.md).


## <a name="stopstart-server-to-optimize-cost"></a>Arrêter/démarrer le serveur pour optimiser les coûts

Le service de serveur flexible vous permet d’arrêter et de démarrer le serveur à la demande pour optimiser les coûts. La facturation du niveau de calcul est immédiatement arrêtée lorsque le serveur est arrêté. Cela peut vous permettre de réaliser des économies significatives en termes de développement, de test et de charges de travail de production prévisibles liées au temps. Le serveur reste à l’état arrêté pendant trente jours, sauf si le redémarrage intervient plus tôt.

Pour plus d’informations, consultez [Concepts de serveur](concept-servers.md).

## <a name="enterprise-grade-security-compliance-and-privacy"></a>Sécurité de qualité professionnelle, conformité et confidentialité

Le service à serveur flexible utilise le module de chiffrement conforme à la norme FIPS 140-2 pour chiffrer le stockage des données au repos. Toutes les données sont chiffrées, y compris les sauvegardes et les fichiers temporaires créés lors de l'exécution des requêtes. Le service utilise le chiffrement AES 256 bits inclus dans le chiffrement de stockage Azure, et les clés peuvent être gérées par le système (par défaut).

Le service chiffre les données en mouvement avec le protocole TLS appliqué par défaut. Par défaut, le serveur flexible prend en charge les connexions chiffrées avec le protocole TLS 1.2 et refuse toutes les connexions entrantes qui utilisent les protocoles TLS 1.0 et TLS 1.1. L’application du protocole SSL peut être désactivée en définissant le paramètre require_secure_transport et le paramètre tls_version (version minimale) pour votre serveur.

Pour plus d’informations, consultez [Utiliser les connexions chiffrées sur des serveurs flexibles](how-to-connect-tls-ssl.md).

Les serveurs flexibles permettent un accès privé complet aux serveurs à l’aide du [réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md) (intégration au réseau virtuel). Les serveurs du réseau virtuel Azure sont uniquement accessibles et connectés via des adresses IP privées. Avec l’intégration au réseau virtuel, l’accès public est refusé et les serveurs ne sont pas accessibles à l’aide de points de terminaison publics.

Pour plus d’informations, consultez [Concepts relatifs aux réseaux](concepts-networking.md).

## <a name="monitoring-and-alerting"></a>Surveillance et alerte

Le service à serveur flexible est équipé de fonctionnalités intégrées d'analyse des performances et d'alerte. Toutes les métriques Azure présentent une fréquence d’une minute et chaque métrique fournit 30 jours d’historique. Vous pouvez configurer des alertes basées sur les métriques. Le service expose les métriques du serveur hôte pour superviser l’utilisation des ressources et permet de configurer les journaux des requêtes lentes. Grâce à ces outils, vous pouvez rapidement optimiser vos charges de travail et configurer votre serveur pour bénéficier de performances optimales. Azure Database pour MySQL - Serveur flexible vous permet de visualiser les données des journaux des requêtes lentes et d’audit à l’aide d’Azure Monitor Workbooks. Les workbooks vous offrent un canevas flexible pour l’analyse des données et la création de rapports visuels enrichis au sein du portail Azure. Azure Database pour MySQL - Serveur flexible fournit trois modèles de classeurs prêts à l’emploi, à savoir Vue d’ensemble du serveur, [Audit](tutorial-configure-audit.md) et [Query Performance Insight](tutorial-query-performance-insights.md). Le classeur [Query Performance Insight](tutorial-query-performance-insights.md) est conçu pour vous permettre de passer moins de temps à résoudre les problèmes de performances de base de données en fournissant les informations suivantes :

* N premières requêtes de longue durée et leurs tendances associées.
* Détails des requêtes : Consultez le texte de la requête ainsi que l’historique de l’exécution indiquant le temps de requête minimal, maximal, moyen et d’écart type.
* Utilisation des ressources (processeur, mémoire et stockage).

De plus, vous pouvez utiliser et intégrer des outils de supervision fournis par la communauté, comme [Percona Monitoring and Management avec votre serveur flexible MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/monitor-azure-database-for-mysql-using-percona-monitoring-and/ba-p/2568545). 

Pour plus d'informations, voir [Concepts de réplication](concepts-monitoring.md).

## <a name="migration"></a>Migration

Le service exécute la version de la communauté de MySQL. Cela permet une compatibilité totale des applications et requiert un coût de refactorisation minimal pour migrer une application existante développée sur le moteur MySQL vers un serveur flexible. La migration vers un serveur flexible peut être effectuée à l’aide de l’option suivante :

### <a name="offline-migrations"></a>Migrations hors connexion
*   Utilisation d’Azure Data Migration Service lorsque la bande passante réseau entre la source et Azure est correcte (par exemple, ExpressRoute à haute vitesse). En savoir plus avec les instructions pas à pas : [Migrer MySQL vers Azure Database pour MySQL hors connexion à l’aide de DMS (Azure Database Migration Service)](../../dms/tutorial-mysql-azure-mysql-offline-portal.md)
*   Utilisez mydumper/myloader pour tirer parti des paramètres de compression afin de déplacer efficacement des données sur des réseaux à faible vitesse (tels que des réseaux Internet publics). En savoir plus avec les instructions pas à pas [Migrer des bases de données volumineuses vers Azure Database pour MySQL à l’aide de mydumper/myloader](../../mysql/concepts-migrate-mydumper-myloader.md)

### <a name="online-or-minimal-downtime-migrations"></a>Migrations en ligne ou avec temps d’arrêt minimal
Utilisez la réplication de données dans une sauvegarde/restauration mydumper/myloader cohérente pour l’amorçage initial. En savoir plus avec les instructions pas à pas : [Tutoriel : migration de la base de données Azure pour MySQL avec temps d’arrêt minimal – Serveur unique vers Azure Database pour MySQL – Serveur flexible](../../mysql/howto-migrate-single-flexible-minimum-downtime.md)

Pour migrer d’Azure Database pour MySQL -Serveur unique vers Serveur flexible en cinq étapes simples, consultez [ce blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/migrate-from-azure-database-for-mysql-single-server-to-flexible/ba-p/2674057).

Pour plus d’informations, consultez [Sélectionner les outils appropriés en vue de la migration vers Azure Database pour MySQL](../../mysql/how-to-decide-on-right-migration-tools.md)

## <a name="azure-regions"></a>Régions Azure

L’un des avantages de l’exécution de votre charge de travail dans Azure est sa portée mondiale. Le serveur flexible pour Azure Database pour MySQL est disponible aujourd’hui dans les régions Azure suivantes :

| Region | Disponibilité | Haute disponibilité dans la même zone | Haute disponibilité redondante interzone |
| --- | --- | --- | --- |
| Australie Est | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Sud-Australie Est | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Brésil Sud | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Centre du Canada | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Est du Canada | :heavy_check_mark: | :x: | :x: |
| Inde centrale | :heavy_check_mark: | :heavy_check_mark: | :x: |
| USA Centre | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Asie Est (Hong Kong, R.A.S.) | :heavy_check_mark: | :heavy_check_mark: | :x: |
| USA Est | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| USA Est 2 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| France Centre | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Allemagne Centre-Ouest | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Japon Est | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OuJapon Est | :heavy_check_mark: | :x: | :x: |
| Centre de la Corée | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Corée du Sud | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Centre-Nord des États-Unis | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Europe Nord | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Centre-Nord des États-Unis | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Norvège Est | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Afrique du Sud Nord | :heavy_check_mark: | :heavy_check_mark: | :x: |
| États-Unis - partie centrale méridionale | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Asie Sud-Est | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Suisse Nord | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Émirats arabes unis Nord | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Sud du Royaume-Uni | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Ouest du Royaume-Uni | :heavy_check_mark: | :x: | :x: |
| Centre-USA Ouest | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Europe Ouest | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| USA Ouest | :heavy_check_mark: | :heavy_check_mark: | :x: |
| USA Ouest 2 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| USA Ouest 3 | :heavy_check_mark: | :heavy_check_mark: | :x: |

## <a name="contacts"></a>Contacts

Pour toute question ou suggestion au sujet du serveur flexible d’Azure Database pour MySQL, envoyez un e-mail à l’équipe Azure Database pour MySQL ([@Ask Azure DB pour MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Cette adresse e-mail n’est pas un alias du support technique.

En outre, tenez compte des points de contact suivants le cas échéant :

- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez lu l'introduction au mode de déploiement Azure Database pour MySQL - Serveur unique, vous êtes prêt à :

- Créer votre premier serveur
  - [Créer un serveur flexible Azure Database pour MySQL à l'aide du portail Azure](quickstart-create-server-portal.md)
  - [Créer un serveur flexible Azure Database pour MySQL à l'aide d'Azure CLI](quickstart-create-server-cli.md)
  - [Gérer un serveur flexible Azure Database pour MySQL à l'aide d'Azure CLI](how-to-manage-server-portal.md)

- Générez votre première application à l’aide de votre langage préféré :
  - [Python](connect-python.md)
  - [PHP](connect-php.md)
