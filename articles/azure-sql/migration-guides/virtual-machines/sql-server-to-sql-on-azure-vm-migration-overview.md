---
title: SQL Server vers SQL Server sur machine virtuelle Azure (Vue d’ensemble de la migration)
description: Découvrez les différentes stratégies de migration qui existent lorsque vous souhaitez migrer de SQL Server vers SQL Server sur des machines virtuelles Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: chadam
ms.date: 09/07/2021
ms.openlocfilehash: 163fb5cba55248fcc478e219a6b7c014fedbb689
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397791"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>Vue d’ensemble de la migration : SQL Server vers SQL Server sur les machines virtuelles Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Découvrez les différentes stratégies de migration qui existent pour migrer de SQL Server vers SQL Server sur des machines virtuelles Azure. 

Vous pouvez migrer SQL Server s’exécutant en local ou sur :

- SQL Server sur les machines virtuelles  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform – GCP)

Pour obtenir d’autres guides de migration, consultez [Migration de base de données](/data-migration). 

## <a name="overview"></a>Vue d’ensemble

Migrez vers [SQL Server sur les machines virtuelles Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) lorsque vous souhaitez utiliser l’environnement familier de SQL Server avec le contrôle du système d’exploitation et que vous souhaitez tirer parti des fonctionnalités fournies par le cloud, telles que la haute disponibilité intégrée des machines virtuelles, les [sauvegardes automatisées](../../virtual-machines/windows/automated-backup.md) et la [mise à jour corrective automatisée](../../virtual-machines/windows/automated-patching.md). 

Réalisez des économies en dotant votre propre licence du [modèle de licence Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) ou étendez la prise en charge de SQL Server 2008 et SQL Server 2008 R2 en obtenant des [mises à jour de sécurité gratuites](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md). 


## <a name="choose-appropriate-target"></a>Choisir la cible appropriée

