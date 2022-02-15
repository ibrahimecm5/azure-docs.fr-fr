---
title: 'Démarrage rapide : Connexion d’utilisateurs dans des applications monopages JavaScript (SPA) avec du code d’authentification | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, découvrez comment une application monopage (SPA) JavaScript peut connecter les utilisateurs de comptes personnels, professionnels et scolaires à l’aide du flux de code d’autorisation.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/12/2021
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:JavaScript", devx-track-js, mode-other
ms.openlocfilehash: afd45394932c772692eb9ffaa995bdf8dbf11ce2
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138031965"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow-with-pkce"></a>Démarrage rapide : Connexion d’utilisateurs et récupération d’un jeton d’accès dans une application monopage JavaScript à l’aide du flux de code d’authentification avec PKCE

> [!div renderon="docs"]
> Bienvenue ! Ce n’est probablement pas la page que vous attendiez. Pendant que nous travaillons sur un correctif, ce lien devrait vous permettre d’accéder au bon article :
>
> > [Démarrage rapide : Application JavaScript à page unique avec connexion utilisateur](single-page-app-quickstart.md?pivots=devlang-javascript)
> 
> Nous vous prions de nous excuser pour le désagrément et nous vous remercions de votre patience.

> [!div renderon="portal" class="sxs-lookup"]
> Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application monopage JavaScript peut connecter des utilisateurs et appeler Microsoft Graph en utilisant le flux du code d’autorisation avec PKCE (Proof Key for Code Exchange). L’exemple de code montre comment obtenir un jeton d’accès pour appeler l’API Microsoft Graph ou n’importe quelle API web.
> 
> Consultez [Fonctionnement de l’exemple](#how-the-sample-works) pour obtenir une illustration.
> 
> ## <a name="prerequisites"></a>Prérequis
> 
> * Un abonnement Azure – [Créer un abonnement Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
> * [Node.JS](https://nodejs.org/en/download/)
> * [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code
> 
> 
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, ajoutez l’**URI de redirection** `http://localhost:3000/`.
> > [!div class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
> 
> > [!div class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-javascript/green-check.png) Votre application est configurée avec ces attributs.
> 
> #### <a name="step-2-download-the-project"></a>Étape 2 : Téléchargez le projet
> 
> Exécuter le projet avec un serveur web en utilisant Node.js
> 
> > [!div class="nextstepaction"]
> > [Téléchargez l’exemple de code](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip).
> 
> > [!div class="sxs-lookup"]
> > > [!NOTE]
> > > `Enter_the_Supported_Account_Info_Here`
> 
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
> 
> Nous avons configuré votre projet avec les valeurs des propriétés de votre application.
> 
> Exécutez le projet avec un serveur web en utilisant Node.js.
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
> 1. Sélectionnez **Se connecter** pour lancer le processus de connexion, puis appelez l’API Microsoft Graph.
> 
>     La première fois que vous vous connectez, vous êtes invité à autoriser l’application à accéder à votre profil et à vous connecter. Quand vous êtes connecté, les informations de votre profil utilisateur s’affichent sur la page.
> 
> ## <a name="more-information"></a>Informations complémentaires
> 
> ### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple
> 
> ![Diagramme montrant le flux de code d’autorisation pour une application monopage.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)
> 
> ### <a name="msaljs"></a>MSAL.js
> 
> La bibliothèque MSAL.js connecte les utilisateurs et demande les jetons utilisés pour accéder à une API protégée par la plateforme d’identités Microsoft. Le fichier *index.html* de l’exemple contient une référence à la bibliothèque :
> 
> ```html
> <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
> "sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
> ```
> 
> Si vous avez déjà installé Node.js, vous pouvez télécharger la dernière version à l’aide du gestionnaire de package Node.js (npm) :
> 
> ```console
> npm install @azure/msal-browser
> ```
> 
> ## <a name="next-steps"></a>Étapes suivantes
> 
> Pour un guide pas à pas plus détaillé sur la création de l’application utilisée dans ce guide de démarrage rapide, consultez le tutoriel suivant :
> 
> > [!div class="nextstepaction"]
> > [Tutoriel pour se connecter et appeler MS Graph](./tutorial-v2-javascript-auth-code.md)
