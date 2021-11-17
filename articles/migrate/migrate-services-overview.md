---
title: À propos d’Azure Migrate
description: Découvrez le service Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 7370413d792cbabf1b18db5b70da6260417c04d4
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157914"
---
# <a name="about-azure-migrate"></a>À propos d’Azure Migrate

Cet article donne une vue d’ensemble du service Azure Migrate.

Azure Migrate fournit un hub centralisé pour évaluer et migrer les serveurs, l’infrastructure, les applications et les données en local vers Azure. Il procure les éléments suivants :

- **Plateforme de migration unifiée** : un portail unique pour démarrer, exécuter et effectuer le suivi de votre migration vers Azure.
- **Panoplie d’outils** : Panoplie d’outils d’évaluation et de migration. Les outils Azure Migrate incluent Azure Migrate : découverte et évaluation et Azure Migrate : migration de serveur. Azure Migrate s’intègre aussi avec d’autres services et outils Azure, et avec des offres d’éditeur de logiciels indépendant.
- **Évaluation et migration** : dans le hub Azure Migrate, vous pouvez évaluer et migrer les éléments suivants :
    - **Serveurs, bases de données et applications web** : évaluez les serveurs locaux, notamment les applications web et les instances SQL et migrez-les vers des machines virtuelles Azure ou vers Azure VMware Solution (AVS) (préversion).
    - **Bases de données** : Évaluez vos bases de données locales et migrez-les vers Azure SQL Database ou SQL Managed Instance.
    - **Applications web** : évaluez les applications web locales et migrez-les vers Azure App service.
    - **Bureaux virtuels** : évaluez votre infrastructure VDI (infrastructure de bureau virtuel) locale et migrez-la vers Windows Virtual Desktop dans Azure.
    - **Données** : migrez rapidement et à moindre coût de grandes quantités de données vers Azure à l’aide des produits Azure Data Box.

## <a name="integrated-tools"></a>Outils intégrés

Le hub Azure Migrate comprend ces outils :

