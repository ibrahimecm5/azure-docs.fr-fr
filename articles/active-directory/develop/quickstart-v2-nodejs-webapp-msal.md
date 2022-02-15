---
title: 'Démarrage rapide : Ajouter l’authentification à une application web Node.js avec MSAL Node | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez découvrir comment implémenter l’authentification avec une application web Node.js et la bibliothèque d’authentification Microsoft (MSAL) pour Node.js.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:js", devx-track-js, mode-api
ms.openlocfilehash: 1c2165569be8b59c0234e0e56d147d53ea66331a
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138039334"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-nodejs-web-app-using-the-auth-code-flow"></a>Démarrage rapide : Connecter des utilisateurs et obtenir un jeton d’accès dans une application web Node.js à l’aide du flux de code d’authentification


> [!div renderon="docs"]
> Bienvenue ! Ce n’est probablement pas la page que vous attendiez. Pendant que nous travaillons sur un correctif, ce lien devrait vous permettre d’accéder au bon article :
>
> > [Démarrage rapide : l’application web Node.js qui connecte les utilisateurs avec le nœud MSAL](web-app-quickstart.md?pivots=devlang-nodejs-msal)
> 
> Nous vous prions de nous excuser pour le désagrément et nous vous remercions de votre patience.

> [!div renderon="portal" class="sxs-lookup"]
> Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application web Node.js peut connecter des utilisateurs en utilisant le flux du code d’autorisation. Cet exemple de code montre également comment obtenir un jeton d’accès pour appeler l’API Microsoft Graph.
> 
> Consultez [Fonctionnement de l’exemple](#how-the-sample-works) pour obtenir une illustration.
> 
> Ce guide de démarrage rapide utilise la bibliothèque d’authentification Microsoft pour Node.js (MSAL Node) avec le flux de code d’autorisation.
> 
> ## <a name="prerequisites"></a>Prérequis
> 
> * Un abonnement Azure. [Créez un abonnement Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Node.JS](https://nodejs.org/en/download/)
> * [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code
> 
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Étape 1 : Configurer l’application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, vous devez créer un secret client et ajouter l’URL de réponse suivante : `http:/> /localhost:3000/redirect`.
> > [!div class="nextstepaction"]
> > [Apporter cette modification pour moi]()
> 
> > [!div class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-windows-desktop/green-check.png) Votre application est configurée avec ces attributs >.
> 
> #### <a name="step-2-download-the-project"></a>Étape 2 : Téléchargez le projet
> 
> Exécutez le projet avec un serveur web en utilisant Node.js.
> 
> > [!div class="nextstepaction"]
> > [Téléchargez l’exemple de code](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).
> 
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
> 
> Exécutez le projet avec Node.js.
> 
> 1. Pour démarrer le serveur, exécutez les commandes suivantes dans le répertoire du projet :
> 
>     ```console
>     npm install
>     npm start
>     ```
> 
> 1. Accédez à `http://localhost:3000/`.
> 
> 1. Sélectionnez **Se connecter** pour démarrer le processus de connexion.
> 
>     La première fois que vous vous connectez, vous êtes invité à autoriser l’application à accéder à votre profil et à vous connecter. Une fois que vous êtes connecté, un message de journal s’affiche sur la ligne de commande.
> 
> ## <a name="more-information"></a>Informations complémentaires
> 
> ### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple
> 
> L’exemple héberge un serveur web sur localhost, port 3000. Quand un navigateur web accède à ce site, l’exemple redirige immédiatement l’utilisateur vers une page d’authentification Microsoft. Pour cette raison, l’exemple ne contient aucun élément HTML ni élément d’affichage. En cas de réussite de l’authentification, le message « OK » s’affiche.
> 
> ### <a name="msal-node"></a>MSAL Node
> 
> La bibliothèque MSAL Node connecte les utilisateurs et demande les jetons utilisés pour accéder à une API protégée par la plateforme d’identités Microsoft. Vous pouvez télécharger la dernière version à l’aide du gestionnaire de package (npm) Node.js :
> 
> ```console
> npm install @azure/msal-node
> ```
> 
> ## <a name="next-steps"></a>Étapes suivantes
> 
> > [!div class="nextstepaction"]
> > [Ajout de l’authentification à une application web existante - Exemple de code GitHub >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/auth-code)
