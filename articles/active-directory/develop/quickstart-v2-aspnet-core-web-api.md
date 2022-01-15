---
title: 'Démarrage rapide : Protéger une API web ASP.NET Core avec la plateforme d’identités Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez télécharger et modifier un exemple de code qui montre comment protéger une API web ASP.NET Core en utilisant la plateforme d’identités Microsoft pour les autorisations.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/11/2022
ms.author: jmprieur
ms.custom: devx-track-csharp, "scenarios:getting-started", "languages:aspnet-core", mode-api
ms.openlocfilehash: c30b50ae0f5eb92f21232096e032a214ec05e316
ms.sourcegitcommit: 04420fb4695bd70408d9854ad5b2af8a9bbfbc64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2022
ms.locfileid: "136846742"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>Démarrage rapide : Protéger une API web ASP.NET Core avec la plateforme d’identités Microsoft

Dans ce guide de démarrage rapide, vous téléchargez un exemple de code d’API web ASP.NET Core et examinez la manière dont il limite l’accès aux ressources par les comptes autorisés uniquement. L’exemple prend en charge l’autorisation des comptes Microsoft personnels et comptes de n’importe quelle organisation Azure Active Directory (Azure AD).


## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Client Azure Active Directory](quickstart-create-new-tenant.md)
- [SDK .NET Core 3.1+](https://dotnet.microsoft.com/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ou [Visual Studio Code](https://code.visualstudio.com/)

## <a name="step-1-register-the-application"></a>Étape 1 : Enregistrement de l’application

Tout d’abord, inscrivez l’API web dans votre locataire Azure AD, puis ajoutez une étendue en effectuant les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Répertoires + abonnements**:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour basculer vers le locataire dans lequel vous voulez inscrire l’application.
1. Recherchez et sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
1. Pour **Nom**, entrez un nom pour votre application. Par exemple, entrez **AspNetCoreWebApi-Quickstart**. Les utilisateurs de votre application verront ce nom, que vous pourrez changer ultérieurement.
1. Sélectionnez **Inscription**.
1. Sous **Gérer**, sélectionnez **Exposer une API** > **Ajouter une étendue**. Pour l’**URI d’ID d’application** acceptez l’option par défaut en sélectionnant **Enregistrer et continuer**, puis entrez les informations suivantes :
    - **Nom de l’étendue** : `access_as_user`
   - **Qui peut donner son consentement ?**  : **Administrateurs et utilisateurs**
   - **Nom d’affichage du consentement administrateur** : `Access AspNetCoreWebApi-Quickstart`
   - **Description du consentement de l’administrateur** : `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
   - **Nom d’affichage du consentement utilisateur** : `Access AspNetCoreWebApi-Quickstart`
   - **Description du consentement de l’utilisateur** : `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
   - **État** : **Activé**
1. Sélectionnez **Ajouter une étendue** pour finaliser l’ajout de l’étendue.

## <a name="step-2-download-the-aspnet-core-project"></a>Étape 2 : Télécharger le projet ASP.NET Core

[Télécharger la solution ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) à partir de GitHub.

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]


## <a name="step-3-configure-the-aspnet-core-project"></a>Étape 3 : Configurer le projet ASP.NET Core

Dans cette étape, vous allez configurer l’exemple de code pour qu’il fonctionne avec l’inscription d’application que vous avez créée précédemment.

1. Extrayez l’archive .zip dans un dossier proche de la racine de votre lecteur. Par exemple, extrayez-le dans *C:\Azure-Samples*.

   Nous vous recommandons d’extraire l’archive dans un répertoire près de la racine de votre lecteur pour éviter les erreurs dues à des limitations de longueur de chemin sur Windows.