**Outil** | **Évaluer et migrer** | **Détails**
--- | --- | ---
**Azure Migrate : découverte et évaluation** | Découvrir et évaluer des serveurs, notamment SQL et applications web | Découvrez et évaluez des serveurs locaux s’exécutant sur VMware, Hyper-V et les serveurs physiques pour les préparer à une migration vers Azure.
**Azure Migrate : Server Migration** | Migrer des serveurs | Migrez des machines virtuelles VMware, des machines virtuelles Hyper-V, des serveurs physiques, d’autres serveurs virtualisés, ainsi que des machines virtuelles de cloud public vers Azure.
**Assistant de migration des données** | Évaluez les bases de données SQL Server pour la migration vers Azure SQL Database, Azure SQL Managed Instance ou des machines virtuelles Azure exécutant SQL Server. | Assistant Migration de données est un outil autonome pour évaluer les serveurs SQL. Il aide à identifier les problèmes potentiels bloquant la migration. Il identifie les fonctionnalités non prises en charge, les nouvelles fonctionnalités dont vous pouvez bénéficier après la migration, et le chemin approprié pour la migration de base de données. [Plus d’informations](/sql/dma/dma-overview)
**Azure Database Migration Service** | Migrer des bases de données locales vers des machines virtuelles Azure exécutant SQL Server, Azure SQL Database ou des SQL Managed Instances | [En savoir plus](../dms/dms-overview.md) sur Database Migration Service.
**Movere** | Évaluer des serveurs | [En savoir plus](#movere) sur Movere.
**Assistant Migration d’applications web** | Évaluez les applications web locales et migrez-les vers Azure. |  Assistant Migration Azure App Service est un outil pour évaluer des sites web locaux en vue de leur migration vers Azure App Service.<br/><br/> Utilisez Migration Assistant pour migrer des applications web .NET et PHP vers Azure. [En savoir plus](https://appmigration.microsoft.com/) sur Azure App Service Migration Assistant.
**Azure Data Box** | Migrer des données hors connexion | Pour déplacer de grandes quantités de données hors connexion vers Azure, utilisez la gamme de produits Azure Data Box. [Plus d’informations](../databox/index.yml)

> [!NOTE]
> Si vous êtes dans Azure Government, les outils externes intégrés et les offres d’éditeurs de logiciels indépendants ne peuvent pas envoyer de données à Azure Migrate. Vous pouvez employer les outils de manière indépendante.

## <a name="isv-integration"></a>Intégration d’éditeurs de logiciels indépendants

Azure Migrate s’intègre à plusieurs offres d’éditeurs de logiciels indépendants. 

**Fournisseurs de logiciels indépendants**    | **Fonctionnalité**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrez des serveurs.
[Cloudamize](https://www.cloudamize.com/platform) | Évaluez des serveurs.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Évaluez et migrez des serveurs.
[Device42](https://docs.device42.com/) | Évaluez des serveurs.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Évaluez l’infrastructure VDI.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrez des serveurs.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Évaluez des serveurs.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Évaluez des serveurs et des bases de données.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | Migrez des serveurs.

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Outil Azure Migrate : découverte et évaluation

L’outil Azure Migrate : découverte et évaluation découvre et évalue les machines virtuelles VMware locales, les machines virtuelles Hyper-V et les serveurs physiques pour une migration vers Azure.

Voici ce que fait l’outil :

- **Préparé pour Azure** : vous permet d’évaluer si les serveurs locaux, serveurs SQL et applications web sont prêts pour une migration vers Azure.
- **Dimensionnement d’Azure** : vous permet d’estimer la taille des machines virtuelles Azure, la configuration Azure SQL et le nombre de nœuds Azure VMware Solution après la migration.
- **Estimation des coûts Azure** : estimez les coûts liés à l’exécution de serveurs locaux dans Azure.
- **Analyse des dépendances** : identifie les dépendances entre serveurs et les stratégies d’optimisation pour déplacer les serveurs interdépendants vers Azure. En savoir plus sur l’outil de découverte et d’évaluation avec l’[analyse des dépendances](concepts-dependency-visualization.md).

La découverte et l’évaluation utilisent une [appliance Azure Migrate](migrate-appliance.md) légère que vous déployez localement.

- L’appliance s’exécute sur une machine virtuelle ou un serveur physique. Vous pouvez l’installer facilement à l’aide d’un modèle téléchargé.
- L’appliance découvre des serveurs locaux. Elle envoie aussi continuellement des métadonnées de serveurs et des données de performances vers Azure Migrate.
- La découverte de l’appliance se fait sans agent. Rien n’est installé sur les serveurs découverts.
- Une fois la découverte d’appliance effectuée, vous pouvez regrouper les serveurs découverts et exécuter des évaluations pour chaque groupe.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate : Outil Server Migration

Azure Migrate : l’outil de migration de serveurs vous aide à migrer des serveurs vers Azure :

**Migrer** | **Détails**
--- | ---
Machines virtuelles VMware locales | Migrez des machines virtuelles vers Azure à l’aide d’une migration sans agent ou basée sur un agent.<br/><br/> Pour une migration sans agent, la migration de serveurs utilise la même appliance que celle utilisée par l’outil de découverte et d’évaluation pour la découverte et l’évaluation des serveurs.<br/><br/> Pour la migration basée sur un agent, Server Migration utilise une appliance de réplication.
Machines virtuelles Hyper-V en local | Migrez des machines virtuelles vers Azure.<br/><br/> Server Migration utilise des agents de fournisseur installés sur l’hôte Hyper-V pour la migration.
Serveurs physiques ou serveurs locaux hébergés sur d’autres clouds | Vous pouvez migrer des serveurs physiques vers Azure. Vous pouvez également migrer d’autres serveurs virtualisés et des machines virtuelles à partir d’autres clouds publics, en les traitant comme des serveurs physiques à des fins de migration. Server Migration utilise une appliance de réplication pour la migration.

## <a name="selecting-assessment-and-migration-tools"></a>Sélection des outils d’évaluation et de migration

Dans le hub Azure Migrate, vous sélectionnez l’outil que vous voulez utiliser pour l’évaluation ou la migration, et vous l’ajoutez ensuite à un projet. Si vous ajoutez un outil d’un éditeur de logiciels indépendant ou Movere :

- Pour commencer, obtenez une licence ou inscrivez-vous pour obtenir un essai gratuit en suivant les instructions de l’outil. Chaque ISV ou outil spécifie la gestion des licences d’outils.
- Chaque outil a une option permettant de se connecter à Azure Migrate. Suivez les instructions de l’outil pour vous connecter.
- Suivez votre migration sur l’ensemble des outils à partir du projet.

## <a name="movere"></a>Movere

Movere est une plateforme SaaS (Software as a Service). Elle améliore les fonctionnalités décisionnelles en présentant avec précision des environnements informatiques complets en une seule journée. Les organisations et entreprises croissent, évoluent et optimisent numériquement. Ce faisant, Movere leur procure la confiance nécessaire pour visualiser et contrôler leurs environnements, quelle que soit la plateforme, l’application ou la géographie.

Microsoft a fait l’[acquisition](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) de Movere, qui aujourd’hui n’est plus vendu en tant qu’offre autonome. Movere est disponible par le biais des programmes « Microsoft Solution Assessment » et « Microsoft Cloud Economics ». [En savoir plus](https://www.movere.io) sur Movere.

Nous vous encourageons à examiner aussi Azure Migrate, notre service de migration intégré. Azure Migrate fournit un hub central pour simplifier votre migration vers le cloud. Le hub prend en charge de manière complète les charges de travail, telles que les serveurs physiques et virtuels, les bases de données et les applications. La visibilité de bout en bout facilite le suivi de la progression tout au long de la découverte, de l’évaluation et de la migration.

Grâce aux outils d’ISV partenaires et Azure intégrés, Azure Migrate offre un large éventail de fonctionnalités, notamment :

- Découverte des serveurs virtuels et physiques
- Dimensionnement correct basé sur les performances
- Planification des coûts
- Évaluations basée sur l’importation
- Analyse des dépendances des applications sans agent

Si vous recherchez des conseils d’experts pour démarrer, laissez-vous guider par le programme [Fournisseurs de services managés Azure Expert](https://azure.microsoft.com/partners) de Microsoft. Consultez le [site web Azure Migrate](https://azure.microsoft.com/services/azure-migrate/).

## <a name="azure-migrate-versions"></a>Versions d’Azure Migrate

Il existe deux versions du service Azure Migrate.

- **Version actuelle** : utilisez cette version pour créer des projets, découvrir des serveurs locaux et orchestrer des évaluations et migrations. [Apprenez-en davantage](whats-new.md) sur les nouveautés de cette version.
- **Version précédente** : la version précédente d’Azure Migrate, également appelée Azure Migrate classique, prend en charge seulement l’évaluation des serveurs locaux s’exécutant sur VMware. Azure Migrate classique sera mis hors service en février 2024. Après février 2024, la version classique d’Azure Migrate ne sera plus prise en charge et les métadonnées d’inventaire dans les projets classiques seront supprimées. Vous ne pouvez pas mettre à niveau des projets ou composants de la version précédente vers la nouvelle version. Vous devez [créer un nouveau projet](create-manage-projects.md), et y [ajouter des outils d’évaluation et de migration](./create-manage-projects.md). Consultez les tutoriels pour comprendre comment utiliser les outils d’évaluation et de migration disponibles. Si vous aviez un espace de travail Log Analytics attaché à un projet classique, vous pouvez l’attacher à un projet de la version actuelle après avoir supprimé le projet classique.

    Pour accéder à des projets existants dans le portail Azure, recherchez et sélectionnez **Azure Migrate**. Le tableau de bord **Azure Migrate** affiche une notification et un lien permettant d’accéder aux anciens projets.

## <a name="next-steps"></a>Étapes suivantes

- Essayez nos tutoriels pour évaluer des [machines virtuelles VMware](./tutorial-discover-vmware.md), des [machines virtuelles Hyper-V](./tutorial-discover-hyper-v.md) ou des [serveurs physiques](./tutorial-discover-physical.md).
- [Revoir les questions fréquemment posées](resources-faq.md) sur Azure Migrate.
