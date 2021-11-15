---
title: Migrer des applications clientes confidentielles vers MSAL.NET
titleSuffix: Microsoft identity platform
description: Découvrez comment migrer une application cliente confidentielle de la Bibliothèque d’authentification Active Directory pour .NET vers la bibliothèque d’authentification Microsoft pour .NET.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 4e362812224f8e538d7a36dfa5378e23f6438d6e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462819"
---
# <a name="migrate-confidential-client-applications-from-adalnet-to-msalnet"></a>Migrer des applications clientes confidentielles de ADAL.NET vers MSAL.NET

Cet article décrit comment migrer une application cliente confidentielle de la Bibliothèque d’authentification Active Directory pour .NET (ADAL.NET) vers la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET). Les applications clientes confidentielles sont des applications web, des API web et des applications démons qui appellent un autre service à leur propre compte. Pour plus d’informations sur les applications confidentielles, consultez [Flux d’authentification et scénarios d’applications](authentication-flows-app-scenarios.md). Si votre application est basée sur ASP.NET Core, utilisez [Microsoft.Identity.Web](microsoft-identity-web.md).

Pour les inscriptions d’application :

- Vous n’avez pas besoin de créer une inscription d’application. (Vous conservez le même ID client.)
- Vous n’avez pas besoin de modifier les préautorisations (autorisations d’API accordées par l’administrateur).

## <a name="migration-steps"></a>Étapes de la migration

1. Recherchez le code à l’aide de ADAL.NET dans votre application.

   Le code qui utilise ADAL dans une application cliente confidentielle instancie `AuthenticationContext` et appelle `AcquireTokenByAuthorizationCode` ou une valeur de remplacement de `AcquireTokenAsync` avec les paramètres suivants :

   - Chaîne `resourceId`. Cette variable est l’URI ID d’application de l’API web que vous souhaitez appeler.
   - Instance de `IClientAssertionCertificate` ou `ClientAssertion`. Cette instance fournit les informations d’identification du client pour votre application afin de prouver l’identité de celle-ci.

