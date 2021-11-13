---
title: Fichier Include
description: Fichier Include
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 52549e92a066df3caea479b0a20b21c747cb5ed4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461142"
---
## <a name="set-up-prerequisites"></a>Configurer les prérequis

- [Node.js](https://nodejs.org/) versions Active LTS et Maintenance LTS (8.11.1 et 10.14.1 recommandées).

## <a name="set-up"></a>Configurer

### <a name="create-a-new-nodejs-application"></a>Créer une application Node.js

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir communication-access-tokens-quickstart && cd communication-access-tokens-quickstart
```

Exécutez `npm init -y` pour créer un fichier `package.json` avec les paramètres par défaut.

```console
npm init -y
```

### <a name="install-the-package"></a>Installer le package

Utilisez la commande `npm install` pour installer le kit de développement logiciel (SDK) Azure Communication Services Identity pour JavaScript.

```console

npm install @azure/communication-identity@beta --save
npm install @azure/msal-node --save
npm install express --save

```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

## <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez un nouveau fichier texte dans votre éditeur de code
1. Ajoutez un appel `require` pour charger `CommunicationIdentityClient`
1. Créer la structure du programme, y compris la gestion des exceptions de base

    ```javascript
    const { CommunicationIdentityClient } = require('@azure/communication-identity');
    const express = require("express");
    const msal = require('@azure/msal-node');
    
    const SERVER_PORT = process.env.PORT || 80;
    const REDIRECT_URI = "http://localhost"; 
    
    // Quickstart code goes here
    
    app.listen(SERVER_PORT, () => console.log(`Communication access token application started on ${SERVER_PORT}!`))
    
    ```

1. Enregistrez le nouveau fichier sous `issue-communication-access-token.js` dans le répertoire `access-tokens-quickstart`.

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>Étape 1 : Recevoir le jeton utilisateur Azure AD par le biais de la bibliothèque MSAL

La première étape du flux d’échange de jetons consiste à obtenir un jeton pour votre utilisateur Teams à l’aide de [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md).

```javascript
const msalConfig = {
    auth: {
        clientId: "<contoso_application_id>",
        authority: "https://login.microsoftonline.com/<contoso_tenant_id>",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const provider = new msal.CryptoProvider();

const app = express();

app.get('/', async (req, res) => {
    const {verifier, challenge} = await provider.generatePkceCodes();
    const authCodeUrlParameters = {
        scopes: ["https://auth.msft.communication.azure.com/VoIP"],
        redirectUri: REDIRECT_URI,
        codeChallenge: challenge, 
        codeChallengeMethod: "S256"
    };

    pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
        res.redirect(response);
    }).catch((error) => console.log(JSON.stringify(error)));
});

app.get('/redirect', async (req, res) => {
    const tokenRequest = {
        code: req.query.code,
        scopes: ["https://auth.msft.communication.azure.com/VoIP"],
        redirectUri: REDIRECT_URI,
    };

    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("Response: ", response);
        //TODO: the following code snippets go here
        res.sendStatus(200);
    }).catch((error) => {
        console.log(error);
        res.status(500).send(error);
    });
});
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>Étape 2 : Initialiser CommunicationIdentityClient

Instanciez un `CommunicationIdentityClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Ajoutez le code suivant à la méthode `then` :

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>Étape 3 : Échanger le jeton utilisateur Azure AD contre le jeton d’accès Teams

Utilisez la méthode `getTokenForTeamsUser` afin d’émettre, pour l’utilisateur Teams, un jeton d’accès qui peut être utilisé avec les SDK Azure Communication Services.

```javascript
let accessToken = await identityClient.getTokenForTeamsUser(teamsToken);
console.log(`Token: ${accessToken}`);
```

## <a name="run-the-code"></a>Exécuter le code

À partir d’une invite de console, accédez au répertoire contenant le fichier *issue-communication-access-token.js*, puis exécutez la commande `node` suivante pour exécuter l’application.

```console
node ./issue-communication-access-token.js
```
