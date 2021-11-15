---
title: Limites - Azure Database pour PostgreSQL - Serveur flexible
description: Cet article décrit les limites dans Azure Database pour PostgreSQL - Serveur flexible, telles que le nombre de connexions et les options du moteur de stockage.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: c28e3b84251c645ff690098950066b3bb653f3df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467527"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Limites dans Azure Database pour PostgreSQL - Serveur flexible


Les sections suivantes décrivent les limites fonctionnelles et les limites de capacités du service de base de données. Si vous souhaitez en savoir plus sur les niveaux de ressources (calcul, mémoire, stockage), consultez l’article sur [le calcul et le stockage](concepts-compute-storage.md).

## <a name="maximum-connections"></a>Nombre maximal de connexions

Le nombre maximal de connexions par niveau tarifaire et de vCores est le suivant : Le système Azure a besoin de trois connexions pour superviser Azure Database pour PostgreSQL - Serveur flexible.

| Nom de la référence SKU             | vCores | Taille de la mémoire | Nombre maximal de connexions | Nombre maximal de connexions utilisateur |
|----------------------|--------|-------------|-----------------|----------------------|
| **Expansible**        |        |             |                 |                      |
| B1ms                 | 1      | 2 Gio       | 50              | 47                   |
| B2s                  | 2      | 4 Gio       | 100             | 97                   |
| **Usage général**  |        |             |                 |                      |
| D2s_v3/D2ds_v4    | 2      | 8 Gio       | 859             | 856                  |
| D4s_v3/D4ds_v4    | 4      | 16 Gio      | 1719            | 1716                 |
| D8s_v3/D8ds_V4    | 8      | 32 Gio      | 3438            | 3435                 |
| D16s_v3/D16ds_v4   | 16     | 64 Gio      | 5 000            | 4997                 |
| D32s_v3/D32ds_v4   | 32     | 128 Go     | 5 000            | 4997                 |
| D48s_v3/D48ds_v4   | 48     | 192 Gio     | 5 000            | 4997                 |
| D64s_v3/D64ds_v4   | 64     | 256 Gio     | 5 000            | 4997                 |
| **Mémoire optimisée** |        |             |                 |                      |
| E2s_v3  / E2ds_v4    | 2      | 16 Gio      | 1719            | 1716                 |
| E4s_v3  / E4ds_v4    | 4      | 32 Gio      | 3438            | 3433                 |
| E8s_v3/E8ds_v4    | 8      | 64 Gio      | 5 000            | 4997                 |
| E16s_v3/E16ds_v4   | 16     | 128 Go     | 5 000            | 4997                 |
| E20ds_v4             | 20     | 160 Gio     | 5 000            | 4997                 |
| E32s_v3/E32ds_v4   | 32     | 256 Gio     | 5 000            | 4997                 |
| E48s_v3/E48ds_v4   | 48     | 384 Gio     | 5 000            | 4997                 |
| E64s_v3/E64ds_v4   | 64     | 432 Gio     | 5 000            | 4997                 |

Lorsque la limite du nombre de connexions est dépassée, vous pouvez recevoir l’erreur suivante :
> FATAL: sorry, too many clients already.

> [!IMPORTANT]
> Pour une expérience optimale, nous vous recommandons d’utiliser un gestionnaire de regroupement de connexions comme PgBouncer pour gérer efficacement les connexions. Azure Database pour PostgreSQL - Serveur flexible offre une [solution de regroupement de connexions intégrée](concepts-pgbouncer.md) à PgBouncer. 

