---
title: Créer un lab dans Azure DevTest Labs à partir d’un modèle Azure Resource Manager
description: Dans ce guide de démarrage rapide, vous créez un labo dans Azure DevTest Labs à l’aide d’un modèle Azure Resource Manager (modèle ARM). Un administrateur de laboratoire définit un laboratoire, crée dans celui-ci des machines virtuelles et configure des stratégies.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/29/2021
ms.openlocfilehash: 7d6a1390c935d68b978146d9eb06e8d2681e50c2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847502"
---
# <a name="quickstart-use-an-arm-template-to-create-a-lab-in-devtest-labs"></a>Démarrage rapide : Utiliser un modèle ARM pour créer un lab dans DevTest Labs

Ce guide de démarrage rapide décrit comment utiliser un modèle Azure Resource Manager (modèle ARM) pour créer un labo dans Azure DevTest Labs. Le lab contient une machine virtuelle Windows Server 2019 Datacenter.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm-claimed%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.devtestlab/dtl-create-lab-windows-vm-claimed/azuredeploy.json":::

Trois ressources Azure sont définies dans le modèle :

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs) : crée un labo DevTest Labs.
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks) : crée un réseau virtuel DevTest Labs.
- [Microsoft.DevTestLab labs/virtualmachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines) : crée une machine virtuelle DevTest Labs.

Pour obtenir d’autres exemples de modèle ARM, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Déployer le modèle

1. Sélectionnez le bouton **Déployer sur Azure** ci-dessous pour vous connecter à Azure et ouvrir le modèle Resource Manager.

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.devtestlab%2Fdtl-create-lab-windows-vm-claimed%2Fazuredeploy.json)

1. Entrez ou sélectionnez les valeurs suivantes :

    |Propriété | Description |
    |---|---|
    |Abonnement| Dans la liste déroulante, sélectionnez l’abonnement Azure à utiliser pour le lab.|
    |Resource group| Dans la liste déroulante, sélectionnez votre groupe de ressources existant ou **Créer**.|
    |Région | La valeur est renseignée automatiquement à l’aide de l’emplacement utilisé pour le groupe de ressources.|
    |Nom du labo| Entrez un nom de labo unique pour l’abonnement.|
    |Emplacement| Laissez la valeur actuelle. |
    |Nom de la machine virtuelle| Entrez un nom de machine virtuelle unique pour l’abonnement.|
    |Taille de machine virtuelle | Laissez la valeur actuelle. |
    |User Name | Entrez un nom d’utilisateur pour la machine virtuelle.|
    |Mot de passe| Entrez un mot de passe entre 8 et 123 caractères.|

   :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Capture d’écran de la page Créer un lab.":::

1. Sélectionnez **Vérifier + créer**, puis quand la validation réussit, sélectionnez **Créer**. Vous accédez alors à la page **Vue d’ensemble** du déploiement, où vous pouvez superviser la progression. Les durées des déploiements varient en fonction du matériel, de l’image de base et des artefacts sélectionnés. La durée du déploiement des configurations utilisées dans ce modèle est d’environ 12 minutes.

## <a name="validate-the-deployment"></a>Valider le déploiement

1. Une fois le déploiement effectué, sélectionnez **Accéder à la ressource** depuis la page **Vue d’ensemble** du modèle ou depuis **Notifications**.

   :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Capture d’écran montrant le déploiement effectué et le bouton Accéder au groupe de ressources.":::

1. La page **Groupe de ressource** liste les ressources du groupe de ressources, notamment votre lab et ses ressources dépendantes, comme les réseaux virtuels et les machines virtuelles. Sélectionnez votre ressource **Lab DevTest** pour accéder à la page **Vue d’ensemble** de votre lab.

   :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-overview.png" alt-text="Capture d’écran de la vue d’ensemble du groupe de ressources.":::

1. Dans la page **Vue d’ensemble** de votre labo, vous pouvez voir votre machine virtuelle sous la section **Mes machines virtuelles**.

   :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Capture d’écran montrant la page Vue d’ensemble du labo avec la machine virtuelle.":::

1. Revenez en arrière et listez les groupes de ressources pour votre abonnement. Notez que le déploiement a créé un groupe de ressources pour y placer la machine virtuelle. La syntaxe est le nom du labo + nom de la machine virtuelle + nombres aléatoires. Avec les valeurs utilisées dans cet article, le nom généré automatiquement est `MyOtherLab-myVM-173385`.

   :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-list.png" alt-text="Capture d’écran de la liste des groupes de ressources.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez des ressources pour éviter les frais liés à l’exécution du lab et de la machine virtuelle sur Azure. Si vous comptez exécuter le tutoriel suivant pour accéder à la machine virtuelle dans le lab, vous pouvez nettoyer les ressources à la fin de ce tutoriel. Dans le cas contraire, procédez ainsi : 

1. Revenez à la page d’accueil du lab que vous avez créé.

1. Dans le menu du haut, sélectionnez **Supprimer**.

   :::image type="content" source="./media/create-lab-windows-vm-template/portal-lab-delete.png" alt-text="Capture d’écran du bouton Supprimer du lab.":::

1. Dans la page **Voulez-vous vraiment le supprimer**, entrez le nom du lab dans la zone de texte et sélectionnez **Supprimer**.

1. Pendant la suppression, vous pouvez sélectionner **Notifications** en haut de votre écran pour voir la progression. La suppression du lab prend un certain temps. Passez à l’étape suivante une fois le lab supprimé.

1. Si vous avez créé le lab dans un groupe de ressources existant, toutes les ressources de lab ont été supprimées. Si vous avez créé un groupe de ressources pour ce tutoriel, il est maintenant vide et peut être supprimé. Vous ne pouvez pas supprimer le groupe de ressources plus tôt quand le lab y est toujours.

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé un lab contenant une machine virtuelle. Pour savoir comment accéder au lab et à la machine virtuelle, passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Didacticiel : Accéder au laboratoire](tutorial-use-custom-lab.md)