1. Une fois que vous avez identifié les applications qui utilisent ADAL.NET, installez le package MSAL.NET NuGet [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) et mettez à jour les références de votre bibliothèque de projet. Pour plus d’informations, consultez le document [Installation d’un package NuGet](https://www.bing.com/search?q=install+nuget+package). Si vous voulez utiliser des sérialiseurs de cache de jetons, installez également [Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache).

1. Mettez à jour le code en fonction du scénario client confidentiel. Certaines étapes sont communes et s’appliquent à tous les scénarios de clients confidentiels. D’autres étapes sont propres à chaque scénario. 

   Les scénarios de clients confidentiels sont les suivants :

   - [Scénarios de démon](?tabs=daemon#migrate-daemon-apps) pris en charge par les applications web, les API web et les applications de console démon.
   - [API web appelant les API web en aval](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis) prises en charge par les API web appelant les API web en aval pour le compte de l’utilisateur.
   - [Application web appelant des API web](?tabs=authcode#migrate-a-web-api-that-calls-downstream-web-apis) prises en charge par les applications web qui connectent les utilisateurs et appellent une API web en aval.

Vous avez peut-être fourni un wrapper autour de ADAL.NET pour gérer les certificats et la mise en cache. Cet article utilise la même approche pour illustrer le processus de migration de ADAL.NET vers MSAL.NET. Toutefois, ce code est fourni à des fins de démonstration uniquement. Ne copiez/collez pas ces wrappers et ne les intégrez pas tels quels dans votre code.

## <a name="daemon"></a>[Démon](#tab/daemon)

### <a name="migrate-daemon-apps"></a>Migrer des applications démon

Les scénarios de démon utilisent le [flux d’informations d’identification du client](v2-oauth2-client-creds-grant-flow.md) OAuth 2.0. On parle également d’appels de service à service. Votre application acquiert un jeton pour son propre compte, et non pour le compte d’un utilisateur.

#### <a name="find-out-if-your-code-uses-daemon-scenarios"></a>Déterminer si votre code utilise des scénarios de démon

Le code ADAL pour votre application utilise des scénarios démon s’il contient un appel à `AuthenticationContext.AcquireTokenAsync` avec les paramètres suivants :

- Une ressource (URI ID d’application) en tant que premier paramètre
- `IClientAssertionCertificate` ou `ClientAssertion` en tant que deuxième paramètre.

`AuthenticationContext.AcquireTokenAsync` n’a pas de paramètre de type `UserAssertion`. Si c’est le cas, votre application est une API web et utilise l’[API web qui appelle le scénario des API web en aval](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis).

#### <a name="update-the-code-of-daemon-scenarios"></a>Mettre à jour le code des scénarios de démon

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

Dans ce cas, remplacez l’appel à `AuthenticationContext.AcquireTokenAsync` par un appel à `IConfidentialClientApplication.AcquireTokenClient`.

Voici une comparaison du code ADAL.NET et MSAL.NET pour les scénarios de démon :

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult()
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);


  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                );

  return authResult;
 }
}
```
:::column-end:::   
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult()
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }

  var authResult = await app.AcquireTokenForClient(
              new [] { $"{resourceId}/.default" })
              // .WithTenantId(specificTenant)
              // See https://aka.ms/msal.net/withTenantId
              .ExecuteAsync()
              .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>Tirer parti de la mise en cache des jetons

Pour tirer parti du cache en mémoire, l’instance de `IConfidentialClientApplication` doit être conservée dans une variable membre. Si vous recréez l’application cliente confidentielle chaque fois que vous demandez un jeton, vous ne bénéficierez pas du cache de jeton.

Vous devez sérialiser `AppTokenCache` si vous choisissez de ne pas utiliser le cache de jeton d’application en mémoire par défaut. De même, si vous souhaitez implémenter un cache de jeton distribué, vous devez sérialiser `AppTokenCache`. Pour plus d’informations, consultez [Cache de jeton pour une application web ou une API web (application cliente confidentielle)](msal-net-token-cache-serialization.md?tabs=aspnet) et l’exemple [active-directory-dotnet-v1-to-v2/ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache).

[Apprenez-en davantage sur le scénario de démon](scenario-daemon-overview.md) et la façon dont il est implémenté avec MSAL.NET ou Microsoft.Identity.Web dans de nouvelles applications.

## <a name="web-api-calling-downstream-web-apis"></a>[API web appelant les API web en aval](#tab/obo)

### <a name="migrate-a-web-api-that-calls-downstream-web-apis"></a>Migrer une API web qui appelle des API web en aval

Les API web qui appellent des API web en aval utilisent le flux [on-behalf-of (OBO)](v2-oauth2-on-behalf-of-flow.md) OAuth 2.0. L’API web utilise le jeton d’accès extrait de l’en-tête HTTP **Authorize** et le valide. Ce jeton est ensuite échangé contre un jeton pour appeler l’API web en aval. Ce jeton est utilisé en tant qu’instance `UserAssertion` à la fois dans ADAL.NET et MSAL.NET.

#### <a name="find-out-if-your-code-uses-obo"></a>Déterminer si votre code utilise OBO

Le code ADAL pour votre application utilise OBO s’il contient un appel à `AuthenticationContext.AcquireTokenAsync` avec les paramètres suivants :

- Une ressource (URI ID d’application) en tant que premier paramètre
- `IClientAssertionCertificate` ou `ClientAssertion` en tant que deuxième paramètre.
- Paramètre de type `UserAssertion`

#### <a name="update-the-code-by-using-obo"></a>Mettre à jour le code à l’aide de OBO

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

Dans ce cas, remplacez l’appel à `AuthenticationContext.AcquireTokenAsync` par un appel à `IConfidentialClientApplication.AcquireTokenOnBehalfOf`.

Voici une comparaison de l’exemple de code OBO pour ADAL.NET et MSAL.NET :

:::row:::
   :::column span="":::
      ADAL
   :::column-end:::
   :::column span="":::
      MSAL
   :::column-end:::
:::row-end:::
:::row:::
:::column span="":::
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId, 
  string tokenUsedToCallTheWebApi)
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                      userAssertion,
                                );

  return authResult;
 }
}
```
:::column-end:::
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string tokenUsedToCallTheWebApi)
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }


  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await app.AcquireTokenOnBehalfOf(
              new string[] { $"{resourceId}/.default" },
              userAssertion)
              // .WithTenantId(specificTenant) 
              // See https://aka.ms/msal.net/withTenantId
              .ExecuteAsync()
              .ConfigureAwait(false);
  
  return authResult;
 }
}
```
:::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>Tirer parti de la mise en cache des jetons

Pour la mise en cache des jetons dans les OBO, vous devez utiliser un cache de jeton réparti. Pour plus d’informations, consultez [Cache des jetons pour une application web ou une API web (application cliente confidentielle)](msal-net-token-cache-serialization.md?tabs=aspnet) et l’[exemple de code](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache).

```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```

[En savoir plus sur les API web appelant les API web en aval](scenario-web-api-call-api-overview.md) et la façon dont elles sont implémentées avec MSAL.NET ou Microsoft.Identity.Web dans les nouvelles applications.

## <a name="web-app-calling-web-apis"></a>[Application web appelant des API web](#tab/authcode)

### <a name="migrate-a-web-app-that-calls-web-apis"></a>Migrer une application web qui appelle des API web

Si votre application utilise ASP.NET Core, nous vous recommandons vivement d’effectuer la mise à jour vers Microsoft.Identity.Web, qui traite tout à votre place. Pour une présentation rapide, consultez l’[annonce de la disponibilité générale de Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/wiki/1.0.0). Pour plus d’informations sur la façon de l’utiliser dans une application Web, consultez [Pourquoi utiliser Microsoft.Identity.Web dans les applications web ?](https://aka.ms/ms-id-web/webapp).

Les applications web se connecter qui connectent les utilisateurs et appellent des API web pour le compte d’utilisateurs utilisent le [flux de code d’autorisation](v2-oauth2-auth-code-flow.md) OAuth 2.0. En général :

1. L’application web se connecte à un utilisateur en exécutant une première branche du workflow du code d’autorisation. Pour ce faire, elle accède au point de terminaison d’autorisation de la plateforme d’identités Microsoft. L’utilisateur se connecte et effectue des authentifications multifacteur si nécessaire. En guise de résultat de cette opération, l’application reçoit le code d’autorisation. La bibliothèque d’authentification n’est pas utilisée à ce stade.
1. L’application exécute la deuxième branche du flux du code d’autorisation. Elle utilise le code d’autorisation pour obtenir un jeton d’accès, un jeton d’ID et un jeton d’actualisation. Votre application doit fournir la valeur `redirectUri` qui est l’URI où le point de terminaison de la plateforme d’identités Microsoft fournira les jetons de sécurité. Une fois que l’application a reçu cet URI, elle appelle généralement `AcquireTokenByAuthorizationCode` pour ADAL ou MSAL afin d’échanger le code et d’obtenir un jeton qui sera stocké dans le cache de jeton.
1. L’application utilise ADAL ou MSAL pour appeler `AcquireTokenSilent` afin de pouvoir obtenir des jetons et d’appeler les API web nécessaires. Cela s’effectue à partir des contrôleurs d’application web.

#### <a name="find-out-if-your-code-uses-the-auth-code-flow"></a>Déterminer si votre code utilise le circuit de code d’authentification

Le code ADAL pour votre application utilise le circuit de code d’authentification s’il contient un appel à `AuthenticationContext.AcquireTokenByAuthorizationCodeAsync`.

#### <a name="update-the-code-by-using-the-authorization-code-flow"></a>Mettre à jour le code en utilisant le flux du code d’autorisation

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)] 

Dans le constructeur, remplacez l’appel à `AuthenticationContext.AcquireTokenAsync` par un appel à `IConfidentialClientApplication.AcquireTokenByAuthorizationCode`.

Voici une comparaison des exemples de flux de code d’autorisation pour ADAL.NET et MSAL.NET :

:::row:::
   :::column span="":::
      ADAL
   :::column-end:::
   :::column span="":::
      MSAL
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
     = "https://login.microsoftonline.com/common";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string authorizationCode)
 {


  var ac = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var authResult = await ac.AcquireTokenByAuthorizationCodeAsync(
                                      authorizationCode,
                                      redirectUri,
                                      clientAssertionCert,
                                      resourceId,
                                );
  return authResult;
 }
}
```      
   :::column-end:::
   :::column span="":::
