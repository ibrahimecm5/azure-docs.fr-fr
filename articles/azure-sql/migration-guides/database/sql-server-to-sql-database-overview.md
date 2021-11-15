---
title: 'De SQL Server à Azure SQL Database : Vue d’ensemble de la migration'
description: Découvrez les outils et options à disposition pour migrer vos bases de données SQL Server vers Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 11/06/2020
ms.openlocfilehash: c147dfb2dbb16e350214dedff39a36625735834d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062944"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Vue d’ensemble de la migration : de SQL Server vers Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Découvrez les options et les considérations relatives à la migration de vos bases de données SQL Server vers Azure SQL Database. 

Vous pouvez migrer des bases de données SQL Server s’exécutant en local sur : 

- SQL Server sur les machines virtuelles Azure.  
- Azure SQL Database (PaaS).
- Azure SQL Managed Instance (PaaS).
- Amazon Web Services (AWS) Elastic Compute Cloud (EC2).
- AWS Relational Database Service (RDS).
- Compute Engine dans Google Cloud Platform (GCP).  
- Cloud SQL pour SQL Server dans GCP. 

Pour obtenir d’autres guides de migration, consultez [Migration de base de données](/data-migration). 

## <a name="overview"></a>Vue d’ensemble

[Azure SQL Database](../../database/sql-database-paas-overview.md) est une option cible recommandée pour les charges de travail SQL Server qui nécessitent une plateforme en tant que service (PaaS) entièrement gérée. SQL Database gère la plupart des fonctions de gestion de base de données. Elle offre également des fonctionnalités intégrées de haute disponibilité, de traitement intelligent des requêtes, d’évolutivité et de performances pour s’adapter à de nombreux types d’applications. 

