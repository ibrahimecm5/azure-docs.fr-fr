---
title: 'SQL Server vers Azure SQL Managed Instance : Guide de migration'
description: Ce guide vous apprend à migrer des bases de données SQL Server vers Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 06/25/2021
ms.openlocfilehash: d00eb47a1e366d5ae9f3ba559a65e57f5c9c9baa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043999"
---
# <a name="migration-guide-sql-server-to-azure-sql-managed-instance"></a>Guide de migration : SQL Server vers Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Ce guide vous aide à migrer votre instance de SQL Server vers Azure SQL Managed Instance. 

Vous pouvez migrer SQL Server s’exécutant en local ou sur : 

- SQL Server sur les machines virtuelles  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform – GCP)  
- Cloud SQL pour SQL Server (Google Cloud Platform - GCP) 

Pour plus d’informations sur la migration, consultez la [vue d’ensemble de la migration](sql-server-to-managed-instance-overview.md). Pour obtenir d’autres guides de migration, consultez [Migration de base de données](/data-migration). 

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Flux du processus de migration":::

## <a name="prerequisites"></a>Prérequis 

Pour migrer votre serveur SQL vers Azure SQL Managed Instance, assurez-vous d’avoir : 

- Choisi une [ méthode de migration ](sql-server-to-managed-instance-overview.md#compare-migration-options) et les outils correspondants pour votre méthode.
- Installé l’[outil DMA (Assistant Migration de données)](https://www.microsoft.com/download/details.aspx?id=53595) sur une machine qui peut se connecter à votre serveur SQL Server source.
- Créé une instance cible d’[Azure SQL Managed Instance](../../managed-instance/instance-create-quickstart.md)
- Configuré la connectivité et les autorisations nécessaires pour accéder à la fois à la source et à la cible. 
- Examen des fonctionnalités du moteur de base de données SQL Server [disponibles dans Azure SQL Managed instance](../../database/features-comparison.md). 


## <a name="pre-migration"></a>Prémigration

Une fois que vous avez vérifié que votre environnement source est pris en charge, commencez par la phase de prémigration. Découvrez toutes les sources de données existantes, évaluez la faisabilité de la migration et identifiez les problèmes bloquants susceptibles d’empêcher la migration de s’effectuer correctement.  

### <a name="discover"></a>Découvrez

Durant la phase de découverte, analysez le réseau pour identifier toutes les instances et fonctionnalités de SQL Server utilisées par votre organisation. 

Utilisez [Azure Migrate](../../../migrate/migrate-services-overview.md) pour évaluer la pertinence de la migration des serveurs locaux, effectuer un dimensionnement en fonction des performances et fournir des estimations de coût pour les exécuter dans Azure. 

Vous pouvez également utiliser  [Microsoft Assessment and Planning Toolkit (« MAP Toolkit »)](https://www.microsoft.com/download/details.aspx?id=7826) pour évaluer votre infrastructure informatique actuelle. La boîte à outils fournit un puissant outil d’inventaire, d’évaluation et de création de rapports, qui permet de simplifier le processus de planification de la migration. 

Pour plus d’informations sur les outils utilisables au cours de la phase de découverte, consultez [Services et outils disponibles pour les scénarios de migration de données](../../../dms/dms-tools-matrix.md). 

Une fois les sources de données découvertes, évaluez les instances de SQL Server locales que vous pouvez migrer vers Azure SQL Managed Instance pour identifier les obstacles ou les problèmes de compatibilité liés à la migration.
Passez aux étapes suivantes pour évaluer et migrer des bases de données vers Azure SQL Managed Instance :

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-sql-managed-instance-steps.png" alt-text="Étapes pour la migration vers Azure SQL Managed Instance":::

- [Évaluer la compatibilité de SQL Managed Instance](#assess) où vous devez vous assurer qu’aucun problème bloquant ne peut empêcher vos migrations.
  Cette étape comprend également la création d’un [référentiel de performance](sql-server-to-managed-instance-performance-baseline.md#create-a-baseline) pour déterminer l’utilisation des ressources sur votre instance de SQL Server source. Cette étape est nécessaire si vous souhaitez déployer une Managed Instance correctement dimensionnée et vérifier que les performances après la migration ne sont pas altérées.
- [Choisir des options de connectivité des applications](../../managed-instance/connect-application-instance.md).
- [Déployer sur une instance gérée dimensionnée de façon optimale](#deploy-to-an-optimally-sized-managed-instance) où vous allez choisir les caractéristiques techniques (nombre de vCores, quantité de mémoire) et le niveau de performance (critique pour l’entreprise, usage général) de votre Managed Instance.
- [Sélectionner une méthode de migration et effectuer la migration](sql-server-to-managed-instance-overview.md#compare-migration-options) à l’endroit où vous migrez vos bases de données à l’aide des options de migration hors connexion et en ligne.
- [Surveiller et corriger les applications](#monitor-and-remediate-applications) pour vous assurer que vous disposez des performances attendues.


### <a name="assess"></a>Évaluer 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Déterminez si SQL Managed Instance est compatible avec les exigences de base de données de votre application. SQL Managed Instance est conçu pour faciliter la migration « lift-and-shift » de la plupart des applications existantes qui utilisent SQL Server. Toutefois, vous risquez parfois d’avoir besoin de fonctionnalités ou de capacités qui ne sont pas encore prises en charge et dont le coût d’implémentation d’une solution de contournement est trop élevé. 

Vous pouvez utiliser l’Assistant Migration de données (version 4.1 et ultérieure) pour évaluer les bases de données et obtenir les informations suivantes : 

- [Recommandations relatives aux cibles Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Recommandations relatives aux références SKU Azure](/sql/dma/dma-sku-recommend-sql-db)

Pour évaluer votre environnement à l’aide de l’Assistant Migration de données, suivez les étapes ci-dessous : 

1. Ouvrez l’outil [DMA (Assistant Migration de données)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Sélectionnez **Fichier**, puis choisissez **Nouvelle évaluation**. 
1. Spécifiez un nom de projet, sélectionnez le type de serveur source SQL Server, et le type de serveur cible Azure SQL Managed Instance. 
1. Sélectionnez les types de rapport d’évaluation à générer. Par exemple, la compatibilité de base de données et la parité des fonctionnalités. Selon le type d’évaluation, les autorisations nécessaires sur le serveur SQL Server source peuvent être différentes.  L’outil DMA met en évidence les autorisations nécessaires d’après l’outil Advisor choisi avant d’exécuter l’évaluation.
    - La catégorie **parité des fonctionnalités** fournit un ensemble complet de recommandations, d’alternatives disponibles dans Azure et d’étapes d’atténuation, qui vous aident à planifier votre projet de migration. (autorisations d’administrateur système nécessaires)
    - La catégorie **problèmes de compatibilité** identifie les problèmes de compatibilité des fonctionnalités partiellement ou non prises en charge, qui peuvent bloquer la migration. Elle fournit également les recommandations à suivre pour les résoudre (autorisations `CONNECT SQL`, `VIEW SERVER STATE` et `VIEW ANY DEFINITION` nécessaires).
1. Spécifiez les détails de la connexion source de votre serveur SQL Server, puis connectez-vous à la base de données source.
1. Sélectionnez **Démarrer l’évaluation**. 
1. À la fin du processus, sélectionnez et passez en revue les rapports d’évaluation pour identifier les problèmes bloquants et les problèmes de parité des fonctionnalités relatifs à la migration. Vous pouvez également exporter le rapport d’évaluation vers un fichier partagé avec d’autres équipes ou membres du personnel de votre organisation. 
1. Déterminez le niveau de compatibilité de la base de données pour réduire les efforts postmigration.  
1. Identifiez la meilleure référence SKU d’Azure SQL Managed Instance pour votre charge de travail locale. 

Pour en savoir plus, consultez [Effectuer une évaluation de migration SQL Server avec l’Assistant Migration de données](/sql/dma/dma-assesssqlonprem).

Si SQL Managed Instance n’est pas une cible appropriée pour votre charge de travail, SQL Server sur les machines virtuelles Azure peut représenter une cible alternative viable pour votre entreprise.

#### <a name="scaled-assessments-and-analysis"></a>Évaluations mises à l’échelle et analyse

L’Assistant Migration de données prend en charge l’exécution d’évaluations mises à l’échelle et la centralisation des rapports d’évaluation à des fins d’analyse. Si vous disposez de plusieurs serveurs et bases de données qui doivent être évalués et analysés à grande échelle afin de fournir une vue plus large du parc de données, cliquez sur les liens suivants pour en savoir plus.

- [Effectuer des évaluations mises à l’échelle à l’aide de PowerShell](/sql/dma/dma-consolidatereports)
- [Analyse des rapports d’évaluation à l’aide de Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>L’exécution d’évaluations à grande échelle sur plusieurs bases de données peut également être automatisée à l’aide de l’[utilitaire en ligne de commande de DMA](/sql/dma/dma-commandline). Vous pouvez ensuite charger les résultats vers [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) pour approfondir l’analyse et préparer la cible.

### <a name="deploy-to-an-optimally-sized-managed-instance"></a>Déployer sur une instance gérée dimensionnée de façon optimale

En vous basant sur les informations de la phase de découverte et d’évaluation, créez une cible SQL Managed Instance de taille appropriée. Pour ce faire, vous pouvez utiliser le [portail Azure](../../managed-instance/instance-create-quickstart.md), [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md) ou un modèle [ARM (Azure Resource Manager)](../../managed-instance/create-template-quickstart.md).

SQL Managed Instance est adapté pour des charges de travail locales qui planifient une migration vers le cloud. Un [modèle d’achat](../../database/service-tiers-vcore.md) est ainsi présenté. Il offre davantage de flexibilité dans le choix du niveau de ressources pour vos charges de travail. Localement, vous êtes probablement habitué à dimensionner ces charges de travail à l’aide de cœurs physiques et de bandes passantes E/S. Le modèle d’achat pour une instance gérée repose sur des mémoires à tores magnétiques virtuelles, ou « vCores », avec un stockage et des E/S supplémentaires disponibles séparément. Le modèle vCore vous permet de comprendre plus facilement vos exigences de calcul dans le cloud par rapport à ce que vous utilisez localement aujourd’hui. Ce modèle d’achat vous permet de dimensionner au mieux votre environnement de destination dans le cloud. Quelques conseils généraux pouvant vous aider à choisir les caractéristiques et le niveau de service appropriés sont décrits ici :

- En fonction de l’utilisation de l’UC de référence, vous pouvez approvisionner une instance gérée correspondant au nombre de cœurs que vous utilisez sur SQL Server, tout en tenant compte des caractéristiques de l’UC qui sont susceptibles d’être mises à l’échelle, afin de satisfaire aux [caractéristiques des machines virtuelles où est installé Managed Instance](../../managed-instance/resource-limits.md#hardware-generation-characteristics).
- En fonction de l’utilisation de la mémoire de référence, choisissez [le niveau de service disposant de la mémoire correspondante](../../managed-instance/resource-limits.md#hardware-generation-characteristics). La quantité de mémoire ne peut pas être choisie directement. Vous devez donc sélectionner l’instance gérée avec la quantité de vCores disposant de la mémoire correspondante (par exemple, 5,1 Go/vCore dans Gen5).
- En fonction de la latence d’E/S de référence du sous-système de fichiers, choisissez entre les niveaux de service Usage général (latence supérieure à 5 ms) et Critique pour l’entreprise (latence inférieure à 3 ms).
- En fonction du débit de référence, pré-allouez la taille des fichiers de données ou des fichiers journaux pour atteindre les performances d’E/S attendues.

Vous pouvez choisir des ressources de calcul et de stockage au moment du déploiement, puis les modifier plus tard sans temps d’arrêt pour votre application par le biais du [portail Azure](../../database/scale-resources.md) :

:::image type="content" source="media/sql-server-to-managed-instance-overview/managed-instance-sizing.png" alt-text="Dimensionnement de l’instance gérée":::

Pour apprendre à créer l’infrastructure de réseau virtuel et une instance gérée, voir [Créer une instance gérée](../../managed-instance/instance-create-quickstart.md).

> [!IMPORTANT]
> Il est important de maintenir votre réseau virtuel de destination et le sous-réseau en adéquation avec la [Configuration requise de réseau virtuel d’instance gérée](../../managed-instance/connectivity-architecture-overview.md#network-requirements). Toute incompatibilité risque de vous empêcher de créer des instances ou d’utiliser celles que vous avez déjà créées. Apprenez-en davantage sur la [création de réseaux](../../managed-instance/virtual-network-subnet-create-arm-template.md) et la [configuration de réseaux existants](../../managed-instance/vnet-existing-add-subnet.md).

## <a name="migrate"></a>Migrer

Une fois que vous avez effectué les tâches associées à la phase de prémigration, vous êtes prêt à effectuer la migration du schéma et des données. 

Migrez les données à l’aide de la [méthode de migration](sql-server-to-managed-instance-overview.md#compare-migration-options) de votre choix.

SQL Managed Instance cible des scénarios d’utilisateur qui exigent une migration de base de données en masse depuis des implémentations locales ou de machine virtuelle. Ils constituent le meilleur choix lorsque vous avez besoin d’effectuer une migration « lift-and-shift » du backend des applications qui utilisent régulièrement des fonctionnalités au niveau de l’instance et/ou entre plusieurs bases de données. Si cela correspond à votre scénario, vous pouvez déplacer toute une instance vers un environnement correspondant dans Azure sans avoir à redéfinir l’architecture de vos applications.

Pour déplacer des instances SQL, vous devez planifier avec soin :

- La migration de toutes les bases de données qui ont besoin d’être colocalisées (celles qui s’exécutent sur la même instance).
- La migration des objets au niveau de l’instance dont votre application dépend, notamment les connexions, les informations d’identification, les travaux et opérateurs de l’Agent SQL, ainsi que les déclencheurs au niveau du serveur.

SQL Managed Instance est un service géré qui vous permet de déléguer certaines des activités courantes d’administration des bases de données à la plateforme puisqu’elles y sont intégrées. Ainsi, certaines données au niveau de l’instance n’ont pas besoin de migrer, notamment les travaux de maintenance pour les sauvegardes régulières ou la configuration Always On, étant donné que la [haute disponibilité](../../database/high-availability-sla.md) est intégrée.

SQL Managed Instance prend en charge les options de migration de base de données suivantes (actuellement ce sont les seules méthodes de migration prises en charge) :

- Azure Database Migration Service - Migration ne nécessitant quasiment aucun temps d’arrêt.
- Native `RESTORE DATABASE FROM URL` - Utilise des sauvegardes natives à partir de SQL Server et nécessite un temps d’arrêt.

Ce guide décrit les deux options les plus répandues : Azure Database Migration Service (DMS) et la fonctionnalité native de sauvegarde et restauration.

### <a name="database-migration-service"></a>Database Migration Service

Pour effectuer des migrations à l’aide de DMS, suivez les étapes ci-dessous :

1. [Inscrivez le fournisseur de ressources **Microsoft.DataMigration**](../../../dms/quickstart-create-data-migration-service-portal.md#register-the-resource-provider) dans votre abonnement, si vous effectuez cette opération pour la première fois.
1. Créez une instance Azure Database Migration Service là où vous voulez (de préférence dans la même région que votre instance cible d’Azure SQL Managed Instance), puis sélectionnez un réseau virtuel existant, ou créez-en un autre pour héberger votre instance de DMS.
1. Une fois votre instance de DMS créée, créez un projet de migration, puis spécifiez le type de serveur source **SQL Server** et le type de serveur cible **Azure SQL Database Managed Instance**. Choisissez le type d’activité dans le panneau de création de projet : migration de données en ligne ou hors connexion. 
1.  Spécifiez les détails du serveur SQL Server source dans la page **Détails de la source de migration** ainsi que les détails de la cible Azure SQL Managed Instance dans la page **Détails de la cible de migration**. Sélectionnez **Suivant**.
1. Choisissez la base de données à migrer. 
1. Fournissez les paramètres de configuration nécessaires pour spécifier le **partage réseau SMB** qui contient vos fichiers de sauvegarde de base de données. Utilisez les informations d’identification d’utilisateur Windows auprès de DMS, qui permettent d’accéder au partage réseau. Fournissez les **détails de votre compte de stockage Azure**. 
1. Passez en revue le récapitulatif de la migration, puis choisissez **Exécuter la migration**. Vous pouvez ensuite superviser l’activité de migration et vérifier la progression de la migration de votre base de données.
1. Une fois la base de données restaurée, choisissez **Démarrer le basculement**. Le processus de migration copie la sauvegarde de la fin du journal, une fois qu’elle est disponible dans le partage réseau SMB, puis la restaure sur la cible. 
1. Arrêtez tout trafic entrant à destination de votre base de données source, puis mettez à jour la chaîne de connexion vers la nouvelle base de données Azure SQL Managed Instance. 

Pour accéder à un tutoriel étape par étape basé sur cette option de migration, consultez [Procéder à la migration en ligne de SQL Server vers Azure SQL Managed Instance à l’aide de DMS](../../../dms/tutorial-sql-server-managed-instance-online.md). 
   

### <a name="backup-and-restore"></a>Sauvegarde et restauration 

L’une des fonctionnalités clés d’Azure SQL Managed Instance pour accélérer et faciliter la migration des bases de données est la restauration native des fichiers de sauvegarde de base de données (`.bak`) présents sur le service [Stockage Azure](https://azure.microsoft.com/services/storage/). La sauvegarde et la restauration sont des opérations asynchrones qui dépendent de la taille de votre base de données. 

Le diagramme suivant fournit une vue d’ensemble du processus :

![Le diagramme affiche SQL Server avec une flèche intitulée SAUVEGARDE / Télécharger vers l’URL qui passe à Azure Storage, et une deuxième flèche intitulée RESTAURER à partir de l’URL qui circule du stockage Azure vers une Managed Instance de SQL.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> Le temps nécessaire pour effectuer une sauvegarde, la charger vers le stockage Azure et exécuter une opération de restauration native sur Azure SQL Managed Instance dépend de la taille de la base de données. Prenez en compte un temps d’arrêt suffisant pour les grandes bases de données. 

Le tableau suivant fournit des informations supplémentaires sur les méthodes que vous pouvez utiliser en fonction de la version de SQL Server source que vous exécutez :

|Étape|Moteur SQL et version|Méthode de sauvegarde/restauration|
|---|---|---|
|Placer la sauvegarde sur Stockage Azure|Avant 2012 SP1 CU2|Charger le fichier .bak directement sur Stockage Azure|
| |2012 SP1 CU2 - 2016|Sauvegarde directe utilisant la syntaxe [WITH CREDENTIAL](/sql/t-sql/statements/restore-statements-transact-sql) dépréciée|
| |2016 et versions ultérieures|Sauvegarde directe utilisant [WITH SAS CREDENTIAL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restaurer à partir d’un Stockage Azure vers une instance gérée| |[RESTORE FROM URL avec SAS CREDENTIAL](../../managed-instance/restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Lorsque vous migrez une base de données protégée par [Transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md) vers une instance gérée à l’aide d’une option de restauration native, le certificat correspondant du serveur SQL Server local ou de machine virtuelle Azure doit être migré avant la restauration de la base de données. Pour des instructions détaillées, voir [Migrer un certificat TDE vers une instance gérée](../../managed-instance/tde-certificate-migrate.md).
> - La restauration de bases de données système n’est pas prise en charge. Pour effectuer la migration d’objets au niveau de l’instance (stockés dans des bases de données master et msdb), nous vous recommandons de les scripter et d’exécuter des scripts T-SQL sur l’instance de destination.

Pour effectuer une migration à l’aide de la fonctionnalité de sauvegarde et restauration, suivez les étapes ci-dessous : 

1. Sauvegardez votre base de données sur le Stockage Blob Azure. Par exemple, utilisez la fonctionnalité de [sauvegarde vers une URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) dans [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Utilisez [Microsoft Azure Tools](https://go.microsoft.com/fwlink/?LinkID=324399) pour prendre en charge les bases de données antérieures à SQL Server 2012 SP1 CU2. 
1. Connectez-vous à votre instance Azure SQL Managed Instance via SQL Server Management Studio. 
1. Créez des informations d’identification à l’aide d’une signature d’accès partagé pour accéder à votre compte Stockage Blob Azure avec vos sauvegardes de base de données. Par exemple :

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Restaurez la sauvegarde à partir du conteneur Azure Storage Blob. Par exemple : 

   ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Une fois la restauration effectuée, visualisez la base de données dans l’**Explorateur d’objets** de SQL Server Management Studio. 

Pour en savoir plus sur cette option de migration, consultez [Restaurer une base de données dans Azure SQL Managed Instance avec SSMS](../../managed-instance/restore-sample-database-quickstart.md).

> [!NOTE]
> Une opération de restauration de base de données est asynchrone et peut être retentée. SQL Server Management Studio peut générer une erreur en cas d’interruption de la connexion ou d’expiration d’un délai d’attente. Azure SQL Database continue d’essayer de restaurer la base de données en arrière-plan et vous pouvez suivre l’avancement de la restauration dans les vues [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) et [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).


## <a name="data-sync-and-cutover"></a>Synchronisation des données et basculement

Quand vous utilisez des options de migration qui répliquent/synchronisent en permanence les changements apportés aux données de la source vers la cible, les données et le schéma sources peuvent varier et dériver par rapport à la cible. Pendant la synchronisation des données, vérifiez que tous les changements apportés à la source sont capturés et appliqués à la cible au cours du processus de migration. 

Une fois que vous avez vérifié que les données sont identiques sur la source et la cible, vous pouvez effectuer le basculement de l’environnement source vers l’environnement cible. Il est important de planifier le processus de basculement avec les équipes métier/applicatives. Ainsi, vous avez la garantie que l’interruption minimale qui a lieu pendant le basculement n’affecte pas la continuité de l’activité. 

> [!IMPORTANT]
> Pour plus d’informations sur les étapes spécifiques associées à l’exécution d’un basculement dans le cadre de migrations à l’aide du service DMS, consultez [Exécution du basculement de migration](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


## <a name="post-migration"></a>Postmigration

Une fois que vous avez réussi la phase de migration, vous devez effectuer toute une série de tâches postmigration pour vérifier que tout fonctionne de manière fluide et efficace. 

La phase postmigration est cruciale pour résoudre les problèmes de justesse et d’exhaustivité des données ainsi que pour gérer les problèmes de performances liés à la charge de travail. 

### <a name="monitor-and-remediate-applications"></a>Surveiller et corriger les applications 
Après avoir effectué la migration vers une instance gérée, vous devez suivre le comportement de l’application et les performances de votre charge de travail. Ce processus comporte les activités suivantes :

- [Comparer les performances de la charge de travail s’exécutant sur l’instance Managed Instance](sql-server-to-managed-instance-performance-baseline.md#compare-performance) avec celles du [référentiel de performance que vous avez créé sur l’instance SQL Server source](sql-server-to-managed-instance-performance-baseline.md#create-a-baseline).
- [Superviser les performances de votre charge de travail](sql-server-to-managed-instance-performance-baseline.md#monitor-performance) en permanence pour identifier les problèmes potentiels et les améliorations.

### <a name="perform-tests"></a>Effectuer des tests

L’approche de test pour la migration de base de données comprend les activités suivantes :

1. **Développer des tests de validation** : pour tester la migration d’une base de données, vous devez utiliser des requêtes SQL. Vous devez créer les requêtes de validation à exécuter sur les bases de données source et cible. Vos requêtes de validation doivent couvrir l’étendue que vous avez définie.
1. **Configurer un environnement de test**: l’environnement de test doit contenir une copie de la base de données source et de la base de données cible. Veillez à isoler l’environnement de test.
1. **Exécuter des tests de validation** : exécutez les tests de validation sur la source et sur la cible, puis analysez les résultats.
1. **Exécuter des tests de performances**: exécutez un test de performances sur la source et sur la cible, puis analysez et comparez les résultats.


## <a name="leverage-advanced-features"></a>Tirer parti des fonctionnalités avancées 

Veillez à tirer parti des fonctionnalités cloud avancées offertes par SQL Managed Instance, notamment la [haute disponibilité intégrée](../../database/high-availability-sla.md), la [détection des menaces](../../database/azure-defender-for-sql.md) ainsi que la [supervision et le paramétrage de votre charge de travail](../../database/monitor-tune-overview.md). 

[Azure SQL Analytics](../../../azure-sql/database/monitor-tune-overview.md) vous permet de superviser un grand nombre d’instances managées de manière centralisée.

Certaines fonctionnalités SQL Server sont disponibles uniquement une fois que vous avez fait passer le [niveau de compatibilité de la base de données](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) au dernier niveau (150). 


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans les différents scénarios de migration de données et de base de données ainsi que leurs tâches spécialisées, consultez [Services et outils de migration de données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur Azure SQL Managed Instance, consultez :
   - [Niveaux de service dans Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Différences entre SQL Server et Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Calculatrice du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Pour plus d’informations sur l’infrastructure et le cycle d’adoption pour les migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour savoir comment exécuter un test A/B sur la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).
