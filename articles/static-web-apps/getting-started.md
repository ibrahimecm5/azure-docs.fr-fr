---
title: 'Démarrage rapide : Création de votre premier site statique avec le service Azure Static Web Apps'
description: Apprenez à déployer un site statique sur Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 06/16/2021
ms.author: cshoe
ms.openlocfilehash: a7b7504a99be6784e2767abe0daf9ba64066d0c1
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134618"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Démarrage rapide : Création de votre premier site statique avec Azure Static Web Apps

Azure Static Web Apps publie un site web en créant une application à partir d’un référentiel de code. Dans ce guide de démarrage rapide, vous allez déployer une application dans Azure Static Web Apps à l’aide de l’extension Visual Studio Code.

Si vous n’avez pas d’abonnement Azure, [créez un compte d’essai gratuit](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prérequis

- [GitHub](https://github.com)
- Compte [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Extension Azure Static Web Apps pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Installez Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ensuite, ouvrez Visual Studio Code et accédez à **Fichier > Ouvrir le dossier** pour ouvrir le référentiel cloné sur votre machine dans l’éditeur.

## <a name="create-a-static-web-app"></a>Créer une application web statique

1. Dans Visual Studio Code, sélectionnez le logo Azure dans la barre d’activités pour ouvrir la fenêtre des extensions Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo Azure":::

    > [!NOTE]
    > Vous devez vous connecter à Azure et GitHub dans Visual Studio Code pour continuer. Si vous n’êtes pas déjà authentifié, l’extension vous invitera à vous connecter aux deux services pendant le processus de création.

1. Sous l'étiquette _Static Web Apps_, sélectionnez le **signe plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nom de l’application":::
    
    > [!NOTE]
    > L’extension Azure Static Web Apps de Visual Studio Code simplifie le processus de création à l’aide d’une série de valeurs par défaut. Si vous souhaitez un contrôle affiné du processus de création, ouvrez la palette de commande, puis sélectionnez **Azure Static Web Apps : Créer une application web statique (avancé)** .

1. La palette de commandes s’ouvre en haut de l’éditeur et vous invite à sélectionner un nom pour votre abonnement.

    Sélectionnez votre abonnement et appuyez sur <kbd>Entrée</kbd>.

    :::image type="content" source="media/getting-started/extension-subscription.png" alt-text="Sélectionner un abonnement Azure":::

1. Ensuite, nommez votre application.

    Tapez **my-first-static-web-app** et appuyez sur <kbd>Entrée</kbd>.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Création d’application web statique":::

1. Sélectionnez une région proche de chez vous.

    > [!NOTE]
    > Azure Static Web Apps distribue globalement vos ressources statiques. La région que vous sélectionnez détermine l’emplacement de votre application de fonction API et de vos environnements intermédiaires facultatifs.

1. Sélectionnez les présélections correspondant à votre type d'application.

    # <a name="no-framework"></a>[Pas de framework](#tab/vanilla-javascript)

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Présélections d'application : Pas de framework":::

    Entrez **/src** comme emplacement des fichiers d’application, puis appuyez sur <kbd>Entrée</kbd>.

    Cette application ne produit pas une sortie de build. Vérifiez que l’emplacement de sortie de la build est vide et appuyez sur <kbd>Entrée</kbd>.

    # <a name="angular"></a>[Angular](#tab/angular)

    :::image type="content" source="media/getting-started/extension-presets-angular.png" alt-text="Présélections d'application : Angular":::

    Entrez **/** comme emplacement des fichiers d’application.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Emplacement des fichiers d'application : Angular":::

    Entrez **dist/angular-basic** pour l’emplacement de sortie de la build.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Emplacement de sortie de la build d'application : Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Présélections d'application : React":::

    Entrez **/** comme emplacement des fichiers d’application.

    Entrez **build** comme emplacement de sortie de build.

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Présélections d'application : Vue":::

    Entrez **/** comme emplacement des fichiers d’application.

    Entrez **dist** comme emplacement de sortie de build.

    ---

1. Une fois l’application créée, une notification de confirmation s’affiche dans Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Confirmation créée":::

    Lorsque le déploiement est en cours, l’extension Visual Studio Code signale l’état de la build.

    :::image type="content" source="media/getting-started/extension-waiting-for-deployment.png" alt-text="En attente de déploiement":::

    Une fois le déploiement terminé, vous pouvez naviguer directement sur votre site web.

1. Pour afficher le site web dans le navigateur, cliquez avec le bouton droit sur le projet dans l'extension Static Web Apps, puis sélectionnez **Parcourir le site**.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Parcourir le site":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, vous pouvez supprimer l’instance Azure Static Web Apps par le biais de l’extension.

Dans la fenêtre de l’Explorateur Visual Studio Code, revenez à la section _Static Web Apps_ et cliquez avec le bouton droit sur **my-first-static-web-app**, puis sélectionnez **Supprimer**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Supprimer l’application":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une API](add-api.md)
