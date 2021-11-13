---
title: Authentification unique (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: En savoir plus sur la création d’expériences d’authentification unique à l’aide de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: e715b0b2176564c8a492df20b9193d66a8fb85c8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466083"
---
# <a name="single-sign-on-with-msaljs"></a>Authentification unique avec MSAL.js

L’authentification unique (SSO) permet aux utilisateurs d’indiquer une seule fois leurs informations d’identification pour se connecter et établir une session, qui peut être réutilisée dans plusieurs applications sans avoir à se réauthentifier. La session offre une expérience fluide à l’utilisateur et réduit la répétition d’invites à entrer des informations d’identification.

Azure Active Directory (Azure AD) fournit des fonctionnalités d’authentification unique aux applications, en définissant un cookie de session lorsque l’utilisateur s’authentifie pour la première fois. La bibliothèque MSAL.js permet aux applications d’appliquer un cookie de session de plusieurs façons.

## <a name="sso-between-browser-tabs"></a>Authentification unique entre des onglets de navigateur

Lorsque votre application est ouverte dans plusieurs onglets et que vous authentifiez l’utilisateur sur un onglet, ce dernier l’est également sur les autres onglets et ne reçoit plus d’invites. Le fichier MSAL.js met en cache le jeton d’ID de l’utilisateur dans le navigateur `localStorage` et connecte cet utilisateur à l’application sur les autres onglets ouverts.

Par défaut, MSAL.js utilise `sessionStorage`, qui n’autorise pas le partage de la session entre les onglets. Pour configurer l’authentification unique entre les onglets, dans MSAL.js, veillez à définir le paramètre `cacheLocation` sur `localStorage`, comme indiqué ci-dessous.

```javascript
const config = {
  auth: {
    clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg",
  },
  cache: {
    cacheLocation: "localStorage",
  },
};

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Authentification unique entre les applications

Lorsqu’un utilisateur s’authentifie, un cookie de session est défini sur le domaine Azure AD dans le navigateur. Le fichier MSAL.js s’appuie sur ce cookie de session pour assurer l’authentification unique de l’utilisateur entre les différentes applications. Il met également en cache les jetons d’ID et jetons d’accès de l’utilisateur dans le stockage de navigateur par domaine d’application. Par conséquent, le comportement de l’authentification unique varie selon les cas :

### <a name="applications-on-the-same-domain"></a>Applications sur le même domaine

Lorsque les applications sont hébergées sur le même domaine, l’utilisateur peut se connecter à une application une seule fois et être authentifié auprès des autres applications sans recevoir d’invites. Le fichier MSAL.js utilise les jetons mis en cache pour l’utilisateur sur le domaine pour fournir l’authentification unique.

### <a name="applications-on-different-domain"></a>Applications sur un autre domaine

Lorsque les applications sont hébergées sur des domaines différents, les jetons mis en cache sur le domaine A ne sont pas accessibles pour le fichier MSAL.js dans le domaine B.

Lorsqu’un utilisateur est connecté au domaine A, accède à une application sur le domaine B, il est redirigé ou invité dans la page de connexion. Comme Azure AD a toujours le cookie de session utilisateur, il connecte l’utilisateur et ne demande pas d’informations d’identification.

Si l’utilisateur dispose de plusieurs comptes d’utilisateur connectés via plusieurs sessions à Azure AD, il est invité à choisir le compte avec lequel il doit s’authentifier.

### <a name="automatically-select-account-on-azure-ad"></a>Sélection automatique du compte sur Azure AD

Dans certains cas, l’application a accès au contexte d’authentification de l’utilisateur et il n’est pas nécessaire de contourner l’invite de sélection de compte Azure AD lorsque plusieurs comptes sont connectés. Le contournement de l’invite de sélection de compte Azure AD peut être effectué de différentes manières :

**Avec l’ID de session**

L’ID de session (SID) est une [revendication facultative](active-directory-optional-claims.md) qui peut être configurée dans les jetons d’ID. Une revendication permet à l’application d’identifier la session Azure AD de l’utilisateur et ce, indépendamment du nom ou du compte de cet utilisateur. Vous pouvez transmettre le SID dans les paramètres de la requête d’appel de `acquireTokenSilent`. Le `acquireTokenSilent` dans les paramètres de la demande autorise Azure AD à contourner la sélection de compte. Le SID est lié au cookie de session et ne dépasse pas les contextes de navigateur.

```javascript
var request = {
  scopes: ["user.read"],
  sid: sid,
};

userAgentApplication
  .acquireTokenSilent(request)
  .then(function (response) {
    const token = response.accessToken;
  })
  .catch(function (error) {
    //handle error
  });
```

Il peut être utilisé uniquement avec les requêtes d’authentification en mode silencieux effectuées par l’appel de `acquireTokenSilent` dans MSAL.js. Pour trouver les étapes de configuration des revendications facultatives dans votre manifeste d’application, consultez [Fournir des revendications facultatives à votre application](active-directory-optional-claims.md).

**Utilisation d’un indicateur de connexion**

Si vous n’avez pas de revendication SID configurée ou si vous avez besoin de contourner l’invite de sélection de compte dans des appels d’authentification interactifs, vous pouvez fournir un élément `login_hint` dans les paramètres de requête et, éventuellement, un élément `domain_hint` en tant que `extraQueryParameters` dans les méthodes interactives du fichier MSAL.js (`loginPopup`, `loginRedirect`, `acquireTokenPopup` et `acquireTokenRedirect`). Par exemple :

```javascript
var request = {
  scopes: ["user.read"],
  loginHint: preferred_username,
  extraQueryParameters: { domain_hint: "organizations" },
};

userAgentApplication.loginRedirect(request);
```

Pour obtenir les valeurs de login_hint et domain_hint en lisant les revendications retournées dans le jeton d’ID de l’utilisateur.

- Le paramètre **loginHint** doit être défini sur la revendication `preferred_username` dans le jeton d’ID.

- **domain_hint** ne doit être passé que lors de l’utilisation de l’autorité /common. L’indicateur de domaine est déterminé par l’ID de locataire (tid). Si la revendication `tid` du jeton d’ID est `9188040d-6c67-4c5b-b112-36a304b66dad`, cela correspond à des consommateurs. Sinon, il s’agit d’organisations.

Pour plus d’informations sur **login_hint** et **domain_hint**, consultez [Flux d’octroi implicite](v2-oauth2-implicit-grant-flow.md).

## <a name="sso-without-msaljs-login"></a>Authentification unique sans connexion via MSAL.js

Par conception, le fichier MSAL.js nécessite l’appel d’une méthode de connexion pour établir un contexte d’utilisateur avant d’obtenir des jetons pour les API. Dans la mesure où les méthodes de connexion sont interactives, l’utilisateur voit une invite.

Dans certains cas, les applications ont accès au jeton d’ID ou au contexte de l’utilisateur authentifié via une authentification lancée par une autre application, et souhaitent utiliser l’authentification unique pour acquérir des jetons sans avoir à se connecter d’abord via MSAL.js.

Exemple : Un utilisateur est connecté à un compte Microsoft dans un navigateur qui héberge une autre application JavaScript exécutée en tant qu’extension (add-on) ou plug-in, ce qui demande une connexion au compte Microsoft.

L’expérience d’authentification unique de ce scénario peut être obtenue comme suit :

Passez l’élément `sid` s’il est disponible (ou `login_hint` et éventuellement `domain_hint`) en tant que paramètres de requête d’appel de `acquireTokenSilent` dans MSAL.js, comme suit :

```javascript
var request = {
  scopes: ["user.read"],
  loginHint: preferred_username,
  extraQueryParameters: { domain_hint: "organizations" },
};

userAgentApplication
  .acquireTokenSilent(request)
  .then(function (response) {
    const token = response.accessToken;
  })
  .catch(function (error) {
    //handle error
  });
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>Authentification unique dans ADAL.js pour la mise à jour de MSAL.js

Le fichier MSAL.js fournit une fonctionnalité de parité avec ADAL.js, pour les scénarios d’authentification Azure AD. Pour faciliter la migration depuis ADAL.js vers MSAL.js et éviter l’envoi d’invites d’authentification à vos utilisateurs, la bibliothèque lit le jeton d’ID représentant la session de l’utilisateur dans le cache du fichier ADAL.js, et le connecte en toute transparence via MSAL.js.

Pour tirer parti du comportement de l’authentification unique lors de la mise à jour du fichier ADAL.js, vous devez vous assurer que les bibliothèques utilisent le paramètre `localStorage` pour la mise en cache des jetons. Définissez le paramètre `cacheLocation` sur `localStorage` dans la configuration des fichiers MSAL.js et ADAL.js lors de l’initialisation, en procédant comme suit :

```javascript
// In ADAL.js
window.config = {
  clientId: "g075edef-0efa-453b-997b-de1337c29185",
  cacheLocation: "localStorage",
};

var authContext = new AuthenticationContext(config);

// In latest MSAL.js version
const config = {
  auth: {
    clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg",
  },
  cache: {
    cacheLocation: "localStorage",
  },
};

const myMSALObj = new UserAgentApplication(config);
```

Une fois que `cacheLocation` est configuré, MSAL.js peut lire l’état de l’utilisateur authentifié qui a été mis en cache dans ADAL.js et l’utiliser pour assurer l’authentification unique dans MSAL.js.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’authentification unique, consultez :

- [Protocole SAML d’authentification unique](single-sign-on-saml-protocol.md)
- [Durées de vie des jetons configurables](active-directory-configurable-token-lifetimes.md)
