---
title: 'Démarrage rapide : Configurer la connexion pour une application monopage (SPA)'
titleSuffix: Azure AD B2C
description: Dans ce guide de démarrage rapide, vous allez exécuter un exemple d’application monopage qui utilise Azure Active Directory B2C pour la connexion aux comptes.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: a4ab049f739ff1ec940234cb6559c88547a47b20
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036680"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Démarrage rapide : Configurer la connexion pour une application monopage à l’aide d’Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) assure la gestion des identités de cloud pour préserver la protection de votre application, de votre entreprise et de vos clients. Azure AD B2C permet à vos applications de s’authentifier auprès de comptes des réseaux sociaux et de comptes d’entreprise à l’aide de protocoles standard ouverts. 

Dans ce démarrage rapide, vous utilisez un exemple d’application monopage pour vous connecter à l’aide d’un fournisseur d’identité sociale et appeler une API web protégée par Azure AD B2C.

<!--[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] -->

## <a name="prerequisites"></a>Prérequis

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.JS](https://nodejs.org/en/download/)
- Compte de réseau social Facebook, Google ou Microsoft
- Exemple de code de GitHub : [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) :

    Vous pouvez [télécharger l’archive zip](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) ou cloner le dépôt.

    ```console
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

## <a name="run-the-application"></a>Exécution de l'application

1. Démarrez le serveur en exécutant les commandes suivantes à partir de l’invite de commandes Node.js :

    ```console
    npm install
    npm update
    npm start
    ```

    Le serveur démarré par *server.js* affiche le port sur lequel il est à l’écoute :

    ```console
    Listening on port 6420...
    ```

1. Accédez à l’URL de l’application. Par exemple : `http://localhost:6420`.

    ![Exemple d’application monopage affichée dans un navigateur](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Connectez-vous à l’aide de votre compte

1. Sélectionnez **Se connecter** pour démarrer le parcours utilisateur.
1. Azure AD B2C présente la page de connexion d’une entreprise fictive appelée « Fabrikam » pour l’exemple d’application web. Pour vous inscrire au moyen d’un fournisseur d’identité sociale, sélectionnez le bouton en regard du fournisseur d’identité que vous souhaitez utiliser.

    ![Page de connexion ou d’inscription affichant des boutons de fournisseur d’identité](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Vous vous authentifiez (vous connectez) avec les informations d’identification de votre compte de réseau social et autorisez l’application à lire les informations de votre compte de réseau social. En accordant l’accès, l’application peut extraire des informations de profil du compte de réseau social, telles que votre nom et votre ville.

1. Terminez le processus de connexion pour le fournisseur d’identité.

## <a name="access-a-protected-api-resource"></a>Accéder à une ressource d’API protégée

Cliquez sur **Call API** (Appeler une API) pour que votre nom d’affichage soit retourné par l’API web sous forme d’un objet JSON.

![Exemple d’application dans un navigateur affichant la réponse de l’API web](./media/quickstart-single-page-app/call-api-spa.png)

L’exemple d’application monopage inclut un jeton d’accès dans la requête envoyée à la ressource de l’API web protégée.

<!-- ## Clean up resources

You can use your Azure AD B2C tenant if you plan to try other Azure AD B2C quickstarts or tutorials. When no longer needed, you can [delete your Azure AD B2C tenant](faq.yml#how-do-i-delete-my-azure-ad-b2c-tenant-).-->

## <a name="next-steps"></a>Étapes suivantes

<!---In this quickstart, you used a sample single-page application to:

- Sign in with a social identity provider
- Create an Azure AD B2C user account (created automatically at sign-in)
- Call a web API protected by Azure AD B2C -->

- Commencer à créer votre propre [locataire Azure Active Directory B2C dans le portail Azure](tutorial-create-tenant.md)