1. Dans votre éditeur de code, ouvrez la solution qui est située dans le dossier *webapi*.
1. Ouvrez le fichier *appsettings.json* pour modifier le code suivant :

   ```json
   "ClientId": "Enter_the_Application_Id_here",
   "TenantId": "Enter_the_Tenant_Info_Here"
   ```

   - Remplacez `Enter_the_Application_Id_here` par l’ID d’application (client) de l’application que vous avez inscrite dans le portail Azure. Vous trouverez l’ID d’application (client) dans la page **Vue d’ensemble** de l’application.
   - Remplacez `Enter_the_Tenant_Info_Here` par l’un des éléments suivants :
      - Si votre application prend en charge les **Comptes dans cet annuaire organisationnel uniquement**, remplacez cette valeur par l’ID de l’annuaire (locataire) (un GUID) ou par le nom du locataire (par exemple, `contoso.onmicrosoft.com`). L’ID de l’annuaire (locataire) se trouve dans la page **Vue d’ensemble** de l’application.
      - Si votre application prend en charge **Comptes dans un annuaire organisationnel**, remplacez cette valeur par `organizations`.
      - Si votre application prend en charge **Tous les utilisateurs de compte Microsoft**, conservez la valeur `common`.

Pour ce guide de démarrage rapide, ne modifiez pas les autres valeurs du fichier *appsettings.json*.

## <a name="how-the-sample-works"></a>Fonctionnement de l’exemple

L’API web reçoit un jeton d’une application cliente, et le code de l’API web valide ce jeton. Ce scénario est expliqué de façon plus détaillée dans [Scénario : API web protégée](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Classe de démarrage

Le middleware (intergiciel) *Microsoft.AspNetCore.Authentication* utilise une classe `Startup` qui s’exécute lors du démarrage du processus d’hébergement. Dans sa méthode `ConfigureServices`, la méthode d’extension `AddMicrosoftIdentityWebApi` fournie par *Microsoft.Identity.Web* est appelée.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

La méthode `AddAuthentication()` configure le service pour ajouter l’authentification basée sur JwtBearer.

La ligne contenant `.AddMicrosoftIdentityWebApi` ajoute à votre API web l’autorisation de la plateforme d’identités Microsoft. Elle est ensuite configurée pour valider les jetons d’accès émis par la plateforme d’identités Microsoft en fonction des informations contenues dans la section `AzureAD` du fichier de configuration *appsettings.json* :

| Clé *appsettings.json* | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | ID d’application (client) de l’application inscrite dans le portail Azure.                                                                                       |
| `Instance`             | Point de terminaison du service d’émission de jeton de sécurité (STS) pour l’utilisateur à authentifier. Cette valeur est généralement `https://login.microsoftonline.com/`, ce qui indique le cloud public Azure. |
| `TenantId`             | Nom de votre locataire ou de son ID (GUID), ou `common` pour connecter les utilisateurs avec des comptes professionnels ou scolaires, ou des comptes personnels Microsoft.                             |

La méthode `Configure()` contient deux méthodes importantes, `app.UseAuthentication()` et `app.UseAuthorization()`, qui activent leurs fonctionnalités nommées :

```csharp
// The runtime calls this method. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>Protéger un contrôleur, la méthode d’un contrôleur ou une page Razor

Vous pouvez protéger un contrôleur ou les méthodes d’un contrôleur en utilisant l’attribut `[Authorize]`. Cet attribut limite l’accès au contrôleur ou aux méthodes en autorisant uniquement les utilisateurs authentifiés. Une demande d’authentification peut alors être démarrée pour accéder au contrôleur si l’utilisateur n’est pas authentifié.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validation-of-scope-in-the-controller"></a>Validation de l’étendue dans le contrôleur

Le code de l’API vérifie ensuite que les étendues nécessaires se trouvent dans le jeton à l’aide de `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` :

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Le dépôt GitHub qui contient cet exemple de code d’API web ASP.NET Core inclut des instructions et d’autres exemples de code qui vous montrent comment :

- Ajoutez l’authentification à une nouvelle API web ASP.NET Core.
- Appelez l’API web à partir d’une application de bureau.
- Appelez des API en aval comme Microsoft Graph et d’autres API Microsoft.

> [!div class="nextstepaction"]
> [Tutoriels concernant l’API web ASP.NET Core sur GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
