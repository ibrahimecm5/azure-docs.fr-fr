---
title: Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel via Portail Azure
description: Découvrez comment joindre un runtime d’intégration Azure-SSIS à un réseau virtuel via Portail Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15b2a1bbac1d89c74a2fdea2f5ce3af51261b29e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087891"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network-via-azure-portal"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel via Portail Azure

Cet article explique comment joindre votre runtime d’intégration Azure-SQL Server Integration Services (SSIS) existant dans Azure Data Factory (ADF) à un réseau virtuel via Portail Azure ou via l’interface utilisateur d’ADF. 

Avant de joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel, vous devez d’abord configurer correctement le réseau virtuel. Pour cela, consultez l’article [Configuration de réseau virtuel pour un runtime d’intégration Azure-SSIS](azure-ssis-integration-runtime-virtual-network-configuration.md). Ensuite, suivez les étapes de la section ci-dessous qui s’applique au type de votre réseau virtuel (Azure Resource Manager/classique). Enfin, effectuez les étapes de la dernière section pour joindre votre runtime d’intégration Azure SSIS IR au réseau virtuel. 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>Configurer un réseau virtuel Azure Resource Manager

Utilisez Portail Azure pour configurer un réseau virtuel Azure Resource Manager avant de tenter d’y joindre votre runtime d’intégration Azure-SSIS.

1. Démarrez Microsoft Edge ou Google Chrome. À l’heure actuelle, seuls ces navigateurs Web prennent en charge l’IU ADF. 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

1. Sélectionnez **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels**. 

1. Filtrez et sélectionnez votre réseau virtuel dans la liste. 

