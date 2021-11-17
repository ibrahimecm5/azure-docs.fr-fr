---
title: Sauvegarder et restaurer dans un serveur flexible Azure Database pour MySQL
description: Découvrez les concepts de sauvegarde et de restauration avec un serveur flexible Azure Database pour MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 61002f3943001dd145cd30593b6972d743f24eec
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472794"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server"></a>Sauvegarder et restaurer dans un serveur flexible Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Un serveur flexible Azure Database pour MySQL crée automatiquement des sauvegardes de serveur et les stocke de manière sécurisée dans un stockage localement redondant au sein de la région. Les sauvegardes peuvent être utilisées pour restaurer votre serveur à un point dans le temps. La sauvegarde et la restauration sont une partie essentielle de toute stratégie de continuité d’activité, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles.

## <a name="backup-overview"></a>Présentation de la sauvegarde

Un serveur flexible prend des sauvegardes de capture instantanée des fichiers de données et les stocke dans un stockage localement redondant. Le serveur effectue également une sauvegarde des journaux des transactions qu’il stocka dans un stockage localement redondant. Celles-ci vous permettent de restaurer un serveur à n’importe quel point dans le temps au sein de votre période de rétention de sauvegarde configurée. La période de rétention de sauvegarde par défaut est de sept jours. Vous pouvez éventuellement configurer la sauvegarde de la base de données de 1 à 35 jours. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES 256 bits pour les données stockées au repos.

Vous ne pouvez pas exporter ces fichiers de sauvegarde. Les sauvegardes sont utilisables uniquement pour les opérations de restauration dans le serveur flexible. Vous pouvez également utiliser[mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) à partir d’un client MySQL pour copier une base de données.

## <a name="backup-frequency"></a>Fréquence de sauvegarde

Les sauvegardes sur des serveurs flexibles sont basées sur des captures instantanées. La première sauvegarde de captures instantanées est planifiée immédiatement après la création d’un serveur. Les sauvegardes de captures instantanées interviennent une fois par jour. Les sauvegardes des journaux des transactions se produisent toutes les cinq minutes.

## <a name="backup-redundancy-options"></a>Options de redondance de sauvegarde

Azure Database pour MySQL stocke plusieurs copies de vos sauvegardes afin que vos données soient protégées contre des événements planifiés ou non, notamment des défaillances matérielles temporaires, des pannes de réseau ou de courant, et des catastrophes naturelles majeures. Azure Database pour MySQL offre la possibilité de choisir entre le stockage de sauvegarde redondant localement, redondant interzone ou géoredondant dans les niveaux De base, Usage général et À mémoire optimisée. Par défaut, le stockage de sauvegarde de serveur Azure Database pour MySQL est redondant localement pour les serveurs avec une configuration haute disponibilité dans la même zone ou une configuration sans haute disponibilité, et redondant interzone pour les serveurs avec une configuration de haute disponibilité redondante interzone.

La redondance de sauvegarde garantit que votre base de données est conforme à ses objectifs de disponibilité et de durabilité même en cas de défaillance et Azure Database pour MySQL propose trois options aux utilisateurs : 

- **Stockage de sauvegarde redondant localement** : lorsque les sauvegardes sont stockées dans un stockage de sauvegarde redondant localement, plusieurs copies des sauvegardes sont stockées dans le même centre de données. Cette option protège vos données contre les défaillances de disque et de rack du serveur. Cela fournit également une durabilité d’au moins 99,999999999 % (11 9) des objets Sauvegardes sur une année donnée. Par défaut, le stockage de sauvegarde pour les serveurs avec une configuration haute disponibilité (HA) dans la même zone ou une configuration sans haute disponibilité est défini sur redondant localement.  