```csharp
using Microsoft.Identity.Client;
using Microsoft.Identity.Web;
using System;
using System.Security.Claims;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority
    = "https://login.microsoftonline.com/{tenant}";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();

 public IConfidentialClientApplication CreateApplication()
 {
  IConfidentialClientApplication app;

  app = ConfidentialClientApplicationBuilder.Create(ClientId)
               .WithCertificate(certificate)
               .WithAuthority(authority)
               .WithRedirectUri(redirectUri.ToString())
               .WithLegacyCacheCompatibility(false)
               .Build();

  // Add a token cache. For details about other serialization
  // see https://aka.ms/msal-net-cca-token-cache-serialization
  app.AddInMemoryTokenCache();

  return app;
 }

 // Called from 'code received event'.
 public async Task<AuthenticationResult> GetAuthenticationResult(
      string resourceId,
      string authorizationCode)
 {
  IConfidentialClientApplication app = CreateApplication();

  var authResult = await app.AcquireTokenByAuthorizationCode(
                  new[] { $"{resourceId}/.default" },
                  authorizationCode)
                  .ExecuteAsync()
                  .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

L’appel de `AcquireTokenByAuthorizationCode` ajoute un jeton au cache de jetons lorsque le code d’autorisation est reçu. Pour obtenir des jetons supplémentaires pour d’autres ressources ou locataires, utilisez `AcquireTokenSilent` dans vos contrôleurs.

```csharp
public partial class AuthWrapper
{
 // Called from controllers
 public async Task<AuthenticationResult> GetAuthenticationResult(
      string resourceId2,
      string authority)
 {
  IConfidentialClientApplication app = CreateApplication();
  AuthenticationResult authResult;

  var scopes = new[] { $"{resourceId2}/.default" };
  var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());

