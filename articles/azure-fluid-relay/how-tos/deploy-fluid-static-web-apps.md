---
title: Guide pratique pour déployer des applications Fluid avec Azure Static Web Apps
description: Explication détaillée de la façon dont les applications Fluid peuvent être hébergées sur Azure Static Web Apps
author: sdeshpande3
ms.author: sdeshpande
ms.date: 08/19/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: a5b30b3097ef3e04557473dcaf42e875470b7563
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096603"
---
# <a name="how-to-deploy-fluid-applications-using-azure-static-web-apps"></a>Guide pratique pour déployer des applications Fluid avec Azure Static Web Apps

Cet article explique comment effectuer un déploiement des applications Fluid en utilisant Azure Static Web Apps. Le référentiel [FluidHelloWorld](https://github.com/microsoft/FluidHelloWorld/tree/main-azure) contient une application Fluid appelée **DiceRoller** qui permet à tous les clients connectés de lancer un dés et de voir le résultat.  Dans ce guide de démonstration, vous déployez une application DiceRoller sur Azure Static Web Apps à l’aide de l’extension Visual Studio Code.

Si vous n’avez pas d’abonnement Azure, [créez un compte d’essai gratuit](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prérequis

- [GitHub](https://github.com)
- Compte [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Extension Azure Static Web Apps pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Installez Git](https://www.git-scm.com/downloads)

[!INCLUDE [fork-fluidhelloworld](../includes/fork-fluidhelloworld.md)]

## <a name="connect-to-azure-fluid-relay-service"></a>Se connecter à un service Azure Fluid Relay

Vous pouvez vous connecter au service Azure Fluid Relay en fournissant l’ID et la clé de locataire qui sont générés de manière unique pour vous lors de la création de la ressource Azure. Vous pouvez créer votre propre implémentation de fournisseur de jetons ou utiliser les deux implémentations de fournisseur de jetons fournies par l’infrastructure Fluid : **InsecureTokenProvider** et **AzureFunctionTokenProvider**.

Pour en savoir plus sur l’utilisation d’InsecureTokenProvider pour le développement local, consultez [Connexion au service](connect-fluid-azure-service.md#connecting-to-the-service) et [Authentification et autorisation dans votre application](../concepts/authentication-authorization.md#the-token-provider).

### <a name="using-azurefunctiontokenprovider"></a>Utilisation d’AzureFunctionTokenProvider

**AzureFunctionTokenProvider** est un fournisseur de jetons qui n’expose pas la clé secrète dans le code côté client et qui peut être utilisé dans les scénarios de production. Cette implémentation du fournisseur de jetons peut être utilisée pour extraire un jeton d’un point de terminaison HTTPS qui est responsable de la signature des jetons d’accès à l’aide de la clé de locataire. Cela offre un moyen sécurisé de générer le jeton et de le renvoyer à l’application cliente.

```js
import { AzureClient, AzureFunctionTokenProvider } from "@fluidframework/azure-client";

const config = {
    tenantId: "myTenantId",
    tokenProvider: new AzureFunctionTokenProvider("https://myAzureAppUrl"+"/api/GetAzureToken", { userId: "test-user",userName: "Test User" }),
    orderer: "https://myOrdererUrl",
    storage: "https://myStorageUrl",
};

const clientProps = {
    connection: config,
};

const client = new AzureClient(clientProps);
```

Pour utiliser ce fournisseur de jetons, vous devez déployer un point de terminaison HTTPS qui signera les jetons et transmettre l’URL à votre point de terminaison au fournisseur AzureFunctionTokenProvider.

### <a name="deploying-an-azure-function-using-azure-static-web-apps"></a>Déploiement d’une fonction Azure à l’aide d’Azure Static Web Apps

Les Azure Static Web Apps vous permettent de développer un site web de pile complète sans avoir à gérer la configuration côté serveur d’un environnement d’hébergement web entier. Vous pouvez déployer Azure Functions avec votre site web statique. À l’aide de cette capacité, vous pouvez déployer une fonction Azure déclenchée par HTTP qui signera les jetons.

Pour plus d’informations sur le déploiement d’API avec Azure Function sur votre application web statique, consultez [Ajouter une API à Azure Static Web Apps avec Azure Functions](../../static-web-apps/add-api.md).

> [!NOTE]
> Vous pouvez utiliser l’exemple de code de fonction Azure dans [Implémentation d’une fonction Azure pour signer des jetons](azure-function-token-provider.md#implement-an-azure-function-to-sign-tokens) afin d’implémenter votre fonction.

Une fois votre fonction Azure déployée, vous devez mettre à jour l’URL transmise à AzureFunctionTokenProvider.

```js
import { AzureClient } from "@fluidframework/azure-client";

const config = {
    tenantId: "myTenantId",
    tokenProvider: new AzureFunctionTokenProvider("https://myStaticWebAppUrl/api/GetAzureToken", { userId: "test-user",userName: "Test User" }),
    orderer: "https://myOrdererUrl",
    storage: "https://myStorageUrl",
};

const clientProps = {
    connection: config,
};

const client = new AzureClient(config);
```

Exécutez la commande `npm run build` à partir du répertoire racine pour régénérer l’application. Cela génère un dossier `dist` avec le code d’application qui doit être déployé sur l’application web statique.

[!INCLUDE [sign-in-extensions](../includes/sign-in-extensions.md)]

## <a name="create-a-static-web-app"></a>Créer une application web statique

1. Dans Visual Studio Code, sélectionnez le logo Azure dans la barre d’activités pour ouvrir la fenêtre des extensions Azure.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-azure-logo.png" alt-text="Image du logo Azure sur un arrière-plan blanc.":::

    > [!NOTE]
    > Vous devez vous connecter à Azure et à GitHub dans Visual Studio Code pour continuer. Si vous n’êtes pas déjà authentifié, l’extension vous invitera à vous connecter aux deux services pendant le processus de création.

1. Sous l'étiquette *Static Web Apps*, sélectionnez le **signe plus**.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-create-button.png" alt-text="Image de l’interface utilisateur de l’extension Static Web Apps, en mettant en surbrillance le bouton créer.":::
    
    > [!NOTE]
    > L’extension Azure Static Web Apps de Visual Studio Code simplifie le processus de création à l’aide d’une série de valeurs par défaut. Si vous souhaitez un contrôle affiné du processus de création, ouvrez la palette de commande, puis sélectionnez **Azure Static Web Apps : Créer une application web statique (avancé)** .

1. La palette de commandes s’ouvre en haut de l’éditeur et vous invite à sélectionner un nom pour votre abonnement.

    Sélectionnez votre abonnement et appuyez sur <kbd>Entrée</kbd>.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-subscription.png" alt-text="Image de l’interface utilisateur de sélection de l’abonnement Azure, qui montre un seul abonnement à sélectionner.":::

1. Ensuite, nommez votre application.

    Tapez **my-first-static-web-app** et appuyez sur <kbd>Entrée</kbd>.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-create-app.png" alt-text="Image de l’interface utilisateur de l’extension Static Web Apps, qui affiche une zone de texte pour entrer le nom de l’application.":::

1. Sélectionnez une région proche de chez vous.

    > [!NOTE]
    > Azure Static Web Apps distribue globalement vos ressources statiques. La région que vous sélectionnez détermine l’emplacement de votre application de fonction API et de vos environnements intermédiaires facultatifs.

1. Définissez d’autres options de déploiement.
    
    - Quand vous êtes invité à sélectionner une nuild prédéfinie pour configurer la structure de projet par défaut, sélectionnez **Personnalisé**.
    - Emplacement du code de votre application : `/`
    - Emplacement du code de fonction Azure : `api`

1. Une fois l’application créée, une notification de confirmation s’affiche dans Visual Studio Code.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-confirmation.png" alt-text="Image de la notification présentée dans Visual Studio Code lors de la création de l’application. La notification lit : Création réussie de l’application web statique my-first-static-web-app. GitHub Actions génère et déploie votre application, elle est disponible une fois le déploiement terminé.":::

    Lorsque le déploiement est en cours, l’extension Visual Studio Code signale l’état de la build.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-waiting-for-deployment.png" alt-text="Image de l’interface utilisateur de l’extension Static Web Apps, qui affiche une liste d’applications web statiques sous chaque abonnement. L’application web statique en surbrillance a l’état d’attente de déploiement affiché en regard de celle-ci.":::

    Une fois le déploiement terminé, vous pouvez naviguer directement sur votre site web.

1. Pour afficher le site web dans le navigateur, cliquez avec le bouton droit sur le projet dans l'extension Static Web Apps, puis sélectionnez **Parcourir le site**.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-browse-site.png" alt-text="Image du menu qui s’affiche lorsque vous cliquez avec le bouton droit sur une application web statique. L’option Parcourir le site est mise en surbrillance.":::

1. L’emplacement de votre code d’application, de votre fonction Azure et de la build obtenue fait partie du fichier de workflow `azure-static-web-apps-xxx-xxx-xxx.yml` situé dans le répertoire `/.github/workflows`. Ce fichier est créé automatiquement lors de la création de l’application web statique. Il définit une action GitHub pour générer et déployer votre application web statique.


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, vous pouvez supprimer l’instance Azure Static Web Apps par le biais de l’extension.

Dans la fenêtre de l’Explorateur Visual Studio Code, revenez à la section _Static Web Apps_ et cliquez avec le bouton droit sur **my-first-static-web-app**, puis sélectionnez **Supprimer**.

:::image type="content" source="../../static-web-apps/media/getting-started/extension-delete.png" alt-text="Image du menu qui s’affiche lorsque vous cliquez avec le bouton droit sur une application web statique. L’option Supprimer est mise en surbrillance.":::