- **Stockage de sauvegarde redondant interzone** : lorsque les sauvegardes sont stockées dans un stockage de sauvegarde redondant interzone, les copies multiples sont non seulement stockées dans la zone de disponibilité où votre serveur est hébergé, mais elles sont également répliquées dans une autre zone de disponibilité de la même région. Cette option peut être utilisée pour les scénarios qui nécessitent une haute disponibilité ou pour limiter la réplication des données au sein d’un pays ou d’une région afin de répondre aux exigences en matière de résidence des données. Cela fournit également une durabilité d’au moins 99,9999999999 % (12 9) des objets Sauvegardes sur une année donnée. Vous pouvez sélectionner l’option Haute disponibilité redondante interzone au moment de la création du serveur pour garantir un stockage de sauvegarde redondant interzone. La haute disponibilité d’un serveur peut être désactivée après la création, mais le stockage de sauvegarde restera redondant interzone.  

- **Stockage de sauvegarde géoredondant** : lorsque les sauvegardes sont stockées dans un stockage de sauvegarde géoredondant, les copies multiples sont non seulement stockées dans la région où votre serveur est hébergé, mais sont également répliquées dans la région associée géographiquement. Cela permet de bénéficier d’une meilleure protection et de la possibilité de restaurer votre serveur dans une région différente en cas de sinistre. Cela fournit également une durabilité d’au moins 99,99999999999999 % (16 9) des objets Sauvegardes sur une année donnée. Vous pouvez activer l’option Géoredondance au moment de la création du serveur pour garantir un stockage de sauvegarde géoredondant. La géoredondance est prise en charge pour les serveurs hébergés dans l’une des [régions Azure appairées](../../best-practices-availability-paired-regions.md). 

> [!NOTE]
> La géoredondance et la haute disponibilité redondante interzone pour prendre en charge la redondance de zone sont actuellement exposées comme une opération au moment de la création uniquement.

## <a name="moving-from-other-backup-storage-options-to-geo-redundant-backup-storage"></a>Migration à partir d’autres options de stockage de sauvegarde vers un stockage de sauvegarde géoredondant 

La configuration du stockage géoredondant pour la sauvegarde est uniquement possible lors de la création du serveur. Une fois que le serveur est approvisionné, vous ne pouvez pas modifier l’option de redondance du stockage de sauvegarde. Toutefois, vous pouvez toujours déplacer votre stockage de sauvegarde existant vers un stockage géoredondant en utilisant les méthodes suggérées suivantes : 

- **Migration à partir d’un stockage de sauvegarde redondant localement vers un stockage de sauvegarde géoredondant** : pour déplacer votre stockage de sauvegarde à partir d’un stockage redondant localement vers un stockage géoredondant, vous pouvez effectuer une opération de restauration à un point dans le temps et modifier la configuration du serveur Calcul + Stockage afin d’activer la géoredondance pour le serveur source redondant localement. Les serveurs HA redondants interzone peuvent également être restaurés en tant que serveur géoredondant de la même façon que le stockage de sauvegarde sous-jacent est redondant localement. 

- **Migration à partir d’un stockage de sauvegarde redondant interzone vers un stockage de sauvegarde géoredondant** : Azure Database pour MySQL ne prend pas en charge la conversion de stockage redondant interzone en stockage géoredondant via la modification des paramètres Calcul + Stockage ou une opération de restauration à un point dans le temps. Pour déplacer votre stockage de sauvegarde d’un stockage redondant interzone vers un stockage géoredondant, la seule option prise en charge est la création d’un nouveau serveur et la migration des données à l’aide du [vidage et de la restauration](../concepts-migrate-dump-restore.md).


## <a name="backup-retention"></a>Rétention des sauvegardes

Les sauvegardes sont conservées en fonction du paramètre de période de rétention de sauvegarde sur le serveur. Vous pouvez sélectionner une période de rétention comprise entre 1 et 35 jours avec une période de rétention par défaut de sept jours. Vous pouvez définir la période de rétention lors de la création du serveur ou ultérieurement en mettant à jour la configuration de la sauvegarde à l’aide du portail Azure.

