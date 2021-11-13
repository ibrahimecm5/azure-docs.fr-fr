---
title: Vérifier les étendues et les rôles d’application avec une API web protégée | Azure
titleSuffix: Microsoft identity platform
description: Vérifiez que l’API est appelée uniquement par les applications pour le compte des utilisateurs qui ont les étendues adéquates et par les applications de démon ayant les rôles d’application appropriés.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2021
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 23b9f2e8bb14d74a7f2b722945251acb182b71db
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222568"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API web protégée : Vérifier les étendues et les rôles d’application

Cet article décrit comment ajouter une autorisation à votre API web. Cette protection garantit que l’API est appelée uniquement par :

- Des applications pour le compte d’utilisateurs qui ont des étendues adaptées.
- Des applications de démon qui ont les rôles d’application appropriés.

Les extraits de code de cet article proviennent des exemples de code suivants sur GitHub :

- [Tutoriel incrémentiel sur l’API web ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
- [Exemple d’API web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

Pour protéger une API web ASP.NET ou ASP.NET Core, vous devez ajouter l’attribut `[Authorize]` à l’un des éléments suivants :

- Le contrôleur proprement dit, si vous voulez que toutes les actions de contrôleur soient protégées
- L’action de contrôleur individuelle pour votre API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     // ...
    }
```

Mais cette protection n’est pas suffisante. Elle garantit uniquement qu’ASP.NET et ASP.NET Core valident le jeton. Votre API doit vérifier que le jeton utilisé pour appeler l’API est demandée avec les revendications attendues. Ces revendications doivent notamment être vérifiées :

- Les _étendues_ si l’API est appelée pour le compte d’un utilisateur.
- Les _rôles d’application_ si l’API peut être appelée à partir d’une application démon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Vérifier les étendues dans les API appelées pour le compte d’utilisateurs

Si une application cliente appelle votre API pour le compte d’un utilisateur, l’API doit demander un jeton du porteur ayant des étendues spécifiques pour elle. Pour plus d’informations, consultez [Configuration de code | Jeton du porteur](scenario-protected-web-api-app-configuration.md#bearer-token).

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Dans ASP.NET Core, vous pouvez utiliser Microsoft.Identity.Web pour vérifier les étendues de chaque action du contrôleur. Vous pouvez également les vérifier au niveau du contrôleur ou de l’ensemble de l’application.

#### <a name="verify-the-scopes-on-each-controller-action"></a>Vérifier les étendues de chaque action de contrôleur

Vous pouvez vérifier les étendues de l’action du contrôleur à l’aide de l'attribut `[RequiredScope]`. Cet attribut comporte plusieurs remplacements. Un qui prend directement les étendues requises, et une qui prend une clé de la configuration.

##### <a name="verify-the-scopes-on-a-controller-action-with-hardcoded-scopes"></a>Vérifier les étendues d’une action de contrôleur avec des étendues codées en dur

L’extrait de code suivant montre l’utilisation de l'attribut `[RequiredScope]` avec des étendues codées en dur.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    [RequiredScope(scopeRequiredByApi)]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-action-with-scopes-defined-in-configuration"></a>Vérifier les étendues d’une action de contrôleur avec des étendues définies dans la configuration

Vous pouvez également déclarer ces étendues requises dans la configuration et faire référence à la clé de configuration :

Par exemple, si vous avez la configuration suivante dans appsettings.js :

```JSon
{
 "AzureAd" : {
   // more settings
   "Scopes" : "access_as_user access_as_admin"
  }
}
```

Ensuite, référencez-la dans l’attribut `[RequiredScope]` :

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    [RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-scopes-conditionally"></a>Vérifier les étendues de manière conditionnelle

Dans certains cas, vous souhaiterez vérifier les étendues de manière conditionnelle. Pour ce faire, utilisez la méthode d'extension `VerifyUserHasAnyAcceptedScope` sur le `HttpContext`.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-at-the-level-of-the-controller"></a>Vérifier les étendues au niveau du contrôleur

Vous pouvez également vérifier les étendues pour l’ensemble du contrôleur.

##### <a name="verify-the-scopes-on-a-controller-with-hardcoded-scopes"></a>Vérifier les étendues sur un contrôleur avec des étendues codées en dur

L’extrait de code suivant montre l’utilisation de l'attribut `[RequiredScope]` avec des étendues codées en dur sur le contrôleur.

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(scopeRequiredByApi)]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-with-scopes-defined-in-configuration"></a>Vérifier les étendues sur le contrôleur avec des étendues définies dans la configuration

Comme pour une action, vous pouvez également déclarer ces étendues requises dans la configuration et faire référence à la clé de configuration :

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-more-globally"></a>Vérifier les étendues plus globalement

La définition d’étendues précises pour votre API web et la vérification des étendues dans chaque action de contrôleur est l’approche recommandée. Toutefois, il est également possible de vérifier les étendues au niveau de l’application ou d’un contrôleur. Pour plus d’informations, consultez [Autorisation basée sur les revendications](/aspnet/core/security/authorization/claims) dans la documentation d’ASP.NET Core.

#### <a name="what-is-verified"></a>Qu’est-ce qui est vérifié ?

L’attribut `[RequiredScope]` et la méthode `VerifyUserHasAnyAcceptedScope` effectuent des étapes semblables aux suivantes :

- Vérifier qu’il existe une revendication nommée `http://schemas.microsoft.com/identity/claims/scope` ou `scp`.
- Vérifier que la revendication a une valeur qui contient l’étendue attendue par l’API.

### <a name="aspnet-classic"></a>[ASP.NET Classique](#tab/aspnet)

Dans une application ASP.NET, vous pouvez valider les étendues de la manière suivante :

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateScopes(new[] {"read"; "admin" } );
       // ...
    }
