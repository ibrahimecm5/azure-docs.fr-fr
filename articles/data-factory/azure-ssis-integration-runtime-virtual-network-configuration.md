---
title: Configurer un réseau virtuel pour l’injection du runtime d’intégration Azure-SSIS Integration Runtime
description: Découvrez comment configurer un réseau virtuel pour l’injection du runtime d’intégration Azure-SSIS Integration Runtime.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 11/03/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e3683011794e89acbac6bbbb985c7cb85ebd9ebd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475980"
---
# <a name="configure-a-virtual-network-for-injection-of-azure-ssis-integration-runtime"></a>Configurer un réseau virtuel pour l’injection du runtime d’intégration Azure-SSIS Integration Runtime

Quand vous utilisez SSIS (SQL Server Integration Services) dans ADF (Azure Data Factory), il existe deux méthodes pour joindre votre runtime d’intégration Azure-SSIS Integration Runtime à un réseau virtuel : standard et express. La méthode express démarre votre runtime d’intégration Azure-SSIS IR plus rapidement. Elle ne présente aucune exigence de trafic entrant, elle présente moins d’exigences de trafic sortant, mais quelques limitations par rapport à la méthode standard.

## <a name="compare-the-standard-and-express-virtual-network-injection-methods"></a><a name="compare"></a>Comparer les méthodes d’injection de réseau virtuel standard et express

Voici un tableau mettant en évidence les différences entre les méthodes d’injection de réseau virtuel standard et express :

| Comparaison | Injection de réseau virtuel standard | Injection de réseau virtuel express |
|------------|------------------------------------|-----------------------------------|
| **Durée de démarrage du runtime d’intégration Azure-SSIS IR** | Environ 30 minutes. | Environ 5 minutes. | 
| **Paramètres de groupe de ressources et abonnement Azure** | *Microsoft.Batch* doit être inscrit en tant que fournisseur de ressources dans l’abonnement au réseau virtuel.<br/><br/>La création d’une adresse IP publique, d’un équilibreur de charge et d’un groupe de sécurité réseau (NSG) doit être autorisée dans le groupe de ressources de réseau virtuel. | *Microsoft.Batch* doit être inscrit en tant que fournisseur de ressources dans l’abonnement au réseau virtuel. | 
| **Sous-réseau de réseau virtuel** | Le sous-réseau ne doit pas être dédié à d’autres services Azure. | Le sous-réseau ne doit pas être dédié à d’autres services Azure.<br/><br/>Le sous-réseau doit être délégué à *Microsoft.Batch/batchAccounts*. | 
| **Autorisation de réseau virtuel** | L’utilisateur qui crée le runtime d’intégration Azure-SSIS IR doit disposer de l’autorisation _Microsoft.Network/virtualNetworks/\*/join_. | L’utilisateur qui crée le runtime d’intégration Azure-SSIS IR doit disposer de l’autorisation *Microsoft.Network/virtualNetworks/subnets/join/action*. | 
| **Adresses IP publiques statiques** | (Facultatif) Apportez vos propres adresses IP publiques statiques (BYOIP) pour Azure-SSIS IR. | (Facultatif) Configurez la traduction d’adresses réseau (NAT) de réseau virtuel pour configurer une adresse IP publique statique pour Azure-SSIS IR. | 
| **Serveur DNS personnalisé** | Recommandé pour transférer les requêtes DNS non résolues aux programmes de résolution récursifs Azure. | Recommandé pour transférer les requêtes DNS non résolues aux programmes de résolution récursifs Azure.<br/><br/>Nécessite une installation personnalisée standard pour Azure-SSIS IR. | 
| **Trafic entrant** | Les ports *29876, 29877* doivent être ouverts pour le trafic TCP avec l’étiquette de service *BatchNodeManagement* en tant que source. | Non requis. | 
| **Trafic sortant** | Le port *443* doit être ouvert pour le trafic TCP avec l’étiquette de service *AzureCloud* en tant que destination. | Le port *443* doit être ouvert pour le trafic TCP avec l’étiquette de service *DataFactoryManagement* en tant que destination. | 
| **Verrou de ressource** | Non autorisé dans le groupe de ressources. | Non autorisé dans le réseau virtuel. | 
| **Runtimes d’intégration Azure-SSIS IR par réseau virtuel** | Illimité. | Une seule. | 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/standard-express-virtual-network-injection.png" alt-text="Capture d’écran des méthodes d’injection de réseau virtuel standard et express" lightbox="media/join-azure-ssis-integration-runtime-virtual-network/standard-express-virtual-network-injection.png":::

Votre réseau virtuel doit être configuré différemment en fonction de votre méthode d’injection. Si vous utilisez la méthode express, consultez l’article [Méthode d’injection rapide de réseau virtuel](azure-ssis-integration-runtime-express-virtual-network-injection.md). Dans le cas contraire, consultez l’article [Méthode d’injection de réseau virtuel standard](azure-ssis-integration-runtime-standard-virtual-network-injection.md).
  