Une connexion PostgreSQL, même inactive, peut utiliser environ 10 Mo de mémoire. De plus, la création de nouvelles connexions prend du temps. La plupart des applications requièrent de nombreuses connexions à courte durée, ce qui aggrave la situation. Par conséquent, il y a moins de ressources disponibles pour votre charge de travail réelle; ce qui entraîne une diminution des performances. Le regroupement de connexions peut être utilisé afin de réduire le nombre de connexions inactives et de réutiliser les connexions existantes. Pour en savoir plus, consultez notre [billet de blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Limitations fonctionnelles

### <a name="scale-operations"></a>Opérations de mise à l’échelle

- La mise à l’échelle du stockage du serveur nécessite un redémarrage du serveur.
- Le stockage du serveur ne peut être mis à l’échelle que par incréments de 2x. Pour plus d’informations, consultez [Calcul et stockage](concepts-compute-storage.md).
- La diminution de la taille de stockage du serveur n’est pas prise en charge pour le moment.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur

- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment. Si vous souhaitez mettre à niveau vers la version principale suivante, effectuez une [sauvegarde et une restauration](../howto-migrate-using-dump-and-restore.md) vers un serveur créé avec la nouvelle version du moteur.

### <a name="storage"></a>Stockage

- Une fois configurée, la taille de stockage ne peut pas être réduite. Vous devez créer un serveur offrant la taille de stockage souhaitée, effectuer une opération manuelle de [vidage et restauration](../howto-migrate-using-dump-and-restore.md), puis migrer vos bases de données vers le nouveau serveur.
- La fonctionnalité de croissance automatique du stockage n’est pas disponible. Supervisez l’utilisation et augmentez la taille du stockage. 
- Quand l’utilisation du stockage atteint 95 % ou quand la capacité disponible est inférieure à 5 Gio, le serveur passe automatiquement en **mode Lecture seule** pour éviter les erreurs associées à la saturation des disques. 
- Nous vous recommandons de définir des règles d’alerte pour `storage used` ou `storage percent` lorsque ceux-ci dépassent certains seuils, afin que vous puissiez agir de manière proactive, par exemple en augmentant la taille de stockage. Par exemple, vous pouvez définir une alerte si le pourcentage de stockage dépasse 80 % d’utilisation.
  
### <a name="networking"></a>Mise en réseau

- Le déplacement sur et hors du réseau virtuel n’est pas pris en charge actuellement.
- La combinaison de l’accès public avec un déploiement au sein d’un réseau virtuel n’est pas prise en charge actuellement.
- Les règles de pare-feu ne sont pas prises en charge sur le réseau virtuel. Des groupes de sécurité réseau peuvent être utilisés à la place.
- Les serveurs de base de données à accès public peuvent se connecter à l’Internet public, par exemple par le biais de `postgres_fdw`, et cet accès ne peut pas être restreint. Les serveurs basés sur des réseaux virtuels peuvent disposer d’un accès sortant limité à l’aide de groupes de sécurité réseau.

### <a name="high-availability-ha"></a>Haute disponibilité (HA)

- Consultez la page [Documentation sur les limitations de haute disponibilité redondantes dans une zone](concepts-high-availability.md#zone-redundant-high-availability---limitations).

### <a name="availability-zones"></a>Zones de disponibilité

- Le déplacement manuel de serveurs vers une zone de disponibilité différente n’est pas pris en charge pour le moment.
- La zone de disponibilité du serveur de secours HA ne peut pas être configurée manuellement.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Moteur Postgres, extensions et PgBouncer

- Postgres 10 et les versions antérieures ne sont pas prises en charge. Nous vous recommandons d’utiliser l’option [Serveur unique](../overview-single-server.md) si vous avez besoin d’anciennes versions de Postgres.
- La prise en charge des extensions est actuellement limitée aux extensions `contrib` Postgres.
- Le regroupement de connexions PgBouncer intégré n’est pas disponible actuellement pour les serveurs burstables.
- L’authentification SCRAM n’est pas prise en charge avec la connectivité utilisant PgBouncer intégré.

### <a name="stopstart-operation"></a>Opération d’arrêt/démarrage

- Le serveur ne peut pas être arrêté pendant plus de sept jours.

### <a name="scheduled-maintenance"></a>Maintenance planifiée

- Le changement de la fenêtre de maintenance moins de cinq jours avant une mise à niveau déjà planifiée n’affecte pas cette mise à niveau. Les changements prennent effet uniquement lors de la maintenance planifiée suivante.

### <a name="backing-up-a-server"></a>Sauvegarde d’un serveur

- Les sauvegardes sont gérées par le système ; il n’existe actuellement aucun moyen d’exécuter ces sauvegardes manuellement. Nous vous recommandons d’utiliser `pg_dump` à la place.
- Les sauvegardes sont toujours des sauvegardes complètes basées sur des instantanés (pas des sauvegardes différentielles), ce qui peut entraîner une augmentation de l’utilisation du stockage de sauvegarde. Notez que les journaux des transactions (journaux WAL) sont distincts des sauvegardes complètes/différentielles et sont archivés en permanence.

### <a name="restoring-a-server"></a>Restauration d’un serveur

- Lors de l’utilisation de la fonctionnalité de restauration à un instant dans le passé, le nouveau serveur est créé avec les mêmes configurations de calcul et de stockage que le serveur sur lequel il est basé.
- Les serveurs de bases de données basés sur le réseau virtuel sont restaurés sur le même réseau virtuel lorsque vous effectuez une restauration à partir d’une sauvegarde.
- Le nouveau serveur créé lors d’une restauration ne dispose pas des règles de pare-feu qui existaient sur le serveur d’origine. Des règles de pare-feu doivent être créées séparément pour le nouveau serveur.
- La restauration d’un serveur supprimé n’est pas prise en charge.
- La restauration inter-région n’est pas prise en charge.

### <a name="other-features"></a>Autres fonctionnalités

* L’authentification Azure AD n’est pas encore prise en charge. Nous vous recommandons d’utiliser l’option [Serveur unique](../overview-single-server.md) si vous avez besoin de l’authentification Azure AD.
* Les réplicas en lecture ne sont pas encore pris en charge. Nous vous recommandons d’utiliser l’option [Serveur unique](../overview-single-server.md) si vous avez besoin de réplicas en lecture.
* Le déplacement de ressources dans un autre abonnement n’est pas pris en charge. 


## <a name="next-steps"></a>Étapes suivantes

- Comprendre [ce qui est disponible pour les options de calcul et de stockage](concepts-compute-storage.md)
- Découvrir [les versions prises en charge de la base de données PostgreSQL](concepts-supported-versions.md)
- Consulter le [guide pratique : sauvegarder et restaurer un serveur dans Azure Database pour PostgreSQL à l’aide du portail Azure](how-to-restore-server-portal.md)
