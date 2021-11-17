---
title: Vue d’ensemble – Azure Database pour MySQL
description: Découvrez le service Azure Database pour MySQL, service de base de données relationnelle dans le cloud Microsoft qui repose sur MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: e93432347de7df3b2743143c68078b3dfc853848
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894352"
---
# <a name="what-is-azure-database-for-mysql"></a>Qu’est-ce qu’Azure Database pour MySQL ?

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database pour MySQL est un service de base de données relationnelle du cloud Microsoft qui repose sur le moteur de base de données [MySQL Community Edition](https://www.mysql.com/products/community/) (disponible avec la licence GPLv2), versions 5.6 (mise hors service), 5.7 et 8.0. Azure Database pour MySQL offre :

- Haute disponibilité dans la même zone et redondante interzone.
- Contrôle maximal avec possibilité de sélectionner votre fenêtre de maintenance planifiée.
- La protection des données à l’aide de sauvegardes automatiques et une restauration à un point dans le temps jusqu’à 35 jours.
- Mise à jour corrective et maintenance automatisées du matériel sous-jacent, du système d’exploitation et du moteur de base de données pour assurer la sécurité et la mise à jour du service.
- Des performances prévisibles, grâce aux tarifs du paiement à l’utilisation ;
- Mise à l’échelle élastique en quelques secondes.
- Contrôles d’optimisation des coûts avec référence SKU expansible à faible coût et possibilité d’arrêter/de démarrer le serveur.
- Sécurité de qualité professionnelle, conformité à la pointe du secteur et confidentialité pour protéger les données sensibles au repos et en mouvement.
- Surveillance et automatisation pour simplifier la gestion et la surveillance des déploiements à grande échelle.
- Expérience de support à la pointe du secteur.

Ces fonctionnalités ne demandent pratiquement aucune administration, et toutes sont fournies sans coût supplémentaire. Elles vous permettent de vous concentrer sur le développement rapide de vos applications et d’accélérer leur mise sur le marché, plutôt que de consacrer du temps et des ressources à gérer des machines virtuelles et une infrastructure. Par ailleurs, vous pouvez continuer à développer votre application avec les outils open source et la plateforme de votre choix pour travailler avec la rapidité et l’efficacité exigées par votre activité, et ce sans avoir à acquérir de nouvelles compétences.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Diagramme conceptuel d’Azure Database pour MySQL":::

## <a name="deployment-models"></a>Modèles de déploiement

Deux modes de déploiement sont disponibles pour Azure Database pour MySQL optimisé par MySQL Community Edition :
- Serveur flexible
- Serveur unique 

### <a name="azure-database-for-mysql---flexible-server"></a>Serveurs flexibles Azure Database pour MySQL

Azure Database pour MySQL - Serveur flexible est un service de base de données prêt pour la production entièrement géré et conçu pour offrir un contrôle et une flexibilité plus granulaires des fonctions de gestion de base de données et des paramètres de configuration. L’architecture de serveur flexible permet aux utilisateurs d’opter pour une haute disponibilité au sein d’une même zone de disponibilité et dans plusieurs zones de disponibilité. Les serveurs flexibles offrent de meilleurs contrôles d’optimisation des coûts grâce à la possibilité de d’arrêter/de démarrer le serveur et un niveau de calcul expansible, ce qui est idéal pour les charges de travail ne nécessitant pas en permanence une capacité de calcul complète. Un serveur flexible prenant également en charge des instances réservées, il vous permet d’économiser jusqu’à 63 % sur les coûts, ce qui est idéal pour des charges de travail de production dont les exigences en matière de capacité de calcul sont prévisibles. Le service prend en charge la version de la communauté de MySQL 5.7 et 8.0. Le service est aujourd'hui généralement disponible dans un grand nombre de [régions Azure](flexible-server/overview.md#azure-regions).

L’option de déploiement Serveur flexible offre trois niveaux de calcul : Expansible, Usage général et Mémoire optimisée. Chaque niveau offre des capacités de calcul et de mémoire différentes pour prendre en charge les charges de travail de votre base de données. Vous pouvez créer votre première application sur un niveau Expansible pour un faible coût mensuel, puis adapter l’échelle aux besoins de votre solution. L’évolutivité dynamique permet de répondre en toute transparence à l’évolution rapide des besoins en ressources de votre base de données. Vous payez uniquement pour les ressources dont vous avez besoin et seulement quand vous en avez besoin. Pour plus d’informations, consultez [Calcul et stockage](flexible-server/concepts-compute-storage.md).

Les serveurs flexibles sont idéalement adaptés pour ce qui suit :
- Facilité de déploiement, mise à l’échelle simplifiée et faible surcharge de gestion de base de données pour les fonctions telles que les sauvegardes, la haute disponibilité, la sécurité et le monitoring
- Développements d’applications nécessitant la version de la communauté de MySQL avec un meilleur contrôle et des personnalisations
- Charges de travail de production avec la haute disponibilité dans la même zone, redondante interzone et les fenêtres de maintenance managées
- Expérience de développement simplifiée 
- Sécurité de qualité professionnelle

Pour une présentation détaillée du mode de déploiement Serveur flexible, consultez [Vue d'ensemble du mode Serveur flexible](flexible-server/overview.md). Pour les dernières mises à jour apportées au serveur flexible, consultez [Nouveautés d’Azure Database pour MySQL - Serveur flexible](flexible-server/whats-new.md).

### <a name="azure-database-for-mysql---single-server"></a>Serveur unique Azure Database pour MySQL 

Azure Database pour MySQL - Serveur unique est un service de base de données entièrement géré et conçu pour une personnalisation minimale. La plateforme de serveur unique est conçue pour prendre en charge la plupart des fonctions de gestion de base de données, comme les correctifs, les sauvegardes, la haute disponibilité et la sécurité, avec un minimum de configuration et de contrôle de la part de l’utilisateur. L’architecture est optimisée à des fins de haute disponibilité intégrée avec une disponibilité de 99,99 % sur une seule zone de disponibilité. Elle prend en charge la version de la communauté de MySQL 5.6 (hors service), 5.7 et 8.0. Le service est aujourd'hui généralement disponible dans un grand nombre de [régions Azure](https://azure.microsoft.com/global-infrastructure/services/).

Les serveurs uniques ne sont parfaitement adaptés **que pour des applications existantes utilisant déjà un serveur unique**. Pour tous les nouveaux développements ou migrations, Serveur flexible est l’option de déploiement recommandée. Pour en savoir plus sur les différences entre les options de déploiement Serveur flexible et Serveur unique, consultez la documentation [expliquant comment choisir l’option de déploiement adaptée à vos besoins](select-right-deployment-type.md).

Pour une présentation détaillée du mode de déploiement Serveur unique, consultez [Vue d’ensemble du mode Serveur unique](single-server-overview.md). Pour les dernières mises à jour apportées au serveur flexible, consultez [Nouveautés d’Azure Database pour MySQL - Serveur flexible](single-server-whats-new.md).

## <a name="contacts"></a>Contacts
Pour toute question ou suggestion au sujet de l’utilisation d’Azure Database pour MySQL, envoyez un e-mail à l’équipe Azure Database pour MySQL ([@Ask Azure DB pour MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Cette adresse e-mail n’est pas un alias de support technique.

En outre, tenez compte des points de contact suivants le cas échéant :

- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les deux modes de déploiement pour Azure Database pour MySQL et choisir les options appropriées en fonction de vos besoins.

- [Serveur unique](single-server/index.yml)
- [Serveur flexible](flexible-server/index.yml)
- [Choisir l’option de déploiement MySQL appropriée pour votre charge de travail](select-right-deployment-type.md)
