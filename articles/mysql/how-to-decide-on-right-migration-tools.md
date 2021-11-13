---
title: Sélectionner les outils appropriés en vue de la migration vers Azure Database pour MySQL
description: Cette rubrique fournit une table de décision qui aide les clients à choisir les bons outils pour migrer vers Azure Database pour MySQL.
ms.service: mysql
author: shriram-muthukrishnan
ms.author: shriramm
ms.reviewer: maghan
ms.topic: how-to
ms.custom: ''
ms.date: 10/12/2021
ms.openlocfilehash: 686874492f44df1171a320aaacf8e1967f8cb060
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270760"
---
# <a name="select-the-right-tools-for-migration-to-azure-database-for-mysql"></a>Sélectionner les outils appropriés en vue de la migration vers Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

## <a name="overview"></a>Vue d’ensemble

Les migrations sont des projets multiétapes qui sont difficiles à mener à bien. La migration de serveurs de base de données sur plusieurs plateformes implique plus qu’une simple migration de données et de schémas. En effet, elle nécessite le déplacement de bien d’autres composants, comme les paramètres de configuration du serveur et du réseau, les règles de contrôle d’accès, etc. Ceux-ci sont nécessaires pour garantir que le serveur de base de données dans la nouvelle plateforme cible fonctionne exactement comme la source. 

Pour obtenir des informations détaillées et des cas d’usage sur la migration de bases de données vers Azure Database pour MySQL, vous pouvez consulter le [guide de migration de bases de données](migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md). Ce document fournit des conseils qui vous aideront à planifier et à mener à bien une migration MySQL vers Azure. 

Les migrations peuvent généralement être classées en deux catégories : hors connexion et en ligne. 

- Dans le cas d’une migration hors connexion, le serveur source est mis hors connexion, puis une copie de sauvegarde et une restauration des bases de données sont effectuées sur le serveur cible. 

- Dans le cas d’une migration en ligne (migration avec un temps d’arrêt minime), le serveur source autorise les mises à jour. La solution de migration se charge alors de la réplication des modifications en cours entre le serveur source et le serveur cible, ainsi que des opérations initiales de copie de sauvegarde et de restauration sur la cible. 

Si votre application peut se permettre un certain temps d’arrêt, les migrations hors connexion sont toujours recommandées car elles sont simples et faciles à exécuter. En revanche, si votre application ne peut se permettre qu’un temps d’arrêt minimal, une migration en ligne est le meilleur choix. Les migrations de la majorité des systèmes OLTP (traitement des paiements, commerce électronique, etc.) appartiennent à cette catégorie. 

## <a name="decision-table"></a>Table de décision

Pour vous aider à sélectionner les bons outils pour migrer vers Azure Database pour MySQL, passez en revue le tableau suivant. 

| Scénarios | Outils recommandés | Liens |
|-------|------|------------|
| Migrations hors connexion pour déplacer des bases de données >= 1 To | Copie de sauvegarde et restauration avec **MyDumper/MyLoader** + machine virtuelle de calcul haute performance | [Migrer des bases de données volumineuses vers Azure Database pour MySQL avec mydumper/myloader](concepts-migrate-mydumper-myloader.md) <br><br> [Bonnes pratiques pour la migration de bases de données volumineuses vers Azure Database pour MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)|
| Migrations hors connexion pour déplacer des bases de données < 1 To  | Si la bande passante réseau entre la source et la cible est correcte (par exemple, route express haute vitesse), utilisez **Azure Database Migration Service** (DMS)  <br><br>   **OU** <br><br> Si la bande passante réseau est faible entre la source et Azure, utilisez **MyDumper/MyLoader + machine virtuelle de calcul haute performance** pour tirer parti des paramètres de compression et déplacer efficacement les données sur des réseaux à faible débit  <br><br> **OU** <br><br> Utilisez **mysqldump** et la fonctionnalité **Exporter/importer de MySQL Workbench** afin d’effectuer des migrations hors connexion pour des bases de données plus petites.  | [Tutoriel : Migrer MySQL vers Azure Database pour MySQL hors connexion avec DMS - Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-offline-portal.md)<br><br>  [Migrer Amazon RDS pour MySQL vers Azure Database pour MySQL à l’aide de MySQL Workbench](how-to-migrate-rds-mysql-workbench.md)<br><br>  [Importation et exportation - Azure Database pour MySQL](concepts-migrate-import-export.md)|
| Migration en ligne |  **Mydumper/Myloader avec réplication des données entrantes** <br><br> Vous pouvez envisager d’utiliser **Mysqldump avec réplication des données entrantes** pour de petites bases de données (moins de 100 Go).  Ces méthodes s’appliquent aux migrations externes et intra-plateformes. | [Configurer la réplication des données entrantes - Serveur flexible Azure Database pour MySQL](flexible-server/how-to-data-in-replication.md) <br><br> [Tutoriel : Migration d’une base de données Azure Database pour MySQL - Serveur unique vers Azure Database pour MySQL - Serveur flexible avec un temps d’arrêt minimal](howto-migrate-single-flexible-minimum-downtime.md) |
|Migrations d’un serveur unique vers un serveur flexible |  **Hors connexion** : script d’interpréteur de commandes personnalisé hébergé dans [GitHub](https://github.com/Azure/azure-mysql/tree/master/azuremysqltomysqlmigrate). Ce script déplace également d’autres composants du serveur, tels que les paramètres de sécurité et les configurations des paramètres du serveur. <br><br>**En ligne** : **Mydumper/Myloader avec réplication des données entrantes** |  [Migrer d’Azure Database pour MySQL - Serveur unique vers Serveur flexible en 5 étapes simples](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/migrate-from-azure-database-for-mysql-single-server-to-flexible/ba-p/2674057)<br><br>   [Tutoriel : Migration d’une base de données Azure Database pour MySQL - Serveur unique vers Azure Database pour MySQL - Serveur flexible avec un temps d’arrêt minimal](howto-migrate-single-flexible-minimum-downtime.md)| 

## <a name="next-steps"></a>Étapes suivantes
* [Migration de MySQL local vers Azure Database pour MySQL](migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md)

<br><br>
