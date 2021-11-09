---
title: Configurer l’authentification dans un exemple d’application Android à l’aide d’Azure Active Directory B2C
description: Cet article explique comment utiliser Azure Active Directory B2C pour inscrire et connecter des utilisateurs dans une application Android.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 699e548809b123589466062f1e9600edee9e7b78
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007932"
---
# <a name="configure-authentication-in-a-sample-android-app-by-using-azure-ad-b2c"></a>Configurer l'authentification dans une application Android exemple en utilisant Azure AD B2C

Cet article utilise un exemple d’application Android (Kotlin et Java) pour illustrer l’ajout de l’authentification Azure Active Directory B2C (Azure AD B2C) à vos applications mobiles.

## <a name="overview"></a>Vue d'ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0. Vous pouvez utiliser OIDC pour connecter de façon sécurisée des utilisateurs à une application. Cet exemple d’application mobile utilise la [bibliothèque d’authentification Microsoft (MSAL)](../active-directory/develop/msal-overview.md) avec le flux PKCE du code d’autorisation OIDC. MSAL est une bibliothèque fournie par Microsoft qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation dans les applications mobiles. 

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

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, inscrivez deux applications dans le répertoire d’Azure AD B2C.  

- L’inscription de l’**application mobile** permet à votre application de se connecter grâce à Azure AD B2C. Lors de l’inscription de l’application, spécifiez l’*URI de redirection*. L’URI de redirection est le point de terminaison vers lequel les utilisateurs sont redirigés par Azure AD B2C après qu’ils se sont authentifiés avec Azure AD B2C. Le processus d’inscription d’application génère un *ID d’application*, également appelé *ID client*, qui identifie de façon univoque votre application mobile (par exemple *ID d’application : 1*).

- L’inscription de **l’API web** permet à votre application d’appeler une API web protégée. Elle expose les autorisations de l’API web (étendues). Ce processus génère un *ID d’application*, qui sert d’identificateur unique de l’API web (par exemple *ID d’application : 2*). Accordez à votre application mobile (ID d’application : 1) des autorisations sur les étendues de l’API web (ID d’application : 2). 


L’architecture et l’inscription des applications sont illustrées dans les diagrammes suivants :

