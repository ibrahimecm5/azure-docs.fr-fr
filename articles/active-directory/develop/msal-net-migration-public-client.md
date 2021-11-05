---
title: Migrer des applications clientes publiques vers MSAL.NET
titleSuffix: Microsoft identity platform
description: Découvrez comment migrer une application cliente publique de la Bibliothèque d’authentification Active Directory pour .NET vers la bibliothèque d’authentification Microsoft pour .NET.
services: active-directory
author: sahmalik
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2021
ms.author: sahmalik
ms.reviewer: saeeda, shermanouko, jmprieur
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 0bb1dc80d9bebf0488bfa2a272f5f7fa5303b31b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018012"
---
# <a name="migrate-public-client-applications-from-adalnet-to-msalnet"></a>Migrer des applications clientes publiques d’ADAL.NET vers MSAL.NET

Cet article décrit comment migrer une application cliente publique de la Bibliothèque d’authentification Active Directory pour .NET (ADAL.NET) vers la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET). Les applications clientes publiques sont des applications de bureau, notamment des applications Win32, WPF, UWP et autres applications mobiles, qui appellent un autre service au nom de l’utilisateur. Pour plus d’informations sur les applications clientes publiques, consultez [Flux d’authentification et scénarios d’applications](authentication-flows-app-scenarios.md).

## <a name="migration-steps"></a>Étapes de la migration

1. Recherchez le code à l’aide de ADAL.NET dans votre application.

   Le code qui utilise ADAL dans une application cliente publique instancie `AuthenticationContext` et appelle une valeur de remplacement de `AcquireTokenAsync` avec les paramètres suivants :

   - Chaîne `resourceId`. Cette variable est l’URI ID d’application de l’API web que vous souhaitez appeler.
   - `clientId` qui est l’identificateur de votre application, également appelé ID d’application.

