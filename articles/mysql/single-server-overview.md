---
title: Vue d'ensemble d'Azure Database pour MySQL - Serveur unique
description: Découvrez Azure Database pour MySQL - Serveur unique, un service de base de données relationnelle du cloud Microsoft qui repose sur MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 6/19/2021
ms.openlocfilehash: 4238ac653d6b34294d956ea9ff72fe2184ddd1be
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281643"
---
# <a name="azure-database-for-mysql-single-server"></a>Azure Database pour MySQL - Serveur unique

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Deux modes de déploiement sont disponibles pour [Azure Database pour MySQL](overview.md) optimisé par MySQL Community Edition :

- Serveur flexible 
- Serveur unique

Cet article contient une vue d’ensemble et une présentation des concepts de base du modèle de déploiement Serveur unique. Pour en savoir plus sur le mode de déploiement Serveur flexible, consultez [Vue d'ensemble du mode Serveur flexible](flexible-server/index.yml). Pour plus d’informations sur la façon de déterminer l’option de déploiement qui convient à votre charge de travail, consultez [choix de l’option de serveur MySQL appropriée dans Azure](select-right-deployment-type.md).

## <a name="overview"></a>Vue d’ensemble
Azure Database pour MySQL - Serveur unique est un service de base de données complètement managé conçu pour une personnalisation minimale. La plateforme de serveur unique est conçue pour prendre en charge la plupart des fonctions de gestion de base de données, comme les correctifs, les sauvegardes, la haute disponibilité et la sécurité, avec un minimum de configuration et de contrôle de la part de l’utilisateur. L’architecture est optimisée à des fins de haute disponibilité intégrée avec une disponibilité de 99,99 % sur une seule zone de disponibilité. Elle prend en charge la version de la communauté de MySQL 5.6 (hors service), 5.7 et 8.0. Le service est aujourd'hui généralement disponible dans un grand nombre de [régions Azure](https://azure.microsoft.com/global-infrastructure/services/).

Les serveurs uniques ne sont parfaitement adaptés **que pour des applications existantes utilisant déjà un serveur unique**. Pour tous les nouveaux développements ou migrations, Serveur flexible est l’option de déploiement recommandée. Pour en savoir plus sur les différences entre les options de déploiement Serveur flexible et Serveur unique, consultez la documentation [expliquant comment choisir l’option de déploiement adaptée à vos besoins](select-right-deployment-type.md).

## <a name="high-availability"></a>Haute disponibilité

Le modèle de déploiement Serveur unique est optimisé pour offrir une haute disponibilité intégrée et une élasticité à moindre coût. L'architecture distingue le calcul du stockage. Le moteur de base de données s'exécute sur un conteneur de calcul propriétaire, tandis que les fichiers de données résident sur le stockage Azure. Le stockage gère trois copies synchrones localement redondantes des fichiers de base de données, ce qui garantit la durabilité des données. 

Lors des événements de basculement, qu'ils soient planifiés ou non, si le serveur tombe en panne, le service maintient la haute disponibilité des serveurs grâce à la procédure automatisée suivante :

1. Un nouveau conteneur de calcul est approvisionné
2. Le stockage contenant les fichiers de données est mappé avec le nouveau conteneur 
3. Le moteur de base de données MySQL est mis en ligne sur le nouveau conteneur de calcul
4. Le service de passerelle assure un basculement transparent qui ne nécessite aucune modification du côté des applications. 
  
Le temps de basculement standard est compris entre 60 et 120 secondes. La conception native cloud du serveur unique lui permet de garantir 99,99 % de disponibilité, ce qui élimine les coûts engendrés par un serveur de secours passif.

Avec un temps de disponibilité de 99,99 %, l'excellent contrat de niveau de service (SLA) d'Azure s'appuie sur un réseau mondial de centres de données gérés par Microsoft et permet d'exécuter vos applications 24 heures sur 24, 7 jours sur 7.

:::image type="content" source="media/single-server-overview/1-singleserverarchitecture.png" alt-text="Diagramme conceptuel de l’architecture Azure Database pour MySQL - Serveur unique"::: 

## <a name="automated-patching"></a>Mise à jour corrective automatisée

Le service effectue une mise à jour corrective automatisée du matériel, du système d’exploitation et du moteur de base de données sous-jacents. Le correctif comprend les mises à jour de sécurité et de logiciel. Pour le moteur MySQL, la mise à niveau des versions mineures est automatique et incluse dans le cadre du cycle de mise à jour corrective. Aucune action de l’utilisateur ni aucun paramètre de configuration ne sont requis pour la mise à jour corrective. La fréquence des mises à jour correctives est gérée par le service en fonction du caractère critique de la charge utile. En général, le service suit un calendrier de publication mensuel dans le cadre de l’intégration et de la publication continues. Les utilisateurs peuvent s’abonner à la [notification de maintenance planifiée](concepts-monitoring.md) pour recevoir une notification de la maintenance à venir 72 heures avant l’événement.

## <a name="automatic-backups"></a>Sauvegardes automatiques

Un serveur unique crée automatiquement des sauvegardes de serveur et les conserve dans un stockage géoredondant ou redondant localement configuré par l’utilisateur. Les sauvegardes peuvent être utilisées pour restaurer votre serveur à n'importe quel point dans le temps au cours de la période de rétention des sauvegardes. La période de rétention de sauvegarde par défaut est de sept jours. La rétention peut être configurée sur une durée maximum de 35 jours. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES de 256 bits. Pour plus d'informations, consultez [Sauvegardes](concepts-backup.md).

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustez les performances et la mise à l’échelle en quelques secondes

Trois références SKU sont disponibles pour le serveur unique : De base, Usage général et À mémoire optimisée. Le niveau De base conviendra à un développement à faible coût et à des charges de travail à faible simultanéité. Les niveaux Usage général et À mémoire optimisée conviendront quant à eux aux charges de travail de production nécessitant une simultanéité et une mise à l'échelle de haut niveau, ainsi que des performances prévisibles. Vous pouvez créer votre première application sur une petite base de données pour un faible coût mensuel, puis adapter l’échelle aux besoins de votre solution. La mise à l'échelle du stockage s'effectue en ligne et prend en charge la croissance automatique du stockage. L’évolutivité dynamique permet de répondre en toute transparence à l’évolution rapide des besoins en ressources de votre base de données. Vous ne payez que pour les ressources que vous consommez. Pour plus d’informations, consultez [Niveaux tarifaires](./concepts-pricing-tiers.md).

## <a name="enterprise-grade-security-compliance-and-governance"></a>Sécurité, conformité et gouvernance de niveau entreprise

Le serveur unique utilise le module de chiffrement conforme à la norme FIPS 140-2 pour chiffrer le stockage des données au repos. Toutes les données sont chiffrées, y compris les sauvegardes et les fichiers temporaires créés lors de l'exécution des requêtes. Le service utilise le chiffrement AES 256 bits inclus dans le chiffrement du stockage Azure, et les clés peuvent être gérées par le système (par défaut) ou [par le client](concepts-data-encryption-mysql.md). Le service chiffre les données en mouvement avec les protocoles SSL/TLS appliqués par défaut. Le service prend en charge les versions 1.2, 1.1 et 1.0 du protocole TLS, avec la possibilité d'appliquer la [version minimum de TLS](concepts-ssl-connection-security.md). 

Le service autorise l’accès privé aux serveurs avec une [liaison privée](concepts-data-access-security-private-link.md) et offre une protection contre les menaces par le biais du plan facultatif [Microsoft Defender pour les bases de données relationnelles open source](../security-center/defender-for-databases-introduction.md). Microsoft Defender pour les bases de données relationnelles détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des bases de données.

Outre l’authentification native, le serveur unique prend en charge l’authentification [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). L'authentification Azure AD est un mécanisme de connexion aux serveurs MySQL utilisant des identités définies et gérées dans Azure AD. Avec l'authentification Azure AD, vous pouvez gérer les identités des utilisateurs de la base de données et d'autres services Azure à un emplacement central, ce qui simplifie et centralise le contrôle d'accès.

La fonctionnalité d'[enregistrement d'audit](concepts-audit-logs.md) est disponible pour suivre toute l'activité au niveau de la base de données. 

