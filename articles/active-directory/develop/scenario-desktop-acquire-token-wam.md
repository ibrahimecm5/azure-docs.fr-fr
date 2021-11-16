---
title: Acquérir un jeton pour appeler une API web à l’aide d’un gestionnaire de comptes web (application de bureau) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment générer une application de bureau qui appelle des API web en vue d’acquérir un jeton pour l’application à l’aide d’un gestionnaire de comptes web.
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: b06c771d14d0257ac69db9d3a9a40d3eadeba3e7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131436257"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-wam"></a>Application de bureau appelant des API web : acquérir un jeton à l’aide d’un gestionnaire de comptes web

MSAL est en mesure d’appeler un gestionnaire de comptes web, un composant Windows 10 fourni avec le système d’exploitation. Ce composant agit comme un répartiteur d’authentification et les utilisateurs de votre application bénéficient de l’intégration avec des comptes connus de Windows, tels que le compte avec lequel vous avez ouvert votre session Windows.

## <a name="availability"></a>Disponibilité

MSAL 4.25+ prend en charge un gestionnaire de comptes web sur UWP, .NET Classic, .NET Core 3.x et .NET 5.

Pour .NET Classic et .NET Core 3.x, la fonctionnalité de gestionnaire de comptes web est entièrement prise en charge, mais vous devez ajouter une référence au package [Microsoft.Identity.Client.Desktop](https://www.nuget.org/packages/Microsoft.Identity.Client.Desktop/), à côté de MSAL, et appeler `.WithWindowsBroker()` au lieu de `WithBroker()`.

Pour .NET 5, ciblez `net5.0-windows10.0.17763.0` (ou version ultérieure) et pas seulement `net5.0`. Votre application s’exécutera toujours sur des versions antérieures de Windows si vous ajoutez `<SupportedOSPlatformVersion>7</SupportedOSPlatformVersion>` dans csproj. MSAL utilise un navigateur lorsque un gestionnaire de comptes web n’est pas disponible.

## <a name="wam-value-proposition"></a>Proposition de valeur du gestionnaire de comptes web

L’utilisation d’un répartiteur d’authentification tel qu’un gestionnaire de comptes web présente de nombreux avantages.

- Sécurité renforcée (votre application n’a pas besoin de gérer le jeton d’actualisation puissant)
- Meilleure prise en charge de Windows Hello, de l’accès conditionnel et des clés FIDO
- Intégration avec l’affichage « E-mail et comptes » de Windows
- Meilleure authentification unique (les utilisateurs ne doivent entrer leur mot de passe qu’une seule fois)
- La plupart des correctifs de bogues et autres améliorations seront fournis avec Windows

## <a name="wam-limitations"></a>Limitations du gestionnaire de comptes web

- Les autorités B2C ne sont pas prises en charge.
- Disponible sur Win10, Win Server 2016, Win Server 2019. Sur Mac, Linux et les versions antérieures de Windows, MSAL basculera vers un navigateur.

## <a name="wam-calling-pattern"></a>Modèle d’appel de gestionnaire de comptes web

Vous pouvez utiliser le modèle suivant pour utiliser un gestionnaire de comptes web.

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

Appelez `.WithBroker(true)`. Si aucun répartiteur n’est présent (par exemple, Win 8.1, Mac ou Linux), MSAL bascule vers un navigateur. Des règles d’URI de redirection s’appliquent au navigateur.

## <a name="redirect-uri"></a>URI de redirection

Les URI de redirection du gestionnaire de comptes web n’ont pas besoin d’être configurés dans MSAL, mais doivent l’être dans l’inscription d’application.

### <a name="win32-net-framework--net-5"></a>Win32 (.NET Framework/.NET 5)

```
ms-appx-web://microsoft.aad.brokerplugin/{client_id}
```

### <a name="uwp"></a>UWP
```csharp
            // returns smth like S-1-15-2-2601115387-131721061-1180486061-1362788748-631273777-3164314714-2766189824
            string sid = WebAuthenticationBroker.GetCurrentApplicationCallbackUri().Host.ToUpper();

            // the redirect uri you need to register
            string redirectUri = $"ms-appx-web://microsoft.aad.brokerplugin/{sid}";
```

## <a name="token-cache-persistence"></a>Persistance du cache de jetons

Il est important de conserver le cache de jetons de MSAL, car MSAL doit y enregistrer des ID de compte de gestionnaire de comptes web internes. À défaut, le redémarrage de l’application signifie que l’API `GetAccounts` manquera de certains comptes. Notez que, sur UWP, MSAL sait où enregistrer le cache de jetons.

## <a name="getaccounts"></a>GetAccounts

`GetAccounts` retourne les comptes des utilisateurs qui se sont déjà connectés de manière interactive à l’application.

En outre, le gestionnaire de comptes web peut répertorier les comptes professionnels et scolaires pour l’ensemble du système d’exploitation configurés dans Windows (pour les applications Win32, pas pour les applications UWP). Pour choisir cette fonctionnalité, définissez `ListWindowsWorkAndSchoolAccounts` dans `WindowsBrokerOptions` sur **true**. Vous pouvez l’activer comme ci-dessous.

```csharp
.WithWindowsBrokerOptions(new WindowsBrokerOptions()
{
    // GetAccounts will return Work and School accounts from Windows
    ListWindowsWorkAndSchoolAccounts = true,

    // Legacy support for 1st party apps only
    MsaPassthrough = true
})
```

>[!NOTE]
> Les comptes Microsoft (à savoir, outlook.com, etc.) ne sont répertoriés ni dans Win32, ni dans UWP pour des raisons de confidentialité.

Les applications ne peuvent pas supprimer de comptes de Windows ! 

## <a name="removeasync"></a>RemoveAsync

- Supprime toutes les informations de compte du cache de jetons de MSAL. Cela inclut les informations de comptes MSA (c.-à-d. de comptes personnels) et d’autres informations de comptes copiées par MSAL dans son cache.
- Supprime les comptes d’application uniquement (pas pour l’ensemble du système d’exploitation).

>[!NOTE]
> Les applications ne peuvent pas supprimer de comptes de système d’exploitation. Seuls des utilisateurs peuvent le faire. Si un compte de système d’exploitation est transmis dans `RemoveAsync`, puis la commande `GetAccounts` appelée avec `ListWindowsWorkAndSchoolAccounts` activé, le même compte de système d’exploitation est toujours renvoyé.

## <a name="other-considerations"></a>Autres éléments à prendre en compte

- Les opérations interactives du gestionnaire de comptes web doivent être sur le thread d’interface utilisateur. MSAL lève une exception explicite quand il n’est pas sur le thread d’interface utilisateur. Cela ne s’applique PAS aux applications console.
- `WithAccount`fournit une expérience d’authentification accélérée si le compte MSAL a été obtenu à l’origine via le gestionnaire de comptes web, ou si celui-ci peut trouver un compte professionnel ou scolaire dans Windows.
- Le gestionnaire de comptes web ne peut préremplir le champ du nom d’utilisateur avec un indicateur de connexion que si un compte professionnel ou scolaire avec le même nom d’utilisateur est trouvé dans Windows.
- Si le gestionnaire de comptes web n’est pas en mesure d’offrir une expérience d’authentification accélérée, il affiche un sélecteur de compte. Les utilisateurs peuvent ajouter de nouveaux comptes.

![« Sélecteur de compte du gestionnaire de comptes web »](media/scenario-desktop-acquire-token-wam/wam-account-picker.png)

- Windows mémorise automatiquement les nouveaux comptes. Les comptes professionnels ou scolaires ont la possibilité de rejoindre l’annuaire de l’organisation ou de s’en dissocier complètement. Dans ce cas, le compte n’apparaît pas sous « E-mail et comptes ». Les comptes Microsoft sont automatiquement ajoutés à Windows. Les applications ne peuvent pas répertorier ces comptes par programme (mais uniquement via le sélecteur de compte).

## <a name="troubleshooting"></a>Résolution des problèmes

Quand une application qui utilise MSAL est exécutée en tant que processus avec élévation de privilèges, certains de ces appels au sein du gestionnaire de comptes web peuvent échouer en raison de différences de niveaux de sécurité de processus. En interne, MSAL.NET utilise des méthodes Windows natives ([COM](/windows/win32/com/the-component-object-model)) pour s’intégrer avec le gestionnaire de comptes web. À partir de la version 4.32.0, MSAL affiche un message d’erreur descriptif quand il détecte que le processus d’application est exécuté avec élévation de privilèges, et que le gestionnaire de comptes web n’a retourné aucun compte.

Une solution consiste, autant que possible, à ne pas exécuter l’application avec élévation de privilèges. Une autre solution de contournement possible consiste à appeler la méthode `WindowsNativeUtils.InitializeProcessSecurity` au démarrage de l’application. Cela a pour effet de définir la sécurité des processus utilisés par le gestionnaire de comptes web sur les mêmes niveaux. Pour un exemple, voyez cet [exemple d’application](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/devapps/WAM/NetCoreWinFormsWam/Program.cs#L18-L21). Notez cependant qu’en raison de facteurs externes tels que le comportement CLR sous-jacent, il n’est nullement garanti que cette solution de contournement réussisse. Dans ce cas, une `MsalClientException` est levée. Pour plus d’informations, consultez le problème [#2560](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/2560).

## <a name="sample"></a>Exemple

[Exemple WPF utilisant un gestionnaire de comptes web](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2)

[Exemple UWP utilisant un gestionnaire de comptes web, avec Xamarin](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/2-With-broker)

---
## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Appeler une API web à partir de l’appareil de bureau](scenario-desktop-call-api.md).
