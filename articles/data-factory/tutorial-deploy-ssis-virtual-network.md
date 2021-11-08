---
title: Tutoriel pour configurer un runtime Azure-SSIS Integration Runtime de façon à le joindre à un réseau virtuel
description: Découvrez comment configurer un runtime Azure-SSIS Integration Runtime de façon à le joindre à un réseau virtuel.
author: swinarko
ms.author: sawinark
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
ms.openlocfilehash: 8ee28e9c310002c0a0b2e29a77b35c71045c9fc4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040855"
---
# <a name="configure-azure-ssis-integration-runtime-to-join-a-virtual-network"></a>Configuration d’un runtime Azure-SSIS Integration Runtime de façon à le joindre à un réseau virtuel

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ce tutoriel présente la procédure minimale à suivre sur le Portail Azure et l’interface utilisateur (IU) Azure Data Factory (ADF) pour configurer un runtime Azure-SSIS (SQL Server Integration Services) Integration Runtime (IR) de façon à le joindre à un réseau virtuel suivant la méthode d’injection rapide.  Procédez comme suit :

1. Configurez un réseau virtuel pour la méthode d’injection rapide sur le Portail Azure.
1. Joignez votre runtime Azure-SSIS IR au réseau virtuel suivant la méthode d’injection rapide au moyen de l’IU ADF.

## <a name="prerequisites"></a>Prérequis

- **Runtime Azure-SSIS IR** : si vous n’en possédez pas déjà un, commencez par [créer un runtime Azure-SSIS IR au moyen de l’IU ADF](tutorial-deploy-ssis-packages-azure.md).

