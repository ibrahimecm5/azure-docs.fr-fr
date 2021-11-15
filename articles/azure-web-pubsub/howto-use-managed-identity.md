---
title: Identités gérées dans le service Azure Web PubSub
description: Découvrez comment fonctionnent les identités managées dans le service Azure Web PubSub, et comment utiliser une identité managée dans des scénarios serverless.
author: chenyl
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 11/08/2021
ms.author: chenyl
ms.openlocfilehash: 0601308270e86031b570eaeb86a78a42a7e69c28
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997492"
---
# <a name="managed-identities-for-azure-web-pubsub-service"></a>Identités managées pour le service Azure Web PubSub

Cet article explique comment créer une identité managée pour le service Azure Web PubSub et comment l’utiliser.

> [!Important] 
> Le service Azure Web PubSub ne peut prendre en charge qu’une seule identité managée. Cela signifie que vous pouvez ajouter soit une identité affectée par le système, soit une identité affectée par l’utilisateur. 

## <a name="add-a-system-assigned-identity"></a>Ajouter une identité affectée par le système

Pour configurer une identité managée dans le portail Azure, vous allez d’abord créer une instance de service Azure Web PubSub, puis activer la fonctionnalité.

1. Créez une instance de service Azure Web PubSub dans le portail comme vous le faites habituellement. Accédez-y dans le portail.

2. Sélectionnez **Identité**.

4. Dans l’onglet **Attribuée par le système**, définissez **État** sur **Activé**. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/howto-use-managed-identity/system-identity-portal.png" alt-text="Ajouter une identité attribuée par le système dans le portail":::

## <a name="add-a-user-assigned-identity"></a>Ajouter une identité attribuée par l’utilisateur

La création d’une instance Azure Web PubSub avec une identité attribuée par l’utilisateur nécessite la création de l’identité, puis l’ajout de son identificateur de ressource à votre service.

1. Créez une ressource d’identité managée attribuée par l’utilisateur en suivant [ces instructions](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Créez une instance de service Azure Web PubSub dans le portail comme vous le faites habituellement. Accédez-y dans le portail.

3. Sélectionnez **Identité**.

4. Dans l’onglet **Attribuée par l’utilisateur**, sélectionnez **Ajouter**.

5. Recherchez l’identité que vous avez créée précédemment et sélectionnez-la. Sélectionnez **Ajouter**.

    :::image type="content" source="media/howto-use-managed-identity/user-identity-portal.png" alt-text="Ajouter une identité attribuée par l’utilisateur dans le portail":::

## <a name="use-a-managed-identity-in-client-events-scenarios"></a>Utiliser une identité managée dans les scénarios d’événements clients

Azure Web PubSub étant un service complètement managé, vous ne pouvez pas utiliser une identité managée pour obtenir des jetons manuellement. Au lieu de cela, quand le service Azure Web PubSub envoie des événements au gestionnaire d’événements, il utilise l’identité managée pour obtenir un jeton d’accès. Le service définit ensuite le jeton d’accès dans l’en-tête `Authorization` de la requête http.

### <a name="enable-managed-identity-authentication-in-event-handler-settings"></a>Activer l’authentification d’identité managée dans les paramètres du gestionnaire d’événements

1. Ajoutez une identité affectée par le système ou une identité affectée par l’utilisateur.

2. Accédez à la règle et passez à l’**Authentification**.

    :::image type="content" source="media/howto-use-managed-identity/msi-settings.png" alt-text="msi-setting":::

3. Sélectionnez l’application. L’ID d’application devient la revendication `aud` dans le jeton d’accès obtenu, qui peut être utilisée pour une validation dans votre gestionnaire d’événements. Vous pouvez choisir l’une des options suivantes :
    - Utilisez l’application AAD par défaut.
    - Opérez une sélection parmi les applications AAD existantes. L’ID de l’application que vous choisissez sera utilisé.
    - Spécifier une application AAD. La valeur doit être [ID de ressource d’un service Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Si vous validez un jeton d’accès par vous-même dans votre service, vous pouvez choisir un des formats de ressources. Si vous utilisez le Contrôle d'accès en fonction du rôle Azure (Azure RBAC) pour un plan de données, vous devez utiliser la ressource demandée par le fournisseur de services.

### <a name="validate-access-tokens"></a>Validation des jetons d’accès

Le jeton dans l’en-tête `Authorization` est un [jeton d’accès à la plateforme d’identité Microsoft](../active-directory/develop/access-tokens.md#validating-tokens).

Afin de valider les jetons d’accès, votre application doit également valider l’audience et les jetons de signature. Ces éléments doivent être validés d’après les valeurs du document de découverte OpenID. Par exemple, consultez la [version indépendante du client du document](https://login.microsoftonline.com/common/.well-known/openid-configuration).

L’intergiciel Azure Active Directory (Azure AD) intègre des fonctionnalités permettant de valider les jetons d’accès. Vous pouvez parcourir nos [exemples](../active-directory/develop/sample-v2-code.md) pour en trouver un dans le langage de votre choix.

Nous fournissons des bibliothèques et des exemples de code qui montrent comment gérer la validation des jetons. Il existe également plusieurs bibliothèques de partenaire open source disponibles pour la validation de JSON Web Token (JWT). Il existe au moins une option pour la plupart des plateformes et des langues. Pour plus d’informations sur les exemples de code et les bibliothèques d’authentification d’Azure AD, consultez [Bibliothèques d’authentification de plateforme d’identité Microsoft](../active-directory/develop/reference-v2-libraries.md).

En particulier, si le gestionnaire d’événements héberge dans Azure Function ou Web Apps, un moyen simple consiste à [Configurer la connexion à Azure AD](../app-service/configure-authentication-provider-aad.md).

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Utiliser une identité managée pour une référence Azure Key Vault

Le service Web PubSub peut accéder au Key Vault pour obtenir un secret en utilisant l’identité managée.

1. Ajoutez une identité affectée par le système ou une identité affectée par l’utilisateur pour le service Azure Web PubSub.

2. Accordez une autorisation de lecture de secret pour l’identité managée dans les stratégies d’accès du Key Vault. Consultez [Attribuer une stratégie d’accès Key Vault à l’aide du portail Azure](../key-vault/general/assign-access-policy-portal.md)

Actuellement, cette fonctionnalité peut être utilisée dans les scénarios suivants :

- Utilisez la syntaxe `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` pour obtenir des secrets du Key Vault dans le paramètre de modèle d’URL du gestionnaire d’événements.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer une application de conversation serverless avec Azure Functions et le service Azure Web PubSub](quickstart-serverless.md)
