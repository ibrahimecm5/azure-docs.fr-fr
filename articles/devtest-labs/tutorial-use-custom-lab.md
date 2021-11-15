---
title: Accéder à un laboratoire
description: Dans ce tutoriel, vous accédez à un laboratoire dans Azure DevTest Labs. Vous utilisez une machine virtuelle, la refusez, puis la revendiquez.
ms.topic: tutorial
ms.date: 11/03/2021
ms.author: spelluru
ms.openlocfilehash: d5ace346c8f4b853e862b2e12b8b2adee55bab8c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578537"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Didacticiel : accéder à un laboratoire dans Azure DevTest Labs

Dans ce tutoriel, vous utilisez le laboratoire créé dans le [tutoriel : Créer un laboratoire dans Azure DevTest Labs](tutorial-create-custom-lab.md).

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Se connecter à la machine virtuelle de laboratoire
> * Refuser la machine virtuelle de laboratoire
> * Revendiquer la machine virtuelle de laboratoire

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Un [laboratoire dans DevTest Labs avec une machine virtuelle Azure](tutorial-create-custom-lab.md).

## <a name="connect-to-the-lab-vm"></a>Se connecter à la machine virtuelle de laboratoire

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre lab dans **DevTest Labs**.

1. Sous **Mes machines virtuelles**, sélectionnez votre machine virtuelle.

    :::image type="content" source="./media/tutorial-use-custom-lab/my-virtual-machines.png" alt-text="Capture d’écran de la machine virtuelle sous Mes machines virtuelles.":::

1. Dans le menu du haut, sélectionnez **Connexion**. Sélectionnez ensuite le fichier `.rdp` à télécharger sur votre ordinateur.

    :::image type="content" source="./media/tutorial-use-custom-lab/vm-connect.png" alt-text="Capture d’écran du bouton Connexion à la machine virtuelle.":::

1. Dans la boîte de dialogue **Connexion Bureau à distance**, sélectionnez **Connexion**.

1. Dans la boîte de dialogue **Entrer vos informations d’identification**, entrez le mot de passe, puis sélectionnez **OK**.

1. Si vous recevez une boîte de dialogue indiquant que **l’identité de l’ordinateur distant ne peut pas être vérifiée**, cochez la case **Ne plus afficher pour les connexions à cet ordinateur**. Sélectionnez ensuite **Oui**.

    :::image type="content" source="./media/tutorial-use-custom-lab/remote-computer-verification.png" alt-text="Capture d’écran de la vérification de l’ordinateur distant.":::

Pour connaître les étapes de connexion à une machine virtuelle Linux, consultez [Se connecter à une machine virtuelle Linux](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="unclaim-the-lab-vm"></a>Refuser la machine virtuelle de laboratoire

Une fois que vous avez fini d’utiliser la machine virtuelle, refusez-la en procédant comme suit : 

1. Sélectionnez votre machine virtuelle dans DevTest Labs en utilisant les mêmes étapes précédentes.

1. Dans la page de la **machine virtuelle**, dans le menu supérieur, sélectionnez **Refuser**. 

    :::image type="content" source="./media/tutorial-use-custom-lab/virtual-machine-unclaim.png" alt-text="Capture d’écran de l’option Refuser.":::

1. La machine virtuelle est arrêtée avant que la revendication ne cesse. Vous pouvez surveiller l’état de cette opération dans **Notifications**.

1. Fermez la page de la **machine virtuelle** pour revenir à la page **Vue d’ensemble du laboratoire DevTest**.

1. Sous **Mon lab**, sélectionnez **Machines virtuelles pouvant être revendiquées**. La machine virtuelle est désormais disponible pour être revendiquée.

    :::image type="content" source="./media/tutorial-use-custom-lab/claimable-virtual-machines.png" alt-text="Capture d’écran des options sous les machines virtuelles revendicables.":::

## <a name="claim-a-lab-vm"></a>Revendiquer une machine virtuelle de laboratoire

Vous pouvez revendiquer à nouveau la machine virtuelle si vous avez besoin de l’utiliser.

1. Dans la liste des **machines virtuelles pouvant être réclamées**, sélectionnez **...** (points de suspension), puis sélectionnez **Revendiquer la machine**.

    :::image type="content" source="./media/tutorial-use-custom-lab/claimable-virtual-machines-claimed.png" alt-text="Capture d’écran de l’option Revendiquer.":::

1. Vérifiez que la présence de la machine virtuelle dans la liste **Mes machines virtuelles**.

    :::image type="content" source="./media/tutorial-use-custom-lab/my-virtual-machines-2.png" alt-text="Capture d’écran montrant la machine virtuelle renvoyée à Mes machines virtuelles.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez des ressources pour éviter les frais liés à l’exécution du lab et de la machine virtuelle sur Azure. Si vous comptez exécuter le tutoriel suivant pour accéder à la machine virtuelle dans le lab, vous pouvez nettoyer les ressources à la fin de ce tutoriel. Dans le cas contraire, procédez ainsi : 

1. Revenez à la page d’accueil du lab que vous avez créé.

1. Dans le menu du haut, sélectionnez **Supprimer**.

   :::image type="content" source="./media/tutorial-use-custom-lab/portal-lab-delete.png" alt-text="Capture d’écran du bouton Supprimer du lab.":::

1. Dans la page **Voulez-vous vraiment le supprimer**, entrez le nom du lab dans la zone de texte et sélectionnez **Supprimer**.

1. Pendant la suppression, vous pouvez sélectionner **Notifications** en haut de votre écran pour voir la progression. La suppression du lab prend un certain temps. Passez à l’étape suivante une fois le lab supprimé.

1. Si vous avez créé le lab dans un groupe de ressources existant, toutes les ressources de lab ont été supprimées. Si vous avez créé un groupe de ressources pour ce tutoriel, il est maintenant vide et peut être supprimé. Vous ne pouvez pas supprimer le groupe de ressources plus tôt quand le lab y est toujours.
    
## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à accéder à un laboratoire dans Azure DevTest Labs et à l’utiliser. Pour plus d’informations sur l’accès à des machines virtuelles dans un laboratoire et sur leur utilisation, consultez :

> [!div class="nextstepaction"]
> [Procédure : utiliser des machines virtuelles dans un laboratoire](devtest-lab-add-vm.md)