  try
  {
   // try to get an already cached token
   authResult = await app.AcquireTokenSilent(
               scopes,
               account)
                // .WithTenantId(specificTenantId) 
                // See https://aka.ms/msal.net/withTenantId
                .ExecuteAsync().ConfigureAwait(false);
  }
  catch (MsalUiRequiredException)
  {
   // The controller will need to challenge the user
   // including asking for claims={ex.Claims}
   throw;
  }
  return authResult;
 }
}
```

#### <a name="benefit-from-token-caching"></a>Tirer parti de la mise en cache des jetons

Étant donné que votre application web utilise `AcquireTokenByAuthorizationCode`, votre application doit utiliser un cache de jetons distribué pour la mise en cache des jetons. Pour plus d’informations, consultez [Cache de jeton pour une application web ou une API web](msal-net-token-cache-serialization.md?tabs=aspnet) et l’[exemple de code](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache).


```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```

#### <a name="handling-msaluirequiredexception"></a>Gestion de MsalUiRequiredException

Lorsque votre contrôleur tente d’acquérir un jeton en silence pour différentes étendues/ressources, MSAL.NET peut renvoyer une `MsalUiRequiredException`. Ce comportement est prévisible si, par exemple, l’utilisateur doit se reconnecter ou si l’accès à la ressource requiert plus de revendications (en raison d’une stratégie d’accès conditionnel pour l’instance). Pour plus d’informations sur l’atténuation, découvrez comment [Gérer les erreurs et les exceptions dans MSAL.NET](msal-error-handling-dotnet.md).

[En savoir plus sur les applications web appelant les API web](scenario-web-app-call-api-overview.md) et la façon dont elles sont implémentées avec MSAL.net ou Microsoft.Identity.Web dans les nouvelles applications.

---

## <a name="msal-benefits"></a>Avantages MSAL

Les avantages clés de MSAL.NET pour votre application sont les suivants :

- **Résilience**. MSAL.NET permet de rendre votre application résiliente à l’aide des éléments suivants :

   - Avantages du service CCS (Cached Credential Service) Azure AD. CCS fonctionne comme une sauvegarde Azure AD.
   - Renouvellement proactif des jetons si l’API que vous appelez active des jetons de longue durée via l’[évaluation de l’accès continu](app-resilience-continuous-access-evaluation.md).

- **Sécurité**. Vous pouvez acquérir des jetons de preuve de possession (PoP) si l’API Web que vous souhaitez appeler en a besoin. Pour plus d’informations, consultez [Jetons de preuve de possession dans MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Proof-Of-Possession-(PoP)-tokens)

- **Performances et scalabilité** : Si vous n’avez pas besoin de partager votre cache avec ADAL.NET, désactivez la compatibilité du cache hérité lors de la création de l’application cliente confidentielle (`.WithLegacyCacheCompatibility(false)`). Cela augmente considérablement les performances.
  
  ```csharp
  app = ConfidentialClientApplicationBuilder.Create(ClientId)
          .WithCertificate(certificate)
          .WithAuthority(authority)
          .WithLegacyCacheCompatibility(false)
          .Build();
  ```

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="msalserviceexception"></a>MsalServiceException

Les informations de résolution des problèmes suivantes établissent deux hypothèses : 

- Votre code ADAL.NET fonctionnait.
- Vous avez migré vers MSAL en conservant le même ID client.

Si vous recevez une exception avec l’un des messages suivants : 

> `AADSTS700027: Client assertion contains an invalid signature. [Reason - The key was not found.]`

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

Vous pouvez résoudre l’exception en procédant comme suit :

1. Vérifiez que vous utilisez la version la plus récente de [MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
1. Vérifiez que l’hôte d’autorité que vous avez défini lors de la création de l’application cliente confidentielle et de l’hôte d’autorité que vous avez utilisé avec ADAL sont similaires. En particulier, s’agit-il du même [cloud](msal-national-cloud.md) (Azure Government, Azure China 21Vianet ou Azure Allemagne) ?

### <a name="msalclientexception"></a>MsalClientException

Dans des applications mutualisées, vous pouvez avoir des scénarios où vous spécifiez une autorité commune lors de la génération de l’application, puis souhaitez cibler un locataire spécifique (par exemple, le locataire de l’utilisateur) lors de l’appel d’une API web. S’agissant de MSAL.NET 4.37.0, lorsque vous spécifiez `.WithAzureRegion` lors de la création de l’application, vous ne pouvez plus spécifier l’Autorité à l’aide de `.WithAuthority` lors des demandes de jeton. Si vous le faites, vous obtiendrez l’erreur suivante lors de la mise à jour à partir de versions précédentes de MSAL.NET :

  `MsalClientException - "You configured WithAuthority at the request level, and also WithAzureRegion. This is not supported when the environment changes from application to request. Use WithTenantId at the request level instead."`

Pour résoudre ce problème, remplacez `.WithAuthority` sur l’expression AcquireTokenXXX par `.WithTenantId`. Spécifiez le locataire à l’aide d’un GUID ou d’un nom de domaine.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :
- [Différences entre les applications ADAL.NET et MSAL.NET](msal-net-differences-adal-net.md)
- [Sérialisation de cache de jetons dans MSAL.NET](msal-net-token-cache-serialization.md)