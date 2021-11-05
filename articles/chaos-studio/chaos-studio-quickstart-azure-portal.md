---
title: Créer et exécuter une expérience de chaos avec Azure chaos Studio
description: Comprendre les étapes de création et d’exécution d’une expérience Chaos Studio en 10 minutes
services: chaos-studio
author: prashabora
ms.topic: article
ms.date: 10/21/2021
ms.author: prashabora
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: a5635307b3b736f535dd21f54b3c2b1ec326e5b0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097045"
---
# <a name="quickstart-create-and-run-a-chaos-experiment-using-azure-chaos-studio"></a>Démarrage rapide : Créer et exécuter une expérience de chaos avec Azure Chaos Studio 
Commencez à utiliser Chaos Studio en vous servant d’une expérience de service direct d’arrêt de machine virtuelle pour renforcer la résilience de votre service face à cette défaillance en situation réelle. 

## <a name="prerequisites"></a>Prérequis
- Ouvrez le [portail Azure](https://portal.azure.com).
- une machine virtuelle. Si vous ne disposez pas d’une machine virtuelle, vous pouvez [suivre ces étapes pour en créer une](../virtual-machines/linux/quick-create-portal.md).

## <a name="enable-chaos-studio-on-the-virtual-machine-you-created"></a>Activer Chaos Studio sur la machine virtuelle que vous avez créée
1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Recherchez **Chaos Studio (version préliminaire)** dans la barre de recherche.
3. Cliquez sur **Cibles** et accédez à votre machine virtuelle créée.

4. Cochez la case en regard de la machine virtuelle que vous avez créée et cliquez sur **Activer les cibles**, puis sur **Activer les cibles de service direct** dans le menu déroulant.

   ![Vue des cibles dans le portail Azure](images/quickstart-virtual-machine-enabled.png)

5. Une notification s’affiche pour indiquer que la ou les ressources sélectionnées ont été activées correctement.
   
   ![Notification indiquant que la cible a été activée correctement](images/tutorial-service-direct-targets-enable-confirm.png)

## <a name="create-an-experiment"></a>Créer une expérience

1. Cliquez sur **Expériences**.                
   ![Accéder à l’expérience](images/quickstart-left-experiment.png)

2. Cliquez sur **Ajouter une expérience**.

   ![Ajouter une expérience dans le portail Azure](images/add-an-experiment.png)

3. Renseignez les champs **Abonnement**, **Groupe de ressources** et **Emplacement**, là où vous souhaitez déployer l’expérience de chaos. Donnez un **nom** à votre expérience. Cliquez sur **Suivant : Concepteur d’expériences >**

   ![Ajouter des bases d’expérience](images/quickstart-service-direct-add-basics.png)

4. Vous êtes maintenant dans le concepteur d’expérience Chaos Studio. Donnez un nom convivial à votre **étape** et votre **branche**, puis cliquez sur **Ajouter une erreur**.

   ![Concepteur d’expériences](images/quickstart-service-direct-add-designer.png)

5. Sélectionnez **Arrêt de la machine virtuelle** dans la liste déroulante, puis renseignez la **Durée** en indiquant le nombre de minutes pendant lesquelles vous souhaitez que dure la défaillance. 

   ![Propriétés des erreurs](images/quickstart-service-direct-add-fault.png)

6. Cliquez sur **Suivant : Ressources cibles >** .
   ![Ajouter une cible](images/quickstart-service-direct-add-targets.png)

7. Cliquez sur **Add**.

   ![Addt](images/quickstart-add-target.png)

8. Vérifiez la justesse de votre expérience, puis cliquez sur **Vérifier + créer**, puis sur **Créer**.

   ![créer l’expérience](images/quickstart-review-and-create.png)

## <a name="give-experiment-permission-to-your-virtual-machine"></a>Accorder une autorisation d’expérience à votre machine virtuelle
1. Accédez à votre machine virtuelle, puis cliquez sur **Contrôle d’accès (IAM).** 
   ![Ajouter une attribution de rôle](images/quickstart-access-control.png).
2. Cliquez sur **Ajouter**

   ![Bouton Ajouter](images/add.png)

3. Cliquez sur **Ajouter une attribution de rôle**.

   ![Bouton Ajouter une attribution de rôle](images/add-role-assignment.png)

4. Recherchez **Contributeur de machine virtuelle** et sélectionnez le rôle. Cliquez sur **Suivant**.

   ![Choisir le rôle de la machine virtuelle](images/quickstart-virtual-machine-contributor.png)
5. Cliquez sur **Sélectionner des membres** et recherchez le nom de votre expérience. Sélectionnez votre expérience, puis cliquez sur **Sélectionner**. 
   ![sélectionner l’expérience](images/quickstart-select-experiment-role-assignment.png)
 
6. Cliquez sur **Vérifier + attribuer**, puis sur **Vérifier + attribuer**.



## <a name="run-the-chaos-experiment"></a>Exécuter l’expérience de chaos

1. Ouvrez le portail Azure :
    * Si vous utilisez un compte @microsoft.com, [cliquez sur ce lien](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Si vous utilisez un compte externe, [cliquez sur ce lien](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
2. Cochez la case en regard du nom de l’expérience, puis cliquez sur **Démarrer l’expérience**.
    ![Démarrer l’expérience](images/quickstart-experiment-start.png)

3. Cliquez sur **Oui** pour confirmer le démarrage de l’expérience de chaos.

    ![Confirmer que vous souhaitez démarrer l’expérience](images/start-experiment-confirmation.png)
4. (Facultatif) Cliquez sur le nom de l’expérience pour afficher une vue détaillée de l’état d’exécution de l’expérience.


## <a name="clean-up-resources"></a>Nettoyer les ressources

1. Cochez la case en regard du nom de l’expérience, puis cliquez sur **Supprimer**.

   ![Sélectionner l’expérience à supprimer](images/quickstart-delete-experiment.png)

2. Cliquez sur **Oui** pour confirmer la suppression de l’expérience.

3. Recherchez la machine virtuelle que vous avez créée dans la barre de recherche du portail Azure.

   ![Sélection de la machine virtuelle](images/quickstart-cleanup.png)

4. Cliquez sur **Supprimer** pour éviter d’être facturé pour la ressource.

   ![supprimer la machine virtuelle](images/quickstart-cleanup-virtual-machine.png)


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez exécuté une expérience de service direct d’arrêt de machine virtuelle, vous êtes prêt à :
- [Créer une expérience qui utilise des erreurs basées sur un agent](chaos-studio-tutorial-agent-based.md)
