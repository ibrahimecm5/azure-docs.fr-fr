---
title: 'Tutoriel : Créer une application monopage React qui utilise le flux de code d’authentification | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce tutoriel, vous créer une application SPA React qui peut connecter des utilisateurs et utiliser le flux de code d’authentification pour obtenir un jeton d’accès à partir de la plateforme d’identités Microsoft et appeler l’API Microsoft Graph.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/16/2021
ms.author: jamesmantu
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: a42f4e5cd0b58c2078a4ba498b262efb804edcf8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050277"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-react-single-page-app-spa-using-auth-code-flow"></a>Tutoriel : Connecter les utilisateurs et appeler l’API Microsoft Graph à partir d’une application monopage (SPA) React à l’aide du flux de code d’authentification

Dans ce tutoriel, vous générez une application monopage (SPA) React qui connecte les utilisateurs et appelle Microsoft Graph en utilisant le flux de code d’autorisation avec PKCE. L’application SPA que vous générez utilise la bibliothèque d’authentification MSAL (Microsoft Authentication Library) pour React.

Dans ce tutoriel :
> [!div class="checklist"]
> * Créer un projet React avec `npm`
> * Inscrire l’application dans le Portail Azure
> * Ajouter du code pour prendre en charge la connexion et la déconnexion des utilisateurs
> * Ajouter du code pour appeler l’API Microsoft Graph
> * Test de l'application

MSAL React prend en charge le flux de code d’autorisation dans le navigateur à la place du flux d’octroi implicite. MSAL React ne prend **PAS** en charge le flux implicite.

## <a name="prerequisites"></a>Prérequis