![Diagramme de l’application mobile avec les inscriptions et les jetons d’appel de l’API web.](./media/configure-authentication-sample-android-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Appel à une API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="the-sign-out-flow"></a>Flux de déconnexion

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prérequis

Un ordinateur qui exécute : 


- [Kit de développement Java (JDK) 8 ou ultérieur](https://openjdk.java.net/)
- [Apache Maven](https://maven.apache.org/)
- [Niveau d’API Android 16 ou version ultérieure](https://developer.android.com/studio/releases/platforms)
- [Android Studio](https://developer.android.com/studio) ou un autre éditeur de code


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
1. Sous **Nom**, entrez un nom pour l’application (par exemple, *android-app1*).
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Client public/natif (mobile et bureau)** , puis, dans la zone URL, entrez l’un des URI suivants :
    - Pour l’exemple Kotlin : `msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
    - Pour l’exemple Java : `msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
1. Sélectionnez **Inscription**.
1. Une fois l’inscription de l’application terminée, sélectionnez **Vue d’ensemble**.
1. Enregistrez l’**ID d’application (client)** que vous utiliserez ultérieurement pour configurer l’application mobile.

    ![Capture d’écran mettant en évidence l’ID d’application Android.](./media/configure-authentication-sample-android-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-24-grant-the-mobile-app-permissions-for-the-web-api"></a>Étape 2.4 : Accorder à l’application mobile des autorisations pour l’API web

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-android-mobile-app-sample"></a>Étape 3 : Obtenir l’exemple d’application mobile Android

Effectuez l'une des opérations suivantes :

- Téléchargez l’un des exemples suivants : 
   - [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/archive/refs/heads/master.zip)
   - [Java](https://github.com/Azure-Samples/ms-identity-android-java/archive/refs/heads/master.zip) 

   Extrayez l’exemple de fichier .zip dans votre dossier de travail.

- Clonez l’exemple d’application mobile Android à partir de GitHub. 

    #### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


    ```bash
    git clone https://github.com/Azure-Samples/ms-identity-android-kotlin
    ```

    #### <a name="java"></a>[Java](#tab/java)

    ```bash
    git clone https://github.com/Azure-Samples/ms-identity-android-java
    ```

    --- 


## <a name="step-4-configure-the-sample-web-api"></a>Étape 4 : Configurer l’exemple d’API web

Cet exemple acquiert un jeton d’accès avec les étendues appropriées que l’application mobile peut utiliser pour une API web. Pour appeler une API web à partir du code, procédez comme suit :

1. Utilisez une API web existante ou créez-en une nouvelle. Pour plus d’informations, consultez [Activer l’authentification dans votre propre API web à l’aide d’Azure AD B2C](enable-authentication-web-api.md).
1. Modifiez l’exemple de code pour [appeler une API web](enable-authentication-android-app.md#step-6-call-a-web-api).

## <a name="step-5-configure-the-sample-mobile-app"></a>Étape 5 : Configurer l’exemple d’application mobile

Ouvrez l’exemple de projet avec Android Studio ou un autre éditeur de code, puis ouvrez le fichier */app/src/main/res/raw/auth_config_b2c.json*. 

Le fichier de configuration *auth_config_b2c.json* contient des informations sur votre fournisseur d’identité Azure AD B2C. L’application mobile utilise ces informations pour établir une relation de confiance avec Azure AD B2C, connecter et déconnecter les utilisateurs et acquérir des jetons et les valider. 

Mettez à jour les propriétés suivantes des paramètres de l’application :

|Clé  |Valeur  |
|---------|---------|
| [client_id](../active-directory/develop/msal-client-application-configuration.md#client-id) | L’ID d’application mobile de l’[étape 2.3](#step-23-register-the-mobile-app). | 
| [redirect_uri](../active-directory/develop/msal-client-application-configuration.md#redirect-uri) | L’URI de redirection de l’application mobile de l’[étape 2.3](#step-23-register-the-mobile-app). | 
| [autorités](../active-directory/develop/msal-client-application-configuration.md#authority)| L’autorité est une URL qui indique un répertoire auprès duquel la bibliothèque d’authentification Microsoft peut demander des jetons. Utilisez le format suivant : `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`. Remplacez `<your-tenant-name>` par le [nom de votre locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Ensuite, remplacez `<your-sign-in-sign-up-policy>` par les flux d’utilisateur ou la stratégie personnalisée que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow). |
| | | 


Ouvrez la classe `B2CConfiguration` et mettez à jour les membres de classe suivants :

|Clé  |Valeur  |
|---------|---------|
| Stratégies| Liste des flux d’utilisateur ou des stratégies personnalisées que vous avez créés à l’[étape 1](#step-1-configure-your-user-flow).|
| azureAdB2CHostName| La première partie du [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C (par exemple `https://contoso.b2clogin.com`).|
| tenantName| [Nom du locataire](tenant-management.md#get-your-tenant-name) complet Azure AD B2C (par exemple `contoso.onmicrosoft.com`).|
| étendues| Étendues d’API web que vous avez créées à l’[étape 2.4](#step-24-grant-the-mobile-app-permissions-for-the-web-api).|
| | |


## <a name="step-6-run-and-test-the-mobile-app"></a>Étape 6 : Exécuter et tester l’application mobile

1. Générez et exécutez le projet.
1. En haut à gauche, sélectionnez l’icône de menu latéral (également appelée icône de menu à tiroir), comme illustré ici :
    
    ![Capture d’écran mettant en évidence l’icône de menu latéral, ou menu à tiroir.](./media/configure-authentication-sample-android-app/select-hamburger-icon.png)

1. Dans le volet gauche, sélectionnez **Mode B2C**.

    ![Capture d’écran mettant en évidence la commande « Mode B2C » dans le volet gauche.](./media/configure-authentication-sample-android-app/select-azure-ad-b2c-mode.png)

1. Sélectionnez **Exécuter le flux d’utilisateur**.

    ![Capture d’écran mettant en évidence le bouton « Exécuter le flux d’utilisateur ».](./media/configure-authentication-sample-android-app/select-policy-and-sign-in.png)

1. Inscrivez-vous ou connectez-vous avec votre compte social ou votre compte local Azure AD B2C.

1. Une fois l’authentification réussie, votre nom d’affichage apparaît dans le volet **Mode B2C**.

    ![Capture d’écran montrant une authentification réussie, avec l’utilisateur connecté et la stratégie affichée.](./media/configure-authentication-sample-android-app/access-token.png) 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :
* [Activer l’authentification dans votre propre application Android](enable-authentication-android-app.md)
* [Configurer les options d’authentification dans une application Android](enable-authentication-android-app-options.md)
* [Activer l’authentification dans votre propre API web](enable-authentication-web-api.md)
