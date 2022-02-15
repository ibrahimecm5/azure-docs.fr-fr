---
title: 'Démarrage rapide : Application web ASP.NET Core qui connecte des utilisateurs et appelle Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous découvrez comment une application utilise Microsoft.Identity.Web pour implémenter la connexion Microsoft dans une application web ASP.NET Core à l’aide d’OpenID Connect et appeler Microsoft Graph.
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
ms.custom: devx-track-csharp, aaddev, "scenarios:getting-started", "languages:aspnet-core", mode-other
ms.openlocfilehash: 0fa66fab1da99165082b85109da1f95742428c50
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138032040"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Démarrage rapide : Application web ASP.NET Core qui connecte des utilisateurs et appelle Microsoft Graph en leur nom

> [!div renderon="docs"]
> Bienvenue ! Ce n’est probablement pas la page attendue. Pendant que nous travaillons sur un correctif, ce lien vous permet d’accéder à l’article approprié :
>
> > [Démarrage rapide : Application web ASP.NET Core qui connecte des utilisateurs et appelle une API web](web-app-quickstart.md?pivots=devlang-aspnet-core)
> 
> Nous vous prions de nous excuser pour le désagrément et nous vous remercions de votre patience.

> [!div renderon="portal" class="sxs-lookup"]
> Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application web ASP.NET Core peut connecter des utilisateurs à partir de n’importe quelle organisation Azure Active Directory (Azure AD) et appeler Microsoft Graph.
> 
> Consultez [Fonctionnement de l’exemple](#how-the-sample-works) pour obtenir une illustration.
> 
> ## <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> 
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, ajoutez l’**URI de redirection** `https://localhost:44321/signin-oidc` et > l’**URL de déconnexion de canal frontal** `https://localhost:44321/signout-oidc` dans l’inscription d’application.
> > [!div class="nextstepaction"]
> > [Apporter cette modification pour moi]()
> 
> > [!div class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec ces attributs.
> 
> ## <a name="step-2-download-the-aspnet-core-project"></a>Étape 2 : Télécharger le projet ASP.NET Core
> 
> Exécutez le projet.
> 
> > [!div class="nextstepaction"]
> > [Téléchargez l’exemple de code](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip).
> 
> [!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]
> 
> 
> ## <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
> 
> Nous avons configuré votre projet avec les valeurs des propriétés de votre application et il est prêt à être exécuté.
> 
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> 
> ## <a name="about-the-code"></a>À propos du code
> 
> Cette section fournit une vue d’ensemble du code requis pour la connexion des utilisateurs et l’appel de l’API Microsoft Graph en leur nom. Cette vue d’ensemble peut être utile pour comprendre comment le code fonctionne, les principaux arguments et également si vous souhaitez ajouter une connexion à une application ASP.NET Core existante et appeler Microsoft Graph. Elle utilise [Microsoft.Identity.Web](microsoft-identity-web.md), qui est un wrapper autour de [MSAL.NET](msal-overview.md).
> 
> ### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple
> 
> ![Fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-aspnet-core-webapp-calls-graph/> aspnetcorewebapp-intro.svg)
> 
> ### <a name="startup-class"></a>Classe de démarrage
> 
> Le middleware (intergiciel) *Microsoft.AspNetCore.Authentication* utilise une classe `Startup` qui s’exécute lors de l’initialisation du processus d’hébergement :
> 
> ```csharp
> 
>   public void ConfigureServices(IServiceCollection services)
>   {  
>     // Get the scopes from the configuration (appsettings.json)
>     var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');
>   
>       // Add sign-in with Microsoft
>       services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
>         .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
> 
>             // Add the possibility of acquiring a token to call a protected web API
>             .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)
> 
>                 // Enables controllers and pages to get GraphServiceClient by dependency injection
>                 // And use an in memory token cache
>                 .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
>                 .AddInMemoryTokenCaches();
> 
>       services.AddControllersWithViews(options =>
>       {
>           var policy = new AuthorizationPolicyBuilder()
>               .RequireAuthenticatedUser()
>               .Build();
>           options.Filters.Add(new AuthorizeFilter(policy));
>       });
> 
>       // Enables a UI and controller for sign in and sign out.
>       services.AddRazorPages()
>           .AddMicrosoftIdentityUI();
>   }
> ```
> 
> La méthode `AddAuthentication()` configure le service pour ajouter une authentification basée sur les cookies, qui est utilisée dans les scénarios de navigateur, et pour définir la demande d’authentification sur OpenID Connect.
> 
> La ligne contenant `.AddMicrosoftIdentityWebApp` ajoute à votre application l’authentification auprès de la plateforme d’identités Microsoft. Elle est fournie par [Microsoft.Identity.Web](microsoft-identity-web.md). Elle est ensuite configurée pour se connecter à l’aide de la plateforme d’identités Microsoft en fonction des informations contenues dans la section `AzureAD` du fichier de configuration *appsettings.json* :
> 
> | Clé *appsettings.json* | Description                                                                                                                 >                                          |
> |------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | `ClientId`             | **ID d’application (client)** de l’application inscrite dans le portail Azure.                                                                                       |
> | `Instance`             | Point de terminaison du service d’émission de jeton de sécurité (STS) pour l’utilisateur à authentifier. Cette valeur est généralement `https://login.microsoftonline.com/`, ce qui indique le cloud public Azure. |
> | `TenantId`             | Nom de votre locataire ou de son ID (GUID), ou *common* pour connecter les utilisateurs avec des comptes professionnels ou scolaires, ou des comptes personnels Microsoft.                             |
> 
> La méthode `EnableTokenAcquisitionToCallDownstreamApi` permet à votre application d’acquérir un jeton pour appeler des API web protégées. `AddMicrosoftGraph` permet à vos contrôleurs ou pages Razor de tirer parti directement de `GraphServiceClient` (par injection de dépendances) et les méthodes `AddInMemoryTokenCaches` permettent à votre application de tirer parti d’un cache de jeton.
> 
> La méthode `Configure()` contient deux méthodes importantes, `app.UseAuthentication()` et `app.UseAuthorization()`, qui activent leurs fonctionnalités nommées : Également dans la méthode `Configure()`, vous devez inscrire les routes de Microsoft Identity Web avec au moins un appel à `endpoints.MapControllerRoute()` ou un appel à `endpoints.MapControllers()`.
> 
> ```csharp
> app.UseAuthentication();
> app.UseAuthorization();
> 
> app.UseEndpoints(endpoints =>
> {
> 
>     endpoints.MapControllerRoute(
>         name: "default",
>         pattern: "{controller=Home}/{action=Index}/{id?}");
>     endpoints.MapRazorPages();
> });
> 
> // endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
> ```
> 
> ### <a name="protect-a-controller-or-a-controllers-method"></a>Protéger un contrôleur ou la méthode d’un contrôleur
> 
> Vous pouvez protéger un contrôleur ou ses méthodes en appliquant l’attribut `[Authorize]` à la classe du contrôleur ou à une ou plusieurs de ses méthodes. Cet attribut `[Authorize]` limite l’accès en autorisant uniquement les utilisateurs authentifiés. Si l’utilisateur n’est pas déjà authentifié, une demande d’authentification peut être démarrée pour accéder au contrôleur. Dans ce guide de démarrage rapide, les étendues sont lues à partir du fichier de configuration :
> 
> ```csharp
> [AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
> public async Task<IActionResult> Index()
> {
>     var user = await _graphServiceClient.Me.Request().GetAsync();
>     ViewData["ApiResult"] = user.DisplayName;
> 
>     return View();
> }
> ```
> 
> [!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
> 
> ## <a name="next-steps"></a>Étapes suivantes
> 
> Le dépôt GitHub qui contient l’exemple de code ASP.NET Core référencé dans ce guide de démarrage rapide inclut des instructions et d’autres exemples de code qui vous montrent comment :
> 
> - Ajouter l’authentification à une nouvelle application web ASP.NET Core
> - Appeler Microsoft Graph, d’autres API Microsoft ou vos propres API web
> - Ajouter une autorisation
> - Connecter des utilisateurs dans des clouds nationaux ou avec des identités sociales
> 
> > [!div class="nextstepaction"]
> > [Tutoriels concernant les applications web ASP.NET Core sur GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