- **Réseau virtuel** : si vous n’en possédez pas déjà un, commencez par [créer un réseau virtuel au moyen du Portail Azure](../virtual-network/quick-create-portal.md). Veillez à respecter les critères suivants :
  - Il n’existe aucun verrou de ressource dans votre réseau virtuel.
  - L’utilisateur qui crée le runtime Azure-SSIS IR dispose des autorisations de contrôle d’accès en fonction du rôle (RBAC, Role-Based Access Control) nécessaires pour être joint à votre réseau virtuel ou votre sous-réseau. Consultez la section [Sélection des autorisations relatives aux réseaux virtuels](azure-ssis-integration-runtime-express-virtual-network-injection.md#perms).

Les configurations de réseau virtuel suivantes ne sont pas abordées dans ce tutoriel :

- Vous utilisez une adresse IP publique statique pour le runtime Azure-SSIS IR.
- Vous utilisez votre propre serveur DNS (Domain Name System).
- Vous utilisez un groupe de sécurité réseau (NSG, Network Security Group).
- Vous utilisez des itinéraires définis par l’utilisateur (UDR, User-Defined Route).

Si vous souhaitez suivre ces étapes facultatives, consultez l’article [Méthode d’injection rapide de réseau virtuel](azure-ssis-integration-runtime-express-virtual-network-injection.md).

## <a name="configure-a-virtual-network"></a>Configurer un réseau virtuel

Utilisez le Portail Azure pour configurer un réseau virtuel avant de tenter d’y joindre votre runtime Azure-SSIS IR.

1. Démarrez Microsoft Edge ou Google Chrome. À l’heure actuelle, seuls ces navigateurs Web prennent en charge l’IU ADF.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels**.

1. Filtrez et sélectionnez votre réseau virtuel dans la liste.

1. Dans le menu de gauche, sélectionnez **Réseaux** :

   - Veillez à ce qu’il existe un sous-réseau auquel vous pouvez joindre votre runtime Azure-SSIS IR. Consultez la section [Sélection d’un sous-réseau](azure-ssis-integration-runtime-express-virtual-network-injection.md#subnet).

   - Veillez à ce que le sous-réseau sélectionné soit délégué à Azure Batch. Consultez la section [Délégation d’un sous-réseau à Azure Batch](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet).

1. Vérifiez que *Microsoft.Batch* constitue un fournisseur de ressources inscrit dans l’abonnement Azure comportant le réseau virtuel auquel votre runtime Azure-SSIS IR doit être joint. Pour des instructions détaillées, consultez la section [Inscription d’Azure Batch comme fournisseur de ressources](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

## <a name="join-azure-ssis-ir-to-the-virtual-network"></a>Jonction du runtime Azure-SSIS IR à un réseau virtuel

Maintenant que vous avez configuré un réseau virtuel, vous pouvez y joindre votre runtime Azure-SSIS IR :

1. Démarrez Microsoft Edge ou Google Chrome. À l’heure actuelle, seuls ces navigateurs Web prennent en charge l’IU ADF.

1. Dans le menu de gauche du [Portail Azure](https://portal.azure.com), sélectionnez **Fabriques de données**. Si vous ne voyez pas **Fabriques de données** dans le menu, sélectionnez **Autres services**, puis sélectionnez **Fabriques de données** dans la section **INTELLIGENCE + ANALYSE**.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="Liste de fabriques de données":::

1. Sélectionnez votre fabrique ADF avec Azure-SSIS IR dans la liste. La page d’accueil correspondante apparaît. Sélectionnez la vignette **Créer et surveiller**. L’IU ADF s’affiche sous un onglet distinct.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="Page d’accueil Data Factory":::

1. Dans l’IU ADF, basculez vers l’onglet **Modifier**, sélectionnez **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration**.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="Onglet &quot;Runtimes d’intégration&quot;":::

1. Si votre runtime d’intégration Azure-SSIS IR est en cours d’exécution, dans la liste **Runtimes d’intégration**, au niveau de la colonne **Actions**, sélectionnez le bouton **Arrêter** pour votre runtime d’intégration Azure-SSIS IR. Vous ne pouvez pas modifier un runtime d’intégration Azure-SSIS IR tant que vous ne l’arrêtez pas.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="Arrêter le runtime d’intégration":::

1. Dans la liste **Runtimes d’intégration**, au niveau de la colonne **Actions**, sélectionnez le bouton **Modifier** pour votre runtime d’intégration Azure-SSIS IR.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="Modifier le runtime d’intégration":::

1. Dans le volet **Configuration du runtime d’intégration**, parcourez les pages **Paramètres généraux** et **Paramètres de déploiement** en sélectionnant le bouton **Suivant**.

1. Sur la page **Paramètres avancés**, procédez comme suit.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet-express.png" alt-text="Paramètres avancés d’injection rapide de réseau virtuel":::

   1. Activez la case à cocher **Sélectionner un réseau virtuel auquel joindre votre Azure-SSIS Integration Runtime, autoriser ADF à créer certaines ressources réseau, et éventuellement apporter vos propres adresses IP publiques statiques**.

   1. Dans **Abonnement**, sélectionnez l’abonnement Azure possédant votre réseau virtuel.

   1. Pour **Emplacement**, sélectionnez le même emplacement que celui de votre runtime d’intégration.

   1. Pour **Type**, sélectionnez le type de votre réseau virtuel : **Réseau virtuel Azure Resource Manager** ou Réseau virtuel Classic. Nous vous recommandons de sélectionner **Réseau virtuel Azure Resource Manager**, car le réseau virtuel Classic sera bientôt déconseillé.

   1. Pour **Nom du réseau virtuel**, sélectionnez le nom de votre réseau virtuel. Il doit en principe s’agir du réseau ayant servi à configurer un point de terminaison de service de réseau virtuel ou un point de terminaison privé pour votre serveur Azure SQL Database qui héberge SSISDB. Ce peut également être le réseau joint par votre serveur Azure SQL Managed Instance qui héberge SSISDB. Il peut également s’agir de celui connecté à votre réseau local.

   1. Pour **Nom du sous-réseau**, sélectionnez le nom du sous-réseau de votre réseau virtuel. Il doit en principe s’agir du réseau ayant servi à configurer un point de terminaison de service de réseau virtuel pour votre serveur Azure SQL Database qui héberge SSISDB. Ce peut également être un autre réseau que celui qui a été joint par votre serveur Azure SQL Managed Instance qui héberge SSISDB.

   1. Pour **Méthode d’injection de réseau virtuel**, sélectionnez **Rapide** pour choisir la méthode d’injection rapide de réseau virtuel.

   1. Sélectionnez **Validation de réseau virtuel**. Si la validation réussit, sélectionnez **Continuer**.

1. Sur la page **Résumé**, passez en revue tous les paramètres de votre runtime Azure-SSIS IR, puis sélectionnez **Mettre à jour**.

1. Lancez votre runtime Azure-SSIS IR en sélectionnant le bouton **Démarrer** situé dans la colonne **Actions** de votre runtime Azure-SSIS IR. Le démarrage de votre runtime Azure-SSIS IR joint à un réseau virtuel avec la méthode d’injection rapide prend environ 5 minutes.

## <a name="next-steps"></a>Étapes suivantes

- [Configuration d’un réseau virtuel pour injecter un runtime Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Méthode d’injection rapide de réseau virtuel](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Jonction d’un runtime Azure-SSIS IR à un réseau virtuel avec l’IU ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Jonction d’un runtime Azure-SSIS IR à un réseau virtuel avec Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 

- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise le serveur Azure SQL Database pour héberger SSISDB. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il donne des instructions sur pour utiliser un serveur Azure SQL Database configuré avec un point de terminaison de service de réseau virtuel/une règle de pare-feu IP/un point de terminaison privé ou une instance Azure SQL Managed Instance jointe à un réseau virtuel pour héberger SSISDB. Il vous montre comment joindre votre runtime Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
