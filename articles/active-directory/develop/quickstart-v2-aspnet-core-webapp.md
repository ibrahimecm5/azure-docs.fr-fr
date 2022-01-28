---
title: 'Démarrage rapide : Ajouter la connexion Microsoft Identity à une application web ASP.NET Core | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous découvrez comment une application implémente la connexion Microsoft dans une application web ASP.NET Core en utilisant OpenID Connect
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
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:aspnet-core", mode-other
ms.openlocfilehash: 7dad1d4840e24c1a2a7b65094ee5c28f45a73f44
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2022
ms.locfileid: "137802501"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application web ASP.NET Core peut connecter des utilisateurs à partir de n’importe quelle organisation Azure Active Directory (Azure AD).  

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
Pour que l’exemple de code de ce guide de démarrage rapide fonctionne :
- Pour **URI de redirection**, entrez **https://localhost:44321/** et **https://localhost:44321/signin-oidc** .
- Pour **URL de déconnexion du canal frontal**, entrez **https://localhost:44321/signout-oidc** . 

Le point de terminaison d’autorisation émettra des jetons d’ID de demande.
> [!div class="nextstepaction"]
> [Apporter cette modification pour moi]()

> [!div class="alert alert-info"]
> ![Déjà configuré](media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-aspnet-core-project"></a>Étape 2 : Télécharger le projet ASP.NET Core

Exécutez le projet.

> [!div class="nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]


#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
Nous avons configuré votre projet avec les valeurs des propriétés de votre application, il est prêt à être exécuté.

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Informations complémentaires

Cette section offre une vue d’ensemble du code requis pour connecter les utilisateurs. Cette vue d’ensemble peut être utile pour comprendre comment le code fonctionne, quels sont les principaux arguments et comment ajouter une connexion à une application ASP.NET Core existante.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple
>
> ![Diagramme de l’interaction entre le navigateur web, l’application web et la plateforme d’identités Microsoft dans l’exemple d’application.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe de démarrage

Le middleware (intergiciel) *Microsoft.AspNetCore.Authentication* utilise une classe `Startup` qui s’exécute lors du démarrage du processus d’hébergement :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

    services.AddControllersWithViews(options =>
    {
        var policy = new AuthorizationPolicyBuilder()
            .RequireAuthenticatedUser()
            .Build();
        options.Filters.Add(new AuthorizeFilter(policy));
    });
   services.AddRazorPages()
        .AddMicrosoftIdentityUI();
}
```

La méthode `AddAuthentication()` configure le service pour ajouter l’authentification basée sur les cookies. Cette authentification est utilisée dans les scénarios de navigateur et pour définir la demande d’authentification sur OpenID Connect.

La ligne qui contient `.AddMicrosoftIdentityWebApp` ajoute à votre application l’authentification auprès de la plateforme d’identités Microsoft. L’application est ensuite configurée pour connecter les utilisateurs en fonction des informations suivantes contenues dans la section `AzureAD` du fichier de configuration *appsettings.json* :

| Clé *appsettings.json* | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | ID d’application (client) de l’application inscrite dans le portail Azure.                                                                                       |
| `Instance`             | Point de terminaison du service d’émission de jeton de sécurité (STS) pour l’utilisateur à authentifier. Cette valeur est généralement `https://login.microsoftonline.com/`, ce qui indique le cloud public Azure. |
| `TenantId`             | Nom de votre locataire ou de l’ID de locataire (GUID), ou `common` pour connecter les utilisateurs avec des comptes professionnels ou scolaires, ou des comptes personnels Microsoft.                             |

La méthode `Configure()` contient deux méthodes importantes, `app.UseAuthentication()` et `app.UseAuthorization()`, qui activent leurs fonctionnalités nommées : Également dans la méthode `Configure()`, vous devez inscrire les routes Microsoft Identity Web avec au moins un appel à `endpoints.MapControllerRoute()` ou un appel à `endpoints.MapControllers()` :

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{
    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});
```

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Attribut pour la protection d’un contrôleur ou des méthodes

Vous pouvez protéger un contrôleur ou les méthodes d’un contrôleur en utilisant l’attribut `[Authorize]`. Cet attribut limite l’accès au contrôleur ou aux méthodes en autorisant uniquement les utilisateurs authentifiés. Une demande d’authentification peut être démarrée pour accéder au contrôleur si l’utilisateur n’est pas authentifié.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Le dépôt GitHub qui contient ce tutoriel ASP.NET Core inclut des instructions et d’autres exemples de code qui vous montrent comment :

- Ajouter l’authentification à une nouvelle application web ASP.NET Core
- Appeler Microsoft Graph, d’autres API Microsoft ou vos propres API web
- Ajouter une autorisation
- Connecter des utilisateurs dans des clouds nationaux ou avec des identités sociales

> [!div class="nextstepaction"]
> [Tutoriels concernant les applications web ASP.NET Core sur GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
