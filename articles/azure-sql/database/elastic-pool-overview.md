---
title: Gérer plusieurs bases de données avec des pools élastiques
description: Gérez et mettez à l’échelle plusieurs bases de données dans Azure SQL Database (des centaines et des milliers) avec des pools élastiques. Un prix unique pour des ressources que vous pouvez distribuer là où vous en avez besoin.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: arvindshmicrosoft
ms.author: arvindsh
ms.reviewer: mathoma
ms.date: 06/23/2021
ms.openlocfilehash: 669b8610cba44be369ba805834eca2f700d211e0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548878"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données dans Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Les pools élastiques Azure SQL Database représentent une solution simple et rentable de gestion et de mise à l’échelle de plusieurs bases de données qui ont des demandes d’utilisation variables et imprévisibles. Les bases de données d’un pool élastique se trouvent sur un seul serveur et partagent un nombre défini de ressources à prix fixe. Les pools élastiques dans Azure SQL Database permettent aux développeurs SaaS d’optimiser le rapport performance/prix pour un groupe de bases de données dans un budget prescrit tout en offrant une élasticité des performances pour chaque base de données.

> [!div class="nextstepaction"]
> [Enquête pour améliorer Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="what-are-sql-elastic-pools"></a>Présentation des pools élastiques SQL

Les développeurs SaaS créent des applications qui reposent sur des couches de données à grande échelle composées de plusieurs bases de données. Un modèle d’application courant consiste à approvisionner une base de données pour chaque client. Toutefois, les modèles d’utilisation sont souvent imprévisibles et varient d’un client à l’autre, et il est difficile de prévoir les besoins en ressources de chaque utilisateur de base de données. Vous disposez généralement de deux options :

- Sur-approvisionner les ressources en fonction de l’utilisation maximale et payer trop, ou
- Sous-approvisionner pour réduire les coûts, au détriment des performances et de la satisfaction des clients au moment des pics d’utilisation.

Les pools élastiques résolvent ce problème en vous assurant que les bases de données disposent des ressources de performances nécessaires au moment où elles en ont besoin. Ils représentent un mécanisme simple d’allocation de ressources avec un budget prévisible. Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, consultez [Modèles de conception pour les applications SaaS multilocataires avec Azure SQL Database](saas-tenancy-app-design-patterns.md).
>
> [!IMPORTANT]
> Il n’existe pas de facturation par base de données pour les pools élastiques. Vous êtes facturé pour chaque heure d’existence d’un pool selon le nombre d’eDTUs ou de vCores le plus élevé, indépendamment de l’utilisation ou si le pool a été actif de moins d’une heure.

Un pool élastique est un pool que partagent plusieurs bases de données, et pour lequel le développeur peut acheter des ressources lorsque les bases de données l’utilisent de manière imprévisible. Vous pouvez configurer les ressources du pool avec le [modèle d’achat DTU](service-tiers-dtu.md) ou le [modèle d’achat vCore](service-tiers-vcore.md). Le besoin en ressources d’un pool est déterminé par l’utilisation globale de ses bases de données. La quantité de ressources disponibles pour le pool dépend du budget du développeur. Le développeur ajoute des bases de données au pool, définit éventuellement le nombre minimal et maximal de ressources pour les bases de données (le nombre minimal ou maximal de DTU ou de vCore, selon le modèle d’allocation des ressources choisi), puis définit les ressources du pool en fonction du budget. Un développeur peut utiliser des pools pour faire évoluer en toute transparence son service en passant d’une lean startup à une entreprise mature à une vitesse sans cesse croissante.

Au sein du pool, les différentes bases de données peuvent en toute souplesse s’adapter automatiquement en fonction des paramètres définis. Si la charge est élevée, une base de données peut consommer plus de ressources pour répondre à la demande. Les bases de données soumises à des charges légères en consomment moins, et celles qui ne sont soumises à aucune charge n’en consomment pas du tout. L’approvisionnement des ressources pour l’ensemble du pool plutôt que pour des bases de données uniques simplifie vos tâches de gestion. En outre, vous disposez d’un budget prévisible pour le pool. Des ressources supplémentaires peuvent être ajoutées à un pool existant avec un temps d’arrêt minimal. De même, si les ressources supplémentaires ne sont plus nécessaires, elles peuvent être supprimées du pool existant à tout moment. Vous pouvez ajouter ou supprimer des bases de données dans le pool. Si une base de données finit par sous-utiliser les ressources, retirez-la.

> [!NOTE]
> Le déplacement de bases de données en direction ou en dehors d’un pool élastique n’entraîne aucun temps d’arrêt, à l’exception d’un bref laps de temps (de l’ordre de quelques secondes) à la fin de l’opération lorsque les connexions de base de données sont abandonnées.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quand devez-vous envisager d’utiliser un pool élastique SQL Database ?

Les pools sont idéaux dans le cas de nombreuses bases de données avec des modèles d’utilisation spécifiques. Pour une base de données indiquée, ce modèle se caractérise par une faible utilisation moyenne avec des pics d'utilisation relativement rares. À l’inverse, évitez de placer plusieurs bases de données avec une utilisation permanente moyennement élevée dans le même pool élastique.

Plus vous ajoutez de bases de données à un pool, plus vous faites d'économies. En fonction de votre modèle d’utilisation de l’application, il est possible de faire des économies avec seulement deux bases de données S3.

Les sections suivantes vous aident à comprendre comment évaluer si votre collection de bases de données spécifique peut tirer profit de l’utilisation d’un pool. Les exemples utilisent les pools Standard, mais ces principes s’appliquent également aux pools De base et Premium.

### <a name="assessing-database-utilization-patterns"></a>Évaluation des modèles d'utilisation de base de données

La figure suivante montre l’exemple d'une base de données qui est très souvent inactive, mais qui connaît de temps en temps des pics d’activité. Il s’agit d’un modèle d’utilisation particulièrement adapté à un pool :

   ![une base de données unique adaptée à un pool](./media/elastic-pool-overview/one-database.png)

Le graphique illustre l’utilisation de DTU sur une période de 1 heure entre 12h00 et 13h00, où chaque point de données a une granularité de 1 minute. À 12h10, DB1 culmine à 90 DTU, mais son utilisation moyenne totale est inférieure à cinq DTU. Une taille de calcul S3 est requise pour exécuter cette charge de travail dans une base de données unique. Cependant, cela laisse la plupart des ressources inutilisées pendant les périodes de faible activité.

Un pool permet de partager ces DTU inutilisées entre plusieurs bases de données, ce qui réduit la quantité totale de DTU nécessaires et le coût global.

En s'appuyant sur l'exemple précédent, supposons qu'il y ait des bases de données supplémentaires avec des modèles d'utilisation similaires comme DB1. Dans les deux figures ci-dessous, l’utilisation de quatre bases de données et celle de 20 bases de données sont représentées sur le même graphe pour montrer qu’avec le modèle d’achat DTU, leur utilisation ne se chevauche pas dans le temps :

   ![quatre bases de données avec un modèle d’utilisation adapté à un pool](./media/elastic-pool-overview/four-databases.png)

   ![vingt bases de données avec un modèle d’utilisation adapté à un pool](./media/elastic-pool-overview/twenty-databases.png)

L’utilisation globale des DTU pour les 20 bases de données est illustrée par la ligne noire dans la figure ci-dessus. Cela montre que l'utilisation globale des DTU ne dépasse jamais 100 DTU et que les 20 bases de données peuvent partager 100 eDTU sur cette période. Le nombre de DTU est diminué par 20 et le prix par 13 par rapport au placement de chacune des bases de données dans des tailles de calcul S3 pour les bases de données uniques.

Cet exemple est idéal pour les raisons suivantes :

- Il existe de grandes différences entre les pics d’utilisation et l'utilisation moyenne par base de données.
- Les pics d’utilisation de chaque base de données se produisent à différents moments dans le temps.
- Les eDTU sont partagées entre plusieurs bases de données.

Dans le modèle d’achat DTU, le prix d’un pool dépend de ses unités eDTU. Alors que le prix unitaire d’une eDTU pour un pool est 1,5 fois supérieur au prix unitaire d’une DTU pour une base de données unique, les **eDTU de pool peuvent être partagées avec de nombreuses bases de données ; un nombre moins important d’eDTU est donc requis au total**. Ces différences en matière de prix et de partage des eDTU constituent la base du potentiel d'économies que les pools peuvent présenter.

Dans le modèle d’achat vCore, le prix unitaire vCore pour les pools élastiques est le même que pour les bases de données uniques.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Comment choisir la bonne taille de pool ?

Pour un pool, la taille optimale dépend du nombre global de ressources nécessaires pour toutes les bases de données du pool. Cela implique de déterminer ce qui suit :

- Quantité maximale de ressources de calcul utilisées par toutes les bases de données du pool.  Les ressources de calcul sont indexées par eDTU ou vCore selon le modèle d’achat choisi.
- Nombre maximal d’octets de stockage que se partagent toutes les bases de données du pool.

Pour connaître les niveaux de service et les limites de ressources de chacun des modèles d’achat, consultez [Modèle d’achat DTU](service-tiers-dtu.md) ou [Modèle d’achat vCore](service-tiers-vcore.md).

Les étapes suivantes peuvent vous aider à estimer si un pool est plus économique que les bases de données uniques :

1. Pour estimer le nombre d’eDTU ou de vCore nécessaires pour le pool, procédez de la manière suivante :
   - Pour le modèle d’achat DTU :
     - MAX(<*Nombre total de bases de données* &times; *Utilisation moyenne des DTU par base de données*>, <*Nombre de bases de données connaissant un pic simultané* &times; *Utilisation maximale des DTU par base de données*>)
   - Pour le modèle d’achat vCore :
     - MAX(<*Nombre total de bases de données* &times; *Utilisation moyenne des vCores par base de données*>, <*Nombre de bases de données connaissant un pic simultané* &times; *Utilisation maximale des vCores par base de données*>)
2. Estimez l’espace de stockage total nécessaire au pool en ajoutant la taille des données nécessaires à toutes les bases de données du pool. Pour le modèle d’achat DTU, déterminez ensuite la taille du pool d’eDTU qui fournit cette quantité de stockage.
3. Pour le modèle d’achat DTU, prenez la plus grande des estimations d’eDTU de l’étape 1 et de l’étape 2. Pour le modèle d’achat vCore, prenez l’estimation vCore de l’étape 1.
4. Dans la [page des prix de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/), recherchez le plus petit pool dont la taille est supérieure à l’estimation de l’étape 3.
5. Comparez le prix du pool trouvé à l’étape 4 à celui des tailles de calcul appropriées pour les bases de données uniques.

> [!IMPORTANT]
> Si le nombre de bases de données dans un pool approche le maximum pris en charge, veillez à envisager la [gestion des ressources dans les pools élastiques denses](elastic-pool-resource-management.md).

### <a name="per-database-properties"></a>Propriétés par base de données

Vous pouvez éventuellement définir des propriétés « par base de données » pour modifier les modèles de consommation des ressources dans les pools élastiques. Pour plus d’informations, reportez-vous à la documentation sur les limites de ressources pour les pools élastiques [DTU](resource-limits-dtu-elastic-pools.md#database-properties-for-pooled-databases) et [vCore](resource-limits-vcore-elastic-pools.md#database-properties-for-pooled-databases).

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Utilisation d’autres fonctionnalités SQL Database avec des pools élastiques

### <a name="elastic-jobs-and-elastic-pools"></a>Travaux élastiques et pools élastiques

Un pool simplifie les tâches de gestion grâce à l’exécution des scripts dans des **[tâches élastiques](elastic-jobs-overview.md)** . Un travail élastique élimine pratiquement le caractère fastidieux d’un nombre élevé de bases de données.

Pour en savoir plus sur les autres outils de base de données permettant d’utiliser plusieurs bases de données, consultez [Scale-out avec Azure SQL Database](elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Options de continuité de l’activité pour les bases de données d’un pool élastique

Les bases de données mises en pool prennent généralement en charge les mêmes [fonctionnalités de continuité d’activité](business-continuity-high-availability-disaster-recover-hadr-overview.md) que celles disponibles pour les bases de données uniques.

- **Restauration dans le temps**

  La restauration dans le temps utilise les sauvegardes automatiques de base de données pour récupérer une base de données d’un pool à un moment précis dans le temps. Voir [Limite de restauration dans le temps](recovery-using-backups.md#point-in-time-restore)

- **Géorestauration**

  La géorestauration constitue l’option de récupération par défaut lorsque la base de données est indisponible en raison d’un incident survenu dans la région où elle est hébergée. Voir [Restaurer une base de données Azure SQL ou basculer vers une base de données secondaire](disaster-recovery-guidance.md)

- **Géo-réplication active**

  Pour les applications qui ont des exigences de récupération plus agressives que ce qu’offre la géorestauration, configurez la [géoréplication active](active-geo-replication-overview.md) ou un [groupe de basculement automatique](auto-failover-group-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Création d’un pool élastique SQL Database à l’aide du portail Azure

Vous pouvez créer un pool élastique dans le portail Azure de deux façons.

1. Accédez au [portail Azure](https://portal.azure.com) pour créer un pool élastique. Recherchez et sélectionnez **Azure SQL**.
2. Sélectionnez **+Ajouter** pour ouvrir la page **Sélectionner l’option de déploiement SQL**. Vous pouvez afficher des informations supplémentaires sur les pools élastiques en sélectionnant **Afficher les détails** sur la vignette **Bases de données**.
3. Sur la vignette **Bases de données**, sélectionnez **Pool élastique** dans la liste déroulante **Type de ressource**, puis sélectionnez **Créer** :

   ![Créer un pool élastique](./media/elastic-pool-overview/create-elastic-pool.png)

4. Vous pouvez également créer un pool élastique en accédant à un serveur existant, puis en cliquant sur **+ Nouveau pool** pour créer un pool directement sur ce serveur.

> [!NOTE]
> Vous pouvez créer plusieurs pools sur un serveur, mais il est impossible d’ajouter des bases de données de différents serveurs dans le même pool.

Le niveau de service du pool détermine les fonctionnalités disponibles pour les bases de données élastiques du pool, ainsi que le nombre maximal de ressources pour chaque base de données. Pour plus d’informations, consultez les limites de ressources des pools élastiques dans le [modèle DTU](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Pour les limites de ressource vCore de pools élastiques, consultez [vCore-based resource limits - elastic pools](resource-limits-vcore-elastic-pools.md) (limites de ressource vCore - pools élastiques).

Pour configurer les ressources et les prix du pool, cliquez sur **Configurer le pool**. Ensuite, sélectionnez un niveau de service, ajoutez les bases de données au pool, puis configurez les limites de ressources pour le pool et ses bases de données.

Lorsque vous avez terminé la configuration du pool, vous pouvez cliquer sur Appliquer, nommer le pool, puis cliquer sur OK pour créer le pool.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Surveiller un pool élastique et ses bases de données

Dans le portail Azure, vous pouvez surveiller l’utilisation d’un pool élastique et des bases de données que contient ce pool. Vous pouvez également apporter un ensemble de modifications à votre pool élastique et soumettre toutes les modifications en même temps. Ces modifications incluent l’ajout ou la suppression de bases de données, ainsi que le changement des paramètres du pool élastique ou des bases de données.

Vous utilisez les outils intégrés [d’analyse des performances](./performance-guidance.md) et [d’alerte](./alerts-insights-configure-portal.md), combinées avec les indices de performance.  De plus, SQL Database peut [émettre des métriques et des journaux de ressources](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal) pour faciliter la supervision.

## <a name="customer-case-studies"></a>Études de cas clients

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart a utilisé des pools élastiques avec SQL Database pour développer rapidement ses services métier au rythme de 1 000 nouvelles bases de données Azure SQL par mois.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco utilise des pools élastiques avec Azure SQL Database afin de provisionner rapidement des services et de les mettre à l’échelle pour des milliers de locataires dans le cloud.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI utilise des pools élastiques avec Azure SQL Database pour accélérer son cycle de développement et améliorer ses performances et ses services clients.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations sur les prix, consultez la [tarification du pool élastique](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Pour mettre à l’échelle des pools élastiques, consultez [Scaling elastic pools](elastic-pool-scale.md) (mise à l’échelle des pools élastiques) et [Scale an elastic pool - sample code](scripts/monitor-and-scale-pool-powershell.md) (mise à l’échelle d’un pool élastique - exemple de code)
- Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, consultez [Modèles de conception pour les applications SaaS multilocataires avec Azure SQL Database](saas-tenancy-app-design-patterns.md).
- Pour obtenir un didacticiel SaaS utilisant des pools élastiques, consultez [Présentation de l’application SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md).
- Pour en savoir plus sur la gestion des ressources dans les pools élastiques ayant de nombreuses bases de données, consultez [Gestion des ressources dans les pools élastiques denses](elastic-pool-resource-management.md).
