---
title: 'Démarrage rapide : Ajouter une connexion utilisateur à une application web Node.js | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous apprenez à implémenter l’authentification dans une application web Node.js avec OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, "scenarios:getting-started", "languages:ASP.NET", devx-track-js, mode-api
ms.openlocfilehash: bde3b8b7aa581c8111c78ea00585caee2f269286
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138037324"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Démarrage rapide : Ajouter la connexion à l’aide d’OpenID Connect à une application web Node.js

> [!div renderon="docs"]
> Bienvenue ! Ce n’est probablement pas la page que vous attendiez. Pendant que nous travaillons sur un correctif, ce lien devrait vous permettre d’accéder au bon article :
>
> > [Démarrage rapide : ajouter une connexion utilisateur à une application web Node.js avec l’infrastructure Express ](web-app-quickstart.md?pivots=devlang-nodejs-passport)
> 
> Nous vous prions de nous excuser pour le désagrément et nous vous remercions de votre patience.

> [!div renderon="portal" class="sxs-lookup"]
> Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment configurer l’authentification OpenID Connect dans une application web créée à l’aide de Node.js avec Express. L'exemple est conçu pour fonctionner sur n'importe quelle plateforme.
> 
> ## <a name="prerequisites"></a>Prérequis
> 
> - Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Node.js](https://nodejs.org/en/download/).
> 
> ## <a name="register-your-application"></a>Inscrivez votre application
> 
> 1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
> 1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Répertoires + abonnements**:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour basculer vers le locataire dans lequel vous voulez inscrire l’application.
> 1. Recherchez et sélectionnez **Azure Active Directory**.
> 1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
> 1. Entrez un **nom** pour votre application (par exemple, `MyWebApp`). Les utilisateurs de votre application peuvent voir ce nom, et vous pouvez le changer ultérieurement.
> 1. Dans la section **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)** .
> 
>     S’il existe plusieurs URI de redirection, vous devez les ajouter à partir de l’onglet **Authentification**, une fois l’application correctement créée.
> 
> 1. Sélectionnez **Inscrire** pour créer l'application.
> 1. Sur la page **Vue d'ensemble** de l'application, recherchez la valeur de l'**ID d'application (client)** et notez-la. Vous aurez besoin de cette valeur > pour configurer l'application plus tard dans ce projet.
> 1. Sous **Gérer**, sélectionnez **Authentification**.
> 1. Sélectionnez **Ajouter une plateforme** > **Web**. 
> 1. Dans la section **URI de redirection**, entrez `http://localhost:3000/auth/openid/return`.
> 1. Entrez une **Front-channel logout URL** (URL de déconnexion de canal frontal) ayant la valeur `https://localhost:3000`.
> 1. Dans la section **Implicit grant and hybrid flows** (Flux d’octroi implicite et flux hybride), sélectionnez **Jetons d’ID**, car cet exemple nécessite l’activation du [flux d’octroi implicite](./v2-oauth2-implicit-grant-flow.md) pour permettre la connexion de l’utilisateur.
> 1. Sélectionnez **Configurer**.
> 1. Sous **Gérer**, sélectionnez **Certificats et secrets** > **Clés secrètes client** > **Nouvelle clé secrète client**.
> 1. Entrez une description pour la clé (par exemple, secret de l'application).
> 1. Sélectionnez une durée pour la clé : **Dans 1 an, Dans 2 ans** ou **N'expire jamais**.
> 1. Sélectionnez **Ajouter**. La valeur de la clé s’affiche. Copiez la valeur de la clé et enregistrez-la dans un endroit sûr en vue d’une utilisation ultérieure.
> 
> 
> ## <a name="download-the-sample-application-and-modules"></a>Télécharger l'exemple d'application et les modules
> 
> Ensuite, clonez l'exemple de référentiel et installez les modules NPM.
> 
> À partir de votre interpréteur de commandes ou de votre ligne de commande :
> 
> `$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`
> 
> or
> 
> `$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`
> 
> À partir du répertoire racine du projet, exécutez la commande suivante :
> 
> `$ npm install`
> 
> ## <a name="configure-the-application"></a>Configuration de l'application
> 
> Fournissez les paramètres de `exports.creds` dans config.js, comme indiqué.
> 
> * Mettez à jour `<tenant_name>` dans `exports.identityMetadata` avec le nom du locataire Azure AD au format \*.onmicrosoft.com.
> * Mettez à jour `exports.clientID` avec l'ID d'application noté lors de l'inscription de l'application.
> * Mettez à jour `exports.clientSecret` avec le secret d'application noté lors de l'inscription de l'application.
> * Mettez à jour `exports.redirectUrl` avec l'URI de redirection noté lors de l'inscription de l'application.
> 
> **Configuration facultative pour les applications de production :**
> 
> * Mettez à jour `exports.destroySessionUrl` dans config.js, si vous souhaitez utiliser un autre URI `post_logout_redirect_uri`.
> 
> * Dans config.js, définissez `exports.useMongoDBSessionStore` sur true si vous souhaitez utiliser [mongoDB](https://www.mongodb.com) ou d'autres [magasins de sessions compatibles](https://github.com/expressjs/session#compatible-session-stores).
> Dans cet exemple, le magasin de sessions par défaut est `express-session`. Le magasin de sessions par défaut ne convient pas pour la production.
> 
> * Mettez à jour `exports.databaseUri` si vous souhaitez utiliser le magasin de sessions mongoDB et un autre URI de base de données.
> 
> * Mettez à jour `exports.mongoDBSessionMaxAge`. Vous pouvez ici spécifier la durée pendant laquelle vous souhaitez conserver une session dans mongoDB. L'unité utilisée est la seconde.
> 
> ## <a name="build-and-run-the-application"></a>Génération et exécution de l’application
> 
> Démarrez le service mongoDB. Si vous utilisez le magasin de sessions mongoDB dans cette application, vous devez [installer mongoDB](http://www.mongodb.org/) et démarrer le service en premier. Si vous utilisez le magasin de sessions par défaut, vous pouvez ignorer cette étape.
> 
> Exécutez l'application à l'aide de la commande suivante à partir de votre ligne de commande.
> 
> ```
> $ node app.js
> ```
> 
> **La sortie du serveur est-elle difficile à comprendre ?** Dans cet exemple, nous utilisons `bunyan` pour la journalisation. La console ne présentera d'intérêt pour vous que si vous installez également Bunyan et exécutez le serveur comme ci-dessus, mais dans le binaire bunyan :
> 
> ```
> $ npm install -g bunyan
> 
> $ node app.js | bunyan
> ```
> 
> ### <a name="youre-done"></a>Vous avez terminé !
> 
> Un serveur s'exécute correctement sur `http://localhost:3000`.
> 
> [!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
> 
> ## <a name="next-steps"></a>Étapes suivantes
> Découvrez-en plus sur le scénario d'application web pris en charge par la Plateforme d'identités Microsoft :
> > [!div class="nextstepaction"]
> > [Scénario d'application web qui connecte des utilisateurs](scenario-web-app-sign-user-overview.md)
