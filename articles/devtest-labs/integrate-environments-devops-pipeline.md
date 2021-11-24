---
title: Intégrer des environnements dans des Pipelines Azure
description: Découvrez comment intégrer des environnements Azure DevTest Labs à vos pipelines d’intégration continue et de livraison continue (CI/CD) Azure DevOps.
ms.topic: how-to
ms.date: 11/15/2021
ms.openlocfilehash: b3478242dddd7141100f05ae44a09902a2546ffd
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488112"
---
# <a name="integrate-environments-into-your-cicd-pipelines"></a>Intégrer des environnements dans vos pipelines CI/CD 
Dans cet article, vous apprendrez à créer et à déployer un environnement, puis à le supprimer, le tout dans un seul et même pipeline. Vous pouvez utiliser cet environnement pour intégrer vos pipelines de mise en production d’intégration continue (CI)/de livraison continue (CD) à Azure DevTest Labs. Pour l’intégration, vous utiliserez l’extension Azure DevTest Labs Tasks dans Azure DevOps Services. Ces extensions facilitent le déploiement rapide d’un [environnement](devtest-lab-test-env.md) pour une tâche de test spécifique, ainsi que la suppression de l’environnement une fois le test terminé. 

Normalement, vous effectuez chacune de ces tâches individuellement dans votre pipeline personnalisé de build-test-déploiement. Les extensions utilisées dans cet article s’ajoutent à ces [tâches de machine virtuelle DTL créer/supprimer](devtest-lab-integrate-ci-cd.md) :

- Créer un environnement
- Supprimer un environnement

## <a name="prerequisites"></a>Prérequis
Avant de pouvoir intégrer votre pipeline CI/CD à Azure DevTest Labs, vous devez : 
1. Installer l’extension [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) à partir de Visual Studio Marketplace. 
1. [Créer un laboratoire](devtest-lab-create-lab.md) et vous assurer qu’il est configuré pour utiliser **Environnement public**, qui est activé par défaut.

## <a name="create-a-release-definition--environment"></a>Créer une définition de mise en production et un environnement
Pour créer la définition de mise en production, procédez comme suit :

1. Dans votre projet Azure DevOps, sélectionnez **Mises en production** sous la section **Pipelines**.
1. Sous l’onglet **Mises en production**, sélectionnez **Nouveau pipeline**.  Dans la fenêtre **Sélectionner un modèle** à droite, vous trouverez la liste des modèles proposés pour les modèles de déploiement courants. 
1. Pour ce pipeline, sélectionnez **Travail vide** pour commencer à créer l’environnement à utiliser à des fins de développement ou de test.
1. Dans le travail vide, sélectionnez **Tâches** dans la barre d’outils, puis sélectionnez **Étape 1**.

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-stage.png" alt-text="Capture d’écran montrant l’ouverture de l’étape de mise en production du pipeline.":::

1. Pour ajouter des tâches à l’étape, sélectionnez le signe plus (+) dans la section **Travail de l’agent**. Une liste de tâches disponibles pouvant faire l’objet d’une recherche s’affiche. 
1. Dans la fenêtre **Ajouter une tâche**, recherchez `Azure DevTest Labs Create Environment`.
1. Sélectionnez la tâche `Azure DevTest Labs Create Environment` dans les résultats, puis sélectionnez **Ajouter**.
1. Sélectionnez la tâche que vous venez d’ajouter. La fenêtre **Créer un environnement Azure DevTest Labs (préversion)** s’affiche.

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-environment.png" alt-text="Capture d’écran montrant les champs nécessaires pour l’environnement Azure Pipelines pour Azure DevTest Labs.":::

2. Sous l’onglet **Tâches**, configurez l’environnement comme suit :

   |Champ|Description|
   |-----|-----------|
   |**Abonnement Azure RM**|Il s’agit de l’abonnement Azure Resource Manager à configurer avant de l’exécuter. Sélectionnez une connexion dans la liste **Connexions au service Azure disponibles**, ou créez une connexion d’autorisations plus limitée à votre abonnement Azure. Pour plus d’informations, consultez [Point de terminaison de service Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).|
   |**Nom du labo**|Sélectionnez le nom d’un laboratoire que vous avez créé précédemment (voir Prérequis). En pratique, vous devez sélectionner le laboratoire sur lequel vous souhaitez effectuer le déploiement. La ressource est créée dans ce laboratoire.  Vous pouvez également utiliser des variables, par exemple `$(labName)`.  Pour éviter les erreurs provoquées par la saisie manuelle des noms conviviaux, sélectionnez les noms dans les listes déroulantes.|
   |**Nom de l’environnement**|Nom de l’environnement à créer dans le laboratoire sélectionné.|
   |**Nom du dépôt**|Nom du référentiel de code source contenant le modèle. Vous pouvez choisir le référentiel par défaut, `Public Environment Repo`, ou un autre référentiel contenant le modèle que vous souhaitez utiliser. Les référentiels sont conçus dans les stratégies de laboratoire.  Pour éviter les erreurs provoquées par la saisie manuelle des noms conviviaux, sélectionnez les noms dans les listes déroulantes.|
   |**Nom du modèle**|Nom du modèle à utiliser pour créer l’environnement. Sélectionnez le nom du modèle d’environnement. Pour éviter les erreurs provoquées par la saisie manuelle des noms conviviaux, sélectionnez les noms dans les listes déroulantes.| 
   |**Nom de l’environnement**|Entrez un nom qui identifie de façon unique l’instance d’environnement dans le laboratoire.  Ce nom doit être unique dans le lab.|
   |**Fichier de paramètres** & **Remplacements de paramètres**|Utilisez-le pour transmettre des paramètres personnalisés à l’environnement. Utilisez-en une, ou les deux, pour définir les valeurs des paramètres. Par exemple, vous pouvez utiliser ces champs pour transmettre le mot de passe chiffré. Vous pouvez également utiliser des variables pour éviter de transmettre des informations confidentielles dans les journaux et même les raccorder à Azure Key Vault.|

## <a name="delete-the-environment"></a>Supprimer l’environnement
La dernière étape consiste à supprimer l’environnement que vous avez déployé dans votre instance Azure DevTest Labs. Normalement, vous supprimez l’environnement après avoir exécuté les tâches de développement ou de test nécessaires sur les ressources déployées.

Dans la définition de mise en production, sélectionnez **Ajouter des tâches** et, sous l’onglet **Déployer**, ajoutez une tâche **Azure DevTest Labs - Supprimer l’environnement**. Configurez-le comme suit :

1. Pour supprimer la machine virtuelle, consultez [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
    1. Pour **Abonnement RM Azure**, sélectionnez une connexion dans la liste **Connexions au service Azure disponibles**, ou créez une connexion d’autorisations plus limitée à votre abonnement Azure. Pour plus d’informations, consultez [Point de terminaison de service Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
    2. Pour **Nom du lab**, sélectionnez le lab dont fait partie l’environnement.
    3. Pour **Nom de l’environnement**, entrez le nom de l’environnement à supprimer.
2. Entrez un nom pour la définition de mise en production, puis enregistrez-le.

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants : 
- [Créer des environnements de plusieurs machines virtuelles avec les modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
- Modèles Resource Manager de démarrage rapide pour l’automatisation de DevTest Labs, disponibles dans le [dépôt DevTest Labs sur GitHub](https://github.com/Azure/azure-quickstart-templates).
- [Page sur la résolution des problèmes avec VSTS](/azure/devops/pipelines/troubleshooting)
