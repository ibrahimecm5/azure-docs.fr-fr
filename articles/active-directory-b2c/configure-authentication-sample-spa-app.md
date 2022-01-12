---
title: Configurer l’authentification dans un exemple d’application monopage à l’aide d’Azure Active Directory B2C
description: Cet article explique comment utiliser Azure Active Directory B2C pour inscrire et connecter des utilisateurs dans une application monopage.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/25/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 620afdfbcc62c3ce8e07c1572dd4ec20634ebd64
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007876"
---
# <a name="configure-authentication-in-a-sample-single-page-application-by-using-azure-ad-b2c"></a>Configurer l’authentification dans un exemple d’application monopage à l’aide d’Azure AD B2C

Cet article utilise un exemple d’application monopage JavaScript pour illustrer l’ajout d’une authentification Azure Active Directory B2C (Azure AD B2C) à vos applications monopages.

## <a name="overview"></a>Vue d'ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0. Vous pouvez l’utiliser pour connecter de façon sécurisée un utilisateur à une application. Cet exemple d’application monopage utilise [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) et le flux PKCE d’OIDC. MSAL.js est une bibliothèque fournie par Microsoft qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation aux applications monopages.

### <a name="sign-in-flow"></a>Flux de connexion

Le flux de connexion implique les étapes suivantes :

1. L’utilisateur accède à l’application web et sélectionne **Connexion**.
1. L’application lance une requête d’authentification et redirige les utilisateurs vers Azure AD B2C.
1. Les utilisateurs [s’inscrivent ou se connectent](add-sign-up-and-sign-in-policy.md) et [réinitialisent le mot de passe](add-password-reset-policy.md). Ils peuvent également se connecter avec un [compte social](add-identity-provider.md).
1. Une fois les utilisateurs connectés, Azure AD B2C renvoie un code d’autorisation à l’application.
1. L’application monopage valide le jeton d’ID, lit les revendications et permet aux utilisateurs d’appeler des ressources ou des API protégées.

### <a name="app-registration-overview"></a>Vue d’ensemble de l’inscription de l’application

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, vous devez inscrire deux applications dans le répertoire d’Azure AD B2C.  

- L’inscription de l’**application web** permet à votre application de se connecter grâce à Azure AD B2C. Pendant l’inscription, vous spécifiez l’*URI de redirection*. L’URI de redirection est le point de terminaison vers lequel les utilisateurs sont redirigés par Azure AD B2C une fois leur authentification avec Azure AD B2C terminée. Le processus d’inscription de l’application génère un *ID d’application*, également appelé *ID client*, qui identifie votre application de façon unique.

- L’inscription de l’**API web** permet à votre application d’appeler une API web sécurisée. L’inscription comprend les *étendues* de l’API web. Les étendues permettent de gérer les autorisations d’accès aux ressources protégées, telles que votre API web. Vous accordez les autorisations de l’application web aux étendues de l’API web. Lorsqu’un jeton d’accès est demandé, votre application spécifie les autorisations souhaitées dans le paramètre d’étendue de la requête.  

Les inscriptions et l’architecture de l’application sont illustrées dans le diagramme suivant :

