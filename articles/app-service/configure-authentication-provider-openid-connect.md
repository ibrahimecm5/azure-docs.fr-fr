---
title: Configurer un fournisseur OpenID Connect (préversion)
description: Découvrez comment configurer un fournisseur OpenID Connect en tant que fournisseur d’identité pour votre application App Service ou Azure Functions.
ms.topic: article
ms.date: 10/20/2021
ms.reviewer: mahender
ms.openlocfilehash: 0536d28ec91ada939a1ee0b3b7ecf6bf5e621a7a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236497"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Configurer votre application App Service ou Azure Functions pour la connexion à l’aide d’un fournisseur OpenID Connect (préversion)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cet article vous montre comment configurer Azure App Service ou Azure Functions pour utiliser un fournisseur d’authentification personnalisé qui respecte la [spécification OpenID Connect](https://openid.net/connect/). OpenID Connect (OIDC) est une norme de l’industrie utilisée par de nombreux fournisseurs d’identité. Vous n’avez pas besoin de comprendre les détails de la spécification pour configurer votre application de manière à utiliser un fournisseur d’identité conforme.

Vous pouvez configurer votre application pour qu’elle utilise un ou plusieurs fournisseurs OIDC. Chacun doit recevoir un nom alphanumérique unique dans la configuration et seul l’un d’entre eux peut servir de cible de redirection par défaut.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Inscrire votre application auprès du fournisseur d’identité

Votre fournisseur vous demandera d’inscrire les détails de votre application. L’une de ces étapes implique la spécification d’un URI de redirection. Cet URI de redirection est au format `<app-url>/.auth/login/<provider-name>/callback`. Chaque fournisseur d’identité doit fournir des instructions supplémentaires sur la façon d’effectuer ces étapes.

> [!NOTE]
> Certains fournisseurs peuvent exiger des étapes supplémentaires pour leur configuration et l’utilisation des valeurs qu’ils fournissent. Par exemple, Apple fournit une clé privée qui n’est pas elle-même utilisée comme secret client OIDC. Vous devez plutôt l’utiliser pour créer un jeton JWT qui est traité comme le secret que vous fournissez dans la configuration de votre application (voir la section « Création du secret client » de la [documentation Connexion avec Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens))
>

Vous devrez collecter un **ID client** et un **secret client** pour votre application.

> [!IMPORTANT]
> La clé secrète client est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.
>

En outre, vous aurez besoin des métadonnées OpenID Connect pour le fournisseur. Celles-ci sont souvent exposées via un [document de métadonnées de configuration](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), qui est l’URL d’émetteur du fournisseur avec le suffixe `/.well-known/openid-configuration`. Notez cette URL de configuration.

Si vous ne pouvez pas utiliser un document de métadonnées de configuration, vous devrez collecter les valeurs suivantes séparément :

- L’URL d’émetteur (parfois indiquée comme `issuer`)
- Le [point de terminaison d’autorisation OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-3.1) (parfois indiqué comme `authorization_endpoint`)
- Le [point de terminaison de jeton OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-3.2) (parfois indiqué comme `token_endpoint`)
- L’URL du document [Jeu de clés web OAuth 2.0 JSON](https://tools.ietf.org/html/rfc8414#section-2) (parfois indiqué comme `jwks_uri`)

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Ajouter des informations sur le fournisseur à votre application

1. Connectez-vous au [portail Azure] et accédez à votre application.
1. Sélectionnez **Authentification** dans le menu de gauche. Cliquez sur **Add Identity Provider** (Ajouter un fournisseur d’identité).
1. Sélectionnez **OpenID Connect** dans la liste déroulante des fournisseurs d’identité.
1. Indiquez le nom alphanumérique unique sélectionné précédemment dans **Nom du fournisseur OpenID**.
1. Si vous avez l’URL du **document de métadonnées** du fournisseur d’identité, indiquez cette valeur dans **URL des métadonnées**. Sinon, sélectionnez l’option **Fournir les points de terminaison séparément** et placez chaque URL obtenue à partir du fournisseur d’identité dans le champ approprié.
1. Indiquez l’**ID client** et le **Secret client** collectés précédemment dans les champs appropriés.
1. Spécifiez un nom de paramètre d’application pour votre secret client. Votre secret client est stocké sous forme de paramètre d’application, ce qui garantit qu’il est stocké de manière sécurisée. Vous pouvez mettre à jour ce paramètre ultérieurement pour utiliser des [références Key Vault](./app-service-key-vault-references.md) si vous souhaitez gérer le secret dans Azure Key Vault.
1. Appuyez sur le bouton **Ajouter** pour finir la configuration du fournisseur d’identité. 

## <a name="next-steps"></a><a name="related-content"> </a>Étapes suivantes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