Les machines virtuelles Azure s’exécutent dans de nombreuses régions d’Azure et offrent également une variété de [tailles de machine](../../../virtual-machines/sizes.md) et d’[options de stockage](../../../virtual-machines/disks-types.md). Pour déterminer la taille correcte de la machine virtuelle et du stockage pour votre charge de travail SQL Server, consultez [Recommandations de performances pour SQL Server sur les machines virtuelles Azure](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md#vm-size). Pour déterminer les besoins en matière de stockage et de taille de machine virtuelle pour votre charge de travail, il est recommandé de les dimensionner par le biais d’une [évaluation Azure Migrate](../../../migrate/concepts-assessment-calculation.md#types-of-assessments) basée sur les performances. Si cette option n’est pas disponible, consultez l’article suivant sur la création de votre propre [ligne de base pour les performances](https://azure.microsoft.com/services/virtual-machines/sql-server/).

Il convient également de réfléchir à l’installation et à la configuration correctes de SQL Server sur une machine virtuelle. Il est recommandé d’utiliser la [galerie d’images de machine virtuelle Azure SQL](../../virtual-machines/windows/create-sql-vm-portal.md), car cela vous permet de créer une machine virtuelle SQL Server avec la version, l’édition et le système d’exploitation appropriés. Cela permettra également d’inscrire automatiquement la machine virtuelle Azure auprès du [fournisseur de ressources](../../virtual-machines/windows/create-sql-vm-portal.md) SQL Server, activant ainsi des fonctionnalités telles que les sauvegardes automatisées et la mise à jour corrective automatisée.

## <a name="migration-strategies"></a>Stratégies de migration

Il existe deux stratégies de migration pour migrer vos bases de données utilisateur vers une instance SQL sur des machines virtuelles Azure : **migrer** et **lift-and-shift**. 

L’approche appropriée pour votre entreprise dépend généralement des facteurs suivants : 

- Taille et échelle de la migration
- Vitesse de migration
- Prise en charge des applications pour la modification du code
- Nécessité de changer de version de SQL Server, de système d’exploitation ou les deux
- Cycle de vie de la prise en charge de vos produits existants
- Fenêtre de temps d’arrêt de l’application pendant la migration

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="Temps d’arrêt de la migration des machines virtuelles":::

Le tableau suivant décrit les différences entre les deux stratégies de migration :
<br />

| **Stratégie de migration** | **Description** | **Quand utiliser** |
| --- | --- | --- |
| **Lift-and-shift** | Utilisez la stratégie de migration Lift-and-shift pour déplacer l’intégralité des serveurs SQL physiques ou virtuels de leur emplacement actuel vers une instance SQL sur une machine virtuelle Azure sans aucune modification du système d’exploitation ou de la version de SQL Server. Pour effectuer une migration Lift-and-shift, consultez [Azure Migrate](../../../migrate/migrate-services-overview.md). <br /><br /> Le serveur source reste en ligne et traite les requêtes, tandis que le serveur source et le serveur de destination synchronisent les données, ce qui permet une migration presque transparente. | À utiliser pour les migrations simples aux migrations à très grande échelle, applicable aussi aux scénarios comme la sortie d’un centre de données. <br /><br /> Modifications de code minimales ou nulles requises pour les bases de données ou les applications SQL des utilisateurs, ce qui permet des migrations globales plus rapides. <br /><br />Aucune étape supplémentaire n’est requise pour la migration des services décisionnels tels que [SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) et [SSAS](/analysis-services/analysis-services-overview). |
|**Migrer** | Utilisez une stratégie de migration lorsque vous souhaitez mettre à niveau la version cible du serveur SQL et/ou du système d’exploitation. <br /> <br /> Sélectionnez une machine virtuelle Azure sur Place de marché Azure ou une image SQL Server préparée qui correspond à la version source de SQL Server. <br/> <br/> Utilisez l'[extension Azure SQL Migration for Azure Data Studio](../../../dms/migration-using-azure-data-studio.md) pour migrer une ou plusieurs bases de données SQL Server vers SQL Server sur des machines virtuelles Azure avec un minimum de temps d'arrêt. | À utiliser lorsqu’il est nécessaire ou souhaitable d’utiliser des fonctionnalités disponibles dans des versions plus récentes de SQL Server, ou lorsqu’il est nécessaire de mettre à niveau les versions héritées de SQL Server et/ou du système d’exploitation qui ne sont plus prises en charge.  <br /> <br /> Peut nécessiter des modifications de l’application ou de la base de données utilisateur pour prendre en charge la mise à niveau du serveur SQL. <br /><br />Il peut y avoir des considérations supplémentaires relatives à la migration des [services décisionnels](#business-intelligence) s’ils s’inscrivent dans le cadre de la migration. |


## <a name="lift-and-shift"></a>Migration lift-and-shift  

Le tableau suivant détaille la méthode disponible pour la stratégie de migration **Lift-and-shift** pour migrer votre base de données SQL Server vers SQL Server sur les machines virtuelles Azure :
<br />

|**Méthode** | **Version source minimale** | **Version cible minimale** | **Contrainte de taille de la sauvegarde source** |  **Remarques** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Limite de stockage de machine virtuelle Azure](../../../index.yml) |  Le serveur SQL existant doit être déplacé tel quel vers une instance SQL sur une machine virtuelle Azure. Peut mettre à l’échelle des charges de travail de migration allant jusqu’à 35 000 machines virtuelles. <br /><br /> Les serveurs sources restent en ligne et traitent les requêtes pendant la synchronisation des données du serveur, réduisant ainsi au minimum les temps d’arrêt. <br /><br /> **Automatisation et scripts** : [scripts Azure Site Recovery](../../../migrate/how-to-migrate-at-scale.md) et [exemple de migration et de planification mises à l’échelle pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale).|

> [!NOTE]
> Il est maintenant possible d’effectuer un lift-and-shift de votre solution d’[instance de cluster de basculement](sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) et de [groupe de disponibilité](sql-server-availability-group-to-sql-on-azure-vm.md) vers SQL Server sur des machines virtuelles Azure à l’aide d’Azure Migrate. 

## <a name="migrate"></a>Migrer  

En raison de sa facilité de configuration, l’approche de migration recommandée consiste à effectuer localement une [sauvegarde](/sql/t-sql/statements/backup-transact-sql) native de SQL Server puis à copier le fichier sur Azure. Cette méthode prend en charge des bases de données plus volumineuses (> 1 To) pour toutes les versions de SQL Server à partir de 2008 et des sauvegardes de base de données plus volumineuses (> 1 To). Toutefois, pour les bases de données à partir de SQL Server 2014, dont la taille est inférieure à 1 To et qui ont une bonne connectivité à Azure, [la sauvegarde SQL Server vers une URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) est la meilleure approche. 

Lors de la migration de bases de données SQL Server vers une instance SQL sur des machines virtuelles Azure, il est important de choisir une approche adaptée lorsque vous devez basculer vers le serveur cible, car cela influe sur la fenêtre de temps d’arrêt de l’application.

Le tableau suivant détaille toutes les méthodes disponibles pour migrer votre base de données SQL Server vers SQL Server sur les machines virtuelles Azure :
<br />

|**Méthode** | **Version source minimale** | **Version cible minimale** | **Contrainte de taille de la sauvegarde source** | **Remarques** |
| --- | --- | --- | --- | --- |
| **[Extension de migration Azure SQL pour Azure Data Studio](../../../dms/migration-using-azure-data-studio.md)** | SQL Server 2005 | SQL Server 2008 | [Limite de stockage de machine virtuelle Azure](../../../index.yml) |  Il s'agit d'une extension basée sur un assistant facile à utiliser dans Azure Data Studio pour la migration de base(s) de données SQL Server vers SQL Server sur des machines virtuelles Azure. Utilisez la compression afin de réduire la taille de la sauvegarde pour le transfert. <br /><br /> L'extension Azure SQL Migration for Azure Data Studio offre des capacités d'évaluation et de migration dans une interface utilisateur simple.  |
| **[Sauvegarde dans un fichier](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Limite de stockage de machine virtuelle Azure](../../../index.yml) |  Il s’agit d’une technique simple et éprouvée pour déplacer des bases de données d’une machine à l’autre. Utilisez la compression afin de réduire la taille de la sauvegarde pour le transfert. <br /><br /> **Automatisation et scripts** : [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) et [AzCopy dans Stockage Blob](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Sauvegarde vers une URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 12,8 To pour SQL Server 2016, sinon 1 To | Autre façon de déplacer le fichier de sauvegarde sur la machine virtuelle à l’aide du stockage Azure. Utilisez la compression afin de réduire la taille de la sauvegarde pour le transfert. <br /><br /> **Automatisation et scripts** :  [T-SQL ou plan de maintenance](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Assistant Migration de données Microsoft (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Limite de stockage de machine virtuelle Azure](../../../index.yml) |  L’[Assistant Migration de données](/sql/dma/dma-overview) évalue SQL Server localement, puis effectue une mise à niveau en toute transparence vers les versions ultérieures de SQL Server ou migre vers SQL Server sur les machines virtuelles Azure, Azure SQL Database ou Azure SQL Managed Instance. <br /><br /> Ne doit pas être utilisée sur des bases de données utilisateur compatibles le flux de fichier.<br /><br /> L’Assistant Migration de données offre également la possibilité de migrer des [identifiants SQL et Windows](/sql/dma/dma-migrateserverlogins) et d’évaluer des [packages SSIS](/sql/dma/dma-assess-ssis). <br /><br /> **Automatisation et scripts** : [Interface de ligne de commande](/sql/dma/dma-commandline) |
| **[Détachement et attachement](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Limite de stockage de machine virtuelle Azure](../../../index.yml) | Utilisez cette méthode quand vous envisagez de [stocker ces fichiers à l’aide du service Stockage Blob Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) et de les attacher à une instance SQL sur une machine virtuelle Azure, particulièrement utile avec les bases de données très volumineuses ou lorsque le temps de sauvegarde et de restauration est trop long. <br /><br /> **Automatisation et scripts** :  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) et [AzCopy dans Stockage Blob](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Copie des journaux de transaction](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (Windows uniquement) | SQL Server 2008 SP4 (Windows uniquement) | [Limite de stockage de machine virtuelle Azure](../../../index.yml) | La copie des journaux de transaction réplique les fichiers journaux de transactions locaux dans une instance SQL sur une machine virtuelle Azure. <br /><br /> Cela permet de réduire au minimum les temps d’arrêt pendant le basculement et de réduire les frais de configuration par rapport à la configuration d’un groupe de disponibilité Always On. <br /><br /> **Automatisation et scripts** : [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Groupe de disponibilité distribué](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Limite de stockage de machine virtuelle Azure](../../../index.yml) |  Un [groupe de disponibilité distribué](/sql/database-engine/availability-groups/windows/distributed-availability-groups) est un type spécial de groupe de disponibilité qui englobe deux groupes de disponibilité distincts. Les groupes de disponibilité qui participent à un groupe de disponibilité distribué n’ont pas besoin de se trouver au même emplacement et incluent la prise en charge interdomaine. <br /><br /> Cette méthode réduit le temps d’arrêt, à utiliser quand un groupe de disponibilité est configuré localement. <br /><br /> **Automatisation et scripts** : [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> - Pour les transferts de données volumineux avec des options de réseau limitées ou inexistantes, consultez [Transferts de données volumineux avec une connectivité limitée](../../../storage/common/storage-solution-large-dataset-low-network.md).
> - Il est maintenant possible d’effectuer un lift-and-shift de votre solution d’[instance de cluster de basculement](sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) et de [groupe de disponibilité](sql-server-availability-group-to-sql-on-azure-vm.md) vers SQL Server sur des machines virtuelles Azure à l’aide d’Azure Migrate. 

### <a name="considerations"></a>Considérations

La liste suivante répertorie les points clés à prendre en compte lors de l’examen des méthodes de migration :

- Pour obtenir des performances optimales de transfert de données, migrez les bases de données et les fichiers vers une instance SQL sur une machine virtuelle Azure à l’aide d’un fichier de sauvegarde compressé. Pour les bases de données plus volumineuses, en plus de la compression, [fractionnez le fichier de sauvegarde en fichiers plus petits](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) pour accroître les performances lors de la sauvegarde et du transfert. 
- Si vous effectuez une migration à partir de SQL Server 2014 ou une version ultérieure, envisagez de [chiffrer les sauvegardes](/sql/relational-databases/backup-restore/backup-encryption) pour protéger les données lors du transfert sur le réseau.
- Pour réduire le temps d’arrêt lors de la migration de base de données, utilisez l’option de groupe de disponibilité AlwaysOn. 
- Pour réduire le temps d’arrêt sans encourir de frais supplémentaires liés à la configuration d’un groupe de disponibilité, utilisez l’option de copie des journaux de transaction. 
- Pour les options de réseau limitées ou inexistantes, utilisez des méthodes de migration hors connexion, telles que la sauvegarde et la restauration, ou [les services de transfert de disque](../../../storage/common/storage-solution-large-dataset-low-network.md) disponibles dans Azure.
- Pour modifier également la version de SQL Server sur SQL Server sur une machine virtuelle Azure, consultez [Modifier l’édition de SQL Server](../../virtual-machines/windows/change-sql-server-edition.md).

## <a name="business-intelligence"></a>Business Intelligence 

Il peut y avoir des considérations supplémentaires lors de la migration de services décisionnels SQL Server en dehors du cadre des migrations de base de données. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Vous pouvez migrer des packages et des projets SQL Server Integration Services (SSIS) dans SSISDB vers SQL Server sur une machine virtuelle Azure à l’aide de l’une des deux méthodes ci-dessous. 

- Sauvegardez et restaurez SSISDB à partir de l’instance SQL Server source vers SQL Server sur une machine virtuelle Azure. Cela permet de restaurer vos packages dans SSISDB dans le [catalogue Integration Services sur votre serveur SQL Server cible sur la machine virtuelle Azure](/sql/integration-services/catalog/ssis-catalog).
- Redéployez vos packages SSIS sur votre serveur SQL Server cible sur une machine virtuelle Azure à l’aide de l’une des [options de déploiement](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

Si vous avez déployé des packages SSIS en tant que modèle de déploiement de package, vous pouvez les convertir avant la migration. Pour en savoir plus, consultez le [tutoriel de conversion de projet](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model). 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Pour migrer vos rapports SQL Server Reporting Services (SSRS) vers votre serveur SQL Server cible sur une machine virtuelle Azure, consultez [Migrer une installation Reporting Services (mode natif)](/sql/reporting-services/install-windows/migrate-a-reporting-services-installation-native-mode).

Vous pouvez également migrer les rapports SSRS vers des rapports paginés dans Power BI. Utilisez l’ [outil de migration RDL](https://github.com/microsoft/RdlMigration) pour préparer et migrer vos rapports. Microsoft a développé cet outil pour aider les clients à migrer les rapports RDL (Report Definition Language) de leurs serveurs SSRS vers Power BI. Il est disponible sur GitHub et fournit une procédure complète pour un scénario de migration. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services
Les bases de données SQL Server Analysis Services (modèles multidimensionnels ou tabulaires) peuvent être migrées de votre instance SQL Server source vers SQL Server sur une machine virtuelle Azure à l’aide de l’une des options suivantes :

-   Par interaction à l'aide de SSMS
-   Par programmation à l’aide d’AMO (Analysis Management Objects) (AMO)
-   Par script à l’aide de XMLA (XML for Analysis)

Pour plus d’informations, consultez [Déplacer une base de données Analysis Services](/analysis-services/multidimensional-models/move-an-analysis-services-database?view=asallproducts-allversions).

Vous pouvez également envisager de migrer vos modèles tabulaires Analysis Services locaux vers [Azure Analysis Services](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/) ou [Power BI Premium à l’aide des nouveaux points de terminaison en lecture/écriture XMLA](/power-bi/admin/service-premium-connect-tools). 

## <a name="server-objects"></a>Objets de serveur

Selon la configuration de votre instance SQL Server source, il peut y avoir des fonctionnalités SQL Server supplémentaires qui nécessitent une intervention manuelle pour les migrer vers SQL Server sur une machine virtuelle Azure en générant des scripts dans Transact-SQL (T-SQL) à l’aide de SQL Server Management Studio, puis en exécutant les scripts sur l’instance SQL Server cible sur la machine virtuelle Azure. Voici quelques-unes des fonctionnalités couramment utilisées :

- Connexions et rôles
- Serveurs liés
- Sources de données externes
- Travaux de l'Agent
- Alertes
- Messagerie de base de données
- Réplication

## <a name="supported-versions"></a>Versions prises en charge

Lorsque vous vous préparez à migrer des bases de données SQL Server vers SQL Server sur les machines virtuelles Azure, veillez à prendre en compte les versions de SQL Server qui sont prises en charge. Pour obtenir la liste des versions de SQL Server actuellement prises en charge sur les machines virtuelles Azure, consultez [SQL Server sur les machines virtuelles Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

## <a name="migration-assets"></a>Ressources de migration 

Pour obtenir une aide supplémentaire, consultez les ressources suivantes qui ont été développées pour des projets de migration concrets.

|Asset  |Description  |
|---------|---------|
|[Outil et modèle d’évaluation d’une charge de travail de données](https://www.microsoft.com/download/details.aspx?id=103130)| Cet outil fournit les plateformes cibles, la préparation du cloud et le niveau de correction des applications/bases de données « les mieux adaptés » pour une charge de travail donnée. Il propose une génération de rapports et des calculs simples en un clic qui permettent d’accélérer les évaluations d’un vaste domaine en fournissant un processus de décision de plateforme cible automatisé et uniforme.|
|[Automatisation de la collecte des données perfmon à l’aide de Logman](https://www.microsoft.com/download/details.aspx?id=103114)|Outil qui collecte les données perfmon pour comprendre le niveau de performance de la ligne de base et qui contribue à la recommandation de la cible de migration. Cet outil se sert de logman.exe pour créer la commande qui va créer, démarrer, arrêter et supprimer les compteurs de performances définis sur un serveur SQL distant.|
|[Multiple-SQL-VM-VNet-ILB](https://www.microsoft.com/download/details.aspx?id=103104)|Ce livre blanc décrit les étapes à suivre pour configurer plusieurs machines virtuelles Azure dans une configuration de groupe de disponibilité Always On SQL Server.|
|[Machines virtuelles Azure prenant en charge les disques SSD Ultra par région](https://www.microsoft.com/download/details.aspx?id=103105)|Ces scripts PowerShell fournissent une option de programmation permettant de récupérer la liste des régions qui prennent en charge les machines virtuelles Azure prenant en charge les disques SSD Ultra.|

L’équipe d’ingénierie SQL des données a développé ces ressources. La charte fondamentale de cette équipe a pour objet d’initier et d’accélérer une modernisation complexe et de faire face aux projets de migration de plateforme de données vers la plateforme Azure Data de Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer la migration de vos bases de données SQL Server vers SQL Server sur les machines virtuelles Azure, consultez le [guide de migration de base de données individuelle](sql-server-to-sql-on-azure-vm-individual-databases-guide.md). 

- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez l’article [Services et outils disponibles pour la migration de données](../../../dms/dms-tools-matrix.md).

- Pour en savoir plus sur Azure SQL, consultez :
   - [Options de déploiement](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server sur machines virtuelles Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Calculatrice du coût total de possession Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Pour plus d’informations sur l’infrastructure et le cycle d’adoption pour les migrations cloud, consultez :
   -  [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Pour plus d’informations sur la gestion des licences, consultez :
   - [BYOL (apportez votre propre licence) avec Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Bénéficier d’un support étendu gratuit pour SQL Server 2008 et SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour savoir comment exécuter un test A/B sur la couche d’accès aux données, consultez [Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).