1. Dans le menu de gauche, sélectionnez **Réseaux** :

   - Veillez à ce qu’il existe un sous-réseau auquel vous pouvez joindre votre runtime Azure-SSIS IR. Consultez la section [Sélection d’un sous-réseau](azure-ssis-integration-runtime-standard-virtual-network-injection.md#subnet).

   - Si vous utilisez la méthode d’injection rapide de réseau virtuel, assurez-vous que le sous-réseau sélectionné est délégué à Azure Batch. Consultez la section [Déléguer un sous-réseau à Azure Batch](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet).

1. Vérifiez que *Microsoft.Batch* constitue un fournisseur de ressources inscrit dans l’abonnement Azure comportant le réseau virtuel auquel votre runtime Azure-SSIS IR doit être joint. Pour des instructions détaillées, consultez la section [Inscription d’Azure Batch comme fournisseur de ressources](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

## <a name="configure-a-classic-virtual-network"></a>Configurer un réseau virtuel classique

Utilisez Portail Azure pour configurer un réseau virtuel classique avant de tenter d’y joindre votre runtime Azure-SSIS. 

1. Démarrez Microsoft Edge ou Google Chrome. À l’heure actuelle, seuls ces navigateurs Web prennent en charge l’IU ADF. 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

1. Sélectionnez **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels (classiques)** . 

1. Filtrez et sélectionnez votre réseau virtuel dans la liste. 

1. Dans la page **Réseau virtuel (classique)** , sélectionnez **Propriétés**. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="ID de ressource de réseau virtuel classique":::

1. Sélectionnez le bouton Copier au niveau d’**ID DE RESSOURCE** pour copier l’ID de ressource du réseau virtuel classique dans le Presse-papiers. Enregistrez dans OneNote ou un fichier l’ID se trouvant dans le Presse-papiers. 

1. Dans le menu de gauche, sélectionnez **Sous-réseaux**. Assurez-vous que le nombre d’adresses IP disponibles dans le sous-réseau sélectionné est supérieur au double du nombre de nœuds Azure-SSIS IR. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="Nombre d’adresses disponibles sur le réseau virtuel":::

1. Joignez **MicrosoftAzureBatch** au rôle **Contributeur de machines virtuelles classiques** pour le réseau virtuel. 

   1. Dans le menu de gauche, sélectionnez **Contrôle d’accès (IAM)** , puis sélectionnez l’onglet **Attributions de rôle**. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="Boutons &quot;Contrôle d’accès&quot; et &quot;Ajouter&quot;":::

   1. Sélectionnez **Ajouter une attribution de rôle**.

   1. Dans la page **Ajouter une attribution de rôle**, sélectionnez **Contributeur de machines virtuelles classiques** pour **Rôle**. Dans la zone **Sélectionner**, collez **ddbf3205-c6bd-46ae-8127-60eb93363864**, puis sélectionnez **Microsoft Azure Batch** dans la liste des résultats de la recherche. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text="Résultats de la recherche dans la page &quot;Ajouter une attribution de rôle&quot;":::

   1. Sélectionnez **Enregistrer** pour enregistrer les paramètres et fermer la page. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="Enregistrer les paramètres d’accès":::

   1. Confirmez que **MicrosoftAzureBatch** apparaît bien dans la liste des contributeurs. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="Confirmer l’accès à Azure Batch":::

1. Vérifiez que *Microsoft.Batch* constitue un fournisseur de ressources inscrit dans l’abonnement Azure comportant le réseau virtuel auquel votre runtime Azure-SSIS IR doit être joint. Pour des instructions détaillées, consultez la section [Inscription d’Azure Batch comme fournisseur de ressources](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch).

## <a name="join-azure-ssis-ir-to-the-virtual-network"></a>Jonction du runtime Azure-SSIS IR à un réseau virtuel

Après avoir configuré un réseau virtuel Azure Resource Manager ou classique, vous pouvez joindre le runtime d’intégration Azure-SSIS au réseau virtuel :

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

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="Paramètres avancés avec un réseau virtuel":::

   1. Activez la case à cocher **Sélectionner un réseau virtuel auquel joindre votre Azure-SSIS Integration Runtime, autoriser ADF à créer certaines ressources réseau, et éventuellement apporter vos propres adresses IP publiques statiques**. 

   1. Dans **Abonnement**, sélectionnez l’abonnement Azure possédant votre réseau virtuel.

   1. Pour **Emplacement**, sélectionnez le même emplacement que celui de votre runtime d’intégration.

   1. Pour **Type**, sélectionnez le type de votre réseau virtuel : **Réseau virtuel Azure Resource Manager** ou Réseau virtuel Classic. Nous vous recommandons de sélectionner **Réseau virtuel Azure Resource Manager**, car le réseau virtuel Classic sera bientôt déconseillé.

   1. Pour **Nom du réseau virtuel**, sélectionnez le nom de votre réseau virtuel. Il doit en principe s’agir du réseau ayant servi à configurer un point de terminaison de service de réseau virtuel ou un point de terminaison privé pour votre serveur Azure SQL Database qui héberge SSISDB. Ce peut également être le réseau joint par votre serveur Azure SQL Managed Instance qui héberge SSISDB. Il peut également s’agir de celui connecté à votre réseau local. Sinon, vous pouvez utiliser n’importe quel réseau virtuel pour apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR.

   1. Pour **Nom du sous-réseau**, sélectionnez le nom du sous-réseau de votre réseau virtuel. Il doit en principe s’agir du réseau ayant servi à configurer un point de terminaison de service de réseau virtuel pour votre serveur Azure SQL Database qui héberge SSISDB. Ou il doit s’agir d’un sous-réseau différent de celui joint par votre Azure SQL Managed Instance qui héberge SSISDB. Sinon, vous pouvez utiliser n’importe quel sous-réseau pour apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR.

   1. Pour la **méthode d’injection de réseau virtuel**, sélectionnez la méthode de votre injection de réseau virtuel : **Express**/**Standard**. 
   
      Pour comparer ces méthodes, consultez l’article [Comparer les méthodes d’injection de réseau virtuel Standard et Express](azure-ssis-integration-runtime-virtual-network-configuration.md#compare). 
   
      Si vous sélectionnez **Express**, consultez l’article [Méthode d’injection de réseau virtuel Express](azure-ssis-integration-runtime-express-virtual-network-injection.md). 
      
      Si vous sélectionnez **Standard**, consultez l’article [Méthode d’injection de réseau virtuel Standard](azure-ssis-integration-runtime-standard-virtual-network-injection.md).  Avec cette méthode, vous pouvez également :

      1. Sélectionner la case à cocher **Apporter des adresses IP publiques statiques pour votre Azure-SSIS Integration Runtime** pour choisir d’apporter vos propres adresses IP publiques statiques pour Azure-SSIS IR, de manière à pouvoir les autoriser sur le pare-feu de vos banques de données.

         Si vous cochez la case, effectuez les étapes suivantes.

         1. Pour **Première adresse IP publique statique**, sélectionnez la première adresse IP publique statique qui [répond aux exigences](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip) de votre Azure-SSIS IR. Si vous n’en avez pas, cliquez sur le lien **Créer** pour créer des adresses IP publiques statiques sur le portail Azure, puis cliquez sur le bouton Actualiser du portail pour pouvoir les sélectionner.
      
         1. Pour **Deuxième adresse IP publique statique**, sélectionnez la deuxième adresse IP publique statique qui [répond aux exigences](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip) de votre Azure-SSIS IR. Si vous n’en avez pas, cliquez sur le lien **Créer** pour créer des adresses IP publiques statiques sur le portail Azure, puis cliquez sur le bouton Actualiser du portail pour pouvoir les sélectionner.

   1. Sélectionnez **Validation de réseau virtuel**. Si la validation réussit, sélectionnez **Continuer**. 

1. Sur la page **Résumé**, passez en revue tous les paramètres de votre runtime Azure-SSIS IR, puis sélectionnez **Mettre à jour**.

1. Lancez votre runtime Azure-SSIS IR en sélectionnant le bouton **Démarrer** situé dans la colonne **Actions** de votre runtime Azure-SSIS IR. Le démarrage de votre runtime Azure-SSIS joint à un réseau virtuel avec la méthode d’injection rapide ou standard prend environ 5 minutes ou 20 à 30 minutes, respectivement. 

## <a name="next-steps"></a>Étapes suivantes

- [Configuration d’un réseau virtuel pour injecter un runtime Azure-SSIS IR](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Méthode d’injection de réseau virtuel Express](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Méthode d’injection de réseau virtuel standard](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Jonction d’un runtime Azure-SSIS IR à un réseau virtuel avec Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 

- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise le serveur Azure SQL Database pour héberger SSISDB. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il donne des instructions sur pour utiliser un serveur Azure SQL Database configuré avec un point de terminaison de service de réseau virtuel/une règle de pare-feu IP/un point de terminaison privé ou une instance Azure SQL Managed Instance jointe à un réseau virtuel pour héberger SSISDB. Il vous montre comment joindre votre runtime Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
