---
title: Réplication des données entrantes - Azure Database pour MySQL Flexible
description: Découvrez l’utilisation de la réplication des données entrantes pour effectuer une synchronisation entre un serveur externe et le service Azure Database pour MySQL Flexible.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 5c0a63d140bb06bb14bb9c81dd17b0b057c3c8f4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422783"
---
# <a name="replicate-data-into-azure-database-for-mysql-flexible--server"></a>Répliquer des données dans un serveur flexible Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

La réplication des données entrantes vous permet de synchroniser les données d’un serveur MySQL externe avec le service Azure Database pour MySQL Flexible. Le serveur externe peut être hébergé localement, dans des machines virtuelles, Azure Database pour MySQL Single Server, ou il peut s'agir d'un service de base de données hébergé par d'autres fournisseurs de services cloud. La réplication des données est basée sur la position du fichier journal binaire (binlog). Pour en savoir plus sur la réplication binlog, consultez la [vue d’ensemble de la réplication binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

> [!Note]
> La réplication basée sur GTID n’est actuellement pas prise en charge pour les serveurs flexibles Azure Database pour MySQL.<br>
> La configuration de la réplication des données pour les serveurs à haute disponibilité redondante interzone n’est pas prise en charge.

## <a name="when-to-use-data-in-replication"></a>Quand utiliser la réplication des données entrantes

Voici les principaux scénarios à prendre en compte concernant l’utilisation de la réplication des données entrantes :

- **Synchronisation de données hybride :** avec la réplication des données entrantes, vous pouvez maintenir la synchronisation des données entre vos serveurs locaux et un serveur flexible Azure Database pour MySQL. Cette synchronisation est utile pour créer des applications hybrides. Cette méthode est intéressante si vous disposez d'un serveur de base de données local mais souhaitez déplacer les données vers une région proche des utilisateurs finaux.
- **Synchronisation de plusieurs cloud :** pour les solutions de cloud complexes, utilisez la réplication des données entrantes pour synchroniser les données entre un serveur flexible Azure Database pour MySQL et différents fournisseurs de cloud, y compris les machines virtuelles et les services de base de données hébergés dans ces clouds.
- **Migration :** les clients peuvent effectuer une migration minimale à l’aide d’outils open source, tels que [MyDumper/MyLoader](https://centminmod.com/mydumper.html), avec la réplication des données. Un basculement sélectif de la charge de production de la base de données source vers la base de données de destination est possible avec la réplication des données.

Pour les scénarios de migration, utilisez [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS).

## <a name="limitations-and-considerations"></a>Limitations et considérations

### <a name="data-not-replicated"></a>Données non répliquées

La [*base de données système mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) sur le serveur source n’est pas répliquée. De plus, les changements apportés aux comptes et aux autorisations sur le serveur source ne le sont pas non plus. Si vous créez un compte sur le serveur source et que ce compte a besoin d’accéder au serveur réplica, créez manuellement le même compte sur le serveur réplica. Pour une présentation des tables figurant dans la base de données système, consultez le [manuel MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="data-in-replication-not-supported-on-ha-enabled-servers"></a>Réplication des données non prise en charge sur les serveurs avec haute disponibilité
La configuration de la réplication des données pour les serveurs à haute disponibilité redondante interzone n’est pas prise en charge. Sur les serveurs où la haute disponibilité est activée, les procédures stockées pour la réplication `mysql.az_replication_*` ne sont pas disponibles.

### <a name="filtering"></a>Filtrage

La modification du paramètre `replicate_wild_ignore_table` qui a été utilisé pour créer le filtre de réplication pour les tables n’est pas prise en charge actuellement pour Azure Database pour MySQL - Serveur flexible.

### <a name="requirements"></a>Spécifications

- La version du serveur source doit être au moins MySQL version 5.7.
- Nous vous recommandons d’utiliser la même version de serveur source et de réplica. Par exemple, les deux doivent être MySQL version 5.7 ou MySQL version 8.0.
- Nous vous recommandons de disposer d’une clé primaire dans chaque table. Si nous disposons d’une table sans clé primaire, la réplication peut-être lenteur.
- Le serveur source doit utiliser le moteur MySQL InnoDB.
- L’utilisateur doit disposer des autorisations nécessaires pour configurer la journalisation binaire et créer de nouveaux utilisateurs sur le serveur source.
- Si SSL est activé sur le serveur source, vérifiez que le certificat d’autorité de certification SSL fourni pour le domaine a été inclus dans la procédure stockée `mysql.az_replication_change_master`. Consultez les [exemples](./how-to-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) suivants et le paramètre `master_ssl_ca`.
- Vérifiez que la machine qui héberge le serveur source autorise à la fois le trafic entrant et le trafic sortant sur le port 3306.
- Vérifiez que le serveur source dispose d’une **adresse IP publique**, que DNS est accessible publiquement ou que le serveur source comporte un nom de domaine complet (FQDN).
- En cas d’accès public, vérifiez que le serveur source dispose d’une adresse IP publique, que DNS est accessible publiquement, ou que le serveur source comporte un nom de domaine complet (FQDN).
- En cas d’accès privé, assurez-vous que le nom du serveur source peut être résolu et qu’il est accessible à partir du réseau virtuel où l’instance d’Azure Database pour MySQL est en cours d’exécution. Pour plus d’informations, consultez [Résolution de noms pour des ressources dans les réseaux virtuels Azure](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [configurer la réplication des données entrantes](how-to-data-in-replication.md)
- En savoir plus sur la [réplication dans Azure avec des réplicas en lecture](concepts-read-replicas.md)
