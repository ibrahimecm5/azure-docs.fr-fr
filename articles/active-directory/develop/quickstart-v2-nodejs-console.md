---
title: 'Démarrage rapide : Appeler Microsoft Graph dans une application console Node.js | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application console Node.js peut obtenir un jeton d’accès et appeler une API protégée par un point de terminaison de la plateforme d’identités Microsoft, en utilisant l’identité propre de l’application.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.date: 01/10/2022
ms.author: marsma
ms.custom: mode-api
ms.openlocfilehash: eeb50fc335835dcc2765c894e151f39cccbf25e4
ms.sourcegitcommit: b55c580fe2bb9fbf275ddf414d547ddde8d71d8a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2022
ms.locfileid: "136834864"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Node.js en utilisant l’identité de l’application

Dans ce guide de démarrage rapide, vous téléchargez et vous exécutez un exemple de code qui montre comment une application Node.js peut obtenir un jeton d’accès en utilisant l’identité de l’application pour appeler l’API Microsoft Graph et afficher une [liste d’utilisateurs](/graph/api/user-list) dans l’annuaire. L’exemple de code montre comment un travail sans assistance ou un service Windows peut s’exécuter avec l’identité d’une application, au lieu de l’identité d’un utilisateur.

Ce guide de démarrage rapide utilise la [bibliothèque d’authentification Microsoft pour Node.js (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) avec l’[octroi des informations d’identification du client](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Prérequis

* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code


### <a name="download-and-configure-the-sample-app"></a>Télécharger et configurer l’exemple d’application

#### <a name="step-1-configure-the-application-in-azure-portal"></a>Étape 1 : Configurer l’application dans le portail Azure
Pour que l’exemple de code fonctionne dans ce guide de démarrage rapide, vous devez créer un secret client et ajouter l’autorisation d’application **User.Read.All** de l’API Graph.
> [!div class="nextstepaction"]
> [Apporter ces modifications pour moi]()

> [!div class="alert alert-info"]
> ![Déjà configuré](media/quickstart-v2-netcore-daemon/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-nodejs-sample-project"></a>Étape 2 : Télécharger l’exemple de projet Node.js

> [!div class="sxs-lookup nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip).

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-admin-consent"></a>Étape 3 : Consentement de l’administrateur

Si vous essayez d’exécuter l’application à ce stade, vous recevez l’erreur *HTTP 403 - Interdit* : `Insufficient privileges to complete the operation`. Cette erreur se produit parce que les *autorisations d’application uniquement* nécessitent le **consentement de l’administrateur** : un administrateur général de votre annuaire doit donner son consentement à votre application. Sélectionnez l’une des options ci-dessous en fonction de votre rôle :

##### <a name="global-tenant-administrator"></a>Administrateur de locataires général

Si vous êtes administrateur général, accédez à la page **Autorisations de l’API** et sélectionnez **Accorder le consentement de l’administrateur pour Entrer_le_nom_du_locataire_ici**
> > [!div id="apipermissionspage"]
> > [Accéder à la page Autorisations de l’API]()

##### <a name="standard-user"></a>Utilisateur standard

Si vous êtes utilisateur standard de votre locataire, vous devez demander à un administrateur général de vous accorder son **consentement d’administrateur** pour votre application. Pour ce faire, donnez l’URL suivante à votre administrateur :

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

#### <a name="step-4-run-the-application"></a>Étape 4 : Exécution de l'application

Localisez le dossier racine de l’exemple (c’est là où se trouve `package.json`) dans une invite de commandes ou une console. Vous devez installer les dépendances de cet exemple une seule fois :

```console
npm install
```

Ensuite, exécutez l’application via l’invite de commandes ou la console :

```console
node . --op getUsers
```

Vous voyez normalement sur la sortie de la console un fragment JSON représentant une liste d’utilisateurs dans votre annuaire Azure AD.

## <a name="about-the-code"></a>À propos du code

Voici quelques-uns des aspects importants de l’exemple d’application.

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la plateforme d’identités Microsoft. Comme vous l’avez vu, ce guide de démarrage rapide demande des jetons par des autorisations d’application (en utilisant l’identité propre de l’application) au lieu d’autorisations déléguées. Le flux d’authentification utilisé dans ce cas est désigné sous le nom de [flux d’informations d’identification de client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md). Pour plus d’informations sur l’utilisation de MSAL Node avec des applications démon, consultez [Scénario : Application démon](scenario-daemon-overview.md).

 Vous pouvez installer MSAL Node en exécutant la commande npm suivante.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Initialisation MSAL

Vous pouvez ajouter la référence de MSAL en ajoutant le code suivant :

```javascript
const msal = require('@azure/msal-node');
```

Ensuite, initialisez MSAL à l’aide du code suivant :

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Où : |Description |
> |---------|---------|
> | `clientId` | Est l’**ID d’application (client)** de l’application inscrite dans le portail Azure. Vous pouvez retrouver cette valeur dans la page **Vue d’ensemble** de l’application dans le portail Azure. |
> | `authority`    | Point de terminaison STS pour l’utilisateur à authentifier. Généralement `https://login.microsoftonline.com/{tenant}` pour un cloud public, où {tenant} est le nom ou l’ID de votre locataire.|
> | `clientSecret` | Est le secret client créé pour l’application dans le portail Azure. |

Pour plus d’informations, consultez la [documentation de référence sur `ConfidentialClientApplication`](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

### <a name="requesting-tokens"></a>Demande de jetons

Pour demander un jeton à l’aide de l’identité d’application, utilisez la méthode `acquireTokenByClientCredential` :

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Où :| Description |
> |---------|---------|
> | `tokenRequest` | Contient les étendues demandées. Pour les clients confidentiels, utilisez un format similaire à `{Application ID URI}/.default`. Ce format indique que les étendues demandées sont celles qui sont définies de manière statique dans l’objet application défini dans le portail Azure (pour Microsoft Graph, `{Application ID URI}` pointe vers `https://graph.microsoft.com`). Pour les API web personnalisées, `{Application ID URI}` est défini sous la section **Exposer une API** dans Inscription d’application sur le portail Azure. |
> | `tokenResponse` | La réponse contient un jeton d’accès pour les étendues demandées. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement d’applications démon/console avec MSAL Node, consultez le tutoriel :

> [!div class="nextstepaction"]
> [Application démon qui appelle des API web](tutorial-v2-nodejs-console.md)
