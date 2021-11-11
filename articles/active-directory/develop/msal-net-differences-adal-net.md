---
title: Différences entre les applications ADAL.NET et MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Découvrez les différences entre Microsoft Authentication Library pour .NET (MSAL.NET) et Azure AD Authentication Library pour .NET (ADAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 06/09/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 5dfc0c3c006c42f6e56e9f2f15311a2b99396fe4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424094"
---
# <a name="differences-between-adalnet-and-msalnet-apps"></a>Différences entre les applications ADAL.NET et MSAL.NET

La migration de vos applications à l’aide de ADAL vers MSAL comporte des avantages en matière de sécurité et de résilience. Cet article décrit les différences entre MSAL.NET et ADAL.NET. Dans la plupart des cas, vous avez besoin d’utiliser MSAL.NET et la Plateforme d’identités Microsoft, à savoir la dernière génération de bibliothèques d’authentification de Microsoft. En utilisant MSAL.NET, vous acquérez des jetons pour la connexion des utilisateurs à votre application avec Azure AD (comptes professionnels et scolaires), des comptes (personnels) Microsoft (MSA) ou Azure AD B2C.

Si vous êtes déjà familiarisé avec ADAL.NET et le point de terminaison Azure AD pour développeurs (v1.0), découvrez les [différences de la Plateforme d’identités Microsoft](../azuread-dev/azure-ad-endpoint-comparison.md). Vous devez quand même utiliser ADAL.NET si votre application a besoin de connecter les utilisateurs avec des versions antérieures des [services de fédération Active Directory (AD FS)](/windows-server/identity/active-directory-federation-services). Pour plus d'informations, consultez le [support ADFS](https://aka.ms/msal-net-adfs-support).

|   | **ADAL NET**  | **MSAL.NET**  |
|-----------------------------|--------------------------------------------|---------------------|
| **Espaces de noms et packages NuGet**  |ADAL a été consommé à partir du package NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory). L’espace de noms était `Microsoft.IdentityModel.Clients.ActiveDirectory`.  | Ajoutez le package NuGet [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) et utilisez l’espace de noms `Microsoft.Identity.Client`. Si vous générez une application cliente confidentielle, consultez [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web). |
| **Étendues et ressources**  | ADAL.NET acquiert des jetons pour les *ressources*. |  MSAL.NET acquiert des jetons pour les *étendues*. Plusieurs remplacements `AcquireTokenXXX` MSAL.NET exigent un paramètre appelé scopes(`IEnumerable<string> scopes`). Ce paramètre est une simple liste de chaînes qui déclarent les autorisations et les ressources demandées. Les [étendues de Microsoft Graph](/graph/permissions-reference) figurent parmi les plus connues. Vous pouvez également [accéder aux ressources v1.0 à l’aide de MSAL.NET](#scopes). |
| **Classes principales**  | ADAL.NET utilisait [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) comme représentation de votre connexion auprès du service d’émission de jeton de sécurité (STS) ou du serveur d’autorisation, par le biais d’une autorité. | MSAL.NET est conçue autour des [applications clientes](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Elle définit des interfaces `IPublicClientApplication` pour les applications clientes publiques et `IConfidentialClientApplication` pour les applications clientes confidentielles, ainsi qu’une interface `IClientApplicationBase` de base pour le contrat commun aux deux types d’applications.|
| **Acquisition de jeton**  | Dans les clients publics, ADAL utilise `AcquireTokenAsync` et `AcquireTokenSilentAsync` pour les appels d’authentification. | Dans les clients publics, MSAL utilise `AcquireTokenInteractive` et `AcquireTokenSilent` pour les mêmes appels d’authentification. Les paramètres sont différents de ceux de ADAL. <br><br>Les applications clientes confidentielles possèdent des [méthodes d’acquisition de jeton](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-Tokens) avec un nom explicite en fonction du scénario. Une autre différence vient du fait que, dans MSAL.NET, vous n’avez plus besoin de passer la valeur `ClientID` de votre application dans chaque appel AcquireTokenXX. La valeur `ClientID` est définie une seule fois lors de la génération de `IPublicClientApplication` ou `IConfidentialClientApplication`.|
|  **IAccount et IUser**  | ADAL définit la notion d’utilisateur via l’interface IUser. Toutefois, un utilisateur est un agent humain ou logiciel. En tant que tel; un utilisateur peut posséder un ou plusieurs comptes dans la plateforme d’identités Microsoft (plusieurs comptes Azure AD, Azure AD B2C, des comptes personnels Microsoft). L’utilisateur peut également être responsable d’un ou de plusieurs comptes de la plateforme d’identités Microsoft. | MSAL.NET définit le concept de compte (par le biais de l’interface IAccount). L’interface IAccount représente les informations d’un seul compte. L’utilisateur peut avoir plusieurs comptes dans différents locataires. MSAL.NET fournit de meilleures informations dans les scénarios d’invité, car des informations de compte d’accueil sont fournies. Vous pouvez en savoir plus sur les [différences entre IUser et IAccount](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/msal-net-2-released#iuser-is-replaced-by-iaccount).|
|  **Persistance du cache**  | ADAL.NET vous permet d’étendre la classe `TokenCache` pour implémenter la fonctionnalité de persistance souhaitée sur les plateformes sans stockage sécurisé (.NET Framework et .NET Core) en utilisant les méthodes `BeforeAccess` et `BeforeWrite`. Pour plus d’informations, consultez [Sérialisation du cache de jetons dans ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization). | MSAL.NET transforme le cache de jetons en classe sealed, en supprimant la possibilité de l’étendre. Ainsi, votre implémentation de la persistance du cache de jetons doit prendre la forme d’une classe d’assistance qui interagit avec le cache de jetons sealed. Cette interaction est décrite dans l’article [Sérialisation du cache de jetons dans MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization). La sérialisation est différente pour une application cliente publique (voir [Cache de jetons pour une application cliente publique](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application)) et pour une application cliente confidentielle (voir [Cache de jetons pour une application web ou une API web](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application)). |
| **Autorité commune** | ADAL utilise Azure AD v1.0. L’autorité `https://login.microsoftonline.com/common` d’Azure AD v1.0 (utilisée par ADAL) permet aux utilisateurs de se connecter en utilisant n’importe quel compte d’organisation AAD (professionnel ou scolaire). Azure AD v1.0 ne permet pas de se connecter avec des comptes personnels Microsoft. Pour plus d’informations, [Validation de l’autorité dans ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation). | MSAL utilise Azure AD v 2.0. L’autorité `https://login.microsoftonline.com/common` d’Azure AD v2.0 (utilisée par MSAL) permet aux utilisateurs de se connecter avec n’importe quel compte d’organisation AAD (professionnel ou scolaire) ou avec un compte personnel Microsoft. Pour limiter la connexion à l’aide de comptes d’organisation (compte professionnel ou scolaire) dans MSAL, vous devez utiliser le point de terminaison `https://login.microsoftonline.com/organizations`.  Pour plus d’informations, consultez le paramètre `authority` dans l’[application cliente publique](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication). |

## <a name="supported-grants"></a>Octrois pris en charge

Vous trouverez ci-dessous un résumé comparant les octrois MSAL.NET et ADAL.NET pris en charge pour les applications clientes publiques et confidentielles.

### <a name="public-client-applications"></a>Applications clientes publiques

L’image suivante résume certaines des différences entre ADAL.NET et MSAL.NET pour une application cliente publique.

[![Capture d’écran montrant certaines des différences entre ADAL.NET et MSAL.NET pour une application cliente publique.](media/msal-compare-msaldotnet-and-adaldotnet/differences.png)](media/msal-compare-msaldotnet-and-adaldotnet/differences.png#lightbox)

Voici les octrois pris en charge ADAL.NET et MSAL.NET pour les applications de bureau et mobiles.

Accorder                             | MSAL.NET                                                                                                                     | ADAL.NET                                                                                                                                                                                                   |
--------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
Interactive                       | [Acquisition des jetons de manière interactive dans MSAL.NET](scenario-desktop-acquire-token-interactive.md)    | [Authentification interactive](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows)                                              |
Authentification Windows intégrée | [Authentification Windows intégrée](scenario-desktop-acquire-token-integrated-windows-authentication.md)         | [Authentification intégrée sur Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos))  |
Nom d’utilisateur / mot de passe               | [Authentification par nom d'utilisateur/mot de passe](scenario-desktop-acquire-token-username-password.md)                      | [Acquisition des jetons avec un nom d’utilisateur et un mot de passe](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)                                        |
Flux de code d’appareil                  | [Flux de code d’appareil](scenario-desktop-acquire-token-device-code-flow.md)                    | [Profil d’appareil pour les appareils sans navigateurs web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers)                                |

### <a name="confidential-client-applications"></a>Applications clientes confidentielles

L’image suivante résume certaines des différences entre ADAL.NET et MSAL.NET pour une application cliente confidentielle.
 
[![Capture d’écran montrant certaines des différences entre ADAL.NET et MSAL.NET pour une application cliente confidentielle.](media/msal-net-migration/confidential-client-application.png)](media/msal-net-migration/confidential-client-application.png#lightbox)


Voici les octrois pris en charge dans ADAL.NET, MSAL.NET et Microsoft.Identity.Web pour les applications web, les API web et les applications démon :

Type d’application              | Accorder              | MSAL.NET                                                                                                                             | ADAL.NET                                                                                                                                                                                              |
------------------------ | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
Application web, API web, démon | Informations d’identification du client | [Flux d’informations d’identification du client dans MSAL.NET](scenario-daemon-acquire-token.md#acquiretokenforclient-api)                        | [Flux d’informations d’identification du client dans ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows)                                                               |
API Web                  | OBO (On-Behalf-Of)       | [OBO dans MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/on-behalf-of)                 | [Appels de service à service pour le compte de l’utilisateur avec ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user)               |
Application web                  | Code d’authentification          | [Acquisition des jetons avec des codes d’autorisation sur les applications web avec MSAL.NET](scenario-web-app-call-api-acquire-token.md)  | [Acquisition des jetons avec des codes d’autorisation sur les applications web avec ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) |

## <a name="migrating-from-adal-2x-with-refresh-tokens"></a>Migration à partir de ADAL 2. x avec des jetons d’actualisation

Dans ADAL.NET v2. X, les jetons d’actualisation ont été exposés, ce qui vous permet de développer des solutions autour de l’utilisation de ces jetons en les mettant en cache et en utilisant les méthodes `AcquireTokenByRefreshToken` fournies par ADAL 2.x.

Certaines de ces solutions ont été utilisées dans des scénarios comme :

- Services à exécution longue qui exécutent des actions, notamment l’actualisation des tableaux de bord pour les utilisateurs lorsque ces utilisateurs ne sont plus connectés / authentifiés auprès de l’application.
- Scénarios WebFarm pour permettre au client d’apporter le jeton d’actualisation au service web (la mise en cache est effectuée côté client, le cookie est chiffré, et non côté serveur).

Pour des raisons de sécurité, MSAL.NET n’expose pas les jetons d’actualisation. MSAL gère l'actualisation des jetons pour vous.

Heureusement, MSAL.NET dispose d’une API qui vous permet de migrer vos jetons d’actualisation précédents (acquis avec ADAL) dans `IConfidentialClientApplication` :

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration
/// scenarios where you have a RefreshToken available.
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint.
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```

Avec cette méthode, vous pouvez fournir le jeton d’actualisation utilisé précédemment, ainsi que toutes les étendues (ressources) souhaitées. Le jeton d’actualisation est échangé avec un nouveau jeton et mis en cache dans votre application.

Étant donné que cette méthode est destinée aux scénarios inhabituels, elle n’est pas facilement accessible avec `IConfidentialClientApplication` sans être d’abord castée vers `IByRefreshToken`.

L’extrait de code ci-dessous montre du code de migration dans une application cliente confidentielle.

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;

AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

`GetCachedRefreshTokenForSignedInUser` récupère le jeton d’actualisation stocké par une version précédente de l’application qui use ADAL 2.x. `GetTokenCacheForSignedInUser` désérialise un cache pour l’utilisateur connecté (puisque les applications clientes confidentielles doivent avoir un seul cache par utilisateur).

Un jeton d’accès et un jeton d’ID sont retournés dans la valeur `AuthenticationResult` tandis que le nouveau jeton d’actualisation est stocké dans le cache. Vous pouvez également utiliser cette méthode pour divers scénarios d’intégration dans lesquels vous avez un jeton d’actualisation disponible.

## <a name="v10-and-v20-tokens"></a>Jetons v1.0 et v2.0

Il existe deux versions des jetons : les jetons v1.0 et les jetons v2.0. Le point de terminaison v1.0 (utilisé par ADAL) émet des jetons d’ID v1.0, tandis que le point de terminaison v2.0 (utilisé par MSAL) émet des jetons d’ID v2.0. Toutefois, les deux points de terminaison émettent des jetons d’accès de la version du jeton que l’API Web accepte. Une propriété du manifeste d'application de l'API web permet aux développeurs de choisir quelle version du jeton est acceptée. Consultez `accessTokenAcceptedVersion` dans la documentation de référence du [manifeste de l’application](reference-app-manifest.md).

Pour plus d’informations sur les jetons d’accès v1.0 et v2.0, consultez [Jetons d’accès Azure Active Directory](access-tokens.md).

## <a name="exceptions"></a>Exceptions

### <a name="interaction-required-exceptions"></a>Exceptions d’interaction requise

Avec MSAL.NET, vous interceptez `MsalUiRequiredException` comme décrit dans [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {"try to authenticate interactively"}
}
```

Pour plus d’informations, consultez [Gérer les erreurs et les exceptions dans MSAL.NET](msal-error-handling-dotnet.md)

ADAL.NET comportait moins d’exceptions explicites. Par exemple, quand une authentification silencieuse échoue dans ADAL, la procédure consistait à intercepter l’exception et à rechercher le code d’erreur `user_interaction_required` :

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Pour plus d’informations, consultez [le modèle recommandé pour acquérir un jeton dans des applications clientes publiques](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) avec ADAL.NET.

### <a name="prompt-behavior"></a>Comportement d’invite

Le comportement de l’invite dans MSAL.NET est équivalent au comportement de l’invite dans ADAL.NET :

|  ADAL.NET | MSAL.NET | Description |
| ----------- | ----------- | -------------|
| `PromptBehavior.Auto`| `NoPrompt`| Azure AD choisit le comportement le plus approprié (en connectant les utilisateurs silencieusement s’ils sont connectés avec un seul compte, ou en affichant le sélecteur de compte s’ils sont connectés avec plusieurs comptes). |
| `PromptBehavior.Always`| `ForceLogin` | Réinitialise la boîte de dialogue de connexion et force l’utilisateur à ré-entrer ses informations d’identification. |
| `PromptBehavior.RefreshSession`| `Consent`| Oblige l’utilisateur à redonner son consentement à toutes les autorisations. |
| `PromptBehavior.Never`| `Never`| N’utilisez pas cette option. Au lieu de cela, utilisez le [modèle recommandé pour les applications clientes publiques](scenario-desktop-acquire-token.md?tabs=dotnet). |
| `PromptBehavior.SelectAccount`| `SelectAccount`| Affiche le sélecteur de compte et force l’utilisateur à sélectionner un compte. |

### <a name="handling-claim-challenge-exceptions"></a>Gestion des exceptions de demande de revendication

Parfois, lors de l’acquisition d’un jeton, Azure AD génère une exception lorsqu’une ressource nécessitait plus de revendications de la part de l’utilisateur (par exemple, l’authentification à deux facteurs).

Dans MSAL.NET, les exceptions de demande de revendication sont gérées de la manière suivante :

- Les `Claims` font surface dans `MsalServiceException`.
- Il existe une méthode `.WithClaim(claims)` qui peut s’appliquer aux générateurs `AcquireTokenXXX`.

Pour plus d’informations, consultez [Gestion de MsalUiRequiredException](msal-error-handling-dotnet.md#msaluirequiredexception).

Dans ADAL.NET, les exceptions de demande de revendication étaient gérées de la manière suivante :

- `AdalClaimChallengeException` est une exception (dérivation à partir de `AdalServiceException`). Le membre `Claims` contient un fragment JSON avec les revendications attendues. 
- L’application cliente publique qui reçoit cette exception avait besoin d’appeler le remplacement de `AcquireTokenInteractive` qui a un paramètre de revendication. Ce remplacement de `AcquireTokenInteractive` n’essaie même pas d’atteindre le cache, car c’est inutile. En effet, le jeton situé dans le cache n’a pas les bonnes revendications (sinon, aucune `AdalClaimChallengeException` n’aurait été levée). Par conséquent, il n’est pas nécessaire d’examiner le cache. `ClaimChallengeException` peut être reçu dans une API web qui fait OBO, mais `AcquireTokenInteractive` doit être appelé dans une application cliente publique qui appelle cette API web.

Pour plus d’informations, notamment pour obtenir des exemples, consultez [Gestion de AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception).


## <a name="scopes"></a>Étendues

ADAL utilise le concept de ressources avec la chaîne `resourceId` ; MSAL.NET utilise des étendues. La logique utilisée par Azure AD se présente comme suit :

- Pour le point de terminaison ADAL (v1.0) avec jeton d’accès v1.0 (le seul possible), `aud=resource`.
- Pour le point de terminaison MSAL (v2.0) qui demande un jeton d’accès pour une ressource qui accepte des jetons v2.0, `aud=resource.AppId`.
- Concernant le point de terminaison MSAL (v2.0) qui demande un jeton d’accès pour une ressource acceptant un jeton d’accès v1.0, Azure AD analyse l’audience souhaitée d’après l’étendue demandée. Pour ce faire, il suffit de prendre tout ce qui précède la dernière barre oblique et de l’utiliser comme identificateur de ressource. Ainsi, si `https://database.windows.net` attend une audience `https://database.windows.net/`, vous devez demander une étendue `https://database.windows.net//.default` (notez la double barre oblique devant ./default). Cela est illustré par les exemples 1 et 2 ci-dessous.

### <a name="example-1"></a>Exemple 1

Pour acquérir des jetons pour une application acceptant les jetons v1.0 (par exemple, l’API Microsoft Graph, disponible à l’adresse `https://graph.microsoft.com`), vous devez créer des `scopes` en concaténant un identificateur de ressource de votre choix avec une autorisation OAuth2 souhaitée pour cette ressource.

Par exemple, pour accéder au nom de l’utilisateur à une API web v1.0 dont l’URI de l’ID d’application est `ResourceId`, vous devez utiliser :

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Pour lire et écrire avec MSAL.NET Azure Active Directory à l’aide de l’API Microsoft Graph (`https://graph.microsoft.com/`), vous devez créer la liste des étendues, comme dans l’extrait de code ci-dessous :

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

### <a name="example-2"></a>Exemple 2

Si resourceId se termine par une barre oblique « / », vous devez avoir une double barre oblique « / » lors de l’écriture de la valeur de l’étendue. Par exemple, si vous voulez écrire l’étendue correspondant à l’API Azure Resource Manager (`https://management.core.windows.net/`), demandez l’étendue suivante (notez les deux barres obliques).

```csharp
var resource = "https://management.core.windows.net/"
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

En effet, l’API Resource Manager attend une barre oblique dans sa revendication d’audience (`aud`) et il y a une barre oblique qui sépare le nom de l’API de l’étendue.

Si vous souhaitez acquérir un jeton pour toutes les étendues statiques d’une application v1.0, vous devez créer votre liste d’étendues comme indiqué dans l’extrait de code ci-dessous :

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

Pour un flux d’informations d’identification du client, l’étendue à passer est également `/.default`. Cette étendue indique à Azure AD toutes les autorisations au niveau de l'application que l'administrateur a acceptées dans l'inscription de l'application.

## <a name="next-steps"></a>Étapes suivantes

[Migrer vos applications de ADAL vers MSAL](msal-net-migration.md)
[Migrer vos applications clientes confidentielles ADAL.NET pour utiliser MSAL.NET](msal-net-migration-confidential-client.md)