* [Node.js](https://nodejs.org/en/download/) pour l’exécution d’un serveur web local
* [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code

## <a name="how-the-tutorial-app-works"></a>Fonctionnement de l’application du tutoriel

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagramme montrant le flux de code d’autorisation dans une application monopage":::

L’application que vous créez dans ce tutoriel permet à une application SPA React d’interroger l’API Microsoft Graph en faisant l’acquisition de jetons de sécurité auprès de la plateforme d’identités Microsoft. Elle utilise la bibliothèque d’authentification MSAL (Microsoft Authentication Library) pour React, un wrapper de la bibliothèque MSAL.js v2. MSAL React permet aux applications React 16+ d’authentifier les utilisateurs en entreprise à l’aide d’Azure Active Directory (Azure AD) ainsi que les utilisateurs qui ont des comptes Microsoft et des identités de réseaux sociaux tels que Facebook, Google et LinkedIn. La bibliothèque permet aussi aux applications d’accéder aux services cloud Microsoft et à Microsoft Graph.

Dans ce scénario, une fois l’utilisateur connecté, un jeton d’accès est demandé et ajouté aux requêtes HTTP dans l’en-tête d’autorisation. L’acquisition et le renouvellement des jetons sont gérés par la bibliothèque d’authentification Microsoft pour React (MSAL React).

### <a name="libraries"></a>Bibliothèques

Ce tutoriel utilise les bibliothèques suivantes :

|Bibliothèque|Description|
|---|---|
|[MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)|Wrapper de la bibliothèque d’authentification Microsoft pour JavaScript React|
|[Navigateur MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Package de navigateur de la bibliothèque d’authentification Microsoft pour JavaScript v2|

## <a name="get-the-completed-code-sample"></a>Obtenir l’exemple de code complet

Vous préférez plutôt télécharger l’exemple de projet complet de ce tutoriel ? Pour exécuter le projet en utilisant un serveur web local, par exemple Node.js, clonez le dépôt [ms-identity-javascript-react-spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) :

`git clone https://github.com/Azure-Samples/ms-identity-javascript-react-spa`

Ensuite, pour configurer l’exemple de code avant de l’exécuter, passez à l’[étape de configuration](#register-your-application).

Pour poursuivre le tutoriel et générer vous-même l’application, passez à la section suivante, [Prérequis](#prerequisites).

## <a name="create-your-project"></a>Créer votre projet

Une fois [Node.js](https://nodejs.org/en/download/) installé, ouvrez une fenêtre de terminal, puis exécutez les commandes suivantes :

```console
npx create-react-app msal-react-tutorial # Create a new React app
cd msal-react-tutorial # Change to the app directory
npm install @azure/msal-browser @azure/msal-react # Install the MSAL packages
npm install react-bootstrap bootstrap # Install Bootstrap for styling
```

Vous avez démarré un petit projet React en utilisant [Créer une application React](https://create-react-app.dev/docs/getting-started). Il s’agit du point de départ sur lequel s’appuie le reste de ce tutoriel. Si vous souhaitez voir les changements apportés à votre application pendant que vous suivez ce tutoriel, vous pouvez exécuter la commande suivante : 

```console
npm start
```

Une fenêtre de navigateur doit s’ouvrir automatiquement sur votre application. Si ce n’est pas le cas, ouvrez votre navigateur et accédez à http://localhost:3000. Chaque fois que vous enregistrez un fichier avec du code mis à jour, la page se recharge pour refléter les changements apportés.

## <a name="register-your-application"></a>Inscrire votre application

Suivez les étapes décrites dans [Application monopage : inscription d’application](./scenario-spa-app-registration.md) afin de créer une inscription d’application pour votre application SPA à l’aide du portail Azure.

À l’étape [URI de redirection : MSAL.js 2.0 avec flux de code d’authentification](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow), entrez `http://localhost:3000`, l’emplacement par défaut où create-react-app va traiter votre application.

### <a name="configure-your-javascript-spa"></a>Configurer une application SPA JavaScript

1. Créez un fichier nommé *authConfig.js* dans le dossier *src*, qui contiendra vos paramètres de configuration pour l’authentification, puis ajoutez le code suivant :

    ```javascript
    export const msalConfig = {
      auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here", // This is a URL (e.g. https://login.microsoftonline.com/{your tenant ID})
        redirectUri: "Enter_the_Redirect_Uri_Here",
      },
      cache: {
        cacheLocation: "sessionStorage", // This configures where your cache will be stored
        storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
      }
    };
    
    // Add scopes here for ID token to be used at Microsoft identity platform endpoints.
    export const loginRequest = {
     scopes: ["User.Read"]
    };
    
    // Add the endpoints here for Microsoft Graph API services you'd like to use.
    export const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me"
    };
    ```

1. Modifiez les valeurs de la section `msalConfig` de la façon suivante :
    
    |Nom de la valeur| À propos|
    |----------|------|
    |`Enter_the_Application_Id_Here`| **ID d’application (client)** de l’application que vous avez inscrite.|
    |`Enter_the_Cloud_Instance_Id_Here`| instance cloud Azure dans laquelle votre application est inscrite. Pour le cloud Azure principal (ou *mondial*), entrez `https://login.microsoftonline.com`. Pour les clouds **nationaux** (par exemple la Chine), vous trouverez les valeurs appropriées dans [Clouds nationaux](authentication-national-cloud.md).
    |`Enter_the_Tenant_Info_Here`| Définissez cette valeur sur une des options suivantes : Si votre application prend en charge les *comptes dans cet annuaire organisationnel*, remplacez cette valeur par l’ID de l’annuaire (locataire) ou le nom du locataire (par exemple, **contoso.microsoft.com**). Si votre application prend en charge les *Comptes dans un annuaire organisationnel*, remplacez cette valeur par **organizations**. Si votre application prend en charge les *Comptes dans un annuaire organisationnel et comptes personnels Microsoft*, remplacez cette valeur par **common**. Pour limiter la prise en charge aux *Comptes Microsoft personnels uniquement*, remplacez cette valeur par **consumers**. |
    |`Enter_the_Redirect_Uri_Here`|Remplacez par **http://localhost:3000** .|
    |`Enter_the_Graph_Endpoint_Here`| Instance de l’API Microsoft Graph avec laquelle l’application doit communiquer. Pour le point de terminaison de l’API Microsoft Graph **mondial**, remplacez les deux instances de cette chaîne par `https://graph.microsoft.com`. Pour les points de terminaison dans les déploiements dans des clouds **nationaux**, consultez [Déploiements dans des clouds nationaux](/graph/deployments) dans la documentation Microsoft Graph.|
    
    Pour plus d’informations sur les options configurables disponibles, consultez [Initialiser les applications clientes](msal-js-initializing-client-applications.md).

1. Ouvrez le fichier *src/index.js*, puis ajoutez les importations suivantes :

    ```javascript
    import "bootstrap/dist/css/bootstrap.min.css";
    import { PublicClientApplication } from "@azure/msal-browser";
    import { MsalProvider } from "@azure/msal-react";
    import { msalConfig } from "./authConfig";
    ```

1. Sous les importations dans *src/index.js*, créez une instance de `PublicClientApplication` à l’aide de la configuration de l’étape 1.

    ```javascript
    const msalInstance = new PublicClientApplication(msalConfig);
    ``` 

1. Recherchez le composant `<App />` dans *src/index.js*, puis wrappez-le dans le composant `MsalProvider`. Votre fonction d’affichage doit ressembler à ceci :

    ```jsx
    ReactDOM.render(
        <React.StrictMode>
            <MsalProvider instance={msalInstance}>
                <App />
            </MsalProvider>
        </React.StrictMode>,
        document.getElementById("root")
    );
    ``` 


## <a name="sign-in-users"></a>Connexion des utilisateurs

Créez un dossier dans *src* appelé *components*, puis créez dans ce dossier un fichier nommé *SignInButton.jsx*. Ajoutez le code de l’une des sections suivantes pour appeler la connexion à l’aide d’une fenêtre indépendante ou d’une redirection plein cadre :

### <a name="sign-in-using-pop-ups"></a>Se connecter à l’aide de fenêtres contextuelles

Ajoutez le code suivant à *src/components/SignInButton.jsx* pour créer un composant de bouton qui appelle une fenêtre de connexion quand l’utilisateur le sélectionne :

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import Button from "react-bootstrap/Button";

function handleLogin(instance) {
    instance.loginPopup(loginRequest).catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will open a popup for login
 */
export const SignInButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogin(instance)}>Sign in using Popup</Button>
    );
}
```

### <a name="sign-in-using-redirects"></a>Se connecter à l’aide de redirections

Ajoutez le code suivant à *src/components/SignInButton.jsx* pour créer un composant de bouton qui appelle une connexion par redirection quand l’utilisateur le sélectionne :

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import Button from "react-bootstrap/Button";

function handleLogin(instance) {
    instance.loginRedirect(loginRequest).catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will redirect the page to the login prompt
 */
export const SignInButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogin(instance)}>Sign in using Redirect</Button>
    );
}
```

### <a name="add-the-sign-in-button"></a>Ajouter le bouton de connexion

1. Créez dans le dossier *components* un autre fichier nommé *PageLayout.jsx*, puis ajoutez le code suivant pour créer un composant de barre de navigation qui contient le bouton de connexion que vous venez de créer :

    ```jsx
    import React from "react";
    import Navbar from "react-bootstrap/Navbar";
    import { useIsAuthenticated } from "@azure/msal-react";
    import { SignInButton } from "./SignInButton";
    
    /**
     * Renders the navbar component with a sign-in button if a user is not authenticated
     */
    export const PageLayout = (props) => {
        const isAuthenticated = useIsAuthenticated();
    
        return (
            <>
                <Navbar bg="primary" variant="dark">
                    <a className="navbar-brand" href="/">MSAL React Tutorial</a>
                    { isAuthenticated ? <span>Signed In</span> : <SignInButton /> }
                </Navbar>
                <h5><center>Welcome to the Microsoft Authentication Library For React Tutorial</center></h5>
                <br />
                <br />
                {props.children}
            </>
        );
    };
    ```

2. Ouvrez à présent *src/App.js*, et remplacez le contenu existant par le code suivant : 

    ```jsx
    import React from "react";
    import { PageLayout } from "./components/PageLayout";
    
    function App() {
      return (
          <PageLayout>
              <p>This is the main app content!</p>
          </PageLayout>
      );
    }
    
    export default App;
    ```

Votre application dispose désormais d’un bouton de connexion qui s’affiche uniquement pour les utilisateurs non authentifiés !

Quand un utilisateur sélectionne le bouton **Sign in using Popup** ou **Sign in using Redirect** pour la première fois, le gestionnaire `onClick` appelle `loginPopup` (ou `loginRedirect`) pour connecter l’utilisateur. La méthode `loginPopup` ouvre une fenêtre contextuelle avec le *point de terminaison de la plateforme d’identités Microsoft* afin de demander et de valider les informations d’identification de l’utilisateur. Après une connexion réussie, *msal.js* lance le [flux du code d’autorisation](v2-oauth2-auth-code-flow.md).

Un code d’autorisation protégé par PKCE est alors envoyé au point de terminaison de jeton protégé par CORS en vue de l’échange de jetons. Un jeton d’ID, un jeton d’accès et un jeton d’actualisation sont reçus par votre application et traités par *msal.js*, et les informations contenues dans le jeton sont mises en cache.

## <a name="sign-users-out"></a>Déconnecter les utilisateurs

Dans *src/components*, créez un fichier nommé *SignOutButton.jsx*. Ajoutez le code de l’une des sections suivantes pour appeler la déconnexion à l’aide d’une fenêtre indépendante ou d’une redirection plein cadre :

### <a name="sign-out-using-pop-ups"></a>Se déconnecter à l’aide de fenêtres contextuelles

Ajoutez le code suivant à *src/components/SignOutButton.jsx* pour créer un composant de bouton qui appelle une fenêtre de déconnexion quand l’utilisateur le sélectionne :

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import Button from "react-bootstrap/Button";

function handleLogout(instance) {
    instance.logoutPopup().catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will open a popup for logout
 */
export const SignOutButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogout(instance)}>Sign out using Popup</Button>
    );
}
```

### <a name="sign-out-using-redirects"></a>Se déconnecter à l’aide de redirections

Ajoutez le code suivant à *src/components/SignOutButton.jsx* pour créer un composant de bouton qui appelle une déconnexion par redirection quand l’utilisateur le sélectionne :

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import Button from "react-bootstrap/Button";

function handleLogout(instance) {
    instance.logoutRedirect().catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will redirect the page to the logout prompt
 */
export const SignOutButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogout(instance)}>Sign out using Redirect</Button>
    );
}
```

### <a name="add-the-sign-out-button"></a>Ajouter le bouton de déconnexion

Mettez à jour votre composant `PageLayout` dans *src/components/PageLayout.jsx* afin d’afficher le nouveau composant `SignOutButton` pour les utilisateurs authentifiés. Votre code doit ressembler à ceci :

```jsx
import React from "react";
import Navbar from "react-bootstrap/Navbar";
import { useIsAuthenticated } from "@azure/msal-react";
import { SignInButton } from "./SignInButton";
import { SignOutButton } from "./SignOutButton";

/**
 * Renders the navbar component with a sign-in button if a user is not authenticated
 */
export const PageLayout = (props) => {
    const isAuthenticated = useIsAuthenticated();

    return (
        <>
            <Navbar bg="primary" variant="dark">
                <a className="navbar-brand" href="/">MSAL React Tutorial</a>
                { isAuthenticated ? <SignOutButton /> : <SignInButton /> }
            </Navbar>
            <h5><center>Welcome to the Microsoft Authentication Library For React Tutorial</center></h5>
            <br />
            <br />
            {props.children}
        </>
    );
};
```

## <a name="conditionally-render-components"></a>Afficher les composants de manière conditionnelle

Si vous souhaitez afficher certains composants uniquement pour les utilisateurs authentifiés ou non authentifiés, utilisez `AuthenticateTemplate` et/ou `UnauthenticatedTemplate` comme indiqué ci-dessous.

1. Ajoutez l’importation suivante à *src/App.js* :

    ```javascript
    import { AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";
    ```
    
1. Si vous souhaitez afficher certains composants uniquement pour les utilisateurs authentifiés, mettez à jour votre fonction `App` dans *src/App.js* avec le code suivant : 

    ```jsx
    function App() {
        return (
            <PageLayout>
                <AuthenticatedTemplate>
                    <p>You are signed in!</p>
                </AuthenticatedTemplate>
            </PageLayout>
        );
    }
    ```

1. Si vous souhaitez afficher certains composants uniquement pour les utilisateurs non authentifiés, par exemple une invitation à se connecter, mettez à jour votre fonction `App` dans *src/App.js* avec le code suivant : 

    ```jsx
    function App() {
        return (
            <PageLayout>
                <AuthenticatedTemplate>
                    <p>You are signed in!</p>
                </AuthenticatedTemplate>
                <UnauthenticatedTemplate>
                    <p>You are not signed in! Please sign in.</p>
                </UnauthenticatedTemplate>
            </PageLayout>
        );
    }
    ```

## <a name="acquire-a-token"></a>Acquérir un jeton

1. Avant d’appeler une API, telle que Microsoft Graph, vous devez obtenir un jeton d’accès. Ajoutez à *src/App.js* un nouveau composant appelé `ProfileContent` avec le code suivant :

    ```jsx
    function ProfileContent() {
        const { instance, accounts, inProgress } = useMsal();
        const [accessToken, setAccessToken] = useState(null);
    
        const name = accounts[0] && accounts[0].name;
    
        function RequestAccessToken() {
            const request = {
                ...loginRequest,
                account: accounts[0]
            };
    
            // Silently acquires an access token which is then attached to a request for Microsoft Graph data
            instance.acquireTokenSilent(request).then((response) => {
                setAccessToken(response.accessToken);
            }).catch((e) => {
                instance.acquireTokenPopup(request).then((response) => {
                    setAccessToken(response.accessToken);
                });
            });
        }
    
        return (
            <>
                <h5 className="card-title">Welcome {name}</h5>
                {accessToken ? 
                    <p>Access Token Acquired!</p>
                    :
                    <Button variant="secondary" onClick={RequestAccessToken}>Request Access Token</Button>
                }
            </>
        );
    };
    ```

1. Mettez à jour vos importations dans *src/App.js* pour correspondre à ce qui suit :

    ```js
    import React, { useState } from "react";
    import { PageLayout } from "./components/PageLayout";
    import { AuthenticatedTemplate, UnauthenticatedTemplate, useMsal } from "@azure/msal-react";
    import { loginRequest } from "./authConfig";
    import Button from "react-bootstrap/Button";
    ```

1. Enfin, ajoutez votre nouveau composant `ProfileContent` en tant qu’enfant de `AuthenticatedTemplate` dans votre composant `App` au sein de *src/App.js*. Votre composant `App` doit ressembler à ceci :

    ```javascript
    function App() {
      return (
          <PageLayout>
              <AuthenticatedTemplate>
                  <ProfileContent />
              </AuthenticatedTemplate>
              <UnauthenticatedTemplate>
                  <p>You are not signed in! Please sign in.</p>
              </UnauthenticatedTemplate>
          </PageLayout>
      );
    }
    ```

Le code ci-dessus affiche un bouton que les utilisateurs connectés peuvent sélectionner afin de demander un jeton d’accès pour Microsoft Graph.

Une fois qu’un utilisateur s’est connecté, votre application ne doit pas demander aux utilisateurs de se réauthentifier (autrement dit, demander un jeton) chaque fois qu’ils ont besoin d’accéder à une ressource protégée. Pour éviter ces demandes de réauthentification, appelez `acquireTokenSilent`, qui recherche d’abord un jeton d’accès non expiré mis en cache, puis, si nécessaire, utilisez le jeton d’actualisation pour obtenir un nouveau jeton d’accès. Dans certains cas, vous pouvez cependant être amené à forcer les utilisateurs à interagir avec la plateforme d’identité Microsoft. Par exemple :

- Les utilisateurs doivent entrer à nouveau leurs informations d’identification, car la session a expiré.
- Le jeton d’actualisation a expiré.
- Votre application demande l’accès à une ressource et vous avez besoin du consentement de l’utilisateur.
- Une authentification à 2 facteurs est demandée.

L’appel de `acquireTokenPopup` ouvre une fenêtre contextuelle (ou `acquireTokenRedirect` redirige les utilisateurs vers la plateforme d’identité Microsoft). Dans cette fenêtre, les utilisateurs doivent interagir en confirmant leurs informations d’identification, en donnant leur consentement pour la ressource demandée ou en effectuant l’authentification à 2 facteurs.

> [!NOTE]
> Si vous utilisez Internet Explorer, nous vous recommandons d’utiliser les méthodes `loginRedirect` et `acquireTokenRedirect` en raison d’un [problème connu](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups) avec Internet Explorer et les fenêtres contextuelles.

## <a name="call-the-microsoft-graph-api"></a>Appeler l’API Microsoft Graph

1. Créez un fichier nommé *graph.js* dans le dossier *src*, puis ajoutez le code suivant pour effectuer des appels REST à l’API Microsoft Graph :

    ```javascript
    import { graphConfig } from "./authConfig";
    
    /**
     * Attaches a given access token to a Microsoft Graph API call. Returns information about the user
     */
    export async function callMsGraph(accessToken) {
        const headers = new Headers();
        const bearer = `Bearer ${accessToken}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        return fetch(graphConfig.graphMeEndpoint, options)
            .then(response => response.json())
            .catch(error => console.log(error));
    }
    ```

1. Créez ensuite un fichier nommé *ProfileData.jsx* dans *src/components*, puis ajoutez le code suivant :

    ```javascript
    import React from "react";
    
    /**
     * Renders information about the user obtained from Microsoft Graph
     */
    export const ProfileData = (props) => {
        return (
            <div id="profile-div">
                <p><strong>First Name: </strong> {props.graphData.givenName}</p>
                <p><strong>Last Name: </strong> {props.graphData.surname}</p>
                <p><strong>Email: </strong> {props.graphData.userPrincipalName}</p>
                <p><strong>Id: </strong> {props.graphData.id}</p>
            </div>
        );
    };
    ```

1. Ouvrez ensuite *src/App.js*, et ajoutez les lignes suivantes aux importations :
    
    ```javascript
    import { ProfileData } from "./components/ProfileData";
    import { callMsGraph } from "./graph";
    ```

1. Enfin, mettez à jour votre composant `ProfileContent` dans *src/App.js* pour appeler Microsoft Graph et afficher les données de profil après l’obtention du jeton. Votre composant `ProfileContent` doit ressembler à ceci :

    ```javascript
    function ProfileContent() {
        const { instance, accounts } = useMsal();
        const [graphData, setGraphData] = useState(null);
    
        const name = accounts[0] && accounts[0].name;
    
        function RequestProfileData() {
            const request = {
                ...loginRequest,
                account: accounts[0]
            };
    
            // Silently acquires an access token which is then attached to a request for Microsoft Graph data
            instance.acquireTokenSilent(request).then((response) => {
                callMsGraph(response.accessToken).then(response => setGraphData(response));
            }).catch((e) => {
                instance.acquireTokenPopup(request).then((response) => {
                    callMsGraph(response.accessToken).then(response => setGraphData(response));
                });
            });
        }
    
        return (
            <>
                <h5 className="card-title">Welcome {name}</h5>
                {graphData ? 
                    <ProfileData graphData={graphData} />
                    :
                    <Button variant="secondary" onClick={RequestProfileData}>Request Profile Information</Button>
                }
            </>
        );
    };
    ```

Dans les changements effectués ci-dessus, la méthode `callMSGraph()` est utilisée pour envoyer une requête HTTP `GET` à une ressource protégée qui nécessite un jeton. La requête retourne ensuite le contenu à l’appelant. Cette méthode ajoute le jeton acquis dans l’*en-tête d’autorisation HTTP*. Dans l’exemple d’application créé dans ce tutoriel, la ressource protégée est le point de terminaison *me* de l’API Microsoft Graph, qui affiche les informations de profil de l’utilisateur connecté.

## <a name="test-your-application"></a>Tester votre application

Vous avez effectué la création de l’application. Vous êtes désormais prêt à lancer le serveur web et à tester les fonctionnalités de l’application.

1. Démarrez l’application en exécutant la commande suivante à la racine de votre dossier de projet :

   ```console
   npm start
   ```
1. Une fenêtre de navigateur doit s’ouvrir automatiquement sur votre application. Si ce n’est pas le cas, ouvrez votre navigateur et accédez à `http://localhost:3000`. Vous devez voir une page qui ressemble à celle présentée ci-dessous.

    :::image type="content" source="media/tutorial-v2-react/react-01-unauthenticated.png" alt-text="Navigateur web affichant la boîte de dialogue de connexion":::

1. Sélectionnez le bouton de connexion pour vous connecter.

### <a name="provide-consent-for-application-access"></a>Accorder les droits d’accès à l’application

La première fois que vous vous connectez à votre application, vous êtes invité à lui accorder l’accès à votre profil et à vous connecter :

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Boîte de dialogue de contenu affichée dans le navigateur web":::

Si vous donnez votre consentement pour les autorisations demandées, les applications web affichent votre nom, ce qui signifie que la connexion a réussi :

:::image type="content" source="media/tutorial-v2-react/react-02-authenticated.png" alt-text="Résultats d’une connexion réussie dans le navigateur web":::

### <a name="call-the-graph-api"></a>Appeler l’API Graph

Une fois connecté, sélectionnez **See Profile** pour afficher les informations de profil utilisateur retournées dans la réponse à l’appel à l’API Microsoft Graph :

:::image type="content" source="media/tutorial-v2-react/react-03-graph-data.png" alt-text="Informations de profil de Microsoft Graph affichées dans le navigateur":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Informations supplémentaires sur les étendues et les autorisations déléguées

L’API Microsoft Graph nécessite l’étendue *user.read* pour lire le profil d’un utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans le Portail Azure. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. Par exemple, l’API Microsoft Graph nécessite l’étendue *Mail.Read* afin de lister l’e-mail de l’utilisateur.

À mesure que vous ajoutez des étendues, vos utilisateurs peuvent être invités à accorder un consentement supplémentaire pour les étendues ajoutées.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez approfondir le développement d’applications monopages JavaScript sur la plateforme d’identités Microsoft, consultez notre série de scénarios en plusieurs parties :

> [!div class="nextstepaction"]
> [Scénario : Application monopage](scenario-spa-overview.md)
