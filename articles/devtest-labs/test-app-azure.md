---
title: Guide pratique pour tester votre application dans Azure
description: Apprenez à déployer des applications de bureau ou web dans un partage de fichiers et à les tester.
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: af9fc320c6f08e0ef4141aac8076e121a2e6292d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131575938"
---
# <a name="test-your-app-in-azure"></a>Tester votre application dans Azure 

Dans ce guide, vous allez apprendre à tester votre application dans Azure à l’aide de DevTest Labs. Vous utilisez Visual Studio pour déployer votre application sur un partage de fichiers Azure. Vous accéderez ensuite au partage à partir d’une machine virtuelle de laboratoire.  

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Une station de travail locale dotée de [Visual Studio](https://visualstudio.microsoft.com/free-developer-offers/).

- Un laboratoire dans [DevTest Labs](devtest-lab-overview.md).

- Une [machine virtuelle Azure](devtest-lab-add-vm.md) exécutant Windows dans votre laboratoire.

- Un [partage de fichiers](../storage/files/storage-how-to-create-file-share.md) dans le compte de stockage Azure existant de votre laboratoire. Un compte de stockage est automatiquement créé avec un laboratoire.

- Le [partage de fichiers Azure monté](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share) sur votre station de travail locale et votre machine virtuelle de laboratoire.

## <a name="publish-your-app-from-visual-studio"></a>Publier votre application à partir de Visual Studio

Dans cette section, vous publiez votre application à partir de Visual Studio sur un partage de fichiers Azure.

1. Ouvrez Visual Studio, puis choisissez **Créer un nouveau projet** dans la fenêtre de démarrage.

    :::image type="content" source="./media/test-app-in-azure/launch-visual-studio.png" alt-text="Capture d’écran de la page de démarrage de Visual Studio.":::

1. Sélectionnez **Application console** puis **Suivant**.

    :::image type="content" source="./media/test-app-in-azure/select-console-application.png" alt-text="Capture d’écran de l’option permettant de choisir une application console.":::

1. Dans la page **Configurer votre nouveau projet**, laissez les valeurs par défaut, puis sélectionnez **Suivant**.

1. Sur la page **Informations supplémentaires**, laissez les valeurs par défaut et sélectionnez **Créer**.

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur votre projet et sélectionnez **Générer**.

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur votre projet et sélectionnez **Publier**.

    :::image type="content" source="./media/test-app-in-azure/publish-application.png" alt-text="Capture d’écran de l’option de publication de l’application.":::

1. Dans la page **Publier**, sélectionnez **Dossier**, puis **Suivant**.

    :::image type="content" source="./media/test-app-in-azure/publish-to-folder.png" alt-text="Capture d’écran de l’option de publication dans le dossier.":::

1. Pour l’option **Cible spécifique**, sélectionnez **Dossier**, puis **Suivant**.

1. Pour l’option **Emplacement**, sélectionnez **Parcourir**, puis sélectionnez le partage de fichiers que vous avez monté précédemment. Sélectionnez ensuite **OK**, puis **Terminer**. 

    :::image type="content" source="./media/test-app-in-azure/selecting-file-share.png" alt-text="Capture d’écran de l’option permettant de sélectionner le partage de fichiers.":::

1. Sélectionnez **Publier**. Visual Studio génère votre application et la publie sur votre partage de fichiers.

    :::image type="content" source="./media/test-app-in-azure/final-publish.png" alt-text="Capture d’écran du bouton Publier.":::

## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Tester l’application sur votre ordinateur virtuel de test dans le labo

1. Connectez-vous à votre machine virtuelle de laboratoire.

1. Dans la machine virtuelle, lancez **Explorateur de fichiers**, puis sélectionnez **Ce PC** pour trouver le partage de fichiers que vous avez monté précédemment.

    :::image type="content" source="./media/test-app-in-azure/find-share-on-vm.png" alt-text="Capture d’écran d’Explorateur de fichiers.":::

1. Ouvrez le partage de fichiers et vérifiez que l’application que vous avez déployée à partir de Visual Studio y figure. 

    :::image type="content" source="./media/test-app-in-azure/open-file-share.png" alt-text="Capture d’écran du contenu du partage de fichiers.":::

    Vous pouvez maintenant accéder à votre application et la tester sur l’ordinateur virtuel de test que vous avez créé dans Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à utiliser des machines virtuelles dans un labo, voir les articles suivants. 

- [Ajouter une machine virtuelle à un laboratoire](devtest-lab-add-vm.md)
- [Redémarrer une machine virtuelle de laboratoire](devtest-lab-restart-vm.md)
- [Redimensionner une machine virtuelle de laboratoire](devtest-lab-resize-vm.md)
