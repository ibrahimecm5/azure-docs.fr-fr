---
title: Configurer l’authentification dans un exemple d’application iOS Swift en utilisant Azure Active Directory B2C
description: Cet article explique comment utiliser Azure Active Directory B2C pour inscrire et connecter des utilisateurs dans une application iOS Swift.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 07c41b55d494167bcf267d4af1bdd154c6958ee9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007859"
---
# <a name="configure-authentication-in-a-sample-ios-swift-app-by-using-azure-ad-b2c"></a>Configuration de l’authentification dans un exemple d’application iOS Swift avec Azure AD B2C

Cet article utilise un exemple d’application [iOS Swift](https://developer.apple.com/swift/) pour illustrer l’ajout d’une authentification Azure Active Directory B2C (Azure AD B2C) à vos applications mobiles.

## <a name="overview"></a>Vue d'ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0. Vous pouvez utiliser OIDC pour connecter de façon sécurisée des utilisateurs à une application. Cet exemple d’application mobile utilise la [bibliothèque d’authentification Microsoft (MSAL)](../active-directory/develop/msal-overview.md) avec le flux PKCE (Proof Key for Code Exchange) de code d’autorisation OIDC. MSAL est une bibliothèque fournie par Microsoft qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation dans les applications mobiles. 

Le flux de connexion implique les étapes suivantes :

1. Les utilisateurs ouvrent l’application et sélectionnent **Se connecter**.
1. L’application ouvre le navigateur système de l’appareil mobile et lance une demande d’authentification auprès d’Azure AD B2C.
1. Les utilisateurs [s’inscrivent ou se connectent](add-sign-up-and-sign-in-policy.md), [réinitialisent le mot de passe](add-password-reset-policy.md) ou se connectent à l’aide d’un [compte social](add-identity-provider.md).
1. Une fois les utilisateurs connectés, Azure AD B2C retourne un code d’autorisation à l’application.
1. L’application :
    1. Elle échange le code d’autorisation contre un jeton d’ID, un jeton d’accès et un jeton d’actualisation.
    1. Elle lit les revendications du jeton d’ID.
    1. Elle stocke les jetons dans un cache en mémoire pour une utilisation ultérieure.

### <a name="app-registration-overview"></a>Vue d’ensemble de l’inscription de l’application

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, inscrivez deux applications dans l’annuaire Azure AD B2C :  

- L’inscription de l’**application mobile** permet à votre application de se connecter grâce à Azure AD B2C. Lors de l’inscription de l’application, spécifiez l’*URI de redirection*. L’URI de redirection est le point de terminaison vers lequel les utilisateurs sont redirigés par Azure AD B2C après qu’ils se sont authentifiés avec Azure AD B2C. Le processus d’inscription d’application génère un *ID d’application*, également appelé *ID client*, qui identifie de façon univoque votre application mobile (par exemple *ID d’application : 1*).

- L’inscription de **l’API web** permet à votre application d’appeler une API web protégée. Elle expose les autorisations de l’API web (étendues). Le processus d’inscription de l’application génère un *ID d’application*, qui identifie de façon univoque votre API web (par exemple *ID d’application : 2*). Accordez à votre application mobile (ID d’application : 1) des autorisations sur les étendues de l’API web (ID d’application : 2). 


L’architecture et l’inscription de l’application sont illustrées dans les diagrammes suivants :

![Diagramme de l’application mobile avec les inscriptions et les jetons d’appel de l’API web.](./media/configure-authentication-sample-ios-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Appel à une API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="the-sign-out-flow"></a>Flux de déconnexion

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prérequis

Un ordinateur qui exécute : 

- [Xcode 13 ou ultérieur](https://developer.apple.com/xcode/).
- Gestionnaire de dépendances [CocoaPods](https://cocoapods.org/) pour les projets Cocoa Swift et Objective-C.


## <a name="step-1-configure-your-user-flow"></a>Étape 1 : Configurer votre flux d’utilisateurs

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>Étape 2 : Inscrire des applications mobiles

Créez l’inscription de l’application mobile et de l’application d’API web, puis spécifiez les étendues de votre API web.

### <a name="step-21-register-the-web-api-app"></a>Étape 2.1 : Inscrire l’application API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>Étape 2.2 : Configurer les étendues de l’application API web

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-mobile-app"></a>Étape 2.3 : Inscrire l’application mobile

Pour créer l’inscription de l’application mobile, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Sous **Nom**, entrez un nom pour l’application (par exemple, *iOs-app1*).
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Client public/natif (mobile et bureau)** puis, dans la zone URL, entrez `msauth.com.microsoft.identitysample.MSALiOS://auth`.
1. Sélectionnez **Inscription**.
1. Une fois l’inscription de l’application terminée, sélectionnez **Vue d’ensemble**.
1. Enregistrez l’**ID d’application (client)** que vous utiliserez ultérieurement pour configurer l’application mobile.
    ![Capture d’écran mettant en évidence l’ID d’application mobile.](./media/configure-authentication-sample-ios-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-24-grant-the-mobile-app-permissions-for-the-web-api"></a>Étape 2.4 : Accorder à l’application mobile des autorisations pour l’API web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>Étape 3 : Configurer l’exemple d’API web

Cet exemple acquiert un jeton d’accès avec les étendues appropriées que l’application mobile peut utiliser pour une API web. Pour appeler une API web à partir du code, procédez comme suit :

1. Utilisez une API web existante ou créez-en une nouvelle. Pour plus d’informations, consultez [Activer l’authentification dans votre propre API web à l’aide d’Azure AD B2C](enable-authentication-web-api.md).
1. Modifiez l’exemple de code pour [appeler une API web](enable-authentication-iOs-app.md#step-63-call-a-web-api).
1. Après avoir configuré l’API web, copiez l’URI du point de terminaison de l’API web. Vous allez utiliser le point de terminaison de l’API web dans les étapes suivantes.

> [!TIP]
> Si vous n’avez pas d’API web, vous pouvez quand même exécuter cet exemple. Dans ce cas, l’application renvoie le jeton d’accès, mais ne pourra pas appeler l’API web. 

## <a name="step-4-get-the-ios-mobile-app-sample"></a>Étape 4 : Obtenir l’exemple d’application mobile iOS

1. [Téléchargez le fichier zip](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/archive/refs/heads/vNext.zip) ou clonez l’exemple d’application web à partir du [dépôt GitHub](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal). 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/tree/vNext.git
    ``` 

1. Utilisez [CocoaPods](https://cocoapods.org/) pour installer la bibliothèque MSAL. Dans une fenêtre de terminal, accédez au dossier racine du projet. Ce dossier contient le fichier *podfile* . Exécutez la commande suivante :

    ```bash
    pod install
    ```

1. Ouvrez l’espace de travail `MSALiOS.xcworkspace` avec Xcode.



## <a name="step-5-configure-the-sample-mobile-app"></a>Étape 5 : Configurer l’exemple d’application mobile

Ouvrez le fichier *ViewController.swift*. Les membres de la classe `ViewController` contiennent des informations sur votre fournisseur d’identité Azure AD B2C. L’application mobile utilise ces informations pour établir une relation de confiance avec Azure AD B2C, connecter et déconnecter les utilisateurs et acquérir des jetons et les valider. 

Mettez à jour les membres de classe suivants :

|Clé  |Valeur  |
|---------|---------|
|kTenantName| [Nom du locataire](tenant-management.md#get-your-tenant-name) complet Azure AD B2C (par exemple `contoso.onmicrosoft.com`).|
|kAuthorityHostName|La première partie du [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C (par exemple `contoso.b2clogin.com`).|
|kClientID|L’ID d’application mobile de l’[étape 2.3](#step-23-register-the-mobile-app).|
|kRedirectUri|L’URI de redirection de l’application mobile de l’[étape 2.3](#step-23-register-the-mobile-app), `msauth.com.microsoft.identitysample.MSALiOS://auth`.|
|kSignupOrSigninPolicy| Le flux d’utilisateur ou la stratégie personnalisée d’inscription ou de connexion que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|
|kEditProfilePolicy|Le flux d’utilisateur ou la stratégie personnalisée du profil de modification que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|
|kGraphURI| (Facultatif) Le point de terminaison de l’API web que vous avez créé à l’[étape 3](#step-3-configure-the-sample-web-api) (par exemple `https://contoso.azurewebsites.net/hello`).|
| kScopes | Étendues d’API web que vous avez créées à l’[étape 2.4](#step-24-grant-the-mobile-app-permissions-for-the-web-api).|
| | | 



## <a name="step-6-run-and-test-the-mobile-app"></a>Étape 6 : Exécuter et tester l’application mobile

1. Générez et exécutez le projet avec un [simulateur d'appareil iOS connecté](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device).

1. Sélectionnez **Se connecter**, puis inscrivez-vous ou connectez-vous avec votre compte Azure AD B2C local ou avec un compte social.

    ![Capture d’écran mettant en évidence comment lancer le flux de connexion.](./media/configure-authentication-sample-ios-app/sign-in.png)

1. Une fois l’authentification réussie, votre nom d’affichage s’affiche dans la barre de navigation.

    ![Capture d’écran mettant en évidence le jeton d’accès et l’ID utilisateur d’Azure AD B2C.](./media/configure-authentication-sample-ios-app/post-sign-in.png) 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :
* [Activer l’authentification dans votre application iOS en utilisant Azure AD B2C](enable-authentication-ios-app.md)
* [Configurer les options d’authentification dans une application iOS en utilisant Azure AD B2C](enable-authentication-ios-app-options.md)
* [Activer l’authentification dans votre propre API web avec Azure AD B2C](enable-authentication-web-api.md)
