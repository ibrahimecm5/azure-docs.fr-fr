---
title: Activer les options d’application mobile Android avec Azure Active Directory B2C
description: Cet article présente plusieurs façons d'activer les options des applications mobiles Android en utilisant Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/11/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 8631c5c0852f915596e3b51c76054301eca64c02
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289189"
---
# <a name="configure-authentication-options-in-an-android-app-by-using-azure-ad-b2c"></a>Configurer les options d'authentification dans une application Android en utilisant Azure AD B2C 

Cet article décrit les méthodes permettant de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application Android. 

Avant de commencer, familiarisez-vous avec les articles suivants : 
* [Configurer l'authentification dans une application Android exemple en utilisant Azure AD B2C](configure-authentication-sample-android-app.md)
* [Activer l’authentification dans votre propre application Android avec Azure AD B2C](enable-authentication-android-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Pour utiliser un domaine personnalisé et l’ID de votre locataire dans l’URL d’authentification, suivez les instructions fournies dans [Activer les domaines personnalisés](custom-domain.md). Recherchez votre objet de configuration Microsoft Authentication Library (MSAL), puis mettez à jour les *autorités* avec votre nom de domaine personnalisé et votre ID de locataire.


#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

Le code Kotlin suivant montre l’objet de configuration MSAL avant modification :

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```

Le code Kotlin suivant montre l’objet de configuration MSAL après modification :

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```


#### <a name="java"></a>[Java](#tab/java)

Le code Kotlin suivant montre l’objet de configuration MSAL avant modification :

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```
Le code Kotlin suivant montre l’objet de configuration MSAL après modification :

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si vous utilisez une stratégie personnalisée, ajoutez la revendication d’entrée nécessaire, comme décrit dans [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 
1. Recherchez votre objet de configuration MSAL, puis ajoutez la méthode `withLoginHint()` avec l'indice de connexion.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withLoginHint("bob@contoso.com") 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withLoginHint("bob@contoso.com")  
    // More settings here
    .build();

b2cApp.acquireToken(parameters);

```

--- 

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez à la liste le paramètre `domain_hint` avec le nom de domaine correspondant (par exemple `facebook.com`).
1. Passez la liste des paramètres de requête supplémentaires dans la méthode `withAuthorizationQueryStringParameters` de l’objet de configuration MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("domain_hint", "facebook.com"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("domain_hint", "facebook.com"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configurez la personnalisation de la langue](language-customization.md).
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez à la liste le paramètre `ui_locales` avec le code langue correspondant (par exemple `en-us`).
1. Passez la liste des paramètres de requête supplémentaires dans la méthode `withAuthorizationQueryStringParameters` de l’objet de configuration MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("ui_locales", "en-us"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("ui_locales", "en-us"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configurez l’élément [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez le paramètre de chaîne de requête personnalisé, par exemple `campaignId`. Définir la valeur du paramètre (par exemple, `germany-promotion`).
1. Passez la liste des paramètres de requête supplémentaires dans la méthode `withAuthorizationQueryStringParameters` de l’objet de configuration MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("campaignId", "germany-promotion"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("campaignId", "germany-promotion"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Dans votre stratégie personnalisée, définissez un [indicateur de jeton d’ID de profil technique](id-token-hint.md).
1. Dans votre code, générez ou obtenez un jeton d'identification, puis définissez le jeton dans une variable (par exemple, `idToken`). 
1. Créez un objet de liste ou utilisez un objet existant afin d’y stocker des paramètres de requête supplémentaires.
1. Ajoutez le paramètre `id_token_hint` avec la variable correspondante qui stocke le jeton d’ID.
1. Passez la liste des paramètres de requête supplémentaires dans la méthode `withAuthorizationQueryStringParameters` de l’objet de configuration MSAL.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("id_token_hint", idToken))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("id_token_hint", idToken));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

## <a name="embedded-web-view-experience"></a>Expérience de vue web incorporée

Des navigateurs web sont nécessaires pour l’authentification interactive. Par défaut, la bibliothèque MSAL utilise la vue web du système. Pendant la connexion, la bibliothèque MSAL affiche la vue web du système Android avec l’interface utilisateur Azure AD B2C.  

Pour plus d’informations, consultez l’article [Activer l’authentification unique inter-application sur Android avec MSAL](../active-directory/develop/msal-android-single-sign-on.md#sso-through-system-browser).

En fonction de vos besoins, vous pouvez utiliser la vue web incorporée. Il existe des différences de comportement au niveau visuel et de l’authentification unique entre la vue web incorporée et la vue web du système dans MSAL.

![Capture d’écran illustrant les différences entre l’expérience de vue web du système et l’expérience de vue web incorporée.](./media/enable-authentication-android-app-options/system-web-browser-vs-embedded-view.png)

> [!IMPORTANT]
> Nous vous recommandons d’utiliser la plateforme par défaut, qui correspond généralement au navigateur du système. Le navigateur du système mémorise mieux les utilisateurs déjà connectés. Certains fournisseurs d’identité, tels que Google, ne prennent pas en charge une expérience de vue incorporée.

Pour modifier ce comportement, ouvrez le fichier *app/src/main/res/raw/auth_config_b2c.json*. Ajoutez ensuite l’attribut `authorization_user_agent` avec la valeur `WEBVIEW`. L’exemple suivant montre comment modifier le type de vue web pour le convertir en vue incorporée :

```json
{
  "authorization_user_agent": "WEBVIEW" 
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la configuration d’Android, consultez [MSAL pour les options de configuration Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki).
