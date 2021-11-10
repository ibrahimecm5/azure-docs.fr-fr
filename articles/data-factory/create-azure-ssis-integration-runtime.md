---
title: Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory
description: Découvrez comment créer un runtime d’intégration Azure-SSIS dans Azure Data Factory afin de pouvoir déployer et exécuter des packages SSIS dans Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 70e745d18699c6a1acb707528ec38641a1fe7a02
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842888"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article décrit les différentes étapes d’approvisionnement d’un runtime d’intégration (IR) Azure-SQL Server Integration Services (SSIS) dans Azure Data Factory (ADF). Un IR Azure-SSIS prend en charge :

- L’exécution de packages déployés dans le catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database/Managed Instance (modèle de déploiement de projet)
- L’exécution de packages déployés dans le système de fichiers, Azure Files ou une base de données SQL Server (MSDB) hébergée par Azure SQL Managed Instance (modèle de déploiement de package)

Une fois qu’un IR Azure-SSIS est provisionné, vous pouvez utiliser des outils familiers pour déployer et exécuter vos packages dans Azure. Ces outils sont déjà compatibles avec Azure et incluent SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) et des utilitaires en ligne de commande comme [dtutil](/sql/integration-services/dtutil-utility) et [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Le tutoriel [Provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory](./tutorial-deploy-ssis-packages-azure.md) montre comment créer un IR Azure-SSIS à l’aide du portail Azure ou de l’application Data Factory. Ce tutoriel montre également comment utiliser, si vous le souhaitez, un serveur Azure SQL Database ou une instance managée pour héberger le catalogue SSISDB. Cet article s’appuie sur le tutoriel et explique comment effectuer les tâches facultatives suivantes :

- Utiliser un serveur Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger SSISDB. Vous devez au préalable configurer les autorisations et paramètres de réseau virtuel pour que votre runtime d’intégration Azure-SSIS soit joint à un réseau virtuel.

- Utiliser l’authentification Azure Active Directory (Azure AD) avec l’identité managée affectée par l'utilisateur/le système qui a été spécifiée pour la connexion de la fabrique de données à un serveur Azure SQL Database ou à une instance managée. Vous devez au préalable ajouter cette identité managée en tant qu’utilisateur de base de données autorisé à créer une instance SSISDB.

- Joindre votre IR Azure-SSIS à un réseau virtuel ou configurer un IR auto-hébergé en tant que proxy pour votre IR Azure-SSIS afin d’accéder aux données locales.

Ces articles montrent comment configurer un Azure-SSIS IR à l’aide du [portail Azure](create-azure-ssis-integration-runtime-portal.md), d’[Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md) et d’un [modèle Azure Resource Manager](create-azure-ssis-integration-runtime-resource-manager-template.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Abonnement Azure**. Si vous n’avez pas encore d’abonnement, vous pouvez créer un compte d’[essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

- **Serveur Azure SQL Database ou SQL Managed Instance (facultatif)** . Si vous n'avez pas encore de serveur de base de données ou d'instance gérée, créez-en un(e) sur le portail Azure avant de commencer. Data Factory crée à son tour une instance SSISDB sur ce serveur de base de données. 

  Nous vous recommandons de créer le serveur de base de données ou l'instance gérée dans la même région Azure que le runtime d'intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux des exécutions dans SSISDB sans dépasser les régions Azure.

  Gardez à l’esprit les points suivants :

  - L'instance SSISDB peut être créée pour vous en tant que base de données unique, dans le cadre d’un pool élastique, ou dans une instance gérée. Elle peut être accessible dans un réseau public ou en la joignant à un réseau virtuel. Pour savoir comment choisir entre SQL Database et SQL Managed Instance pour héberger SSISDB, consultez la section [Comparer SQL Database et SQL Managed Instance](#comparison-of-sql-database-and-sql-managed-instance) de cet article. 
  
    Si vous utilisez un serveur Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel ou SQL Managed Instance avec un point de terminaison privé pour héberger le catalogue SSISDB, ou si vous avez besoin d'accéder à des données locales sans configurer de runtime d'intégration (IR) auto-hébergé, vous devez joindre votre Azure-SSIS IR à un réseau virtuel. Pour plus d’informations, consultez [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](./join-azure-ssis-integration-runtime-virtual-network.md).

  - Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur de base de données. Ce paramètre ne s'applique pas lorsque vous utilisez un serveur Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel ou SQL Managed Instance avec un point de terminaison privé pour héberger le catalogue SSISDB. Pour plus d’informations, consultez [Sécuriser Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Pour savoir comment activer ce paramètre à l’aide de PowerShell, consultez [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Ajoutez l’adresse IP de l’ordinateur client ou une plage d’adresses IP qui inclut l’adresse IP de l’ordinateur client à la liste d’adresses IP client dans les paramètres de pare-feu du serveur de base de données. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données Azure SQL Database](../azure-sql/database/firewall-configure.md).

  - Vous pouvez vous connecter au serveur de base de données à l’aide de l’authentification SQL avec vos informations d’identification d’administrateur de serveur ou à l’aide de l’authentification Azure AD avec l’identité managée affectée par le système ou l’utilisateur et spécifiée pour votre fabrique de données. Pour cette dernière, vous devez ajouter l’identité managée affectée par le système ou l’utilisateur et spécifiée pour votre fabrique de données à un groupe Azure AD avec autorisations d’accès au serveur de base de données. Pour plus d’informations, consultez [Activer l’authentification Azure AD pour le runtime d’intégration (IR) Azure-SSIS](./enable-aad-authentication-azure-ssis-ir.md).

  - Vérifiez que votre serveur de base de données ne dispose pas déjà d’une instance SSISDB. Le provisionnement d’un IR Azure-SSIS ne prend pas en charge l’utilisation d’une instance SSISDB existante.

- **Réseau virtuel Azure Resource Manager (facultatif)** . Vous devez disposer d’un réseau virtuel Azure Resource Manager si au moins l’une des conditions suivantes est remplie :

  - Vous hébergez le catalogue SSISDB sur un serveur Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé.

  - Vous souhaitez vous connecter à des magasins de données locaux à partir de packages SSIS en cours d’exécution sur votre IR Azure-SSIS sans configurer d’IR auto-hébergé.

- **Azure PowerShell (facultatif)** . Suivez les instructions de la rubrique [Guide pratique pour installer et configurer Azure PowerShell](/powershell/azure/install-az-ps) si vous souhaitez exécuter un script PowerShell pour provisionner votre runtime d’intégration Azure-SSIS.

### <a name="regional-support"></a>Prise en charge régionale

Pour obtenir la liste des régions Azure dans lesquelles Data Factory et un IR Azure-SSIS sont disponibles, consultez la page sur la [disponibilité d’Azure Data Factory et d’un IR Azure-SSIS par région](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Comparaison entre SQL Database et SQL Managed Instance

Le tableau suivant compare certaines fonctionnalités d'un serveur Azure SQL Database et de SQL Managed Instance concernant l'IR Azure-SSIS :

| Fonctionnalité | SQL Database| SQL Managed Instance |
|---------|--------------|------------------|
| **Planification** | L’agent SQL Server n’est pas disponible.<br/><br/>Consultez [Planifier un package dans le cadre d’un pipeline Azure Data Factory](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| L’agent Managed Instance est disponible. |
| **Authentification** | Vous pouvez créer une instance SSISDB avec un utilisateur de base de données autonome représentant un groupe Azure AD avec l’identité managée de votre fabrique de données en tant que membre du rôle **db_owner**.<br/><br/>Consultez [Activer l’authentification Azure AD pour créer un SSISDB sur le serveur Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-database). | Vous pouvez créer une instance SSISDB avec un utilisateur de base de données autonome représentant l’identité managée de votre fabrique de données. <br/><br/>Consultez [Activer l’authentification Azure AD pour créer un SSISDB dans Azure SQL Managed Instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-managed-instance). |
| **Niveau de service** | Quand vous créez un IR Azure-SSIS avec votre serveur Azure SQL Database, vous pouvez sélectionner le niveau de service pour SSISDB. Il existe plusieurs niveaux de service. | Quand vous créez un IR Azure-SSIS avec votre instance managée, vous ne pouvez pas sélectionner le niveau de service pour SSISDB. Toutes les bases de données dans votre instance managée partagent la même ressource allouée à cette instance. |
| **Réseau virtuel** | Votre Azure-SSIS IR peut joindre un réseau virtuel Azure Resource Manager si vous utilisez un serveur Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel. | Votre Azure-SSIS IR peut joindre un réseau virtuel Azure Resource Manager si vous utilisez une instance managée avec un point de terminaison privé. Le réseau virtuel est requis quand vous n’activez pas de point de terminaison public pour votre instance managée.<br/><br/>Si vous joignez votre IR Azure-SSIS au même réseau virtuel que votre instance managée, vérifiez qu’il est sur un sous-réseau différent de celui de votre instance managée. Si vous joignez votre IR Azure-SSIS à un réseau virtuel différent de celui de votre instance managée, nous recommandons un appairage de réseaux virtuels ou une connexion de réseau à réseau. Consultez [Connecter votre application à Azure SQL Database Managed Instance](../azure-sql/managed-instance/connect-application-instance.md). |
| **Transactions distribuées** | Cette fonctionnalité est prise en charge par le biais des transactions élastiques. Les transactions MSDTC (Microsoft Distributed Transaction Coordinator) ne sont pas prises en charge. Si vos packages SSIS utilisent MSDTC pour coordonner les transactions distribuées, envisagez la migration vers les transactions élastiques pour Azure SQL Database. Pour plus d’informations, consultez [Transactions distribuées entre bases de données cloud](../azure-sql/database/elastic-transactions-overview.md). | Non pris en charge. |
| | | |


## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment configurer un IR Azure-SSIS à l’aide du portail Azure](create-azure-ssis-integration-runtime-portal.md).
- [Découvrez comment approvisionner un IR Azure-SSIS à l'aide d'Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md).
- [Découvrez comment approvisionner un IR Azure-SSIS à l'aide d'un modèle Azure Resource Manager](create-azure-ssis-integration-runtime-resource-manager-template.md).
- [Déployez et exécutez vos packages SSIS dans Azure Data Factory](create-azure-ssis-integration-runtime-deploy-packages.md).

Consultez les autres rubriques relatives au runtime d’intégration Azure SSIS dans cette documentation :

- [Runtime d’intégration Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations sur les runtimes d’intégration en général, notamment sur le runtime d’intégration Azure-SSIS.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
- [Déployer, exécuter et surveiller des packages SSIS dans Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Se connecter à SSISDB dans Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Planifier les exécutions de packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
