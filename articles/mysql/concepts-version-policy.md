---
title: Stratégie de prise en charge des versions – Azure Database pour MySQL - Serveur unique et Serveur flexible (préversion)
description: Décrit la stratégie relative aux versions principales et mineures de MySQL prises en charge dans Azure Database pour MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 11/03/2020
ms.openlocfilehash: 248234c816cc3341929417282521d30c2105c671
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323250"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Stratégie de prise en charge des versions Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Cette page décrit la stratégie de contrôle de version d’Azure Database pour MySQL et s’applique aux modes de déploiement Azure Database pour MySQL - Serveur unique et Azure Database pour MySQL - Serveur flexible (préversion).

## <a name="supported-mysql-versions"></a>Versions de MySQL prises en charge

Azure Database pour MySQL a été développé à partir de [MySQL Community Edition](https://www.mysql.com/products/community/), avec le moteur de stockage InnoDB. Le service prend en charge toutes les versions majeures actuelles prises en charge par la communauté, à savoir MySQL 5.6, 5.7 et 8.0. MySQL utilise le schéma de nommage X.Y.Z, où X est la version majeure, Y la version mineure et Z la version du correctif de bogue. Pour plus d’informations sur ce schéma, reportez-vous à la [documentation MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

Azure Database pour MySQL prend actuellement en charge les versions majeures et mineures suivantes de MySQL :

| Version | [Serveur unique](overview.md) <br/> Version mineure actuelle |[Serveur flexible](./flexible-server/overview.md) <br/> Version mineure actuelle  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL version 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (Mise hors service) | Non pris en charge|
|MySQL version 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL Version 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> Dans l’option de déploiement Serveur unique, une passerelle est utilisée pour rediriger les connexions vers des instances de serveur. Une fois la connexion établie, le client de MySQL affiche la version de MySQL définie dans la passerelle, et non la version en cours d’exécution sur votre instance de serveur MySQL. Pour déterminer la version de votre instance de serveur MySQL, utilisez la commande `SELECT VERSION();` à l’invite de MySQL. Si votre application doit se connecter à une version principale spécifique, par exemple v5.7 ou v8.0, vous pouvez le faire en modifiant le port dans la chaîne de connexion de votre serveur, comme expliqué dans notre documentation [ici.](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version)

> [!IMPORTANT]
> MySQL v5.6 est mis hors service sur un serveur unique à partir de février 2021. À compter du 1er septembre 2021, vous ne pourrez pas créer de nouveaux serveurs v5.6 sur l’option de déploiement Azure Database pour MySQL - Serveur unique. Toutefois, vous pouvez effectuer des récupérations à un instant dans le passé et créer des réplicas de lecture pour vos serveurs existants.

Découvrez la stratégie de prise en charge des versions pour les versions mises hors service dans la [documentation relative à la stratégie de prise en charge des versions](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql).

## <a name="major-version-support"></a>Prise en charge de la version principale

Chaque version principale de MySQL est prise en charge par Azure Database pour MySQL à partir de la date du début de la prise en charge de la version par Azure et jusqu’à ce que la version soit supprimée par la communauté MySQL, comme indiqué dans la [stratégie de contrôle de version](https://www.mysql.com/support/eol-notice.html).

## <a name="minor-version-support"></a>Prise en charge de la version mineure

Azure Database pour MySQL effectue automatiquement des mises à niveau de versions mineures vers la version MySQL préférée d’Azure dans le cadre d’une maintenance périodique. 

## <a name="major-version-retirement-policy"></a>Stratégie de retrait de la version majeure

Le tableau ci-dessous fournit les détails relatifs au retrait des versions principales de MySQL. Les dates suivent la [stratégie de contrôle de version de MySQL](https://www.mysql.com/support/eol-notice.html).

| Version | Nouveautés | Date de début de la prise en charge d’Azure | Date de mise hors service|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Caractéristiques](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 mars 2018 | Février 2021
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Caractéristiques](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 mars 2018 | Octobre 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Fonctionnalités](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 décembre 2019 | Avril 2026

## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Versions de moteur MySQL supprimées non prises en charge dans Azure Database pour MySQL

Après la date de suppression pour chaque version de base de données MySQL, si vous continuez d’exécuter la version supprimée, tenez compte des restrictions suivantes :

- Dans la mesure où la communauté ne publie plus de correctifs de bogues ou de correctifs de sécurité, Azure Database pour MySQL n’effectue pas de mise à jour corrective sur le moteur de base de données supprimé pour la résolution de bogues ou de problèmes de sécurité. Il n’y aura pas non plus de mesures de sécurité concernant le moteur de base de données supprimé. Toutefois, Azure continuera à effectuer des opérations de maintenance et de mise à jour périodiques pour l’hôte, le système d’exploitation, les conteneurs et tout autre composant lié aux services.
- Si un problème de prise en charge rencontré est lié à la base de données MySQL, il est possible que nous ne puissions pas vous venir en aide. Dans ce cas, vous devrez mettre à niveau votre base de données afin que nous puissions intervenir.
- Vous ne serez plus en mesure de créer des serveurs de base de données pour la version supprimée. Toutefois, vous pouvez effectuer des récupérations à un instant dans le passé et créer des réplicas de lecture pour vos serveurs existants.
- Les nouvelles fonctionnalités de service développées par Azure Database pour MySQL peuvent uniquement être disponibles pour les versions de serveur de base de données prises en charge.
- Les contrats de niveau de service de durée de fonctionnement s’appliquent uniquement aux problèmes liés au service Azure Database pour MySQL et non aux temps d’arrêt causés par des bogues liés au moteur de base de données.  
- En cas de menace sérieuse pour le service provoquée par la vulnérabilité du moteur de base de données MySQL identifiée dans la version de la base de données supprimée, Azure peut décider d’arrêter votre serveur de base de données pour sécuriser le service. Il vous sera demandé de procéder à la mise à niveau du serveur avant sa mise en ligne. Pendant le processus de mise à niveau, vos données sont toujours protégées à l’aide de sauvegardes automatiques effectuées sur le service, qui peuvent être utilisées pour restaurer la version antérieure si vous le souhaitez. 

## <a name="next-steps"></a>Étapes suivantes

- Consultez Azure Database pour MySQL - Serveur unique [versions prises en charge](./concepts-supported-versions.md)
- Consultez Azure DB pour MySQL : [versions prises en charges](flexible-server/concepts-supported-versions.md) des serveurs flexibles
- Consultez [Vidage et restauration](./concepts-migrate-dump-restore.md) MySQL pour effectuer des mises à niveau.