```

Vous trouverez ci-dessous une version simplifiée de `ValidateScopes` :

```csharp
private void ValidateScopes(IEnumerable<string> acceptedScopes)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim scopeClaim = ClaimsPrincipal.Current.FindFirst("scp");
    if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Forbidden,
            ReasonPhrase = $"The 'scp' claim does not contain '{scopeClaim}' or was not found"
        });
    }
}
```

Pour une version complète de `ValidateScopes` pour ASP.NET Core, [_ScopesRequiredHttpContextExtensions.cs_](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/ScopesRequiredHttpContextExtensions.cs)

---

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Vérifier les rôles d’application dans les API appelées par des applications démon

Si votre API web est appelée par une [application de démon](scenario-daemon-overview.md), cette application doit exiger une autorisation d’application à votre API web. Comme indiqué dans [Exposition des autorisations d’application (rôles d’application)](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles), votre API expose de telles autorisations. Le rôle d’application `access_as_application` en est un exemple.

Vous devez maintenant demander à votre API de vérifier que le jeton qu’elle reçoit contient la revendication `roles` et que cette revendication a la valeur attendue. Le code de vérification est similaire au code qui vérifie les autorisations déléguées, excepté que votre action de contrôleur teste les rôles au lieu des étendues :

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

L’extrait de code suivant montre comment vérifier le rôle d’application

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        // ...
    }
```

À la place, vous pouvez utiliser les attributs [Authorize("role")] sur le contrôleur ou une action (ou une page razor).

```CSharp
[Authorize("role")]
MyController : ApiController
{
    // ...
}
```

Mais pour cela, vous devrez mapper la revendication de rôle à « Roles » dans le fichier Startup.cs :

```CSharp
 services.Configure<OpenIdConnectOptions>(OpenIdConnectDefaults.AuthenticationScheme, options =>
 {
    // The claim in the Jwt token where App roles are available.
    options.TokenValidationParameters.RoleClaimType = "roles";
 });
```

Ce n’est pas la meilleure solution si vous devez également effectuer une autorisation basée sur des groupes.

Pour plus d’informations, consultez le tutoriel incrémentiel d’application web sur l'[autorisation par rôles et groupes](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ).

### <a name="aspnet-classic"></a>[ASP.NET Classique](#tab/aspnet)

Dans une application ASP.NET, vous pouvez valider les rôles d’application de la manière suivante :

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateAppRole("access_as_application");
       // ...
    }
```

Voici une version simplifiée de `ValidateAppRole` :

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
```

Pour une version complète de `ValidateAppRole` pour ASP.NET Core, consultez le code [_RolesRequiredHttpContextExtensions.cs_](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs).

---

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Acceptation de jetons d’application uniquement si l’API web doit être appelée uniquement par des applications de démon

Les utilisateurs peuvent également utiliser des revendications de rôles dans des modèles d’affectation d’utilisateurs, comme indiqué dans [Guide pratique pour ajouter des rôles d’application dans votre application et les recevoir dans le jeton](howto-add-app-roles-in-azure-ad-apps.md). Si les rôles peuvent être attribués aux deux, vérifier les rôles permet aux applications de se connecter en tant qu’utilisateurs et aux utilisateurs de se connecter en tant qu’applications. Nous vous recommandons de déclarer des rôles différents pour les utilisateurs et les applications afin d’éviter cette confusion.

Si vous voulez que seules les applications démon appellent votre API web, ajoutez la condition indiquant que le jeton est un jeton d’application uniquement quand vous validez le rôle d’application.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnly = oid != null && sub != null && oid == sub;
```

La vérification de la condition inverse autorise uniquement les applications qui connectent un utilisateur à appeler votre API.

### <a name="using-acl-based-authorization"></a>Utilisation de l’autorisation basée sur les listes de contrôle d’accès (ACL)

En guise d’alternative à l’autorisation basée sur les rôles d’application, vous pouvez protéger votre API web avec un modèle d’autorisation basé sur les listes de contrôle d’accès (ACL) afin de [contrôler les jetons sans la revendication `roles`](v2-oauth2-client-creds-grant-flow.md#controlling-tokens-without-the-roles-claim).

Si vous utilisez Microsoft.Identity.Web sur ASP.NET Core, vous devez déclarer que vous utilisez une autorisation basée sur les listes de contrôle d’accès (ACL). Dans le cas contraire, Microsoft Identity Web lèvera une exception lorsque ni les rôles ni les étendues ne se trouvent dans les revendications fournies :

```Text
System.UnauthorizedAccessException: IDW10201: Neither scope or roles claim was found in the bearer token.
```

Pour éviter cette exception, affectez la valeur true à la propriété de configuration `AllowWebApiToBeAuthorizedByACL`, dans appsettings.json ou par programmation.

```Json
{
 "AzureAD"
 {
  // other properties
  "AllowWebApiToBeAuthorizedByACL" : true,
  // other properties
 }
}
```

Si vous affectez la valeur true à `AllowWebApiToBeAuthorizedByACL`, il est de **votre responsabilité** de vérifier le mécanisme d’ACL.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Passer en production](scenario-protected-web-api-production.md).
