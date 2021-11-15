---
title: Versions prises en charge – Azure Database pour MySQL
description: Découvrez les versions du serveur MySQL qui sont prises en charge dans le service Azure Database pour MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.custom: ''
ms.date: 11/4/2021
ms.openlocfilehash: 466b3706c2814f4cb833e441c7d19f5d7b2895fe
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845890"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versions prises en charge du serveur de base de données Azure pour MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database pour MySQL a été développé à partir de [MySQL Community Edition](https://www.mysql.com/products/community/), avec le moteur de stockage InnoDB. Le service prend en charge toutes les versions majeures actuelles prises en charge par la communauté, à savoir MySQL 5.7 et 8.0. MySQL utilise le schéma de nommage X.Y.Z, où X est la version majeure, Y la version mineure et Z la version du correctif de bogue. Pour plus d’informations sur ce schéma, reportez-vous à la [documentation MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Se connecter à un nœud de passerelle qui exécute une version spécifique de MySQL

Dans l’option de déploiement Serveur unique, une passerelle est utilisée pour rediriger les connexions vers des instances de serveur. Une fois la connexion établie, le client de MySQL affiche la version de MySQL définie dans la passerelle, et non la version en cours d’exécution sur votre instance de serveur MySQL. Pour déterminer la version de votre instance de serveur MySQL, utilisez la commande `SELECT VERSION();` à l’invite de MySQL. Consultez [Architecture de connectivité](./concepts-connectivity-architecture.md#connectivity-architecture) pour en savoir plus sur les passerelles dans l’architecture de service Azure Database pour MySQL.

Comme Azure Database pour MySQL prend en charge la version principale v5.7 et v8.0, le port par défaut 3306 pour la connexion à Azure Database pour MySQL exécute la version client MySQL 5.6 (le plus petit dénominateur commun) pour prendre en charge les connexions aux serveurs des 2 versions principales prises en charge. Toutefois, si votre application doit se connecter à une version principale spécifique, par exemple v5.7 ou v8.0, vous pouvez le faire en modifiant le port dans la chaîne de connexion de votre serveur.

Dans le service Azure Database pour MySQL, les nœuds de passerelle écoutent le port 3308 pour les clients v5.7 et le port 3309 pour les clients v8.0. En d’autres termes, si vous souhaitez vous connecter au client de passerelle v5.7, vous devez utiliser le nom de serveur complet et le port 3308 pour vous connecter à votre serveur à partir de l’application cliente. De même, si vous souhaitez vous connecter au client de passerelle v8.0, vous pouvez utiliser le nom de serveur complet et le port 3309 pour vous connecter à votre serveur. Pour plus de clarté, consultez l’exemple suivant.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Exemple de connexion via différentes versions de MySQL de passerelle":::

> [!NOTE]
> La connexion à Azure Database pour MySQL via les ports 3308 et 3309 est uniquement prise en charge pour la connectivité publique. Les points de terminaison de service de réseau virtuel et d’Azure Private Link ne peuvent être utilisés qu’avec le port 3306.

## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database pour MySQL prend actuellement en charge les versions majeures et mineures suivantes de MySQL :

| Version | [Serveur unique](overview.md) <br/> Version mineure actuelle |[Serveur flexible (préversion)](./flexible-server/overview.md) <br/> Version mineure actuelle  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL version 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (Mise hors service) | Non pris en charge|
|MySQL version 5.7 | [5.7.32](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-32.html) | [5.7.32](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-32.html)|
|MySQL Version 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Découvrez la stratégie de prise en charge des versions pour les versions mises hors service dans la [documentation relative à la stratégie de prise en charge des versions](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql).

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau

Le service gère automatiquement les correctifs pour les mises à jour des versions des correctifs de bogues. Par exemple, 5.7.20 à 5.7.21.  

La mise à niveau des versions principales est actuellement prise en charge par le service pour les mises à niveau de MySQL v5.6 à v5.7. Pour plus d’informations, consultez [comment effectuer des mises à niveau des versions principales](how-to-major-version-upgrade.md). Si vous voulez effectuer une mise à niveau de la version 5.7 vers la version 8.0, nous vous recommandons d’effectuer [le vidage et la restauration](./concepts-migrate-dump-restore.md) sur un serveur créé avec la nouvelle version du moteur.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la stratégie de version d’Azure Database pour MySQL, consultez [ce document](concepts-version-policy.md).
- Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](./concepts-pricing-tiers.md).