![Diagramme d’une application web avec inscriptions et jetons d’appel d’API web.](./media/configure-authentication-sample-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Appel à une API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>Flux de déconnexion

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prérequis

Un ordinateur qui exécute :

* [Visual Studio Code](https://code.visualstudio.com/) ou un autre éditeur de code
* [Runtime Node.js](https://nodejs.org/en/download/)

## <a name="step-1-configure-your-user-flow"></a>Étape 1 : Configurer votre flux d’utilisateurs

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-spa-and-api"></a>Étape 2 : Inscrire votre application monopage et votre API

Dans cette étape, vous allez créer les inscriptions d’application de l’application monopage et de l’API web, puis spécifier les étendues de votre API web.

### <a name="step-21-register-the-web-api-application"></a>Étape 2.1 : Inscrire l’application API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-scopes"></a>Étape 2.2 : Configurer des étendues

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="step-23-register-the-spa"></a>Étape 2.3 : Inscrire l’application monopage

Pour créer l’inscription de l’application monopage, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **nom** pour l’application (par exemple, *MyApp*).
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Application monopage (SPA)** , puis entrez `http://localhost:6420` dans la zone de texte de l’URL.
1. Sous **Autorisations**, cochez la case **Accorder le consentement administrateur aux autorisations openid et offline access**.
1. Sélectionnez **Inscription**.

### <a name="step-24-enable-the-implicit-grant-flow"></a>Étape 2.4 : Activer le flux d’octroi implicite

Ensuite, activez le flux d’octroi implicite :

1. Sous **Gérer**, sélectionnez **Authentification**.

1. Sélectionnez **Essayer la nouvelle expérience** (si elle est indiquée).

1. Sous **Octroi implicite**, cochez la case **Jetons d’ID**.

1. Sélectionnez **Enregistrer**.

   Enregistrez l’**ID d’application (client)** que vous utiliserez ultérieurement pour configurer l’application web.

    ![Capture d’écran de la page Vue d’ensemble de l’application web pour l’enregistrement de votre ID d’application web.](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  

### <a name="step-25-grant-permissions"></a>Étape 2.5 : Accorder des autorisations

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-spa-sample-code"></a>Étape 3 : Obtenir le code de l’exemple d’application monopage

Cet exemple montre la façon dont une application monopage peut utiliser Azure AD B2C pour l’inscription et la connexion des utilisateurs. L’application acquiert ensuite un jeton d’accès et appelle une API web protégée. 

Pour obtenir l’exemple de code de l’application monopage, vous pouvez effectuer l’une des opérations suivantes : 

* [Téléchargez un fichier zip](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip). 
* Clonez l’exemple à partir de GitHub en exécutant la commande suivante :

    ```bash
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

### <a name="step-31-update-the-spa-sample"></a>Étape 3.1 : Mettre à jour l’exemple d’application monopage

Maintenant que vous avez obtenu l’exemple d’application monopage, mettez à jour le code avec les valeurs d’Azure AD B2C et de l’API web. Dans le dossier de l’exemple, sous le dossier `App`, ouvrez les fichiers JavaScript qui sont répertoriés dans le tableau ci-dessous, puis mettez-les à jour avec les valeurs correspondantes.  


|Fichier  |Clé  |Valeur  |
|---------|---------|---------|
|apiConfig.js|clientId| L’ID d’application monopage de l’[étape 2.3](#step-23-register-the-spa).|
|policies.js| noms| Le flux d’utilisateurs ou la stratégie personnalisée que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|
|policies.js|authorities|Le [nom de locataire](tenant-management.md#get-your-tenant-name) de votre locataire Azure AD B2C (par exemple, `contoso.onmicrosoft.com`). Ensuite, remplacez par les flux d’utilisateur ou la stratégie personnalisée que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow) (par exemple, `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`).|
|policies.js|authorityDomain|Le [nom de locataire](tenant-management.md#get-your-tenant-name) de votre locataire Azure AD B2C (par exemple, `contoso.onmicrosoft.com`).|
|apiConfig.js|b2cScopes|Les étendues de l’API web que vous avez créées à l’[étape 2.2](#step-22-configure-scopes) (par exemple, `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`).|
|authConfig.js|webApi|L’URL de l’API web, `http://localhost:5000/tasks`.|
| | | |

Le code obtenu doit ressembler à l’exemple suivant :

*authConfig.js* :

```javascript
const msalConfig = {
  auth: {
    clientId: "<your-MyApp-application-ID>"
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes
};
```

*policies.js* :

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "your-tenant-name.b2clogin.com"
}
```

*apiConfig.js* :

```javascript
const apiConfig = {
  b2cScopes: ["https://your-tenant-name.onmicrosoft.com/tasks-api/tasks.read"],
  webApi: "http://localhost:5000/tasks"
};
```

## <a name="step-4-get-the-web-api-sample-code"></a>Étape 4 : Obtenir le code de l’exemple d’API web

Maintenant que l’API web est inscrite et que vous avez défini ses étendues, configurez le code de l’API web pour qu’il fonctionne avec votre locataire Azure AD B2C. 

Pour obtenir l’exemple de code de l’API web, effectuez l’une des opérations suivantes :

* [Téléchargez une archive \*.zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip).

* Clonez l’exemple de projet d’API web à partir de GitHub en exécutant la commande suivante :

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
    ```

* Vous pouvez également accéder directement au projet [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) sur GitHub.

### <a name="step-41-update-the-web-api"></a>Étape 4.1 : Mettre à jour l’API web

1. Ouvrez le fichier *config.json* dans votre éditeur de code.
1. Modifiez les valeurs des variables par celles de l’inscription d’application que vous avez créée précédemment. Mettez également à jour le `policyName` avec le flux d’utilisateur que vous avez créé dans le cadre des prérequis (par exemple, *b2c_1_susi*).
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    ```

### <a name="step-42-enable-cors"></a>Étape 4.2 : Activer CORS

Pour autoriser votre application monopage à appeler l’API web Node.js, vous devez activer [CORS (Cross-Origin Resource Sharing)](https://expressjs.com/en/resources/middleware/cors.html) dans l’API web. Dans une application de production, soyez attentif au domaine qui effectue la demande. Dans cet exemple, autorisez les demandes provenant de n’importe quel domaine.

Pour activer CORS, utilisez l’intergiciel (middleware) suivant. Dans l’exemple de code d’API web Node.js que vous avez téléchargé, il a déjà été ajouté au fichier *index.js*.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

## <a name="step-5-run-the-spa-and-web-api"></a>Étape 5 : Exécuter l’application monopage et l’API web

Vous êtes maintenant prêt à tester l’accès délimité de l’application monopage à l’API. Enfin, vous exécutez à la fois l’API web Node.js et l’exemple d’application monopage JavaScript sur votre machine locale. Ensuite, vous vous connectez à l’application monopage et vous sélectionnez le bouton **Call API** (Appeler l’API) pour envoyer une demande à l’API protégée.

### <a name="run-the-nodejs-web-api"></a>Exécuter l’API web Node.js

1. Ouvrez une fenêtre de console et accédez au répertoire qui contient l’exemple d’API web Node.js. Par exemple :

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Exécutez les commandes suivantes :

    ```console
    npm install && npm update
    node index.js
    ```

    La fenêtre de console affiche le numéro de port de l’endroit où est hébergée l’application.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Exécuter l’application monopage

1. Ouvrez une autre fenêtre de console et accédez au répertoire qui contient l’exemple d’application monopage JavaScript. Par exemple :

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Exécutez les commandes suivantes :

    ```console
    npm install && npm update
    npm start
    ```

    La fenêtre de console affiche le numéro de port de l’endroit où est hébergée l’application.

    ```console
    Listening on port 6420...
    ```

1. Pour voir l’application, accédez à l’adresse `http://localhost:6420` dans votre navigateur.

    ![Capture d’écran de l’exemple d’application monopage affichée dans la fenêtre du navigateur.](./media/configure-authentication-sample-spa-app/sample-app-sign-in.png)

1. Terminez le processus d’inscription ou de connexion. Une fois que vous êtes connecté, vous devez voir le message « Utilisateur \<your username> connecté ».
1. Sélectionnez le bouton **Appeler l’API**. L’application monopage envoie le jeton d’accès dans une requête à l’API web protégée, qui renvoie le nom d’affichage de l’utilisateur connecté :

    ![Capture d’écran de l’application monopage dans une fenêtre de navigateur, montrant le résultat JSON du nom d’utilisateur renvoyé par l’API.](./media/configure-authentication-sample-spa-app/sample-app-result.png)

## <a name="deploy-your-application"></a>Déployer votre application 

Dans une application de production, l’URI de redirection de l’inscription d’application est généralement un point de terminaison accessible publiquement dans lequel votre application s’exécute, comme `https://contoso.com/signin-oidc`. 

Vous pouvez ajouter des URI de redirection à vos applications inscrites à tout moment et les modifier. Les restrictions suivantes s’appliquent aux URI de redirection :

* L’URL de réponse doit commencer par le schéma `https`.
* L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les concepts abordés dans cet article :
* [En savoir plus sur l’exemple de code](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa).
* [Activer l’authentification dans votre propre application monopage](enable-authentication-spa-app.md).
* [Configurer les options d’authentification dans votre application monopage](enable-authentication-spa-app-options.md).
* [Activer l’authentification dans votre propre API web](enable-authentication-web-api.md).