2. Une fois que vous avez identifié les applications qui utilisent ADAL.NET, installez le package MSAL.NET NuGet [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) et mettez à jour les références de votre bibliothèque de projet. Pour plus d’informations, consultez le document [Installation d’un package NuGet](https://www.bing.com/search?q=install+nuget+package).

3. Mettez à jour le code en fonction du scénario d’application cliente publique. Certaines étapes sont communes et s’appliquent à tous les scénarios de clients publics. D’autres étapes sont propres à chaque scénario. 

   Les scénarios de clients publics sont les suivants :

   - [Gestionnaire d’authentification web](scenario-desktop-acquire-token-wam.md), l’authentification préférée basée sur un répartiteur sur Windows.
   - [Authentification interactive](scenario-desktop-acquire-token-interactive.md) où l’utilisateur voit s’afficher une interface web pour terminer le processus de connexion.
   - [Authentification Windows intégrée](scenario-desktop-acquire-token-integrated-windows-authentication.md) où un utilisateur se connecte à l’aide de l’identité qu’il a utilisée pour se connecter à un domaine Windows (pour les machines jointes à un domaine ou à Azure AD).
   - [Nom d’utilisateur/mot de passe](scenario-desktop-acquire-token-username-password.md) où la connexion s’établit en fournissant des informations d’identification de nom d’utilisateur/mot de passe.
   - [Flux de code d’appareil](scenario-desktop-acquire-token-device-code-flow.md) où un appareil offrant une expérience utilisateur limitée affiche un code d’appareil pour terminer le processus d’authentification sur un autre appareil.


## <a name="interactive"></a>[Interactive](#tab/interactive)

Dans les scénarios interactifs, votre application cliente publique affiche une interface utilisateur de connexion hébergée dans un navigateur, et l’utilisateur doit se connecter de manière interactive.

#### <a name="find-out-if-your-code-uses-interactive-scenarios"></a>Déterminer si votre code utilise des scénarios interactifs

Le code ADAL pour votre application dans une application cliente publique qui utilise une authentification interactive instancie `AuthenticationContext` et inclut un appel à `AcquireTokenAsync` avec les paramètres suivants.
 - `clientId` qui est un GUID représentant l’inscription de votre application
 - `resourceUrl` indiquant la ressource pour laquelle vous demandez le jeton
 - URI qui est l’URL de réponse
 - Objet `PlatformParameters`. 

 #### <a name="update-the-code-for-interactive-scenarios"></a>Mettre à jour le code pour les scénarios interactifs

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

Dans le constructeur, remplacez l’appel à `AuthenticationContext.AcquireTokenAsync` par un appel à `IPublicClientApplication.AcquireTokenInteractive`.

Voici une comparaison du code ADAL.NET et MSAL.NET pour les scénarios interactifs :

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
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await ac.AcquireTokenAsync("<clientId>",
                                    "https://resourceUrl",
                                    new Uri("https://ClientReplyUrl"),
                                    new PlatformParameters(PromptBehavior.Auto));
```
:::column-end:::   
:::column span="":::
```csharp
// 1. Configuration - read below about redirect URI
var pca = PublicClientApplicationBuilder.Create("client_id")
              .WithBroker()
              .Build();

// Add a token cache, see https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-net-token-cache-serialization?tabs=desktop

// 2. GetAccounts
var accounts = await pca.GetAccountsAsync();
var accountToLogin = // choose an account, or null, or use PublicClientApplication.OperatingSystemAccount for the default OS account

try
{
    // 3. AcquireTokenSilent 
    var authResult = await pca.AcquireTokenSilent(new[] { "User.Read" }, accountToLogin)
                              .ExecuteAsync();
}
catch (MsalUiRequiredException) // no change in the pattern
{
    // 4. Specific: Switch to the UI thread for next call . Not required for console apps.
    await SwitchToUiThreadAsync(); // not actual code, this is different on each platform / tech

    // 5. AcquireTokenInteractive
    var authResult = await pca.AcquireTokenInteractive(new[] { "User.Read" })
                              .WithAccount(accountToLogin)  // this already exists in MSAL, but it is more important for WAM
                              .WithParentActivityOrWindow(myWindowHandle) // to be able to parent WAM's windows to your app (optional, but highly recommended; not needed on UWP)
                              .ExecuteAsync();
}
```
   :::column-end:::
:::row-end:::

Le code MSAL ci-dessus utilise un gestionnaire d’authentification web (WAM), ce qui est l’approche recommandée. Si vous souhaitez utiliser une authentification interactive sans WAM, consultez [Authentification interactive](scenario-desktop-acquire-token-interactive.md).

## <a name="integrated-windows-authentication"></a>[Authentification Windows intégrée](#tab/iwa)

Authentification Windows intégrée permet à votre application cliente publique de se connecter à l’aide de l’identité qu’elle a utilisée pour se connecter à un domaine Windows (pour les machines jointes à un domaine ou à Azure AD).

#### <a name="find-out-if-your-code-uses-integrated-windows-authentication"></a>Déterminer si votre code utilise Authentification Windows intégrée

Le code ADAL pour votre application utilise des scénarios Authentification Windows intégrée s’il contient un appel à `AcquireTokenAsync` disponible en tant que méthode d’extension de la classe `AuthenticationContextIntegratedAuthExtensions`, avec les paramètres suivants :

- `resource` indiquant la ressource pour laquelle vous demandez le jeton
- `clientId` qui est un GUID représentant l’inscription de votre application
- Objet `UserCredential` qui représente l’utilisateur pour lequel vous tentez de demander le jeton.

#### <a name="update-the-code-for-integrated-windows-authentication-scenarios"></a>Mettre à jour le code pour les scénarios Authentification Windows intégrée

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

Dans le constructeur, remplacez l’appel à `AuthenticationContext.AcquireTokenAsync` par un appel à `IPublicClientApplication.AcquireTokenByIntegratedWindowsAuth`.

Voici une comparaison des codes ADAL.NET et MSAL.NET pour les scénarios Authentification Windows intégrée :

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
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(resource, clientId,
                                         new UserCredential("john@contoso.com"));
```
:::column-end:::   
:::column span="":::
```csharp
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }

 Console.WriteLine(result.Account.Username);
}
```
   :::column-end:::
:::row-end:::

## <a name="username-password"></a>[Username Password](#tab/up)

L’authentification par nom d’utilisateur/mot de passe est l’étape à laquelle la connexion se produit en fournissant des informations d’identification nom d’utilisateur/mot de passe.
#### <a name="find-out-if-your-code-uses-username-password-authentication"></a>Déterminer si votre code utilise l’authentification par nom d’utilisateur/mot de passe

Le code ADAL pour votre application utilise des scénarios d’authentification par nom d’utilisateur/mot de passe Windows intégrés s’il contient un appel à `AcquireTokenAsync` disponible en tant que méthode d’extension de la classe `AuthenticationContextIntegratedAuthExtensions` avec les paramètres suivants :

- `resource` indiquant la ressource pour laquelle vous demandez le jeton
- `clientId` qui est un GUID représentant l’inscription de votre application
- Objet `UserPasswordCredential` contenant le nom d’utilisateur et le mot de passe de l’utilisateur pour lequel vous tentez de demander le jeton.

#### <a name="update-the-code-for-username-password-auth-scenarios"></a>Mettre à jour le code pour les scénarios d’authentification par nom d’utilisateur/mot de passe

Dans le constructeur, remplacez l’appel à `AuthenticationContext.AcquireTokenAsync` par un appel à `IPublicClientApplication.AcquireTokenByUsernamePassword`.

Voici une comparaison des codes ADAL.NET et MSAL.NET pour les scénarios d’authentification par nom d’utilisateur/mot de passe :

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

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
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(
   resource, clientId, 
   new UserPasswordCredential("john@contoso.com", johnsPassword));

```
:::column-end:::   
:::column span="":::
```csharp
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
```
   :::column-end:::
:::row-end:::

## <a name="device-code"></a>[Code d’appareil](#tab/devicecode)

L’authentification par flux de code d’appareil est le mode d’authentification où un appareil offrant une expérience utilisateur limitée affiche un code d’appareil pour terminer le processus d’authentification sur un autre appareil.

#### <a name="find-out-if-your-code-uses-device-code-flow-authentication"></a>Déterminer si votre code utilise l’authentification par flux de code d’appareil

Le code ADAL pour votre application utilise des scénarios de flux de code d’appareil s’il contient un appel à `AuthenticationContext.AcquireTokenByDeviceCodeAsync` avec les paramètres suivants :
- Une instance d’objet `DeviceCodeResult` qui est instanciée avec le `resourceID` de la ressource pour laquelle vous demandez un jeton, et un `clientId` qui est le GUID représentant votre application.

#### <a name="update-the-code-for-device-code-flow-scenarios"></a>Mettre à jour le code pour les scénarios de flux de code d’appareil

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

Dans le constructeur, remplacez l’appel à `AuthenticationContext.AcquireTokenAsync` par un appel à `IPublicClientApplication.AcquireTokenWithDeviceCode`.

Voici une comparaison des codes ADAL.NET et MSAL.NET pour les scénarios de flux de code d’appareil :

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
static async Task<AuthenticationResult> GetTokenViaCode(AuthenticationContext ctx)
{
 AuthenticationResult result = null;
 try
 {
  result = await ac.AcquireTokenSilentAsync(resource, clientId);
 }
 catch (AdalException adalException)
 {
  if (adalException.ErrorCode == AdalError.FailedToAcquireTokenSilently
   || adalException.ErrorCode == AdalError.InteractionRequired)
  {
  try
  {
   DeviceCodeResult codeResult = await ctx.AcquireDeviceCodeAsync(resource, clientId);
   Console.WriteLine("You need to sign in.");
   Console.WriteLine("Message: " + codeResult.Message + "\n");
   result = await ctx.AcquireTokenByDeviceCodeAsync(codeResult);
  }
  catch (Exception exc)
  {
   Console.WriteLine("Something went wrong.");
   Console.WriteLine("Message: " + exc.Message + "\n");
  }
 }
 return result;
}

```
:::column-end:::   
:::column span="":::
```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
   :::column-end:::
:::row-end:::

---
### <a name="msal-benefits"></a>Avantages MSAL

Les avantages clés de MSAL.NET pour votre application sont les suivants :

- **Résilience**. MSAL.NET permet de rendre votre application résiliente à l’aide des éléments suivants :

   - Avantages du service CCS (Cached Credential Service) Azure AD. CCS fonctionne comme une sauvegarde Azure AD.
   - Renouvellement proactif des jetons si l’API que vous appelez active des jetons de longue durée via l’[évaluation de l’accès continu](app-resilience-continuous-access-evaluation.md).

### <a name="troubleshooting"></a>Résolution des problèmes

Les informations de résolution des problèmes suivantes établissent deux hypothèses : 

- Votre code ADAL.NET fonctionnait.
- Vous avez migré vers MSAL en conservant le même ID client.

Si vous recevez une exception avec l’un des messages suivants : 

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

Vous pouvez résoudre l’exception en procédant comme suit :

1. Vérifiez que vous utilisez la version la plus récente de MSAL.NET.
1. Vérifiez que l’hôte d’autorité que vous avez défini lors de la création de l’application cliente confidentielle et de l’hôte d’autorité que vous avez utilisé avec ADAL sont similaires. En particulier, s’agit-il du même [cloud](msal-national-cloud.md) (Azure Government, Azure China 21Vianet ou Azure Allemagne) ?

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [différences entre les applications ADAL.NET et MSAL.NET](msal-net-differences-adal-net.md).
En savoir plus sur la [sérialisation du cache de jetons dans MSAL.NET](msal-net-token-cache-serialization.md)
