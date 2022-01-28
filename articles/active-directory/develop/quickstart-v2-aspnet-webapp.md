---
title: 'Démarrage rapide : Application web ASP.NET qui connecte les utilisateurs'
titleSuffix: Microsoft identity platform
description: Téléchargez et exécutez un exemple de code qui montre comment une application web ASP.NET peut connecter des utilisateurs Azure AD.
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
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:ASP.NET", contperf-fy21q1, mode-other
ms.openlocfilehash: b784950dcd1a5b8f1a55d45719a79a64f1fbdfba
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2022
ms.locfileid: "137805049"
---
# <a name="quickstart-aspnet-web-app-that-signs-in-azure-ad-users"></a>Démarrage rapide : Application web ASP.NET qui connecte des utilisateurs Azure AD

Dans ce guide de démarrage rapide, vous allez télécharger et exécuter un exemple de code qui montre comment une application web ASP.NET peut connecter des utilisateurs avec des comptes Azure AD (Azure Active Directory).

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, entrez **https://localhost:44368/** pour **URI de redirection**.

> [!div class="nextstepaction"]
> [Apporter cette modification pour moi]()

> [!div class="alert alert-info"]
> ![Déjà configuré](media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec cet attribut.

#### <a name="step-2-download-the-project"></a>Étape 2 : Téléchargez le projet

Exécutez le projet avec Visual Studio 2019.
> [!div class="sxs-lookup nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]


#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
Nous avons configuré votre projet avec les valeurs des propriétés de votre application.

1. Extrayez le fichier .zip dans un dossier local proche du dossier racine. Par exemple, extrayez-le dans *C:\Azure-Samples*.
   
   Nous vous recommandons d’extraire l’archive dans un répertoire près de la racine de votre lecteur pour éviter les erreurs dues à des limitations de longueur de chemin sur Windows.
2. Ouvrez la solution dans Visual Studio (*AppModelv2-WebApp-OpenIDConnect-DotNet.sln*).
3. Selon la version de Visual Studio, vous devrez peut-être cliquer avec le bouton droit sur le projet **AppModelv2-WebApp-OpenIDConnect-DotNet**, puis sélectionner **Restaurer les packages NuGet**.
4. Ouvrez la console du Gestionnaire de package en sélectionnant **Afficher** > **Autres fenêtres** > **Console du Gestionnaire de package**. Exécutez ensuite `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`.

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Informations complémentaires

Cette section offre une vue d’ensemble du code requis pour connecter les utilisateurs. Cette vue d’ensemble peut être utile pour comprendre comment le code fonctionne, quels sont les principaux arguments et comment ajouter une connexion à une application ASP.NET existante.


### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple

![Diagramme de l’interaction entre le navigateur web, l’application web et la plateforme d’identités Microsoft dans l’exemple d’application.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>Packages NuGet de l’intergiciel (middleware) OWIN

Vous pouvez configurer le pipeline d’authentification avec une authentification basée sur les cookies en utilisant OpenID Connect dans ASP.NET avec des packages du middleware OWIN. Vous pouvez installer ces packages en exécutant les commandes suivantes dans la console du Gestionnaire de package au sein de Visual Studio :

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>Classe de démarrage OWIN

Le middleware OWIN utilise une *classe de démarrage* qui s’exécute lors du démarrage du processus d’hébergement. Dans ce guide de démarrage rapide, le fichier *startup.cs* se trouve dans le dossier racine. Le code suivant montre les paramètres qu’utilise ce guide de démarrage rapide :

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  | Description |
> |---------|---------|
> | `ClientId`     | ID de l’application inscrite dans le portail Azure. |
> | `Authority`    | Point de terminaison du service d’émission de jeton de sécurité (STS) pour l’utilisateur à authentifier. Il s’agit généralement de `https://login.microsoftonline.com/{tenant}/v2.0` pour le cloud public. Dans cette URL, *{tenant}* est le nom de votre locataire, votre ID de locataire ou `common` pour une référence au point de terminaison commun. (Le point de terminaison commun est utilisé pour les applications multilocataires.) |
> | `RedirectUri`  | URL vers laquelle les utilisateurs sont dirigés après authentification auprès de la plateforme d’identités Microsoft. |
> | `PostLogoutRedirectUri`     | URL vers laquelle les utilisateurs sont envoyés après validation. |
> | `Scope`     | Liste des étendues demandées, séparées par des espaces. |
> | `ResponseType`     | Demande que la réponse de l’authentification contienne un code d’autorisation et un jeton d’ID. |
> | `TokenValidationParameters`     | Liste de paramètres pour la validation du jeton. Dans ce cas, `ValidateIssuer` a la valeur `false` pour indiquer qu’il peut accepter des connexions à partir de tout type de compte : personnel, professionnel ou scolaire. |
> | `Notifications`     | Liste de délégués qui peuvent être exécutés sur des messages `OpenIdConnect`. |


> [!NOTE]
> Le paramètre `ValidateIssuer = false` est une simplification destinée aux seules fins de ce guide de démarrage rapide. Dans les applications réelles, validez l’émetteur. Pour savoir comment procéder, consultez les exemples.

### <a name="authentication-challenge"></a>Demande d’authentification

Vous pouvez forcer un utilisateur à se connecter en effectuant une demande d’authentification dans votre contrôleur :

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> La demande d’une demande d’authentification avec cette méthode est facultative. Normalement, vous l’utilisez quand vous souhaitez qu’une vue soit accessible par des utilisateurs à la fois authentifiés et non authentifiés. Vous pouvez également protéger les contrôleurs à l’aide de la méthode décrite dans la section suivante.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Attribut pour protéger un contrôleur ou les actions d’un contrôleur

Vous pouvez protéger un contrôleur ou les actions d’un contrôleur en utilisant l’attribut `[Authorize]`. Cet attribut limite l’accès au contrôleur ou aux actions en autorisant uniquement les utilisateurs authentifiés à accéder aux actions dans le contrôleur. Une demande d’authentification se produit alors automatiquement quand un utilisateur non authentifié tente d’accéder à l’une des actions ou à l’un des contrôleurs décorés par l’attribut `[Authorize]`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à créer, étape par étape, des applications et des fonctionnalités, et pour obtenir une explication complète de ce démarrage rapide, essayez le tutoriel ASP.NET.

> [!div class="nextstepaction"]
> [Ajouter une connexion à une application web ASP.NET](tutorial-v2-asp-webapp.md)
