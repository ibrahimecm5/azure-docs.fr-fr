---
title: 'Démarrage rapide : Connecter des utilisateurs dans des applications monopages (SPA) Angular JavaScript avec du code d’authentification et appeler Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, découvrez comment une application monopage (SPA) Angular JavaScript peut connecter les utilisateurs de comptes personnels, professionnels ou scolaires à l’aide du flux du code d’autorisation, et appeler Microsoft Graph.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/12/2021
ROBOTS: NOINDEX
ms.author: jamesmantu
ms.custom: aaddev, "scenarios:getting-started", "languages:JavaScript", devx-track-js, mode-other
ms.openlocfilehash: b789dbc70611c838433ae2fb1f4607e4c26fe306
ms.sourcegitcommit: ee9bae378f0b2b63b356a3ef3131640572f8c795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2021
ms.locfileid: "133789691"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-an-angular-spa-using-the-auth-code-flow"></a>Démarrage rapide : Connecter et obtenir un jeton d’accès dans une application monopage (SPA) Angular à l’aide du flux du code d’authentification



Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application monopage (SPA) Angular JavaScript peut connecter des utilisateurs et appeler Microsoft Graph en utilisant le flux du code d’autorisation. L’exemple de code montre comment obtenir un jeton d’accès pour appeler l’API Microsoft Graph ou n’importe quelle API web.

Consultez [Fonctionnement de l’exemple](#how-the-sample-works) pour obtenir une illustration.

Ce guide de démarrage rapide utilise MSAL Angular v2 avec le flux du code d’autorisation.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure – [Créer un abonnement Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, ajoutez l’**URI de redirection** `http://localhost:4200/`.

>[!div class="nextstepaction"]
>[Apporter ces modifications pour moi]()

> [!div class="alert alert-info"]
> ![Déjà configuré](media/quickstart-v2-javascript/green-check.png) Votre application est configurée avec ces attributs.

 #### <a name="step-2-download-the-project"></a>Étape 2 : Téléchargez le projet

Exécuter le projet avec un serveur web en utilisant Node.js

>[!div class="nextstepaction"]
>[Téléchargez l’exemple de code](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip).

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée

Nous avons configuré votre projet avec les valeurs des propriétés de votre application.

#### <a name="step-4-run-the-project"></a>Étape 4 : Exécuter le projet

Exécutez le projet avec un serveur web en utilisant Node.js :

1. Pour démarrer le serveur, exécutez les commandes suivantes dans le répertoire du projet :
    ```console
    npm install
    npm start
    ```
1. Accédez à `http://localhost:4200/`.

1. Sélectionnez **Connexion** pour lancer le processus de connexion, puis appelez l’API Microsoft Graph.

    La première fois que vous vous connectez, vous êtes invité à autoriser l’application à accéder à votre profil et à vous connecter. Une fois que vous êtes connecté, cliquez sur le bouton **Profil** pour afficher les informations utilisateur sur la page.

## <a name="more-information"></a>Informations complémentaires

### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple

![Diagramme montrant le flux de code d’autorisation pour une application monopage.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

La bibliothèque MSAL.js connecte les utilisateurs, et demande les jetons utilisés pour accéder à une API protégée par la plateforme d’identités Microsoft.

Si vous avez déjà installé Node.js, vous pouvez télécharger la dernière version à l’aide du gestionnaire de package Node.js (npm) :

```console
npm install @azure/msal-browser @azure/msal-angular@2
```

## <a name="next-steps"></a>Étapes suivantes

Pour avoir un guide pas à pas détaillé sur la création de l’application de flux de code d’authentification en utilisant JavaScript vanilla, consultez le tutoriel suivant :

> [!div class="nextstepaction"]
> [Tutoriel pour se connecter et appeler MS Graph](./tutorial-v2-javascript-auth-code.md)
