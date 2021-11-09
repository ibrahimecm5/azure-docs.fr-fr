---
title: Configurer l’authentification dans un exemple d’application de bureau WPF à l’aide d’Azure Active Directory B2C
description: Cet article explique comment utiliser Azure Active Directory B2C pour inscrire et connecter des utilisateurs dans une application de bureau WPF.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: d10fd1586ef60bb0c216e8284fa7e6bf44804217
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007615"
---
# <a name="configure-authentication-in-a-sample-wpf-desktop-app-by-using-azure-ad-b2c"></a>Configurer l’authentification dans un exemple d’application de bureau WPF à l’aide d’Azure AD B2C

Cet article utilise un exemple d’application [de bureau Windows Presentation Foundation (WPF)](/visualstudio/designers/getting-started-with-wpf) pour illustrer l’ajout d’une authentification Azure Active Directory B2C (Azure AD B2C) à vos applications de bureau.

## <a name="overview"></a>Vue d’ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0. Vous pouvez utiliser OIDC pour connecter de façon sécurisée des utilisateurs à une application. Cet exemple d’application de bureau utilise la [bibliothèque d’authentification Microsoft (MSAL)](../active-directory/develop/msal-overview.md) avec le flux PKCE (Proof Key for Code Exchange) du code d’autorisation OIDC. La bibliothèque MSAL est une bibliothèque fournie par Microsoft qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation aux applications de bureau. 

Le flux de connexion implique les étapes suivantes :

1. Les utilisateurs ouvrent l’application et sélectionnent **Se connecter**.
1. L’application ouvre le navigateur système de l’appareil de bureau et envoie une requête d’authentification à Azure AD B2C.
1. Les utilisateurs [s’inscrivent ou se connectent](add-sign-up-and-sign-in-policy.md), [réinitialisent le mot de passe](add-password-reset-policy.md) ou se connectent à l’aide d’un [compte social](add-identity-provider.md).
1. Une fois les utilisateurs connectés, Azure AD B2C retourne un code d’autorisation à l’application.
1. L’application :
    1. Elle échange le code d’autorisation contre un jeton d’ID, un jeton d’accès et un jeton d’actualisation.
    1. Elle lit les revendications du jeton d’ID.
    1. Elle stocke les jetons dans un cache en mémoire pour une utilisation ultérieure.

### <a name="app-registration-overview"></a>Vue d’ensemble de l’inscription de l’application

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, inscrivez deux applications dans le répertoire d’Azure AD B2C.  

- L’inscription de l’**application de bureau** permet à votre application de se connecter grâce à Azure AD B2C. Pendant l’inscription de l’application, spécifiez l’*URI de redirection*. L’URI de redirection est le point de terminaison vers lequel les utilisateurs sont redirigés par Azure AD B2C après qu’ils se sont authentifiés avec Azure AD B2C. Le processus d’inscription d’application génère un *ID d’application*, également appelé *ID client*, qui identifie de façon univoque votre application de bureau (par exemple *ID d’application : 1*).

- L’inscription de **l’API web** permet à votre application d’appeler une API web protégée. Elle expose les autorisations de l’API web (étendues). Le processus d’inscription de l’application génère un *ID d’application*, qui identifie de façon univoque votre API web (par exemple *ID d’application : 2*). Accordez à votre application de bureau (ID d’application : 1) des autorisations sur les étendues de l’API web (ID d’application : 2). 

L’architecture et l’inscription de l’application sont illustrées dans les diagrammes suivants :