Le serveur unique est conforme aux principales certifications du secteur, telles que FedRAMP, HIPAA, PCI DSS. Visitez le [Centre de confidentialité Azure](https://www.microsoft.com/trustcenter/security) pour plus d’informations sur la sécurité de la plateforme Azure.

Pour plus d’informations sur les fonctionnalités de sécurité d’Azure Database pour MySQL, consultez la [vue d’ensemble de la sécurité](concepts-security.md).

## <a name="monitoring-and-alerting"></a>Surveillance et alerte

Le serveur unique est équipé de fonctionnalités intégrées d’analyse des performances et d’alerte. Toutes les métriques Azure présentent une fréquence d’une minute et chaque métrique fournit 30 jours d’historique. Vous pouvez configurer des alertes basées sur les métriques. Le service permet de configurer des journaux de requêtes lentes et est fourni avec une fonctionnalité [Magasin de requêtes](concepts-query-store.md) distincte. Le Magasin des requêtes simplifie la résolution des problèmes de performances en vous aidant à identifier rapidement les requête dont l’exécution est la plus longue et qui consomment le plus de ressources. Grâce à ces outils, vous pouvez rapidement optimiser vos charges de travail et configurer votre serveur pour bénéficier de performances optimales. Pour plus d'informations, consultez [Analyse](concepts-monitoring.md).

## <a name="migration"></a>Migration

Le service exécute la version de la communauté de MySQL. Cela permet une compatibilité totale des applications et requiert un coût de refactorisation minimal pour migrer une application existante développée sur le moteur MySQL vers un serveur unique. La migration vers le serveur unique peut être effectuée à l'aide de l'une des options suivantes :

- **Image mémoire et restauration** : pour les migrations hors connexion, au cours desquelles les utilisateurs peuvent se permettre des temps d'arrêt, l'image mémoire et la restauration à l'aide d'outils communautaires tels que mysqldump/mydumper peuvent constituer la méthode de migration la plus rapide. Pour plus d'informations, consultez [Migrer à l'aide de l'image mémoire et de la restauration](concepts-migrate-dump-restore.md). 
- **Azure Database Migration Service** : pour les migrations transparentes et simplifiées hors ligne vers un serveur unique avec une migration de données haute vitesse, [Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-offline-portal.md) peut être utilisé. 
- **Réplication des données entrantes** : pour les migrations avec temps d'arrêt minimal, la réplication des données entrantes, qui repose sur la réplication binlog, peut également être utilisée. Pour les migrations avec temps d'arrêt minimal, la réplication des données entrantes est préférée par les experts qui, en pratique, cherchent à mieux contrôler la migration. Pour plus d'informations, consultez [Réplication des données entrantes](concepts-data-in-replication.md).

## <a name="contacts"></a>Contacts

Pour toute question ou suggestion au sujet de l’utilisation d’Azure Database pour MySQL, envoyez un e-mail à l’équipe Azure Database pour MySQL ([@Ask Azure DB pour MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Cette adresse e-mail n’est pas un alias du support technique.

En outre, tenez compte des points de contact suivants le cas échéant :

- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez lu l'introduction au mode de déploiement Azure Database pour MySQL - Serveur unique, vous êtes prêt à :

- Créer votre premier serveur
  - [Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
  - [Création d’un serveur de base de données Azure pour MySQL à l’aide d’Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)
  - [Exemples de CLI Azure pour Azure Database pour MySQL](sample-scripts-azure-cli.md)

- Générez votre première application à l’aide de votre langage préféré :
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Go](./connect-go.md)
