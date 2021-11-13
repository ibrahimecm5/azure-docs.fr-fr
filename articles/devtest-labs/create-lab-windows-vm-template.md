---
title: Créer un lab dans Azure DevTest Labs à partir d’un modèle Azure Resource Manager
description: Dans ce guide de démarrage rapide, vous créez un labo dans Azure DevTest Labs à l’aide d’un modèle Azure Resource Manager (modèle ARM). Un administrateur de laboratoire définit un laboratoire, crée dans celui-ci des machines virtuelles et configure des stratégies.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/15/2021
ms.openlocfilehash: 0cdc7db0f29a09040b7d40507cfad840b75f0c91
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246880"
---
# <a name="quickstart-use-an-arm-template-to-create-a-lab-in-devtest-labs"></a>Démarrage rapide : Utiliser un modèle ARM pour créer un lab dans DevTest Labs

Dans ce guide de démarrage rapide, vous utilisez un modèle Azure Resource Manager (modèle ARM) pour créer un lab dans Azure DevTest Labs. Le lab contient une machine virtuelle Windows Server 2019 Datacenter.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Dans ce guide de démarrage rapide, vous effectuez les actions suivantes :

> [!div class="checklist"]
> * Vérifiez le modèle.
> * Déployez le modèle.
> * Vérifier le déploiement
> * Supprimer des ressources.

## <a name="prerequisites"></a>Prérequis

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Ce guide de démarrage rapide utilise le modèle [Creates a lab in Azure DevTest Labs with a Windows Server VM](https://azure.microsoft.com/resources/templates/dtl-create-lab-windows-vm/). Le modèle définit les ressources suivantes :

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs)
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [Microsoft.DevTestLab labs/virtualmachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devtestlab/dtl-create-lab-windows-vm/azuredeploy.json":::

Pour obtenir d’autres exemples de modèle ARM, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Déployer le modèle

1. Sélectionnez le bouton **Déployer sur Azure** suivant pour utiliser le modèle ARM. Le modèle ouvre l’écran de création de lab dans le portail Azure :

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Dans l’écran **Creates a lab in Azure DevTest Labs with a Windows Server VM**, indiquez les éléments suivants :

   - **Resource group**, créez un groupe de ressources pour le nettoyer facilement par la suite.
   - **Region**, sélectionnez une localisation pour le groupe de ressources.
   - **Lab Name**, entrez un nom pour la nouvelle instance Lab.
   - **Vm Name**, entrez un nom pour la machine virtuelle à créer. 
   - **User Name**, entrez un nom pour l’utilisateur pouvant accéder à la machine virtuelle. 
   - **Password**, entrez un mot de passe pour l’utilisateur.

1. Sélectionnez **Vérifier + créer**, puis quand la validation réussit, sélectionnez **Créer**.

   :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Capture d’écran de la page Créer un lab.":::

## <a name="validate-the-deployment"></a>Valider le déploiement
1. Pendant le déploiement, vous pouvez sélectionner **Notifications** en haut de votre écran et **Déploiement en cours** pour voir la page **Vue d’ensemble** du déploiement. Le déploiement, en particulier la création d’une machine virtuelle, prend un certain temps.

   :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Capture d’écran montrant le déploiement en cours.":::

1. Une fois le déploiement effectué, sélectionnez **Accéder au groupe de ressources** pour accéder au groupe de ressources de votre lab.

   :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Capture d’écran montrant le déploiement effectué et le bouton Accéder au groupe de ressources.":::

1. La page **Groupe de ressource** liste les ressources du groupe de ressources, notamment votre lab et ses ressources dépendantes, comme les réseaux virtuels et les machines virtuelles. Sélectionnez votre ressource **Lab DevTest** pour accéder à la page **Vue d’ensemble** de votre lab.

1. Dans la page **Vue d’ensemble** de votre lab, sélectionnez **Machines virtuelles pouvant être revendiquées**.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Capture d’écran montrant la page Vue d’ensemble du lab avec le lien Machines virtuelles pouvant être revendiquées.":::

1. Dans la page **Machines virtuelles pouvant être revendiquées**, sélectionnez les points de suspension **Plus d’options** à côté de la machine virtuelle, puis sélectionnez **Revendiquer la machine**.

   :::image type="content" source="./media/create-lab-windows-vm-template/claim-vm.png" alt-text="Capture d’écran montrant la page Machines virtuelles pouvant être revendiquées avec l’option Revendiquer la machine.":::

1. Dans la page **Vue d’ensemble** du lab, vérifiez que la machine virtuelle s’affiche sous **Mes machines virtuelles** avec l’état **En cours de démarrage**. Attendez que l’état passe à **En cours d’exécution**.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-vm.png" alt-text="Capture d’écran montrant la page Vue d’ensemble du lab avec la machine virtuelle listée.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez les ressources du guide de démarrage rapide pour éviter les frais liés à l’exécution du lab et de la machine virtuelle sur Azure. Si vous comptez exécuter le tutoriel suivant pour accéder à la machine virtuelle dans le lab, vous pouvez nettoyer les ressources à la fin de ce tutoriel.

Vous ne pouvez pas supprimer un groupe de ressources contenant un lab. Supprimez d’abord le lab pour pouvoir supprimer le groupe de ressources.

1. Dans la page d’accueil du lab, sélectionnez **Supprimer** dans la barre d’outils.

   :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Capture d’écran montrant le bouton Supprimer dans la page d’accueil du lab.":::

1. Dans la page de confirmation, tapez le nom du lab, puis sélectionnez **Supprimer**.

1. Pendant la suppression, vous pouvez sélectionner **Notifications** en haut de votre écran pour voir la progression. La suppression du lab prend un certain temps.

1. Une fois le lab supprimé, sélectionnez le **Groupe de ressources** dans la page d’accueil du lab. Si la page d’accueil du lab n’est plus disponible, recherchez **Groupes de ressources** dans la zone de recherche Azure, puis sélectionnez le groupe de ressources qui contenait votre lab.

   :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Capture d’écran montrant la confirmation de la suppression dans les notifications.":::

1. Sur la page **Groupe de ressources**, sélectionnez **Supprimer un groupe de ressources** dans la barre d’outils. Dans la page de confirmation, tapez le nom du groupe de ressources et sélectionnez **Supprimer**. Vous pouvez consulter les notifications pour vérifier que le groupe de ressources est supprimé.
 
   :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Capture d’écran montrant le bouton Supprimer le groupe de ressources.":::

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé un lab contenant une machine virtuelle. Pour savoir comment accéder au lab et à la machine virtuelle, passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Didacticiel : Accéder au laboratoire](tutorial-use-custom-lab.md)