![Diagramme de l’application de bureau avec une API web, les inscriptions et les jetons.](./media/configure-authentication-sample-wpf-desktop-app/desktop-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Appel à une API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="the-sign-out-flow"></a>Flux de déconnexion

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prérequis

Un ordinateur qui exécute [Visual Studio 2019](https://www.visualstudio.com/downloads/) avec un développement de bureau .NET.

## <a name="step-1-configure-your-user-flow"></a>Étape 1 : Configurer votre flux d’utilisateurs

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-applications"></a>Étape 2 : Inscrire vos applications

Créez l’inscription d’application de l’application de bureau et de l’API web, puis spécifier les étendues de votre API web.

### <a name="step-21-register-the-web-api-app"></a>Étape 2.1 : Inscrire l’application API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>Étape 2.2 : Configurer les étendues de l’application API web

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-desktop-app"></a>Étape 2.3 : Inscrire l’application de bureau

Pour créer l’inscription de l’application de bureau, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Sous **Nom**, entrez un nom pour l’application (par exemple, *desktop-app1*).
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Client public/natif (bureau et bureau)** puis, dans la zone URL, entrez `https://your-tenant-name.b2clogin.com/oauth2/nativeclient`. Remplacez `your-tenant-name` par votre [nom de locataire](tenant-management.md#get-your-tenant-name). Pour d’autres options, consultez [Configurer l’URI de redirection](enable-authentication-wpf-desktop-app-options.md#configure-the-redirect-uri).
1. Sélectionnez **Inscription**.
1. Une fois l’inscription de l’application terminée, sélectionnez **Vue d’ensemble**.
1. Enregistrez l’**ID d’application (client)** que vous utiliserez ultérieurement pour configurer l’application de bureau.
    ![Capture d’écran mettant en évidence l’ID de l’application de bureau.](./media/configure-authentication-sample-wpf-desktop-app/get-azure-ad-b2c-app-id.png)  

### <a name="step-24-grant-the-desktop-app-permissions-for-the-web-api"></a>Étape 2.4 : Accorder à l’application de bureau les autorisations pour l’API web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>Étape 3 : Configurer l’exemple d’API web

Cet exemple montre comment acquérir un jeton d’accès avec les étendues appropriées que l’application de bureau peut utiliser pour une API web.  Pour appeler une API web à partir du code, procédez comme suit :

1. Utilisez une API web existante ou créez-en une nouvelle. Pour plus d’informations, consultez [Activer l’authentification dans votre propre API web à l’aide d’Azure AD B2C](enable-authentication-web-api.md).
1. Après avoir configuré l’API web, copiez l’URI du point de terminaison de l’API web. Vous allez utiliser le point de terminaison de l’API web dans les étapes suivantes.

> [!TIP]
> Si vous n’avez pas d’API web, vous pouvez quand même exécuter cet exemple. Dans ce cas, l’application renvoie le jeton d’accès, mais ne pourra pas appeler l’API web. 

## <a name="step-4-get-the-wpf-desktop-app-sample"></a>Étape 4 : Obtenir l’exemple d’application de bureau WPF

1. [Téléchargez le fichier .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git) ou clonez l’exemple d’application web à partir du [référentiel GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git). 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ``` 

1. Ouvrez la solution **active-directory-b2c-wpf** (le fichier *active-directory-b2c-wpf.sln*) dans Visual Studio.



## <a name="step-5-configure-the-sample-desktop-app"></a>Étape 5 : Configurer l’exemple d’application de bureau

Dans le projet **active-directory-b2c-wpf**, ouvrez le fichier *App.xaml.cs*. Les membres de la classe `App.xaml.cs` contiennent des informations sur votre fournisseur d’identité Azure AD B2C. L’application de bureau utilise ces informations pour établir une relation de confiance avec Azure AD B2C, connecter et déconnecter les utilisateurs et acquérir des jetons et les valider. 

Mettez à jour les membres de classe suivants :

|Clé  |Valeur  |
|---------|---------|
|`TenantName`|La première partie du [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C (par exemple `contoso.b2clogin.com`).|
|`ClientId`|L’ID d’application de bureau de l’[étape 2.3](#step-23-register-the-desktop-app).|
|`PolicySignUpSignIn`| Le flux d’utilisateur ou la stratégie personnalisée d’inscription ou de connexion que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|
|`PolicyEditProfile`|Le flux d’utilisateur ou la stratégie personnalisée du profil de modification que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|
|`ApiEndpoint`| (Facultatif) Le point de terminaison de l’API web que vous avez créé à l’[étape 3](#step-3-configure-the-sample-web-api) (par exemple `https://contoso.azurewebsites.net/hello`).|
| `ApiScopes` | Étendues d’API web que vous avez créées à l’[étape 2.4](#step-24-grant-the-desktop-app-permissions-for-the-web-api).|
| | | 

Votre fichier *App.xaml.cs* final doit ressembler au code C# suivant :

```csharp
public partial class App : Application
{

private static readonly string TenantName = "contoso";
private static readonly string Tenant = $"{TenantName}.onmicrosoft.com";
private static readonly string AzureAdB2CHostname = $"{TenantName}.b2clogin.com";
private static readonly string ClientId = "<web-api-app-application-id>";
private static readonly string RedirectUri = $"https://{TenantName}.b2clogin.com/oauth2/nativeclient";

public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string[] ApiScopes = { $"https://{Tenant}//api/tasks.read" };
public static string ApiEndpoint = "https://contoso.azurewebsites.net/hello";
```

## <a name="step-6-run-and-test-the-desktop-app"></a>Étape 6 : Exécuter et tester l’application de bureau

1. [Restaurez les packages NuGet](/nuget/consume-packages/package-restore).
1. Sélectionnez F5 pour générer et exécuter l’exemple.
1. Sélectionnez **Se connecter**, puis inscrivez-vous ou connectez-vous avec votre compte Azure AD B2C local ou avec un compte social.

    ![Capture d’écran mettant en évidence comment lancer le flux de connexion.](./media/configure-authentication-sample-wpf-desktop-app/sign-in.png)

1. Après une inscription ou une connexion réussie, les détails du jeton s’affichent dans le volet inférieur de l’application WPF.

    ![Capture d’écran mettant en évidence le jeton d’accès et l’ID utilisateur d’Azure AD B2C.](./media/configure-authentication-sample-wpf-desktop-app/post-signin.png) 

1. Sélectionnez **Appeler l’API** pour appeler votre API web.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer les options d’authentification dans une application du bureau WPF à l’aide d’Azure AD B2C](enable-authentication-wpf-desktop-app-options.md).