## <a name="register-azure-batch-as-a-resource-provider"></a><a name="registerbatch"></a>Inscrire Azure Batch en tant que fournisseur de ressources

À l’aide du portail Azure, vous pouvez inscrire Azure Batch, l’infrastructure sous-jacente pour SSIS dans ADF, en tant que fournisseur de ressources dans l’abonnement Azure qui possède le réseau virtuel que le runtime d’intégration Azure-SSIS IR doit joindre. Si vous avez déjà utilisé Azure Batch ou créé le runtime d’intégration Azure-SSIS IR via l’interface utilisateur ADF dans cet abonnement, il est déjà inscrit. Dans le cas contraire, procédez comme suit :

1. Après avoir sélectionné votre réseau virtuel dans le portail Azure, sélectionnez l’abonnement en surbrillance dans la page **Vue d’ensemble**.  

1. Sélectionnez **Fournisseurs de ressources** dans le menu de gauche.

1. Sélectionnez et inscrivez *Microsoft.Batch* le cas échéant.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="Confirmation de l’état &quot;Inscrit&quot;":::

Si *Microsoft.Batch* ne figure pas dans la liste, vous pouvez [créer un compte Azure Batch vide](../batch/batch-account-create-portal.md) dans votre abonnement que vous supprimerez ultérieurement. 

## <a name="delegate-a-subnet-to-azure-batch"></a><a name="delegatesubnet"></a>Déléguer un sous-réseau à Azure Batch

Si vous utilisez l’injection de réseau virtuel express, vous devez déléguer le sous-réseau dans lequel votre runtime d’intégration Azure-SSIS IR sera injecté à Azure Batch, l’infrastructure sous-jacente pour les services SSIS dans ADF. À l’aide du portail Azure, procédez comme suit :

1. Après avoir sélectionné votre réseau virtuel dans le portail Azure, sélectionnez **Sous-réseaux** dans le menu de gauche.

1. Sélectionnez le nom de votre sous-réseau pour ouvrir son volet et assurez-vous qu’il dispose des adresses IP disponibles pour au moins le double du nombre de nœuds de runtime d’intégration Azure-SSIS IR.

1. Sélectionnez *Microsoft.Batch/batchAccounts* dans le menu déroulant **Déléguer le sous-réseau à un service**.

1. Sélectionnez le bouton **Enregistrer**.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/delegate-subnet-to-batch.png" alt-text="Déléguer un sous-réseau à Azure Batch":::

## <a name="grant-virtual-network-permissions"></a><a name="grantperms"></a>Accorder des autorisations de réseau virtuel

À l’aide du portail Azure, vous pouvez accorder à l’utilisateur qui crée le runtime d’intégration Azure-SSIS IR les autorisations RBAC (contrôle d’accès en fonction du rôle) nécessaires pour joindre le sous-réseau/réseau virtuel. Pour ce faire, procédez comme suit :

1. Après avoir sélectionné votre réseau virtuel dans le portail Azure, si vous utilisez l’injection de réseau virtuel standard, vous pouvez sélectionner **Contrôle d’accès (IAM)** dans le menu de gauche. Si vous utilisez l’injection de réseau virtuel express, vous pouvez sélectionner **Sous-réseaux** dans le menu de gauche, sélectionnez la ligne de votre sous-réseau, puis sélectionner **Gestion des utilisateurs** dans le menu supérieur pour ouvrir la page **Access Control**.

1. Sélectionnez le bouton **Ajouter une attribution de rôle** pour ouvrir la page **Ajouter une attribution de rôle**.

1. Sélectionnez *Contributeur de réseau* ou votre rôle personnalisé dans la liste **Rôle**, puis sélectionnez le bouton **Suivant**.

1. Sélectionnez l’option **Utilisateur, groupe ou principal de service** dans la section **Attribuer l’accès à**.

1. Sélectionnez le lien **Sélectionner les membres** pour rechercher et sélectionner l’utilisateur qui crée le runtime d’intégration Azure-SSIS IR.

1. Sélectionnez les boutons **Sélectionner**, **Suivant** et **Vérifier + attribuer**.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/grant-virtual-network-permissions.png" alt-text="Accorder des autorisations de réseau virtuel":::

## <a name="next-steps"></a>Étapes suivantes

- [Méthode d’injection de réseau virtuel express](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [Méthode d’injection de réseau virtuel standard](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Joindre un runtime Azure-SSIS IR à un réseau virtuel via une interface utilisateur ADF](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Joindre un runtime Azure-SSIS IR à un réseau virtuel avec Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 

- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise le serveur Azure SQL Database pour héberger SSISDB. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il donne des instructions sur l’utilisation d’un serveur Azure SQL Database configuré avec un point de terminaison de service de réseau virtuel/une règle de pare-feu IP/un point de terminaison privé ou une instance Azure SQL Managed Instance jointe à un réseau virtuel pour héberger SSISDB. Il vous montre comment joindre votre runtime Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