La période de rétention de sauvegarde détermine jusqu’à quelle date une opération de restauration à un instant dans le passé peut être effectuée, dans la mesure où elle est basée sur des sauvegardes disponibles. La période de rétention de sauvegarde peut également être traitée comme une fenêtre de récupération du point de vue de la restauration. Toutes les sauvegardes requises pour effectuer une restauration à un instant dans le passé au cours de la période de rétention de sauvegarde sont conservées dans le stockage de sauvegarde. Par exemple, si la période de conservation des sauvegardes est définie sur sept jours, la fenêtre de récupération est considérée comme les sept derniers jours. Dans ce scénario, toutes les sauvegardes nécessaires pour la restauration du serveur au cours des sept derniers jours sont conservées. Avec une fenêtre de rétention de sauvegarde de sept jours, les captures instantanées de base de données et les sauvegardes du journal des transactions sont stockées pour les huit derniers jours (1 jour avant la fenêtre).

## <a name="backup-storage-cost"></a>Coût du stockage de sauvegarde

Le serveur flexible fournit jusqu’à 100 % du stockage de votre serveur provisionné en stockage de sauvegarde sans coût supplémentaire. Tous les stockages de sauvegarde supplémentaires utilisés sont facturés en Go par mois. Par exemple, si vous avez configuré un serveur avec 250 Go de stockage, vous disposez de 250 Go de stockage pour les sauvegardes du serveur sans frais supplémentaires. Si l’utilisation quotidienne de la sauvegarde est de 25 Go, vous pouvez bénéficier jusqu’à 10 jours de stockage de sauvegarde gratuit. Le stockage utilisé pour les sauvegardes de plus de 250 Go est facturé conformément au [modèle de tarification](https://azure.microsoft.com/pricing/details/mysql/).

Vous pouvez utiliser la métrique [Stockage de sauvegarde utilisé](./concepts-monitoring.md) dans Azure MoniTor disponible dans le portail Azure pour surveiller le stockage de sauvegarde consommé par un serveur. La métrique utilisée **Stockage de sauvegarde** représente le total du stockage consommé par l’ensemble des sauvegardes de base de données et des sauvegardes de journaux qui sont conservées en fonction de la période de conservation des sauvegardes définie pour le serveur. Une activité transactionnelle importante sur le serveur peut entraîner une augmentation de l’utilisation du stockage de sauvegarde, quelle que soit la taille totale de la base de données. Le stockage de sauvegarde utilisé pour un serveur géoredondant est le double de celui d’un serveur redondant localement.

Le principal moyen de contrôler le coût du stockage de sauvegarde consiste à définir la période de rétention de sauvegarde appropriée. Vous pouvez sélectionner une période de rétention comprise entre 1 et  35 jours.

> [!IMPORTANT]
> Les sauvegardes à partir d’un serveur de base de données configuré dans une configuration de haute disponibilité redondante interzone se produisent à partir du serveur de base de données principal, car la surcharge est minime avec des sauvegardes de capture instantanée.

## <a name="view-available-full-backups"></a>Afficher les sauvegardes complètes disponibles

Le panneau Sauvegarde et restauration dans le portail Azure répertorie les sauvegardes complètes automatiques effectuées une fois par jour. Vous pouvez utiliser ce panneau pour afficher les horodateurs d’achèvement de toutes les sauvegardes complètes disponibles au cours de la période de rétention du serveur, et pour effectuer des opérations de restauration à l’aide de ces sauvegardes complètes. La liste des sauvegardes disponibles comprend toutes les sauvegardes automatisées complètes au cours de la période de rétention, un horodateur indiquant l’achèvement de l’opération, un horodateur indiquant la durée pendant laquelle une sauvegarde sera conservée et une action de restauration.

## <a name="restore"></a>Restaurer

Dans Azure Database pour MySQL, l’exécution d’une restauration crée un serveur à partir de sauvegardes du serveur d’origine. Deux types de restauration sont disponibles : 

- La restauration à un point dans le temps est disponible avec les deux options de redondance de la sauvegarde et elle crée un serveur dans la même région que votre serveur d’origine.
- La géorestauration est disponible seulement si vous avez configuré votre serveur pour le stockage géoredondant. Elle vous permet de restaurer votre serveur dans la région associée géographiquement. La géorestauration vers d’autres régions n’est pas prise en charge actuellement. 

La durée estimée pour la récupération du serveur dépend de plusieurs facteurs : 

- la taille des bases de données ; 
- le nombre de journaux d’activité de transactions impliqués ; 
- la quantité d’activité devant être relue pour effectuer une récupération au point de restauration ; 
- la bande passante du réseau, si la restauration s’effectue dans une autre région ; 
- le nombre de demandes de restauration simultanées en cours de traitement dans la région cible. 
- la présence d’une clé primaire dans les tables de la base de données. Pour accélérer la récupération, envisagez d’ajouter une clé primaire pour toutes les tables de votre base de données.  


> [!NOTE]
> Le serveur à haute disponibilité deviendra un serveur non HA (haute disponibilité désactivée) après la restauration pour les options Restauration à un point dans le temps et Géorestauration. 

## <a name="point-in-time-restore"></a>Restauration dans le temps

Dans un serveur flexible Azure Database pour MySQL, l’exécution d’une restauration à un instant dans le passé crée un serveur à partir des sauvegardes du serveur flexible dans la même région que votre serveur source. Elle est créée avec la configuration du serveur d’origine pour le niveau de tarification, la taille de stockage, la période de rétention de sauvegarde et l’option de redondance de sauvegarde. De plus, les étiquettes et les paramètres, par exemple, de réseau virtuel et de pare-feu, sont hérités du serveur source. Le niveau de calcul et de stockage du serveur restauré, la configuration et les paramètres de sécurité peuvent être modifiés une fois la restauration terminée.

> [!NOTE]
> Deux paramètres de serveur sont réinitialisés aux valeurs par défaut (et ne sont pas copiés à partir du serveur principal) après l’opération de restauration
> *   time_zone : valeur à définir pour la valeur DEFAULT SYSTEM
> *   event_scheduler : valeur définie sur OFF sur le serveur restauré

La restauration à un point dans le temps est utile dans plusieurs scénarios. Vois des cas d’utilisation courants :
-   Un utilisateur supprime accidentellement des données de la base de données
-   Un utilisateur supprime une table ou une base de données importantes
-   Une application d’utilisateur remplace accidentellement des données correctes par des données incorrectes en raison d’un défaut de l’application.

Vous pouvez choisir entre le dernier point de restauration, le point de restauration personnalisé et le point de restauration le plus rapide (restauration à l’aide d’une sauvegarde complète) via le [portail Azure](how-to-restore-server-portal.md).

-   **Dernier point de restauration** : l’option de dernier point de restauration vous permet de restaurer le serveur au timestamp une fois l’opération de restauration déclenchée. Cette option est utile pour restaurer rapidement le serveur à l’état le plus à jour.
-   **Point de restauration personnalisé** : cette option vous permet de choisir n’importe quel point dans le temps au cours de la période de rétention définie pour ce serveur flexible. Cette option est utile pour restaurer le serveur à un point précis dans le temps afin récupérer une erreur de l’utilisateur.
-   **Point de restauration le plus rapide** : cette option permet aux utilisateurs de restaurer le serveur le plus rapidement possible pour un jour donné de la période de rétention définie pour leur serveur flexible. La restauration la plus rapide est possible en choisissant le point de restauration dans le temps auquel la sauvegarde complète est accomplie. Cette opération de restauration restaure simplement la sauvegarde d’instantané complète, et ne garantit pas la restauration ou la récupération des journaux, ce qui la rend rapide. Pour une opération de restauration réussie, nous vous recommandons de sélectionner un horodatage de sauvegarde complète supérieur au point de restauration le plus ancien dans le temps.

La durée estimée de la récupération dépend de plusieurs facteurs, notamment les tailles des bases de données, la taille de sauvegarde du journal des transactions, la taille de calcul de la référence (SKU) et l’heure de la restauration. La récupération du journal des transactions est la partie la plus longue du processus de restauration. Si l’heure de restauration choisir est plus proche de la planification de sauvegarde de l’instantané, les opérations de restauration sont plus rapides, car l’application du journal des transactions est minimale. Pour estimer le temps de récupération précis de votre serveur, nous vous recommandons vivement de le tester dans votre environnement, car il contient trop de variables spécifiques de l’environnement.

> [!IMPORTANT]
> Si vous restaurez un serveur flexible configuré avec une haute disponibilité redondante interzone, le serveur restauré sera configuré dans la même région et la même zone que votre serveur primaire, et déployé en tant que serveur flexible unique en mode non haute disponibilité. Pour un serveur flexible, consultez [Haute disponibilité redondante interzone](concepts-high-availability.md).

> [!IMPORTANT]
> Il n’est **pas** possible de restaurer des serveurs supprimés. Si vous supprimez le serveur, toutes les bases de données qui appartiennent au serveur sont également supprimées, sans pouvoir être restaurées. À l'issue du déploiement, pour protéger les ressources du serveur d'une suppression accidentelle ou de changements inattendus, les administrateurs peuvent utiliser des [verrous de gestion](../../azure-resource-manager/management/lock-resources.md).

## <a name="geo-restore"></a>La géorestauration

Vous pouvez restaurer un serveur dans sa [région associée géographiquement](../../best-practices-availability-paired-regions.md) où le service est disponible si vous avez configuré votre serveur pour les sauvegardes géoredondantes. La géorestauration vers d’autres régions n’est pas prise en charge actuellement. 

La géorestauration constitue l’option de récupération par défaut lorsque votre serveur est indisponible en raison d’un incident dans la région où il est hébergé. Si un incident à grande échelle dans une région entraîne l’indisponibilité de votre application de base de données, vous pouvez restaurer un serveur à partir des sauvegardes géoredondantes sur un serveur situé dans n’importe quelle autre région. La géorestauration utilise la sauvegarde la plus récente du serveur. Il peut y avoir un délai entre le moment où une sauvegarde est effectuée et celui où elle est répliquée dans une autre région. Ce délai peut atteindre une heure. En cas d’incident, il peut donc y avoir jusqu’à une heure de pertes de données. 

Lors de la géorestauration, les configurations de serveur qui peuvent être modifiées incluent uniquement la configuration de la sécurité (règles de pare-feu et paramètres de réseau virtuel). La modification d’autres configurations de serveur comme le calcul, le stockage ou le niveau tarifaire (De base, Usage général ou À mémoire optimisée) lors de la géorestauration n’est pas prise en charge. 

Le délai estimé de récupération dépend de plusieurs facteurs, notamment du nombre total de bases de données à récupérer dans la même région au même moment, de la taille des bases de données, de la taille du journal des transactions et de la bande passante réseau. 

> [!NOTE]
> Si vous géorestaurez un serveur flexible configuré avec une haute disponibilité redondante interzone, le serveur restauré sera configuré dans la région associée géographiquement et la même zone que votre serveur principal, et déployé en tant que serveur flexible unique en mode non haute disponibilité. Pour un serveur flexible, consultez [Haute disponibilité redondante interzone](concepts-high-availability.md).

> [!IMPORTANT]
> Lorsque la région principale est hors service, il n’est pas possible de créer des serveurs géoredondants dans la région associée géographiquement, car le stockage ne peut pas être provisionné dans la région principale. Il faut attendre que la région principale soit en service pour provisionner des serveurs géoredondants dans la région associée géographiquement. Lorsque la région principale est hors service, vous pouvez toujours géorestaurer le serveur source vers la région associée géographiquement en désactivant l’option de géoredondance dans les paramètres Calcul + Stockage (Configurer le serveur) dans l’expérience du portail de restauration et restaurer en tant que serveur redondant localement pour garantir la continuité de l’activité.  

## <a name="perform-post-restore-tasks"></a>Effectuer des tâches de post-restauration

Après une restauration à l’aide de l’un des mécanismes de récupération **Dernier point de restauration** ou **Point de restauration personnalisé**, vous devez effectuer les tâches suivantes afin que vos utilisateurs et applications soient de nouveau opérationnels :

-   Si le nouveau serveur est destiné à remplacer le serveur d’origine, redirigez les clients et les applications clientes vers le nouveau serveur.
-   Vérifiez que les règles de pare-feu et de réseau virtuel appropriées au niveau serveur sont en place pour permettre aux utilisateurs de se connecter.
-   Assurez-vous que les connexions et les autorisations appropriées au niveau de la base de données sont en place.
-   Configurer les alertes, selon les besoins.

## <a name="frequently-asked-questions-faqs"></a>Forum Aux Questions (FAQ)

### <a name="backup-related-questions"></a>Questions relatives à la sauvegarde

- **Comment sauvegarder mon serveur ?**
Par défaut, Azure Database pour MySQL active les sauvegardes automatisées de l’ensemble de votre serveur (englobant toutes les bases de données créées) avec une période de rétention par défaut de 7 jours. La seule façon d’effectuer manuellement une sauvegarde consiste à utiliser les outils de la communauté tels que mysqldump, comme indiqué [ici](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) ou mydumper comme indiqué [ici](../concepts-migrate-mydumper-myloader.md#create-a-backup-using-mydumper). Si vous voulez sauvegarder Azure Database pour MySQL dans un stockage d’objets blob, reportez-vous à notre blog de la communauté technique [Backup Azure Database for MySQL to a Blob Storage](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/backup-azure-database-for-mysql-to-a-blob-storage/ba-p/803830).

- **Puis-je configurer les sauvegardes automatiques pour qu’elles soient conservées à long terme ?**
Non, actuellement, nous ne prenons en charge qu’un maximum de 35 jours de rétention de sauvegarde automatisée. Vous pouvez effectuer des sauvegardes manuelles et les utiliser pour les besoins de rétention à long terme.

- **Quelles sont les fenêtres de sauvegarde pour mon serveur ? Puis-je les personnaliser ?**
La première sauvegarde de captures instantanées est planifiée immédiatement après la création d’un serveur. Les sauvegardes de captures instantanées interviennent une fois par jour. Les sauvegardes des journaux des transactions se produisent toutes les cinq minutes. Les fenêtres de sauvegarde sont gérées directement par Azure et ne peuvent pas être personnalisées.

- **Mes sauvegardes sont-elles chiffrées ?**
Toutes les données et sauvegardes d’Azure Database pour MySQL ainsi que les fichiers temporaires créés pendant l’exécution de la requête sont chiffrés à l’aide du chiffrement AES 256 bits. Le chiffrement de stockage est toujours activé et ne peut pas être désactivé.

- **Puis-je restaurer une ou plusieurs bases de données ?**
La restauration d’une ou de plusieurs bases de données ou tables n’est pas prise en charge. Si vous souhaitez restaurer des bases de données spécifiques, effectuez une restauration à un point dans le temps, puis extrayez les tables ou bases de données nécessaires.

- **Mon serveur est-il disponible pendant la fenêtre de sauvegarde ?**
Oui. Les sauvegardes sont des opérations en ligne basées sur un instantané. L’opération de capture instantanée ne prend que quelques secondes et n’interfère pas avec les charges de travail de production, ce qui garantit la haute disponibilité du serveur.

- **Lors de la configuration de la fenêtre de maintenance du serveur, devons-nous prendre en compte la fenêtre de sauvegarde ?**
Non, les sauvegardes sont déclenchées en interne dans le cadre du service managé et n’ont pas incidence sur la fenêtre de maintenance gérée.

- **Où sont stockées mes sauvegardes automatisées et comment gérer leur conservation ?**
Azure Database pour MySQL crée automatiquement des sauvegardes de serveur et les conserve dans un stockage géoredondant ou localement redondant configuré par l’utilisateur. Ces fichiers de sauvegarde ne peuvent pas être exportés. La période de rétention de sauvegarde par défaut est de sept jours. Vous pouvez éventuellement configurer la sauvegarde de la base de données de 1 à 35 jours.

- **Comment vérifier mes sauvegardes ?**
Pour valider la disponibilité des sauvegardes terminées avec succès, la meilleure méthode consiste à afficher les sauvegardes automatisées complètes effectuées pendant la période de rétention dans le panneau Sauvegarde et restauration. Si une sauvegarde échoue, elle ne figure pas dans la liste des sauvegardes disponibles et notre service de sauvegarde réessaie toutes les 20 minutes d’effectuer une sauvegarde jusqu’à ce qu’une sauvegarde aboutisse. Ces échecs de sauvegarde sont dus à des charges de production transactionnelles lourdes sur le serveur.

- **Où puis-je voir l’espace utilisé par les sauvegardes ?**
Dans le portail Azure, sous l’onglet Analyse de la section Métriques, vous pouvez rechercher la métrique [Stockage de sauvegarde utilisé](./concepts-monitoring.md) pour vous aider à surveiller l’utilisation totale de la sauvegarde.

- **Qu’advient-il de mes sauvegardes si je supprime mon serveur ?**
Si vous supprimez le serveur, toutes les sauvegardes qui appartiennent au serveur sont également supprimées et ne peuvent pas être restaurées. À l'issue du déploiement, pour protéger les ressources du serveur d'une suppression accidentelle ou de changements inattendus, les administrateurs peuvent utiliser des [verrous de gestion](../../azure-resource-manager/management/lock-resources.md).

- **Comment suis-je facturé pour l’utilisation des sauvegardes ?**
Le serveur flexible fournit jusqu’à 100 % du stockage de votre serveur provisionné en stockage de sauvegarde sans coût supplémentaire. Tout stockage supplémentaires utilisé pour les sauvegardes est facturé par Go par mois, en fonction du [modèle de tarification](https://azure.microsoft.com/pricing/details/mysql/server/). La facturation du stockage de sauvegarde est également régie par la période de conservation sélectionnée et l’option de redondance des sauvegardes choisie, indépendamment de l’activité transactionnelle sur le serveur, qui a un impact direct sur le stockage de sauvegarde total utilisé.

- **Comment les sauvegardes sont-elles conservées pour les serveurs arrêtés ?**
Aucune nouvelle sauvegarde n’est effectuée pour les serveurs arrêtés. Toutes les sauvegardes plus anciennes (dans la fenêtre de conservation) au moment de l’arrêt du serveur sont conservées jusqu’à ce que le serveur soit redémarré, après quoi la conservation des sauvegardes pour le serveur actif est gouvernée par sa fenêtre de conservation des sauvegardes.

- **Comment suis-je facturé pour les sauvegardes pour un serveur arrêté ?**
Lorsque votre instance de serveur est arrêtée, vous êtes facturé pour le stockage approvisionné (y compris les IOPS approvisionnées) et le stockage de sauvegarde (sauvegardes stockées dans la fenêtre de rétention spécifiée). Le stockage de sauvegarde gratuit est limité à la taille de votre base de données approvisionnée et s’applique uniquement aux serveurs actifs.

### <a name="restore-related-questions"></a>Questions relatives à la restauration

- **Comment restaurer mon serveur ?**
Le portail Azure prend en charge la restauration à un instant dans le passé (pour tous les serveurs), permettant aux utilisateurs de restaurer le point de restauration le plus récent ou un point personnalisé. Pour restaurer manuellement votre serveur à partir des sauvegardes effectuées par mysqldump/myDumper, consultez [Restaurer votre base de données à l’aide de myLoader](../concepts-migrate-mydumper-myloader.md#restore-your-database-using-myloader).

- **Pourquoi ma restauration prend-elle autant de temps ?**
La durée estimée pour la récupération du serveur dépend de plusieurs facteurs :
   - La taille des bases de données. Dans le cadre du processus de récupération, la base de données doit être alimentée à partir de la dernière sauvegarde physique et, dès lors, le temps nécessaire à la récupération est proportionnel à la taille de la base de données.
   - La portion active de l’activité de transaction devant être relue pour effectuer une récupération. La récupération peut nécessiter plus de temps en fonction de l’activité de transaction supplémentaire issue du dernier point de contrôle réussi.
   - la bande passante du réseau, si la restauration s’effectue dans une autre région ;
   - le nombre de demandes de restauration simultanées en cours de traitement dans la région cible.
   - la présence d’une clé primaire dans les tables de la base de données. Pour accélérer la récupération, envisagez d’ajouter une clé primaire pour toutes les tables de votre base de données.


## <a name="next-steps"></a>Étapes suivantes

-   Découvrir la [continuité de l’activité](./concepts-business-continuity.md)
-   Découvrir la [haute disponibilité avec redondance interzone](./concepts-high-availability.md)
-   Découvrir [la sauvegarde et la récupération](./concepts-backup-restore.md)