SQL Database offre une certaine flexibilité avec plusieurs [modèles de déploiement](../../database/sql-database-paas-overview.md#deployment-models) et [niveaux de service](../../database/service-tiers-vcore.md#service-tiers) qui prennent en charge différents types d’applications ou de charges de travail.

L’un des principaux avantages de la migration vers SQL Database est que vous pouvez moderniser votre application à l’aide des fonctionnalités PaaS. Vous pouvez ensuite éliminer toute dépendance vis-à-vis des composants techniques délimités au niveau de l’instance, tels que les travaux de SQL Agent.

Vous pouvez également réduire les coûts à l’aide d’[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) pour SQL Server afin de migrer vos licences locales SQL Server vers Azure SQL Database. Cette option est disponible si vous choisissez le [modèle d’achat vCore](../../database/service-tiers-vcore.md).

Veillez à passer en revue les fonctionnalités du moteur de base de données SQL Server [disponibles dans Azure SQL Database](../../database/features-comparison.md) pour valider la prise en charge de votre cible de migration.  

## <a name="considerations"></a>Considérations 

Les facteurs clés à prendre en considération au moment d’évaluer les différentes options de migration dépendent des éléments suivants : 

- Nombre de serveurs et de bases de données
- Taille des bases de données
- Temps d’arrêt acceptable pour l’entreprise pendant le processus de migration 

Les options de migration listées dans ce guide prennent en compte ces facteurs. Pour une migration de données logiques vers Azure SQL Database, le temps de migration peut dépendre du nombre d’objets contenus dans une base de données et de la taille de la base de données. 

Des outils sont disponibles pour différentes charges de travail et préférences utilisateur. Certains outils peuvent être utilisés pour effectuer une migration rapide d’une seule base de données via un outil basé sur une interface utilisateur. D’autres outils peuvent automatiser la migration de plusieurs bases de données pour gérer les migrations à grande échelle. 

## <a name="choose-an-appropriate-target"></a>Choisir une cible appropriée

Tenez compte des recommandations générales pour choisir le modèle de déploiement et le niveau de service d’Azure SQL Database qui conviennent le mieux. Vous pouvez choisir des ressources de calcul et de stockage pendant le déploiement et en changer par la suite via le [portail Azure](../../database/scale-resources.md) sans temps d’arrêt pour votre application.

**Modèles de déploiement** : il est important de bien comprendre la charge de travail de votre application et le modèle d’utilisation avant de vous décider entre une base de données unique ou un pool élastique. 

- Une [base de données unique](../../database/single-database-overview.md) est une base de données entièrement gérée qui convient pour la plupart des microservices et applications cloud modernes.
- Un [pool élastique](../../database/elastic-pool-overview.md) représente une collection de bases de données uniques avec un ensemble partagé de ressources telles que le processeur ou la mémoire. Il convient pour combiner des bases de données dans un pool avec des modèles d’utilisation prévisibles qui peuvent partager efficacement le même ensemble de ressources.

**Modèles d’achat** : choisissez entre les modèles d’achat vCore, par unité de transaction de base de données (DTU) ou sans serveur. 

- Le [modèle vCore](../../database/service-tiers-vcore.md) vous permet de choisir le nombre de vCores pour l’instance Azure SQL Database, ce qui en fait le choix le plus simple quand vous effectuez une transition à partir de SQL Server en local. Cette option est la seule à permettre de réaliser des économies sur le coût de licence grâce à [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- Le [modèle DTU](../../database/service-tiers-dtu.md) extrait les ressources sous-jacentes de calcul, de mémoire et d’E/S afin d’offrir une unité de transaction de base de données (DTU) composite. 
- Le [modèle serverless](../../database/serverless-tier-overview.md) s’adresse aux charges de travail qui requièrent une mise à l’échelle automatique à la demande avec des ressources de calcul facturées par seconde d’utilisation. Le niveau de calcul sans serveur interrompt automatiquement les bases de données pendant les périodes d’inactivité (où seul le stockage est facturé). Il reprend automatiquement les bases de données lorsque des nouvelles activités. 

**Niveaux de service** : effectuez un choix parmi trois niveaux de service conçus pour différents types d'applications.

- Le [niveau de service à usage général/standard ](../../database/service-tier-general-purpose.md) offre une option équilibrée et économique avec des calculs et un stockage adaptés pour des applications aux niveaux intermédiaire et inférieur. La redondance est intégrée au niveau de la couche de stockage pour récupérer après les défaillances. Il est conçu pour la plupart des charges de travail de base de données. 
- Le [niveau service critique pour l’entreprise/Premium](../../database/service-tier-business-critical.md) est destiné aux applications de haut niveau qui requièrent des taux de transactions élevés, des E/S à faible latence et un haut niveau de résilience. Les réplicas secondaires sont disponibles pour le basculement et pour le déchargement des charges de travail de lecture.
- Le [niveau de service Hyperscale](../../database/service-tier-hyperscale.md) s’adresse aux bases de données qui ont des volumes de données croissants et qui doivent faire l’objet d’un scale-up automatique pouvant atteindre une taille de base de données de 100 To. Il est conçu pour les bases de données très volumineuses. 

> [!IMPORTANT]
> Le [taux de journalisation des transactions est régi](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) par Azure SQL Database pour limiter les taux d’ingestion élevés. À cet effet, pendant la migration, vous pourriez avoir à mettre à l’échelle les ressources de la base de données cible (vCores ou DTU) pour alléger la pression sur le processeur ou le débit. Choisissez une base de données cible de taille appropriée, mais prévoyez si nécessaire de procéder à un scale-up des ressources pour la migration. 


### <a name="sql-server-vm-alternative"></a>Alternative à la machine virtuelle SQL Server

Votre entreprise a peut-être des exigences qui font de [SQL Server sur machines virtuelles Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) une cible plus indiquée qu’Azure SQL Database. 

Si l’une des conditions suivantes s’applique à votre entreprise, envisagez de passer à une machine virtuelle SQL Server à la place : 

- Vous avez besoin d’un accès direct au système d’exploitation ou au système de fichiers, par exemple pour installer des agents tiers ou personnalisés sur une même machine virtuelle avec SQL Server. 
- Vous dépendez obligatoirement de fonctionnalités qui ne sont pas encore prises en charge, comme FileStream/FileTable, PolyBase et les transactions entre plusieurs instances. 
- Vous devez conserver une version spécifique de SQL Server (2012, par exemple). 
- Vos exigences de calcul sont beaucoup plus faibles que ce qu’offre une instance gérée (un seul vCore, par exemple), et la consolidation des bases de données n’est pas une option acceptable. 


## <a name="migration-tools"></a>Outils de migration 

Nous vous recommandons d’utiliser les outils de migration suivants : 

|Technology | Description|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Ce service Azure vous permet de découvrir et d’évaluer votre patrimoine de données SQL à grande échelle sur VMware. Il fournit des recommandations sur le déploiement Azure SQL, le dimensionnement cible et les estimations mensuelles. | 
|[Assistant de migration des données](/sql/dma/dma-migrateonpremsqltosqldb)|Cet outil de bureau de Microsoft effectue des évaluations homogènes de SQL Server et des migrations de bases de données uniques vers Azure SQL Database (à la fois le schéma et les données). </br></br>L’outil peut être installé sur un serveur local ou sur votre ordinateur local, sous réserve qu’il dispose d’une connectivité avec vos bases de données sources. Le processus de migration est un déplacement de données logiques entre objets de la base de données source et ceux de la base de données cible.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)|Ce service Azure peut migrer des bases de données SQL Server vers Azure SQL Database via le portail Azure ou automatiquement via PowerShell. Azure Database Migration Service vous impose de sélectionner un réseau virtuel Azure par défaut pendant le provisionnement pour vérifier qu’il existe une connectivité avec vos bases de données SQL Server sources. Vous pouvez migrer des bases de données uniques ou à grande échelle. |
| | |


Le tableau suivant liste d’autres outils de migration : 

|Technology |Description  |
|---------|---------|
|[Réplication transactionnelle](../../database/replication-to-sql-database.md)|Réplique les données des tables de la base de données Azure SQL Server source dans Azure SQL Database en proposant une option de migration de type publication-abonnement tout en préservant la cohérence transactionnelle. Les modifications de données incrémentielles sont propagées aux abonnés à mesure qu’elles se produisent sur les serveurs de publication.|
|[Service d’importation/exportation / BACPAC](../../database/database-import.md)|Un [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) est un fichier Windows avec une extension .bacpac qui encapsule le schéma et les données d’une base de données. BACPAC permet d’exporter des données à partir d’une source SQL Server et d’importer des données dans Azure SQL Database. Un fichier BACPAC peut être importé dans une nouvelle instance SQL Database à partir du portail Azure. </br></br> Pour des raisons d’échelle et de performances, si vous disposez de bases de données volumineuses ou en nombre, envisagez d’utiliser l’outil de ligne de commande [SqlPackage](../../database/database-import.md#using-sqlpackage) pour exporter et importer des bases de données.|
|[Copie en bloc](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|L’[outil bcp (bulk copy program)](/sql/tools/bcp-utility) permet de copier les données d’une instance SQL Server dans un fichier de données. Utilisez l’outil pour exporter les données de votre source et importer le fichier de données dans l’instance Azure SQL Database cible. </br></br> Pour profiter d’opérations de copie en bloc à haut débit lors du déplacement de données vers Azure SQL Database, l’[outil Smart Bulk Copy](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) peut être utilisé pour optimiser la vitesse de transfert en tirant parti des tâches de copie en parallèle.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md).|L’[activité de copie](../../../data-factory/copy-activity-overview.md) d’Azure Data Factory migre les données des bases de données SQL Server sources vers Azure SQL Database à l’aide de connecteurs intégrés et d’un [runtime d’intégration](../../../data-factory/concepts-integration-runtime.md).</br> </br> Azure Data Factory prend en charge un large éventail de [connecteurs](../../../data-factory/connector-overview.md) pour déplacer les données de sources SQL Server vers Azure SQL Database.|
|[Synchronisation des données SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL Data Sync est un service basé sur Azure SQL Database qui vous permet de synchroniser les données choisies de manière bidirectionnelle sur plusieurs bases de données, à la fois locales et dans le cloud.</br>Data Sync est utile dans les cas où les données doivent être tenues à jour entre plusieurs bases de données dans Azure SQL Database ou SQL Server.|
| | |

## <a name="compare-migration-options"></a>Comparer les options de migration

Comparez les options de migration de façon à emprunter la voie qui vous permettra de répondre à vos besoins métier. 

Le tableau suivant compare les options de migration recommandées : 

|Option de migration  |Quand l’utiliser  |Considérations  |
|---------|---------|---------|
|[Assistant de migration des données](/sql/dma/dma-migrateonpremsqltosqldb) | - Migration de bases de données uniques (à la fois le schéma et les données).  </br> - Acceptation de temps d’arrêt pendant le processus de migration des données. </br> </br> Sources prises en charge : </br> - SQL Server (2005 à 2019) local ou sur machine virtuelle Azure </br> - AWS EC2 </br> - AWS RDS </br> - Machine virtuelle SQL Server GCP Compute | - L’activité de migration procède à des déplacements de données entre objets de base de données (de la source vers la cible), d’où la recommandation de l’exécuter durant les heures creuses. </br> - L’Assistant Migration de données (DMA) indique l’état de la migration par objet de base de données, notamment le nombre de lignes migrées.  </br> - Pour les migrations de grande ampleur pour ce qui est du nombre de bases de données ou de la taille de base de données, utilisez Azure Database Migration Service.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)| - Migration de bases de données uniques ou à grande échelle. </br> - Acceptation de temps d’arrêt pendant le processus de migration. </br> </br> Sources prises en charge : </br> - SQL Server (2005 à 2019) local ou sur machine virtuelle Azure </br> - AWS EC2 </br> - AWS RDS </br> - Machine virtuelle SQL Server GCP Compute | - Les migrations à grande échelle peuvent être automatisées via [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> - La durée de la migration dépend de la taille de la base de données et du nombre d’objets qu’elle contient. </br> - Impose une définition de la base de données source en mode Lecture seule. |
| | | |

Le tableau suivant compare les autres options de migration : 

|Méthode ou technologie |Quand l’utiliser    |Considérations  |
|---------|---------|---------|
|[Réplication transactionnelle](../../database/replication-to-sql-database.md)| - Migration avec une publication continue des modifications des tables de base de données sources vers les tables Azure SQL Database cibles. </br> – Effectuer une migration complète ou partielle des tables sélectionnées (sous-ensemble d’une base de données).  </br> </br> Sources prises en charge : </br> - [SQL Server (2016 à 2019) avec certaines limitations](/sql/relational-databases/replication/replication-backward-compatibility) </br> - AWS EC2  </br> - Machine virtuelle SQL Server GCP Compute  | - La configuration est relativement complexe par rapport à d’autres options de migration.   </br> - Offre une option de réplication continue pour migrer les données (sans mettre les bases de données en mode hors connexion).  </br> – La réplication transactionnelle présente des limitations dont vous devez tenir compte si vous configurez l’éditeur sur l’instance SQL source. Pour plus d’informations, consultez [Limitations de la publication d’objets](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects). </br>- Il est possible de [superviser l’activité de réplication](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Service d’importation/exportation / BACPAC](../../database/database-import.md)| - Migration de bases de données d’applications métier individuelles. </br>- Adapté aux bases de données de plus petite taille.  </br>  - Ne nécessite pas de service ou d’outil de migration distinct. </br> </br> Sources prises en charge : </br> - SQL Server (2005 à 2019) local ou sur machine virtuelle Azure </br> - AWS EC2 </br> - AWS RDS </br> - Machine virtuelle SQL Server GCP Compute  |  – Impose un temps d’arrêt dans la mesure où les données doivent être exportées à la source et importées à la destination.   </br> – Les formats de fichiers et les types de données utilisés dans l’exportation ou importation doivent être cohérents avec les schémas de table pour éviter les erreurs de troncation ou de non-correspondance de types de données.  </br> - L’exportation d’une base de données comportant un grand nombre d’objets peut prendre beaucoup plus de temps.       |
|[Copie en bloc](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Effectuer des migrations complètes ou partielles de données. </br> - Acceptation de temps d’arrêt. </br> </br> Sources prises en charge : </br> - SQL Server (2005 à 2019) local ou sur machine virtuelle Azure </br> - AWS EC2 </br> - AWS RDS </br> - Machine virtuelle SQL Server GCP Compute   | – Impose un temps d’arrêt pour exporter les données de la source et les importer dans la cible. </br> - Les formats de fichiers et les types de données utilisés dans l’exportation/importation doivent être en cohérence avec les schémas de table. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md).| - Migration et/ou transformation des données à partir des bases de données SQL Server sources. </br> - Fusion de données issues de plusieurs sources de données vers Azure SQL Database, généralement pour des charges de travail de type décisionnel.  |  - Impose la création de pipelines de déplacement des données dans Data Factory pour déplacer les données de la source vers la destination.   </br> - Le [coût](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) est une considération importante qui varie en fonction des déclencheurs de pipeline, des exécutions d’activité, de la durée de déplacement des données, etc. |
|[Synchronisation des données SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)| - Synchronisation des données entre les bases de données source et cible.</br> - Convient pour l’exécution d’une synchronisation continue entre Azure SQL Database et une instance SQL Server locale dans un flux bidirectionnel. | - Azure SQL Database doit être la base de données Hub pour la synchronisation avec une base de données SQL Server local en tant que base de données membre.</br> - En comparaison de la réplication transactionnelle, SQL Data Sync prend en charge la synchronisation bidirectionnelle des données entre un environnement local et Azure SQL Database. </br> -Selon la charge de travail, son impact sur le niveau de performance peut être plus élevé.|
| | | |

## <a name="feature-interoperability"></a>Interopérabilité des fonctionnalités 

D’autres éléments sont à prendre en considération quand vous migrez des charges de travail qui reposent sur d’autres fonctionnalités SQL Server.

### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Vous pouvez migrer des packages SQL Server Integration Services (SSIS) vers Azure en les redéployant sur le runtime Azure-SSIS dans [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [prend en charge la migration des packages SSIS](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) en offrant un runtime conçu pour exécuter les packages SSIS dans Azure. Vous pouvez également réécrire la logique SSIS ETL (extraction, transformation, chargement) en mode natif dans Azure Data Factory à l’aide des [flux de données](../../../data-factory/concepts-data-flow-overview.md).


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Migrez les rapports SQL Server Reporting Services (SSRS) vers des rapports paginés dans Power BI. Utilisez l’ [outil de migration RDL](https://github.com/microsoft/RdlMigration) pour préparer et migrer vos rapports. Microsoft a développé cet outil pour aider les clients à migrer les rapports RDL (Report Definition Language) de leurs serveurs SSRS vers Power BI. Il est disponible sur GitHub et fournit une procédure complète pour un scénario de migration. 

### <a name="high-availability"></a>Haute disponibilité
La configuration manuelle des fonctionnalités de haute disponibilité de SQL Server comme les instances de cluster de basculement Always On et les groupes de disponibilité Always On deviennent obsolètes sur la base de données SQL cible. L’architecture de haute disponibilité est déjà intégrée aux niveaux de service [Usage général (modèle de disponibilité standard)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) et [Critique pour l’entreprise (modèle de disponibilité Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) pour Azure SQL Database. Le niveau de service Critique pour l’entreprise/Premium propose également un scale-out en lecture qui permet de se connecter à l’un des nœuds secondaires pour des besoins de lecture seule. 

Au-delà de l’architecture de haute disponibilité incluse dans Azure SQL Database, la fonctionnalité des [groupes de basculement automatique ](../../database/auto-failover-group-overview.md) vous permet de gérer la réplication et le basculement des bases de données d’une instance gérée vers une autre région. 

### <a name="logins-and-groups"></a>Connexions et groupes

Les connexions Windows ne sont pas prises en charge dans Azure SQL Database. Créez plutôt une connexion Azure Active Directory. Recréez manuellement les connexions SQL. 

### <a name="sql-agent-jobs"></a>Travaux SQL Agent
Les travaux SQL Agent ne sont pas directement pris en charge dans Azure SQL Database et doivent être déployés dans des [tâches de base de données élastique (préversion)](../../database/job-automation-overview.md).

### <a name="system-databases"></a>Bases de données système
Pour Azure SQL Database, les seules bases de données système applicables sont [MASTER](/sql/relational-databases/databases/master-database) et tempdb. Pour plus d’informations, consultez [Tempdb dans Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="advanced-features"></a>Fonctionnalités avancées 

Veillez à tirer parti des fonctionnalités cloud avancées de SQL Database. Par exemple, vous n’avez plus à vous soucier de la gestion des sauvegardes, car le service le fait à votre place. Vous pouvez effectuer une restauration à n’importe quel [point dans le temps inclus dans la période de rétention](../../database/recovery-using-backups.md#point-in-time-restore). 

Pour renforcer la sécurité, envisagez d’utiliser l’ [authentification Azure AD](../../database/authentication-aad-overview.md), l’[audit](../../database/auditing-overview.md), la  [détection des menaces](../../database/azure-defender-for-sql.md), la  [sécurité au niveau des lignes](/sql/relational-databases/security/row-level-security) et le  [masquage dynamique des données](/sql/relational-databases/security/dynamic-data-masking).

En plus des fonctionnalités de gestion et de sécurité avancées, Azure SQL Database propose des outils qui peuvent vous aider à [superviser et ajuster votre charge de travail](../../database/monitor-tune-overview.md). [Azure SQL Analytics (préversion)](../../../azure-monitor/insights/azure-sql.md) est une solution avancée qui permet de superviser dans une même vue les performances de toutes vos bases de données dans Azure SQL Database à grande échelle et sur plusieurs abonnements. Azure SQL Analytics collecte et visualise des métriques de performances clés à l’aide d’une intelligence intégrée pour résoudre les problèmes de performances.

Le [réglage automatique](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)  supervise en permanence les performances de votre plan d’exécution SQL et corrige automatiquement les problèmes de performances identifiés. 


## <a name="migration-assets"></a>Ressources de migration 

Pour obtenir une aide supplémentaire, consultez les ressources suivantes qui ont été développées pour des projets de migration concrets.

|Asset  |Description  |
|---------|---------|
|[Outil et modèle d’évaluation d’une charge de travail de données](https://www.microsoft.com/download/details.aspx?id=103130)| Cet outil fournit des suggestions pour les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données qui sont les mieux adaptés pour une charge de travail. Il propose des fonctionnalités de génération de rapports et de calculs simples en un clic qui permettent d’accélérer l’évaluation des grands patrimoines en fournissant un processus automatisé et uniforme de prise de décision concernant la plateforme cible.|
|[Création de bases de données en bloc avec PowerShell](https://www.microsoft.com/download/details.aspx?id=103107)|Vous pouvez utiliser un jeu de trois scripts PowerShell qui créent un groupe de ressources (create_rg.ps1), le [serveur logique dans Azure](../../database/logical-servers.md) (create_sqlserver.ps1) et la base de données SQL (create_sqldb.ps1). Sachant que les scripts incluent des fonctionnalités d’exécution en boucle, vous pouvez itérer et créer autant de serveurs et de bases de données que nécessaire.|
|[Déploiement de schémas en bloc avec MSSQL-Scripter et PowerShell](https://www.microsoft.com/download/details.aspx?id=103032)|Cette ressource crée un groupe de ressources, un ou plusieurs [serveurs logiques dans Azure](../../database/logical-servers.md) pour héberger Azure SQL Database, exporte chaque schéma à partir d’une instance SQL Server locale (ou de plusieurs instances SQL Server 2005+), puis les importe dans Azure SQL Database.|
|[Convertir des travaux SQL Server Agent en tâches de base de données élastique](https://www.microsoft.com/download/details.aspx?id=103123)|Ce script migre vos travaux SQL Server Agent sources vers des tâches de base de données élastique.|
|[Utilitaire permettant de déplacer des connexions SQL Server locales vers Azure SQL Database](https://www.microsoft.com/download/details.aspx?id=103111)|Script PowerShell qui crée un script de commande T-SQL visant à recréer les connexions et à sélectionner les utilisateurs de base de données d’une instance SQL Server locale vers Azure SQL Database. Cet outil permet de mapper automatiquement des comptes Active Directory de Windows Server à des comptes Azure AD et éventuellement de migrer des connexions natives SQL Server.|
|[Automatisation de la collecte des données perfmon à l’aide de Logman](https://www.microsoft.com/download/details.aspx?id=103114)|Vous pouvez utiliser l’outil Logman pour collecter des données Perfmon (pour vous aider à comprendre les performances de base) et obtenir des recommandations sur la cible de la migration. Cet outil se sert de logman.exe pour créer la commande qui va créer, démarrer, arrêter et supprimer les compteurs de performances définis sur une instance SQL distante.|

L’équipe d’ingénierie SQL des données a développé ces ressources. La charte fondamentale de cette équipe a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux projets de migration de plateforme de données vers la plateforme Azure Data de Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer la migration de vos bases de données SQL Server vers Azure SQL Database, consultez le [Guide de migration de SQL Server vers Azure SQL Database](sql-server-to-sql-database-guide.md).

- Pour obtenir une matrice de services et d’outils qui peuvent vous aider avec des scénarios de migration de bases de données et de données, ainsi que des tâches spécialisées, consultez la section [Services et outils de migration de données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur SQL Database, consultez :
   - [Vue d’ensemble d’Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Calculatrice du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Pour en savoir plus sur l’infrastructure et le cycle d’adoption des migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Pour plus d’informations sur l’exécution des tests A/B de la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).
