---
title: Sérialisation du cache de jetons (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez la sérialisation et la sérialisation personnalisée du cache de jetons à l’aide de la Bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2021
ms.author: jmprieur
ms.reviewer: mmacy
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 896e52bf70229358a25055a23403ab2a5a2d7963
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054302"
---
# <a name="token-cache-serialization-in-msalnet"></a>Sérialisation du cache de jetons dans MSAL.NET

Après [avoir acquis un jeton](msal-acquire-cache-tokens.md), la Bibliothèque d’authentification Microsoft (MSAL) le met en cache. Les applications clientes publiques (applications de bureau/mobiles) doivent essayer d’obtenir un jeton à partir du cache avant d’acquérir un jeton par une autre méthode. Les méthodes d’acquisition sur les applications clientes confidentielles gèrent le cache elles-mêmes. Cet article décrit la sérialisation par défaut et personnalisée du cache de jetons dans MSAL.NET.

## <a name="quick-summary"></a>Résumé

Nous vous recommandons :
- Dans les applications web et les API web, utilisez [des sérialiseurs de cache de jeton à partir de « Microsoft.Identity.Web.TokenCache »](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization). Ils fournissent même une base de données distribuée ou un système de cache pour stocker les jetons.
  - Dans les [applications web](scenario-web-app-call-api-overview.md) et l’[API web](scenario-web-api-call-api-overview.md) ASP.NET Core, utilisez [Microsoft.Identity.Web](microsoft-identity-web.md) comme API de niveau supérieur dans ASP.NET Core.
  - Dans ASP.NET classic, .NET Core, .NET Framework, utilisez MSAL.NET directement avec les [adaptateurs de sérialisation du cache de jetons pour MSAL](msal-net-token-cache-serialization.md?tabs=aspnet) fournis dans le package NuGet Microsoft.identity.Web.TokenCache. 
- Dans les applications de bureau (qui peuvent utiliser le système de fichiers pour stocker des jetons), utilisez [Microsoft.Identity.Client.Extensions.MSAL](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache) avec MSAL.Net.
- Dans les applications mobiles (Xamarin.iOS, Xamarin.Android, plateforme Windows universelle), ne faites rien car MSAL.NET gère le cache pour vous : ces plateformes disposent d’un stockage sécurisé.

## <a name="aspnet-core-web-apps-and-web-apis"></a>[Applications web et API web ASP.NET Core](#tab/aspnetcore)

La bibliothèque [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web) fournit un package NuGet [Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache) contenant la sérialisation du cache de jetons :

| Méthode d’extension | Description  |
| ---------------- | ------------ |
| `AddInMemoryTokenCaches` | Sérialisation de cache de jeton en mémoire. Cette implémentation est idéale pour les échantillons et les applications démons (jetons application à application / `AcquireTokenForClient`). Elle est également adaptée aux applications de production, mais sachez que le cache de jeton est perdu lorsque l’application web est redémarrée. À partir de Microsoft.Identity.Web 1.19.0, MSAL est configuré de manière à utiliser un cache statique (partagé) dans toutes les instances d’applications, ce qui est nettement plus rapide que les autres mécanismes de mise en cache.
| `AddSessionTokenCaches` | Le cache de jeton est lié à la session utilisateur. Cette option n’est pas idéale si le jeton d’ID contient de nombreuses revendications dans la mesure où le cookie devient trop volumineux.
| `AddDistributedTokenCaches` | Le cache de jeton est un adaptateur par rapport à l’implémentation `IDistributedCache` ASP.NET Core, ce qui vous permet donc de choisir entre un cache de mémoire distribuée, un cache Redis, un cache Ncache distribué ou un cache SQL Server. Pour plus d’informations sur les implémentations `IDistributedCache`, consultez [Cache mémoire distribué](/aspnet/core/performance/caching/distributed).


Voici un exemple de code utilisant le cache en mémoire dans la méthode [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) de la classe [Startup](/aspnet/core/fundamentals/startup) dans une application ASP.NET Core :

```CSharp
#using Microsoft.Identity.Web
```

```CSharp
using Microsoft.Identity.Web;

public class Startup
{
 const string scopesToRequest = "user.read";
  
  public void ConfigureServices(IServiceCollection services)
  {
   // code before
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest })
                .AddInMemoryTokenCaches();
   // code after
  }
  // code after
}
```

Du point de vue du cache, le code serait similaire dans les API web ASP.NET Core


Exemples de caches distribués possibles :

```C#
// or use a distributed Token Cache by adding
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest }
               .AddDistributedTokenCaches();

// and then choose your implementation of distributed cache

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache();

// Or a Redis cache
// Requires the Microsoft.Extensions.Caching.StackExchangeRedis NuGet package
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
// Requires the Microsoft.Extensions.Caching.SqlServer NuGet package
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});

// Or a Cosmos DB cache
// Requires the Microsoft.Extensions.Caching.Cosmos NuGet package
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
    cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
    cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
    cacheOptions.CreateIfNotExists = true;
});
```

Leur utilisation est présentée dans le tutoriel [Application web ASP.NET Core](/aspnet/core/tutorials/first-mvc-app/) au cours de la phase [2-2 Cache de jeton](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache).

## <a name="non-aspnet-core-web-apps-and-web-apis"></a>[Applications web et API web non ASP.NET Core](#tab/aspnet)

Même quand vous utilisez MSAL.NET, vous pouvez tirer parti des sérialiseurs de cache de jeton intégrés à Microsoft.Identity.Web.TokenCache 

### <a name="referencing-the-nuget-package"></a>Référencement du package NuGet

Ajoutez le package NuGet [Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache) à votre projet en plus de MSAL.NET

### <a name="configuring-the-token-cache"></a>Configuration du cache de jeton

Le code suivant montre comment ajouter un cache de jeton correctement partitionné en mémoire à votre application.

```CSharp
using Microsoft.Identity.Web;
using Microsoft.Identity.Client;
using Microsoft.Extensions.DependencyInjection;
```

```CSharp

public static async Task<AuthenticationResult> GetTokenAsync(string clientId, X509Certificate cert, string authority, string[] scopes)
 {
     // Create the confidential client application
     app= ConfidentialClientApplicationBuilder.Create(clientId)
       // Alternatively to the certificate you can use .WithClientSecret(clientSecret)
       .WithCertificate(cert)
       .WithLegacyCacheCompatibility(false)
       .WithAuthority(authority)
       .Build();

     // Add a static in-memory token cache. Other options available: see below
     app.AddInMemoryTokenCache();  // Microsoft.Identity.Web.TokenCache 1.17+
   
     // Make the call to get a token for client_credentials flow (app to app scenario) 
     return await app.AcquireTokenForClient(scopes).ExecuteAsync();
     
     // OR Make the call to get a token for OBO (web api scenario)
     return await app.AcquireTokenOnBehalfOf(scopes, userAssertion).ExecuteAsync();
     
     // OR Make the call to get a token via auth code (web app scenario)
     return await app.AcquireTokenByAuthorizationCode(scopes, authCode);    
     
     // OR, when the user has previously logged in, get a token silently
     var homeAccountId = GetHomeAccountIdFromClaimsPrincipal(); // uid and utid claims
     var account = await app.GetAccountAsync(homeAccountId);
     try
     {
          return await app.AcquireTokenSilent(scopes, account).ExecuteAsync();; 
     } 
     catch (MsalUiRequiredException)
     {
        // cannot get a token silently, so redirect the user to be challenged 
     }
  }
```

### <a name="available-caching-technologies"></a>Technologies de mise en cache disponibles

Au lieu de `app.AddInMemoryTokenCache();`, vous pouvez utiliser différentes technologies de mise en cache, y compris des caches de jetons distribués fournis par .NET.

#### <a name="in-memory-token-cache"></a>Cache de jeton en mémoire

La sérialisation de cache de jeton en mémoire est un excellent exemple. Elle est également adaptée aux applications de production, mais sachez que le cache de jeton est perdu lorsque l’application web est redémarrée.

```CSharp 
     // Add an in-memory token cache
     app.AddInMemoryTokenCache();
```

#### <a name="distributed-caches"></a>Caches distribués

Si vous utilisez `app.AddDistributedTokenCache`, le cache de jeton est un adaptateur par rapport à l’implémentation .NET `IDistributedCache`, ce qui vous permet de choisir entre un cache de mémoire distribuée, un cache Redis, un cache Ncache distribué ou un cache SQL Server. Pour plus d’informations sur les implémentations `IDistributedCache`, consultez [Cache mémoire distribué](/aspnet/core/performance/caching/distributed).

##### <a name="distributed-in-memory-token-cache"></a>Cache de jeton en mémoire distribué

```CSharp 
     // In memory distributed token cache
     app.AddDistributedTokenCache(services =>
     {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory
       services.AddDistributedMemoryCache();
     });
```

##### <a name="sql-server"></a>Serveur SQL

```CSharp 
     // SQL Server token cache
     app.AddDistributedTokenCache(services =>
     {
      services.AddDistributedSqlServerCache(options =>
      {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory

       // Requires to reference Microsoft.Extensions.Caching.SqlServer
       options.ConnectionString = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=TestCache;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False";
       options.SchemaName = "dbo";
       options.TableName = "TestCache";

       // You don't want the SQL token cache to be purged before the access token has expired. Usually
       // access tokens expire after 1 hour (but this can be changed by token lifetime policies), whereas
       // the default sliding expiration for the distributed SQL database is 20 mins. 
       // Use a value which is above 60 mins (or the lifetime of a token in case of longer lived tokens)
       options.DefaultSlidingExpiration = TimeSpan.FromMinutes(90);
      });
     });
```

##### <a name="redis-cache"></a>Le cache Redis

```CSharp 
     // Redis token cache
     app.AddDistributedTokenCache(services =>
     {
       // Requires to reference Microsoft.Extensions.Caching.StackExchangeRedis
       services.AddStackExchangeRedisCache(options =>
       {
         options.Configuration = "localhost";
         options.InstanceName = "Redis";
       });
      });
```

Consultez aussi [Désactivation de la synchronisation du cache](#disabling-cache-synchronization) si vous constatez que l’acquisition de jeton prend parfois autant de temps que le délai d’expiration du cache Redis. 

##### <a name="cosmos-db"></a>Cosmos DB

```CSharp 
      // Cosmos DB token cache
      app.AddDistributedTokenCache(services =>
      {
        // Requires to reference Microsoft.Extensions.Caching.Cosmos
        services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
        {
          cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
          cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
          cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
          cacheOptions.CreateIfNotExists = true;
        });
       });
```

### <a name="disabling-legacy-token-cache"></a>Désactivation du cache de jeton hérité

MSAL dispose d’un code interne spécifique pour permettre d’interagir avec le cache ADAL hérité. Quand MSAL et ADAL ne sont pas utilisés côte à côte (le cache hérité n’est donc pas utilisé), le code de cache hérité associé est inutile. MSAL [4.25.0](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases/tag/4.25.0) ajoute la possibilité de désactiver le code de cache ADAL hérité et d’améliorer les performances d’utilisation du cache. Consultez la demande de tirage (pull request) [#2309](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/pull/2309) pour comparer les performances avant et après la désactivation du cache hérité. Appelez `.WithLegacyCacheCompatibility(false)` sur un générateur d’application comme ci-dessous.

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithLegacyCacheCompatibility(false)
    .Build();
```

### <a name="disabling-cache-synchronization"></a>Désactivation de la synchronisation du cache

Par défaut, MSAL verrouille l’accès au cache au niveau de l’application cliente confidentielle entre les lectures et les écritures du cache. Ce verrou peut poser problème si le sérialiseur de cache prend beaucoup de temps jusqu’à l’expiration du délai d’attente, ce qui peut être le cas avec des caches Redis. Vous pouvez définir l’indicateur `WithCacheSynchronization` sur false pour activer une stratégie de verrouillage du cache optimiste, susceptible d’améliorer les performances, en particulier lorsque des objets ConfidentialClientApplication sont réutilisés entre les demandes. 

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithCacheSynchronization(false)
    .Build();
```
### <a name="samples"></a>Exemples

- L’utilisation des sérialiseurs de cache de jeton dans une infrastructure .NET Framework et des applications .NET Core est illustrée dans cet exemple [ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache) 
- L’exemple suivant est une application web ASP.NET utilisant la même technique : https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect (Voir [WebApp/Utils/MsalAppBuilder.cs)](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs)

## <a name="desktop-apps"></a>[Applications de bureau](#tab/desktop)

Dans les applications de bureau, il est recommandé d’utiliser le cache de jeton multiplateforme.

#### <a name="cross-platform-token-cache-msal-only"></a>Cache de jeton multiplateforme (MSAL uniquement)

MSAL.NET fournit un cache de jeton multiplateforme dans une bibliothèque distincte nommée Microsoft.Identity.Client.Extensions.Msal, dont le code source est disponible à partir de https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet.

##### <a name="referencing-the-nuget-package"></a>Référencement du package NuGet

Ajoutez le package NuGet [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/) à votre projet.

##### <a name="configuring-the-token-cache"></a>Configuration du cache de jeton

Pour plus d'informations, consultez https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache. Voici un exemple d’utilisation du cache de jeton multiplateforme.

```csharp
 var storageProperties =
     new StorageCreationPropertiesBuilder(Config.CacheFileName, Config.CacheDir)
     .WithLinuxKeyring(
         Config.LinuxKeyRingSchema,
         Config.LinuxKeyRingCollection,
         Config.LinuxKeyRingLabel,
         Config.LinuxKeyRingAttr1,
         Config.LinuxKeyRingAttr2)
     .WithMacKeyChain(
         Config.KeyChainServiceName,
         Config.KeyChainAccountName)
     .Build();

 IPublicClientApplication pca = PublicClientApplicationBuilder.Create(clientId)
    .WithAuthority(Config.Authority)
    .WithRedirectUri("http://localhost")  // make sure to register this redirect URI for the interactive login 
    .Build();
    

// This hooks up the cross-platform cache into MSAL
var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties );
cacheHelper.RegisterCache(pca.UserTokenCache);
         
```


##### <a name="plain-text-fallback-mode"></a>Mode de secours en texte brut

Le cache de jeton multiplateforme vous permet de stocker des jetons non chiffrés en texte clair. Elle est destinée à être utilisée dans les environnements de développement à des fins de débogage uniquement. Vous pouvez utiliser le mode de secours en texte brut à l’aide du modèle de code suivant.

```csharp
storageProperties =
    new StorageCreationPropertiesBuilder(
        Config.CacheFileName + ".plaintext",
        Config.CacheDir)
    .WithUnprotectedFile()
    .Build();

var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties).ConfigureAwait(false);
```


## <a name="mobile-apps"></a>[Applications mobiles](#tab/mobile)

Dans MSAL.NET, un cache de jetons en mémoire est fourni par défaut. La sérialisation est fournie par défaut pour les plateformes où un stockage sécurisé est disponible pour un utilisateur dans le cadre de la plateforme : Universal Windows Platform (UWP), Xamarin.iOS et Xamarin.Android.

## <a name="write-your-own-cache"></a>[Écrire votre propre cache](#tab/custom)

Si vous voulez vraiment écrire votre propre sérialiseur de cache de jetons, MSAL.NET fournit une sérialisation de cache de jeton personnalisée dans les sous-plateformes .NET Framework et .NET Core. Les événements sont déclenchés lors de l’accès au cache, les applications peuvent choisir de sérialiser ou de désérialiser le cache. Sur les applications clientes confidentielles qui gèrent les utilisateurs (applications web connectant les utilisateurs et appellent des API web, et API web appelant des API Web en aval), il peut y avoir de nombreux utilisateurs et ceux-ci utilisateurs sont traités en parallèle. Pour des raisons de sécurité et de performances, nous vous recommandons de sérialiser un cache par utilisateur. Les événements de sérialisation calculent une clé de cache en fonction de l’identité de l’utilisateur traité et sérialisent/désérialisent un cache de jeton pour cet utilisateur.

N’oubliez pas que la sérialisation personnalisée n’est pas disponible sur les plateformes mobiles (UWP, Xamarin.iOS et Xamarin.Android). MSAL définit déjà un mécanisme de sérialisation sécurisé et performant pour ces plateformes. Les applications .NET Desktop et .NET Core, en revanche, ont des architectures diverses et MSAL ne peut pas implémenter un mécanisme de sérialisation universel. Par exemple, les sites web peuvent choisir de stocker les jetons dans un cache Redis tandis que les applications de bureau les stockent dans un fichier chiffré. Ainsi, la sérialisation n’est pas fournie prête à l’emploi. Pour obtenir une application de cache de jetons .NET Desktop ou .NET Core persistante, personnalisez la sérialisation.

Les classes et interfaces suivantes sont utilisées dans la sérialisation du cache de jetons :

- `ITokenCache`, qui définit des événements pour s’abonner à des requêtes de sérialisation du cache de jeton, et des méthodes pour sérialiser ou désérialiser le cache dans différents formats (ADAL v3.0, MSAL 2.x et MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback` est un rappel transmis aux événements pour que vous puissiez gérer la sérialisation. Ceux-ci sont appelés avec des arguments de type `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` fournit uniquement la valeur `ClientId` de l’application et une référence à l’utilisateur pour lequel le jeton est disponible.

  ![Diagramme de classes](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET crée les caches de jetons automatiquement et vous fournit le cache `IToken` quand vous appelez les propriétés `UserTokenCache` et `AppTokenCache` d’une application. Vous n’êtes pas censé implémenter l’interface vous-même. Votre responsabilité, lorsque vous implémentez une sérialisation du cache de jetons personnalisée, consiste à :
> - Réagir aux « événements » `BeforeAccess` et `AfterAccess` (ou à leurs versions Async). Le délégué `BeforeAccess` se charge de désérialiser le cache, tandis que le délégué `AfterAccess` est responsable de la sérialisation du cache.
> - Une partie de ces événements stockent ou chargent des objets blob, lesquels sont transmis par le biais de l’argument de l’événement au stockage voulu.

Les stratégies diffèrent selon que vous écrivez une sérialisation du cache de jetons pour une [application cliente publique](msal-client-applications.md) (bureau) ou une [application cliente confidentielle](msal-client-applications.md) (application/API web, application de démon).

### <a name="custom-token-cache-for-a-web-app-or-web-api-confidential-client-application"></a>Cache de jeton pour une application web ou une API web (application cliente confidentielle)

Dans les applications ou API web, le cache peut exploiter la session, un cache Redis, une base de données SQL ou une base de données Cosmos DB. Conservez un cache de jeton par compte dans les applications web et les API web : 
- Pour les applications web, le cache de jeton doit être indexé par ID de compte.
- Pour les API web, le compte doit être indexé avec le hachage du jeton utilisé pour appeler l’API.

Des exemples de sérialiseurs de cache de jeton sont fournis dans [Microsoft.Identity.Web/TokenCacheProviders](https://github.com/AzureAD/microsoft-identity-web/tree/master/src/Microsoft.Identity.Web/TokenCacheProviders).

### <a name="custom-token-cache-for-a-desktop-or-mobile-app-public-client-application"></a>Cache de jeton personnalisé pour une application de bureau ou mobile (application cliente publique)

Depuis MSAL.NET v2.x, vous avez plusieurs possibilités pour sérialiser le cache de jetons d’un client public. Vous pouvez sérialiser le cache uniquement au format MSAL.NET (le cache au format unifié est commun à MSAL et aux plateformes).  Vous pouvez également prendre en charge la sérialisation du cache de jetons [héritée](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) d’ADAL V3.

La personnalisation de la sérialisation du cache de jetons pour partager l’état de l’authentification unique entre ADAL.NET 3.x, ADAL.NET 5.x et MSAL.NET est expliquée dans une partie de l’exemple suivant : [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Le format du cache de jetons MSAL.NET 1.1.4-preview n’est plus pris en charge dans MSAL 2.x. Si vous avez des applications qui exploitent MSAL.NET 1.x, vos utilisateurs devront se reconnecter. Sinon, la migration à partir d’ADAL 4.x (et 3.x) est prise en charge.

#### <a name="simple-token-cache-serialization-msal-only"></a>Sérialisation simple du cache de jetons (MSAL.NET uniquement)

Voici un exemple d’implémentation naïve de la sérialisation personnalisée d’un cache de jetons pour des applications de bureau. Ici, le cache de jetons utilisateur est un fichier situé dans le même dossier que l’application.

Une fois que vous avez généré l’application, vous activez la sérialisation en appelant la méthode `TokenCacheHelper.EnableSerialization()` et en passant l’application `UserTokenCache`.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

La classe d’assistance `TokenCacheHelper` est définie en tant que :

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Un sérialiseur basé sur un fichier de cache de jeton de qualité produit, pour des applications clientes publiques et des applications de bureau s’exécutant sur Windows, Mac et Linux, est disponible depuis la bibliothèque open source [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal). Vous pouvez l’inclure dans vos applications à partir du package NuGet suivant : [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Sérialisation d’un cache de jetons double (cache unifié MSAL et ADAL v3)

Si vous voulez implémenter une sérialisation du cache de jetons au format de cache unifié (commun à ADAL.NET 4.x, MSAL.NET 2.x et d’autres versions de MSAL de même génération ou antérieures sur la même plateforme), examinez le code suivant :

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Cette fois, la classe d’assistance est définie en tant que :

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

---

## <a name="monitor-cache-hit-ratios-and-cache-performance"></a>Surveiller les taux d’accès au cache et les performances de celui-ci

MSAL expose des métriques importantes dans l’objet [AuthenticationResult. AuthenticationResultMetadata](/dotnet/api/microsoft.identity.client.authenticationresultmetadata). Vous pouvez consigner ces métriques pour évaluer l’intégrité de votre application.

| Métrique       | Signification     | Quand déclencher une alarme ?    |
| :-------------: | :----------: | :-----------: |
|  `DurationTotalInMs` | Temps total passé dans MSAL, appels réseau et cache inclus   | Alarme sur une latence élevée globale (> 1 s). La valeur dépend de la source du jeton. À partir du cache : un accès au cache. À partir d’AAD : deux accès au cache + un appel HTTP. Le premier appel (par processus) prend plus de temps en raison d’un appel HTTP supplémentaire. |
|  `DurationInCacheInMs` | Temps passé à charger ou enregistrer le cache de jeton qui est personnalisé par le développeur de l’application (par exemple, enregistrer dans Redis).| Alarme lors de pics. |
|  `DurationInHttpInMs`| Temps passé à effectuer des appels HTTP à AAD.  | Alarme lors de pics.|
|  `TokenSource` | Indique la source du jeton. Les jetons sont récupérés à partir du cache beaucoup plus rapidement (par exemple, ~100 ms au lieu de ~700 ms). Peut être utilisé pour surveiller le taux d’accès au cache et générer des alarmes par rapport à celui-ci. | Utiliser avec `DurationTotalInMs` |


## <a name="next-steps"></a>Étapes suivantes

Les exemples suivants illustrent la sérialisation du cache de jetons.

| Exemple | Plateforme | Description|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Bureau (WPF) | Application Windows Desktop .NET (WPF) appelant l’API Microsoft Graph. ![Le diagramme montre une topologie avec l’application de bureau WPF TodoListClient qui circule jusqu’à Azure AD en acquérant un jeton de manière interactive, puis jusqu’à Microsoft Graph.](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Bureau (Console) | Ensemble de solutions Visual Studio illustrant la migration d’applications Azure AD v1.0 (à l’aide d’ADAL.NET) vers des applications de plateforme d’identité Microsoft (à l’aide de MSAL.NET). En particulier, consultez [Migration de cache de jeton](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md) et [Cache de jeton de client confidentiel](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache) |
[ms-identity-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | ASP.NET (net472) | Exemple de sérialisation de cache de jeton dans une application MVC ASP.NET (à l’aide de MSAL.NET). Consultez en particulier [MsalAppBuilder](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs)
