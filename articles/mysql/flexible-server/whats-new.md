---
title: Nouveautés Azure Database pour MySQL - Serveur flexible
description: Découvrez les récentes mises à jour d’Azure Database pour MySQL - Serveur flexible, un service de base de données relationnelle du cloud Microsoft qui repose sur MySQL Community Edition.
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc, references_regions
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: 8406f9b551d80959db983a3837b441dc9f965782
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005268"
---
# <a name="whats-new-in-azure-database-for-mysql---flexible-server-preview"></a>Nouveautés Azure Database pour MySQL - Serveur flexible (préversion)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

[Azure Database pour MySQL - Serveur flexible](./overview.md#azure-database-for-mysql---flexible-server-preview) est un mode de déploiement qui est conçu pour offrir un contrôle et une flexibilité plus granulaires des fonctions de gestion de base de données et des paramètres de configuration que le mode de déploiement Serveur unique. Le service prend actuellement en charge les versions de la communauté de MySQL 5.7 et 8.0.

Cet article résume les nouvelles versions et fonctionnalités d’Azure Database pour MySQL - Serveur flexible à compter de janvier 2021. Les éléments s’affichent dans l’ordre chronologique inverse, avec les mises à jour les plus récentes en premier.

## <a name="october-2021"></a>Octobre 2021

- **Restauration de la sauvegarde géoredondante dans une région géocouplée pour des scénarios de récupération d’urgence**

    Le service propose maintenant la flexibilité supplémentaire de choisir le stockage d’une sauvegarde géoredondante pour offrir une plus grande résilience des données. Les clients peuvent ainsi effectuer une récupération à la suite d’un sinistre géographique ou d’une défaillance régionale lorsqu’ils ne peuvent pas accéder au serveur de la région primaire. En activant cette fonctionnalité, ils ont la possibilité de mettre en œuvre une géorestauration et de déployer un nouveau serveur dans la région géographique géocouplée en tirant parti de la dernière sauvegarde géoredondante disponible du serveur d’origine. [Plus d’informations](../flexible-server/concepts-backup-restore.md) 

-  **Zones de disponibilité - Sélection lors de la création de réplicas en lecture**

    Lorsque vous créez un réplica en lecture, vous avez la possibilité de sélectionner l’emplacement Zones de disponibilité de votre choix. Une zone de disponibilité est une offre à haute disponibilité qui protège vos applications et vos données contre les défaillances des centres de données. Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. [Plus d’informations](../flexible-server/concepts-read-replicas.md)

- **Réplicas en lecture dans Azure Database pour MySQL - Les serveurs flexibles ne seront plus disponibles sur les références (SKU) de type burstable**
    
    Vous ne serez pas en mesure de créer des réplicas de lecture existants ou de les gérer sur le serveur de niveau Burstable. Dans le but de fournir une expérience de développement et de requête satisfaisante pour les niveaux de référence Burstable, la prise en charge de la création et de la gestion des réplicas de lecture pour les serveurs dans le niveau tarifaire Burstable peut être interrompue. 

    Si vous disposez d’un serveur flexible Azure Database pour MySQL avec un réplica en lecture activé, vous devez mettre à l’échelle votre serveur vers les niveaux tarifaires Usage général ou À mémoire optimisée ou supprimer le réplica en lecture dans un délai de 60 jours. Après la période de 60 jours, vous pouvez continuer à utiliser le serveur principal pour vos opérations de lecture-écriture, mais la réplication pour les serveurs de réplica en lecture sera arrêtée. Pour les serveurs nouvellement créés, l’option de réplica en lecture est disponible uniquement pour les niveaux de tarification Usage général et À mémoire optimisée.  

 - **Supervision d’Azure Database pour MySQL – Serveur flexible avec classeurs Azure Monitor**
 
     Azure Database pour MySQL – Serveur flexible désormais intégré dans les classeurs Azure Monitor. Les classeurs fournissent un canevas flexible pour l’analyse des données et la création de rapports visuels enrichis au sein du portail Azure. Avec cette intégration, le serveur dispose d’un lien vers des classeurs et de quelques exemples de modèles, qui permettent de surveiller le service à grande échelle. Vous pouvez modifier ces modèles, les adapter aux besoins du client et les épingler au tableau de bord pour créer une vue ciblée et organisée des ressources Azure. Les modèles [Query Performance Insight](./tutorial-query-performance-insights.md), [Audit](./tutorial-configure-audit.md) et Vue d’ensemble de l’instance sont actuellement disponibles. [Plus d’informations](./concepts-workbooks.md)

- **Prépayer les ressources de calcul Azure Database pour MySQL avec des instances réservées**

    Azure Database pour MySQL – Serveur flexible vous aide désormais à réaliser des économies grâce au prépaiement des ressources de calcul par rapport au tarif du paiement à l’utilisation. Avec des instances réservées Azure Database pour MySQL, vous prenez un engagement initial pour le serveur MySQL, sur une période d’un ou de trois ans, afin de bénéficier d’une remise importante sur les coûts de calcul. Vous pouvez également échanger une réservation d’Azure Database pour MySQL - Serveur unique avec un serveur flexible. [Plus d’informations](../concept-reserved-pricing.md)

- **Arrêt du serveur pendant 30 jours maximum pendant que le serveur n’est pas en cours d’utilisation**
    
    La fonctionnalité Serveur flexible Azure Database pour MySQL vous offre désormais la possibilité d’arrêter le serveur pendant 30 jours maximum quand il n’est pas utilisé et de le démarrer pendant cette période quand vous êtes prêt à reprendre votre développement. Cela vous permet d’effectuer le développement à votre rythme et de réduire les coûts de développement sur les serveurs de base de données en payant les ressources uniquement quand elles sont en cours d’utilisation. C’est important pour les charges de travail de développement et de test, ainsi que quand vous utilisez le serveur uniquement à un moment de la journée. Lorsque vous arrêtez le serveur, toutes les connexions actives sont supprimées. Lorsque le serveur se trouve à l’état Arrêté, le calcul du serveur n’est pas facturé. Toutefois, le stockage continue à être facturé tant que le stockage du serveur est conservé pour s’assurer que les fichiers de données sont disponibles lors du redémarrage du serveur. [En savoir plus](concept-servers.md#stopstart-an-azure-database-for-mysql-flexible-server)

- **Prise en charge Terraform du Serveur flexible MySQL**
    
    La prise en charge de Terraform pour le Serveur flexible MySQL est désormais disponible avec la [dernière version v2.81.0 d’AzureRM](https://github.com/hashicorp/terraform-provider-azurerm/blob/v2.81.0/CHANGELOG.md). Le document de référence détaillé pour le provisionnement et la gestion d’un Serveur flexible MySQL avec Terraform est disponible [ici](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/mysql_flexible_server). Les bogues ou les problèmes connus peuvent être consultés ou signalés [ici](https://github.com/hashicorp/terraform-provider-azurerm/issues).

- **Problèmes connus**
    - Lorsqu’une région primaire Azure est hors service, il n’est pas possible de créer des serveurs géoredondants dans la région géocouplée, car le stockage ne peut pas être provisionné dans la région primaire Azure. Il faut attendre que la région principale soit en service pour provisionner des serveurs géoredondants dans la région associée géographiquement. 

## <a name="september-2021"></a>Septembre 2021

Cette version d’Azure Database pour MySQL - Serveur flexible inclut les mises à jour suivantes.

- **Disponibilité dans trois régions Azure supplémentaires**

   La préversion publique d’Azure Database pour MySQL - Serveur flexible est maintenant disponible dans les régions Azure suivantes :

   - Ouest du Royaume-Uni
   - Est du Canada
   - OuJapon Est

- **Résolution des bogues**

   La création d’une haute disponibilité de zone identique est résolue dans les régions suivantes :

   - Inde centrale
   - Asie Est
   - Centre de la Corée
   - Afrique du Sud Nord
   - Suisse Nord

## <a name="august-2021"></a>Août 2021

Cette version d’Azure Database pour MySQL - Serveur flexible inclut les mises à jour suivantes.

- **Haute disponibilité au sein d’une même zone à l’aide de la haute disponibilité dans la même zone**

  Le service offre à présent aux clients la possibilité de choisir la zone de disponibilité préférée pour leur serveur de secours lorsqu’ils activent la haute disponibilité. Avec cette fonctionnalité, les clients peuvent placer un serveur de secours dans la même zone que le serveur principal, ce qui réduit le décalage de la réplication entre le serveur principal et les serveurs secondaires. Ceci permet également de réduire les latences entre le serveur d’applications et le serveur de base de données si ces derniers sont placés dans la même zone Azure. [Plus d’informations](./concepts-high-availability.md)

- **Sélection de la zone de secours à l’aide de la haute disponibilité redondante interzone**

  Le service fournit à présent aux clients la possibilité de choisir l’emplacement de la zone du serveur de secours. À l’aide de cette fonctionnalité, les clients peuvent placer leur serveur de secours dans la zone de leur choix. La colocalisation des serveurs de base de données de secours et des applications de secours dans la même zone réduit les latences et permet aux clients de mieux se préparer à des situations de reprise d’activité et à des scénarios de « zone en panne ». [Plus d’informations](./concepts-high-availability.md)

- **Intégration d’une zone DNS privée**

  Le [DNS privé Azure](../../dns/private-dns-privatednszone.md) fournit un service DNS fiable et sécurisé (responsable de la traduction d’un nom de service en adresse IP) pour votre réseau virtuel. Azure Private DNS gère et résout les noms de domaine dans le réseau virtuel sans nécessiter la configuration d’une solution DNS personnalisée. Cela vous permet de connecter votre application en cours d’exécution sur un réseau virtuel à votre serveur flexible s’exécutant sur un réseau virtuel à homologation locale ou globale. L’offre Azure Database pour MySQL - Serveur flexible fournit à présent une intégration avec une zone DNS privée Azure pour permettre une résolution transparente du DNS privé au sein du réseau virtuel actuel ou de tout réseau virtuel homologué auquel la zone DNS privée est liée. Avec cette intégration, si l’adresse IP du serveur flexible backend change pendant le basculement ou tout autre événement, votre zone DNS privée intégrée est automatiquement mise à jour pour garantir que la connectivité de votre application reprend automatiquement une fois que le serveur est en ligne. [Plus d’informations](./concepts-networking-vnet.md)

- **Récupération jusqu’à une date et heure pour un serveur dans un réseau virtuel spécifié**

  L’expérience de la récupération jusqu’à une date et heure pour le service permet à présent aux clients de configurer les paramètres de mise en réseau, ce qui permet aux utilisateurs de basculer entre les options de mise en réseau privée et publique lors de l’exécution d’une opération de restauration. Cette fonctionnalité offre aux clients la possibilité d’injecter un serveur en cours de restauration dans un réseau virtuel spécifié qui sécurise leurs points de terminaison de connexion. [Plus d’informations](./how-to-restore-server-portal.md)

- **Récupération jusqu’à une date et heure pour un serveur dans une zone de disponibilité**

  L’expérience de récupération jusqu’à une date et heure pour le service permet maintenant aux clients de configurer la zone de disponibilité, de colocaliser les serveurs de base de données et les applications de secours dans la même zone pour réduire les latences et permet aux clients de mieux se préparer à des situations de récupération d’urgence et aux scénarios de « zone en panne ». [Plus d’informations](./concepts-high-availability.md)

- **Plug-ins validate_password et caching_sha2_password disponibles en préversion privée**

  Me mode Serveur flexible prend maintenant en charge l’activation des plug-ins validate_password et caching_sha2_password en préversion privée. Envoyez-nous un e-mail à l’adresse AskAzureDBforMySQL@service.microsoft.com

- **Disponibilité dans quatre régions Azure supplémentaires**

   La préversion publique d’Azure Database pour MySQL - Serveur flexible est maintenant disponible dans les régions Azure suivantes :

   - Sud-Australie Est
   - Afrique du Sud Nord
   - Asie Est (Hong Kong, R.A.S.)
   - Inde centrale

   [Plus d’informations](overview.md#azure-regions)

- **Problèmes connus**

   - Juste après le basculement du serveur à haute disponibilité redondant interzone, les clients ne parviennent pas à se connecter au serveur si SSL est utilisé avec le ssl_mode VERIFY_IDENTITY. Ce problème peut être atténué en utilisant le ssl_mode VERIFY_CA.
   - Impossible de créer un serveur à haute disponibilité dans la même zone dans les régions suivantes : Inde Centre, Asie Est, Corée Centre, Afrique du Sud Nord, Suisse Nord.
   - Dans un scénario rare et après un basculement à haute disponibilité, le serveur principal est en mode read_only. Résolvez le problème en mettant à jour la valeur « read_only » du panneau des paramètres du serveur sur OFF (Désactivé).
   - Après avoir correctement mis à l’échelle le calcul dans le panneau Calcul + stockage, les E/S par seconde sont réinitialisées à la référence SKU par défaut. Les clients peuvent contourner le problème en restaurant les E/S par seconde dans le panneau Calcul + stockage à la valeur souhaitée (précédemment définie) après le déploiement du calcul et la réinitialisation des E/S par seconde qui en résulte.

## <a name="july-2021"></a>Juillet 2021

Cette version d’Azure Database pour MySQL - Serveur flexible inclut les mises à jour suivantes.

- **Migration en ligne du mode Serveur unique vers le mode Serveur flexible**

  Les clients peuvent maintenant migrer une instance Azure Database pour MySQL – Serveur unique vers le mode Serveur flexible avec un temps d’arrêt minimal pour leurs applications à l’aide de la réplication des données entrantes. Pour obtenir des instructions pas à pas détaillées, consultez [Migrer une base de données Azure Database pour MySQL – Serveur unique vers un serveur flexible avec un temps d’arrêt minimal](../howto-migrate-single-flexible-minimum-downtime.md).

- **Disponibilité dans les régions USA Ouest et Allemagne Centre-Ouest**

  La préversion publique d’Azure Database pour MySQL - Serveur flexible est maintenant disponible dans les régions Azure USA Ouest et Allemagne Centre-Ouest.

## <a name="june-2021"></a>Juin 2021

Cette version d’Azure Database pour MySQL - Serveur flexible inclut les mises à jour suivantes.

- **Amélioration des performances sur les serveurs de stockage plus petits**

    À partir du 21 juin 2021, la taille de stockage provisionnée minimale autorisée pour l’ensemble des serveurs nouvellement créés passe de 5 Go à 20 Go. En outre, les E/S par seconde disponibles augmentent de 100 à 300. Ces modifications sont récapitulées dans le tableau ci-dessous.

    | **Current** | **À compter du 21 juin 2021** |
    |:----------|:----------|
    | Taille de stockage minimale autorisée : 5 Go | Taille de stockage minimale autorisée : 20 Go |
    | IOPS disponibles : Max. (100, 3 * [Stockage provisionné en Go]) | IOPS disponibles : (300 + 3 * [Stockage provisionné en Go]) |

- **Offre gratuite de 12 mois**

  Depuis le 15 juin 2021, le [compte gratuit Azure](https://azure.microsoft.com/free/) offre aux clients un accès gratuit de 12 mois à Azure Database pour MySQL - Serveur flexible avec 750 heures d’utilisation et 32 Go de stockage par mois. Les clients peuvent profiter de cette offre s’ils souhaitent développer et déployer des applications qui utilisent Azure Database pour MySQL - Serveur flexible. [Plus d’informations](./how-to-deploy-on-azure-free-account.md)

- **Croissance automatique du stockage**

  La croissance automatique du stockage permet à un serveur de disposer en permanence d’un espace de stockage suffisant et de ne pas passer en lecture seule. Si la croissance automatique du stockage est activée, le stockage évolue automatiquement sans affecter la charge de travail. À partir du 21 juin 2021, la croissance automatique du stockage est activée par défaut sur tous les serveurs nouvellement créés. [Plus d’informations](concepts-compute-storage.md#storage-auto-grow)

- **Réplication des données entrantes**

    Le mode Serveur flexible prend maintenant en charge la [réplication des données entrantes](concepts-data-in-replication.md). Utilisez cette fonctionnalité pour synchroniser et migrer des données à partir d’un serveur MySQL exécuté localement, dans des machines virtuelles, sur Azure Database pour MySQL Serveur unique ou sur des services de base de données en dehors d’Azure vers Azure MySQL Database - Serveur flexible. Découvrez comment [configurer la réplication des données entrantes](how-to-data-in-replication.md).

- **Prise en charge de GitHub Actions avec Azure CLI**

  L’interface CLI du mode Serveur flexible permet à présent aux clients d’automatiser les flux de travail pour déployer des mises à jour avec GitHub Actions. Cette fonctionnalité permet de configurer et de déployer des mises à jour de base de données avec le workflow d’action MySQL GitHub. Ces commandes CLI vous aident à configurer un référentiel pour permettre un déploiement continu afin de faciliter le développement. [Plus d’informations](/cli/azure/mysql/flexible-server/deploy?view=azure-cli-latest&preserve-view=true)

- **Correctifs appliqués au basculement forcé à haute disponibilité redondant interzone**

  Cette version inclut des correctifs pour les problèmes connus liés au basculement forcé afin de s’assurer que les paramètres du serveur et les modifications d’IOPS supplémentaires sont conservés entre les basculements.

- **Problèmes connus**

   - La tentative d’exécution d’une opération de scale-up ou de scale-down du calcul sur un serveur existant avec moins de 20 Go de stockage provisionné ne s’effectue pas correctement. Résolvez le problème en mettant à l’échelle le stockage provisionné à 20 Go et en réessayant l’opération de mise à l’échelle du calcul.

## <a name="may-2021"></a>Mai 2021

Cette version d’Azure Database pour MySQL - Serveur flexible inclut les mises à jour suivantes.

- **Disponibilité régionale étendue (France Centre, Brésil Sud et Suisse Nord)**

    La préversion publique d’Azure Database pour MySQL - Serveur flexible est maintenant disponible dans les régions France Centre, Brésil Sud et Suisse Nord. [Plus d’informations](overview.md#azure-regions)

- **La contrainte SSL/TLS 1.2 peut être désactivée**

   Cette version offre une meilleure flexibilité pour personnaliser la mise en œuvre de SSL et de la version minimale de TLS. Pour plus d’informations, consultez [Se connecter à Azure Database pour MySQL - Serveur flexible en utilisant des connexions chiffrées](how-to-connect-tls-ssl.md).

- **Haute disponibilité redondante interzone disponible dans la région Royaume-Uni Sud et dans la région Japon Est**

   Azure Database pour MySQL - Serveur flexible offre à présent une haute disponibilité redondante interzone dans deux régions supplémentaires : Royaume-Uni Sud et Japon Est. [Plus d’informations](overview.md#azure-regions)

- **Problèmes connus**

   - Les modifications d’IOPs supplémentaires ne sont pas prises en compte dans les serveurs à haute disponibilité redondants interzone. Les clients peuvent contourner le problème en désactivant la haute disponibilité, en mettant à l’échelle les IOPS et en réactivant la haute disponibilité redondante interzone.
   - Après le basculement forcé, la zone de disponibilité de secours est reflétée de manière incorrecte dans le portail. (Aucune solution de contournement)
   - Les modifications apportées aux paramètres du serveur ne sont pas prises en compte dans le serveur à haute disponibilité après le basculement forcé. (Aucune solution de contournement)

## <a name="april-2021"></a>Avril 2021

Cette version d’Azure Database pour MySQL - Serveur flexible inclut les mises à jour suivantes.

- **Possibilité de forcer le basculement vers le serveur de secours avec une haute disponibilité redondante interzone**

  Les clients peuvent maintenant forcer manuellement un basculement pour tester les fonctionnalités dans leurs scénarios d’application, ce qui peut les aider à se préparer en cas de panne. [Plus d’informations](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/forced-failover-for-azure-database-for-mysql-flexible-server/ba-p/2280671)

- **Publication du module PowerShell pour Serveur flexible**

  Les développeurs peuvent maintenant utiliser PowerShell pour provisionner, gérer, exploiter et prendre en charge des serveurs flexibles MySQL et les ressources dépendantes. [Plus d’informations](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-the-mysql-flexible-server-powershell-module/ba-p/2203383)

- **Connecter, tester et exécuter des requêtes à l’aide d’Azure CLI**

  L’offre Azure Database pour MySQL - Serveur flexible fournit à présent une expérience de développement améliorée permettant aux clients de se connecter et d’exécuter des requêtes sur leurs serveurs à l’aide d’Azure CLI avec les commandes « az mysql flexible-server connect » et « az mysql flexible-server execute ». [Plus d’informations](connect-azure-cli.md#view-all-the-arguments)

- **Correctifs pour les échecs de provisionnement pour les créations de serveur dans un réseau virtuel avec accès privé**

  Tous les échecs de provisionnement dus à la création d’un serveur dans un réseau virtuel sont résolus. Avec cette version, les utilisateurs peuvent créer des serveurs flexibles avec un accès privé à chaque fois.  

## <a name="march-2021"></a>Mars 2021

Cette version d’Azure Database pour MySQL - Serveur flexible inclut les mises à jour suivantes.

- **Publication de MySQL 8.0.21**

  MySQL 8.0.21 est à présent disponible dans l’offre Serveur flexible dans toutes les principales [régions Azure](overview.md#azure-regions). Les clients peuvent utiliser le portail Azure, Azure CLI ou les modèles Azure Resource Manager pour provisionner la version 8.0.21 de MySQL. [Plus d’informations](quickstart-create-server-portal.md#create-an-azure-database-for-mysql-flexible-server)

- **Prise en charge du positionnement de la zone de disponibilité lors de la création du serveur**

  Les clients peuvent maintenant spécifier la zone de disponibilité de leur choix au moment de la création du serveur. Cette fonctionnalité permet aux clients de colocaliser leurs applications hébergées sur une machine virtuelle Azure, un groupe de machines virtuelles identiques ou une base de données AKS dans les mêmes zones de disponibilité pour réduire la latence de la base de données et améliorer les performances. [Plus d’informations](quickstart-create-server-portal.md#create-an-azure-database-for-mysql-flexible-server)

- **Correctifs de performances pour les problèmes liés à l’exécution d’un serveur flexible dans un réseau virtuel avec accès privé**

  Avant cette version, les performances du serveur flexible se dégradaient considérablement en cas d’exécution dans la configuration du réseau virtuel. Cette version inclut des correctifs pour le problème, ce qui permet aux utilisateurs de voir l’amélioration des performances sur le serveur flexible dans le réseau virtuel.

- **Problèmes connus**

   - SSL/TLS 1.2 est appliqué et ne peut pas être désactivé. (Aucune solution de contournement)
   - Il existe des échecs de provisionnement intermittents pour les serveurs provisionnés dans un réseau virtuel. La solution de contournement consiste à réessayer le provisionnement du serveur jusqu’à ce qu’il aboutisse.

## <a name="february-2021"></a>Février 2021

Cette version d’Azure Database pour MySQL - Serveur flexible inclut les mises à jour suivantes.

- **Publication de la fonctionnalité d’IOPS supplémentaires**

  Azure Database pour MySQL - Serveur flexible prend en charge le provisionnement d’[IOPS](concepts-compute-storage.md#iops) supplémentaires indépendamment du stockage provisionné. Les clients peuvent utiliser cette fonctionnalité pour augmenter ou diminuer le nombre d’IOPS à tout moment en fonction des exigences de leur charge de travail.

- **Problèmes connus**

  Les performances d’Azure Database pour MySQL - Serveur flexible se dégradent avec l’isolation du réseau virtuel avec accès privé (aucune solution de contournement).

## <a name="january-2021"></a>Janvier 2021

Cette version d’Azure Database pour MySQL - Serveur flexible inclut les mises à jour suivantes.

- **Jusqu’à 10 réplicas de lecture pour MySQL - Serveur flexible**

  L’offre Serveur flexible prend maintenant en charge la réplication asynchrone des données d’un serveur Azure Database pour MySQL (la « source ») sur un maximum de 10 serveurs Azure Database pour MySQL (les « réplicas ») dans la même région. Cette fonctionnalité permet de mettre à l’échelle les charges de travail nécessitant beaucoup de lectures et de les équilibrer entre les différents serveurs de réplication en fonction des préférences de l’utilisateur. [Plus d’informations](concepts-read-replicas.md)

## <a name="contacts"></a>Contacts

Si vous avez des questions ou des suggestions concernant l’utilisation d’Azure Database pour MySQL, vous disposez des options suivantes :

- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour fournir des commentaires ou pour demander de nouvelles fonctionnalités, envoyez-nous un e-mail à l’adresse AskAzureDBforMySQL@service.microsoft.com.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [tarification d’Azure Database pour MySQL](https://azure.microsoft.com/pricing/details/mysql/server/).
- Parcourez la [documentation publique](index.yml) d’Azure Database pour MySQL – Serveur flexible.
- Passez en revue les détails sur la [résolution des erreurs de migration courantes](../howto-troubleshoot-common-errors.md).
