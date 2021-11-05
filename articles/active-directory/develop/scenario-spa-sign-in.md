---
title: Connexion et déconnexion d’une application monopage
titleSuffix: Microsoft identity platform
description: Apprendre à créer une application monopage (connexion)
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 2fa29b8cfd15ccee133ef416d729c1807aa0a400
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017917"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Application monopage : Se connecter et se déconnecter

Découvrez comment ajouter une connexion au code de votre application monopage.

Pour obtenir des jetons d’accès aux API de votre application, vous avez besoin d’un contexte d’utilisateur authentifié. Vous pouvez connecter des utilisateurs à votre application dans MSAL.js de deux manières :

* [Fenêtre contextuelle](#sign-in-with-a-pop-up-window), au moyen de la méthode `loginPopup`
* [Redirection](#sign-in-with-redirect), au moyen de la méthode `loginRedirect`

Vous pouvez également passer les étendues des API, pour lesquelles l’utilisateur doit donner son consentement au moment de la connexion.

> [!NOTE]
> Si votre application a déjà accès à un contexte d’utilisateur authentifié ou à un jeton d’ID, vous pouvez ignorer l’étape de connexion et obtenir directement des jetons. Pour obtenir des informations détaillées, consultez [Authentification unique sans connexion via msal.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Choix entre une expérience avec fenêtre indépendante ou avec redirection

Le choix entre la fenêtre contextuelle et la redirection dépend du flux de votre application :

* Si vous ne souhaitez pas que les utilisateurs quittent la page principale de votre application lors de l’authentification, nous vous recommandons d’utiliser la méthode avec fenêtre contextuelle. La redirection de l’authentification se produisant dans une fenêtre contextuelle, l’état de l’application principale est conservé.

* Si les utilisateurs sont soumis à des contraintes imposées par le navigateur ou par des stratégies qui bloquent les fenêtres contextuelles, vous pouvez utiliser la méthode avec redirection. Utilisez la méthode avec redirection dans le navigateur Internet Explorer, car il existe des [problèmes connus avec l’utilisation de fenêtres contextuelles dans Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups).

## <a name="sign-in-with-a-pop-up-window"></a>Se connecter avec une fenêtre contextuelle

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let accountId = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        accountId = loginResponse.account.homeAccountId;
        // Display signed-in user content, call API, etc.
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

Le wrapper MSAL Angular vous permet de sécuriser des itinéraires spécifiques dans votre application en ajoutant `MsalGuard` à la définition d’itinéraire. Cette protection appelle la méthode de connexion en cas d'accès à l'itinéraire.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
    {
        path: 'profile',
        component: ProfileComponent,
        canActivate: [
            MsalGuard
        ]
    },
    {
        path: '',
        component: HomeComponent
    }
];

@NgModule({
    imports: [RouterModule.forRoot(routes, { useHash: false })],
    exports: [RouterModule]
})
export class AppRoutingModule { }
```

Pour une expérience de fenêtre contextuelle, définissez `interactionType` sur `InteractionType.Popup` dans la configuration de Guard. Vous pouvez également passer les étendues nécessitant un consentement comme suit :

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Popup, // Msal Guard Configuration
            authRequest: {
                scopes: ['user.read']
            }
        }, null)
    ]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)
Le wrapper MSAL Angular vous permet de sécuriser des itinéraires spécifiques dans votre application en ajoutant `MsalGuard` à la définition d’itinéraire. Cette protection appelle la méthode de connexion en cas d'accès à l'itinéraire.
```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];
@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Pour une expérience de fenêtre contextuelle, activez l’option de configuration `popUp`. Vous pouvez également passer les étendues nécessitant un consentement comme suit :

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```

# <a name="react"></a>[React](#tab/react)

Le wrapper MSAL React vous permet de protéger des composants spécifiques en les encapsulant dans le composant `MsalAuthenticationTemplate`. Si un utilisateur n'est pas déjà connecté, ce composant appellera la connexion ; sinon, il affichera les composants enfants.

```javascript
import { InteractionType } from "@azure/msal-browser";
import { MsalAuthenticationTemplate, useMsal } from "@azure/msal-react";

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <MsalAuthenticationTemplate interactionType={InteractionType.Popup}>
            <p>This will only render if a user is signed-in.</p>
            <WelcomeUser />
        </MsalAuthenticationTemplate>
      )
};
```

Vous pouvez également utiliser directement les API `@azure/msal-browser` pour appeler une connexion associée aux composants `AuthenticatedTemplate` et/ou `UnauthenticatedTemplate` afin d'afficher du contenu spécifique aux utilisateurs connectés ou déconnectés, respectivement. Il s'agit de l'approche recommandée si vous devez appeler la connexion suite à une interaction utilisateur, comme un clic sur un bouton.

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signInClickHandler(instance) {
    instance.loginPopup();
}

// SignInButton Component returns a button that invokes a popup login when clicked
function SignInButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signInClickHandler(instance)}>Sign In</button>
};

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <WelcomeUser />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
                <SignInButton />
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-in-with-redirect"></a>Se connecter avec une redirection

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let accountId = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    if (response !== null) {
        accountId = response.account.homeAccountId;
        // Display signed-in user content, call API, etc.
    } else {
        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();

        if (currentAccounts.length === 0) {
            // no accounts signed-in, attempt to sign a user in
            myMsal.loginRedirect(loginRequest);
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            accountId = currentAccounts[0].homeAccountId;
        }
    }
}

myMsal.handleRedirectPromise().then(handleResponse);
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

Les méthodes de redirection ne retournent pas de promesse en raison du déplacement en dehors de l’application principale. Pour traiter les jetons retournés et y accéder, enregistrez les rappels de réussite et d’erreur avant d’appeler les méthodes de redirection.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

Le code est ici le même que celui décrit précédemment dans la section relative à la connexion avec une fenêtre contextuelle, sauf que `interactionType` est défini sur `InteractionType.Redirect` pour la configuration de MsalGuard, et que le composant `MsalRedirectComponent` est amorcé pour gérer les redirections.

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalModule, MsalRedirectComponent } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Redirect, // Msal Guard Configuration
            authRequest: {
                scopes: ['user.read']
            }
        }, null)
    ],
    bootstrap: [AppComponent, MsalRedirectComponent]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

Ce code est identique à celui décrit plus haut, à la section traitant de la connexion au moyen d’une fenêtre contextuelle. La redirection correspond au flux par défaut.

# <a name="react"></a>[React](#tab/react)

Le wrapper MSAL React vous permet de protéger des composants spécifiques en les encapsulant dans le composant `MsalAuthenticationTemplate`. Si un utilisateur n'est pas déjà connecté, ce composant appellera la connexion ; sinon, il affichera les composants enfants.

```javascript
import { InteractionType } from "@azure/msal-browser";
import { MsalAuthenticationTemplate, useMsal } from "@azure/msal-react";

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <MsalAuthenticationTemplate interactionType={InteractionType.Redirect}>
            <p>This will only render if a user is signed-in.</p>
            <WelcomeUser />
        </MsalAuthenticationTemplate>
      )
};
```

Vous pouvez également utiliser directement les API `@azure/msal-browser` pour appeler une connexion associée aux composants `AuthenticatedTemplate` et/ou `UnauthenticatedTemplate` afin d'afficher du contenu spécifique aux utilisateurs connectés ou déconnectés, respectivement. Il s'agit de l'approche recommandée si vous devez appeler la connexion suite à une interaction utilisateur, comme un clic sur un bouton.

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signInClickHandler(instance) {
    instance.loginRedirect();
}

// SignInButton Component returns a button that invokes a popup login when clicked
function SignInButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signInClickHandler(instance)}>Sign In</button>
};

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <WelcomeUser />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
                <SignInButton />
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-out-with-a-pop-up-window"></a>Se déconnecter avec une fenêtre contextuelle

MSAL.js v2 fournit une méthode `logoutPopup` qui vide le cache dans le stockage du navigateur et ouvre une fenêtre contextuelle sur la page de déconnexion d'Azure Active Directory (Azure AD). Après la déconnexion, Azure AD redirige la fenêtre contextuelle vers votre application et MSAL.js ferme la fenêtre contextuelle.

Vous pouvez configurer l'URI vers lequel Azure AD doit rediriger après la déconnexion en définissant `postLogoutRedirectUri`. Cet URI doit être enregistré en tant qu’URI de redirection dans l’inscription de votre application.

Vous pouvez également configurer `logoutPopup` de manière à rediriger la fenêtre principale vers une autre page, telle que la page d'accueil ou la page de connexion, une fois la déconnexion terminée en transmettant `mainWindowRedirectUri` dans le cadre de la requête.

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", // defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByHomeId(homeAccountId),
    mainWindowRedirectUri: "your_app_main_window_redirect_uri"
}

await myMsal.logoutPopup(logoutRequest);
```
# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

