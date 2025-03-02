---
title: Limitations – Azure Database pour MySQL – Serveur flexible
description: Cet article décrit les limitations dans Azure Database pour MySQL – Serveur flexible, telles que le nombre de connexions et les options du moteur de stockage.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 85718f5c9f3705d5530a2528c07a5a5c40a971c5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468382"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server"></a>Limitations dans le serveur flexible Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Cet article décrit les limitations existant dans le service Serveur flexible Azure Database pour MySQL. Les [limitations générales](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) du moteur de base de données MySQL sont également applicables. Si vous souhaitez en savoir plus sur les limites appliquées aux ressources (calcul, mémoire, stockage), consultez l’article sur [le calcul et le stockage](concepts-compute-storage.md).

## <a name="server-parameters"></a>Paramètres de serveur

> [!NOTE]
> Si vous recherchez des valeurs minimales/maximales pour des paramètres de serveur comme `max_connections` et `innodb_buffer_pool_size`, ces informations ont été déplacées vers l’article [Paramètres du serveur](./concepts-server-parameters.md) dans les concepts des paramètres du serveur.

Azure Database pour MySQL prend en charge le réglage des valeurs des paramètres de serveur. Les valeurs minimale et maximale de certains paramètres (par exemple, `max_connections`, `join_buffer_size`, `query_cache_size`) sont déterminées par le niveau de calcul et la taille de calcul du serveur. Pour plus d’informations sur ces limites, consultez [Paramètres du serveur](./concepts-server-parameters.md).

## <a name="storage-engines"></a>Moteurs de stockage

MySQL prend en charge de nombreux moteurs de stockage. Voici la liste des moteurs de stockage qui sont pris en charge et non pris en charge dans Azure Database pour MySQL - Serveur flexible :

### <a name="supported"></a>Prise en charge
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Non pris en charge
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Prise en charge des privilèges et de la manipulation des données

De nombreux paramètres de serveur peuvent dégrader de façon inattendue les performances du serveur, ou nier les propriétés ACID du serveur MySQL. Afin de préserver l’intégrité du service et le contrat SLA au niveau du produit, ce service n’expose pas plusieurs rôles.

Le service MySQL n’autorise pas l’accès direct au système de fichiers sous-jacent. Certaines commandes de manipulation de données ne sont pas prises en charge.

### <a name="unsupported"></a>Non pris en charge

Les éléments suivants ne sont pas pris en charge :
- Rôle d’administrateur de base de données : Restreint. Sinon, vous pouvez utiliser le rôle d’utilisateur Administrateur (généré lors de la création d’un serveur) qui vous permet d’exécuter la plupart des instructions DDL et DML.
- Privilège de superutilisateur : de la même façon, le [privilège de superutilisateur](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) est limité.
- DEFINER : requiert des privilèges de superutilisateur pour créer et est limité. Si vous importez des données à l'aide d'une sauvegarde, supprimez les commandes `CREATE DEFINER` manuellement ou à l'aide de la commande `--skip-definer` lors de l'exécution de mysqldump.
- Bases de données système : La [base de données système mysql](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) est en lecture seule ; elle est utilisée pour prendre en charge diverses fonctionnalités PaaS. Vous ne pouvez pas apporter de modifications à la base de données système `mysql`.
- `SELECT ... INTO OUTFILE`: Pas de prise en charge dans le service.

### <a name="supported"></a>Prise en charge
- `LOAD DATA INFILE` est prise en charge, mais le paramètre `[LOCAL]` doit être spécifié et dirigé vers un chemin d'accès UNC (stockage Azure monté via SMB). En outre, si vous utilisez une version du client MySQL >= 8.0, vous devez inclure le paramètre `-–local-infile=1` dans votre chaîne de connexion.

## <a name="functional-limitations"></a>Limitations fonctionnelles

### <a name="zone-redundant-ha"></a>Haute disponibilité redondante interzone
- Cette configuration ne peut être définie qu’au moment de la création du serveur.
- Pas de prise en charge dans le niveau de calcul expansible.

### <a name="networking"></a>Mise en réseau
- Une fois le serveur créé, il n’est pas possible de changer la méthode de connexion. Si le serveur est créé avec *Accès privé (intégration au réseau virtuel)* , il ne peut pas être remplacé par *Accès public (adresses IP autorisées)* après la création, et vice versa.

### <a name="stopstart-operation"></a>Opération d’arrêt/démarrage
- Pas de prise en charge avec les configurations de réplica en lecture (source et réplicas).

### <a name="scale-operations"></a>Opérations de mise à l’échelle
- La diminution du stockage du serveur provisionné n’est pas prise en charge.

### <a name="read-replicas"></a>Réplicas en lecture
- Pas de prise en charge avec les configurations à haute disponibilité redondante interzone (serveur principal et serveur de secours).

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge. Si vous souhaitez mettre à niveau vers la version principale, effectuez une [sauvegarde puis une restauration](../concepts-migrate-dump-restore.md) vers un serveur créé avec la nouvelle version du moteur.

### <a name="restoring-a-server"></a>Restauration d’un serveur
- Avec la restauration à un instant dans le passé, les nouveaux serveurs sont créés avec les mêmes configurations de calcul et de stockage que le serveur source sur lequel il est basé. Vous pouvez effectuer un scale-down du calcul du serveur nouvellement restauré après sa création.
- La restauration d’un serveur supprimé n’est pas prise en charge.

## <a name="features-available-in-single-server-but-not-yet-supported-in-flexible-server"></a>Fonctionnalités disponibles sur un serveur unique, mais pas encore prises en charge dans le serveur flexible
Toutes les fonctionnalités disponibles pour le serveur unique Azure Database pour MySQL sont encore disponibles dans le serveur flexible. Pour obtenir la liste complète des fonctionnalités pour un serveur unique et un serveur flexible, reportez-vous à [Choix de l’option de serveur MySQL appropriée dans la documentation Azure.](../select-right-deployment-type.md#comparing-the-mysql-deployment-options-in-azure)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Choix de l’option de serveur MySQL appropriée dans la documentation Azure](../select-right-deployment-type.md)
- Comprendre [ce qui est disponible pour les options de calcul et de stockage dans le serveur flexible](concepts-compute-storage.md)
- Découvrir les [versions MySQL prises en charge](concepts-supported-versions.md)
- Démarrage rapide : [Utiliser le Portail Azure pour créer un serveur flexible Azure Database pour MySQL](quickstart-create-server-portal.md)
