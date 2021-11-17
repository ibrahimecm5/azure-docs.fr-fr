---
title: 'Démarrage rapide : Ajouter l’authentification à une application web Node.js avec MSAL Node | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez découvrir comment implémenter l’authentification avec une application web Node.js et la bibliothèque d’authentification Microsoft (MSAL) pour Node.js.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: a593aef3ff99edb498e899b0d8db46c7b4636683
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427700"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>Démarrage rapide : Connecter des utilisateurs et obtenir un jeton d’accès dans une application web Node à l’aide du flux de code d’authentification

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application web Node.js peut connecter des utilisateurs en utilisant le flux du code d’autorisation. Cet exemple de code montre également comment obtenir un jeton d’accès pour appeler l’API Microsoft Graph.

Consultez [Fonctionnement de l’exemple](#how-the-sample-works) pour obtenir une illustration.

Ce guide de démarrage rapide utilise la bibliothèque d’authentification Microsoft pour Node.js (MSAL Node) avec le flux de code d’autorisation.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. [Créez un abonnement Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Inscrire et télécharger votre application de démarrage rapide
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
>
> 1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
> 1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Répertoires + abonnements** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour basculer vers le locataire dans lequel vous voulez inscrire l’application.
> 1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
> 1. Entrez un **nom** pour votre application. Les utilisateurs de votre application peuvent voir ce nom, et vous pouvez le changer ultérieurement.
> 1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
> 1. Choisissez `http://localhost:3000/redirect` comme valeur pour **URI de redirection**.
> 1. Sélectionnez **Inscription**.
> 1. Dans la page **Vue d’ensemble**, notez la valeur de **ID d’application (client)** pour une utilisation ultérieure.
> 1. Sous **Gérer**, sélectionnez **Certificats et secrets** > **Clés secrètes client** > **Nouvelle clé secrète client**.  Laissez la description vide et conservez l’expiration par défaut, puis sélectionnez **Ajouter**.
> 1. Notez la valeur du **secret client** pour une utilisation ultérieure.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Étape 1 : Configurer l’application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, vous devez créer un secret client et ajouter l’URL de réponse suivante : `http://localhost:3000/redirect`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-windows-desktop/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-project"></a>Étape 2 : Téléchargez le projet

> [!div renderon="docs"]
> Pour exécuter le projet avec un serveur web en utilisant Node.js, [téléchargez les fichiers principaux du projet](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Exécutez le projet avec un serveur web en utilisant Node.js.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>Étape 3 : Configurer votre application Node
>
> Extrayez le projet, ouvrez le dossier *ms-identity-node-main*, puis ouvrez le fichier *index.js*.
>
> Définissez la valeur `clientID` avec l’ID d’application (client), puis définissez la valeur `clientSecret` avec le secret client.
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> Modifiez les valeurs dans la section `config` :
>
> - `Enter_the_Application_Id_Here` est l’ID d’application (client) de l’application que vous avez inscrite.
>
>    Pour connaître l’ID d’application (client), consultez la page **Vue d’ensemble** de l’inscription d’application dans le portail Azure.
> - `Enter_the_Client_Secret_Here` est le secret client pour l’application que vous avez inscrite.
>
>    Pour récupérer ou générer un nouveau secret client, sous **Gérer**, sélectionnez **Certificats et secrets**.
>
> La valeur `authority` par défaut représente le cloud Azure principal (global) :
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>Étape 4 : Exécuter le projet

Exécutez le projet avec Node.js.

1. Pour démarrer le serveur, exécutez les commandes suivantes dans le répertoire du projet :

    ```console
    npm install
    npm start
    ```

1. Accédez à `http://localhost:3000/`.

1. Sélectionnez **Se connecter** pour démarrer le processus de connexion.

    La première fois que vous vous connectez, vous êtes invité à autoriser l’application à accéder à votre profil et à vous connecter. Une fois que vous êtes connecté, un message de journal s’affiche sur la ligne de commande.

## <a name="more-information"></a>Informations complémentaires

### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple

L’exemple héberge un serveur web sur localhost, port 3000. Quand un navigateur web accède à ce site, l’exemple redirige immédiatement l’utilisateur vers une page d’authentification Microsoft. Pour cette raison, l’exemple ne contient aucun élément HTML ni élément d’affichage. En cas de réussite de l’authentification, le message « OK » s’affiche.

### <a name="msal-node"></a>MSAL Node

La bibliothèque MSAL Node connecte les utilisateurs et demande les jetons utilisés pour accéder à une API protégée par la plateforme d’identités Microsoft. Vous pouvez télécharger la dernière version à l’aide du gestionnaire de package (npm) Node.js :

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajout de l’authentification à une application web existante - Exemple de code GitHub >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/auth-code)