La déconnexion à l'aide d'une fenêtre contextuelle n'est pas prise en charge dans MSAL.js v1

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: 'your_app_logout_redirect_uri'
            }
        }), null, null)
    ]
})

// In app.component.ts
logout() {
    this.authService.logoutPopup({
        mainWindowRedirectUri: "/"
    });
}
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

La déconnexion à l'aide d'une fenêtre contextuelle n'est pas prise en charge dans MSAL Angular v1

# <a name="react"></a>[React](#tab/react)

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signOutClickHandler(instance) {
    const logoutRequest = {
        account: instance.getAccountByHomeId(homeAccountId),
        mainWindowRedirectUri: "your_app_main_window_redirect_uri",
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
    instance.logoutPopup(logoutRequest);
}

// SignOutButton Component returns a button that invokes a popup logout when clicked
function SignOutButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signOutClickHandler(instance)}>Sign Out</button>
};

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <SignOutButton />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-out-with-a-redirect"></a>Se déconnecter à l'aide d'une redirection

MSAL.js fournit une méthode `logout` dans la v1, et une méthode `logoutRedirect` dans la v2, qui vide le cache dans le stockage du navigateur et redirige la fenêtre vers la page de déconnexion d'Azure Active Directory (Azure AD). Après la déconnexion, Azure AD redirige par défaut vers la page qui a appelé la déconnexion.

Vous pouvez configurer l’URI de redirection après la déconnexion en définissant `postLogoutRedirectUri`. Cet URI doit être enregistré en tant qu’URI de redirection dans l’inscription de votre application.

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByHomeId(homeAccountId)
}

myMsal.logoutRedirect(logoutRequest);
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: 'your_app_logout_redirect_uri'
            }
        }), null, null)
    ]
})

// In app.component.ts
logout() {
    this.authService.logoutRedirect();
}
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})
// In app.component.ts
this.authService.logout();
```

# <a name="react"></a>[React](#tab/react)

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signOutClickHandler(instance) {
    const logoutRequest = {
        account: instance.getAccountByHomeId(homeAccountId),
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
    instance.logoutRedirect(logoutRequest);
}

// SignOutButton Component returns a button that invokes a redirect logout when clicked
function SignOutButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signOutClickHandler(instance)}>Sign Out</button>
};

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <SignOutButton />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Acquérir un jeton pour l’application](scenario-spa-acquire-token.md).
