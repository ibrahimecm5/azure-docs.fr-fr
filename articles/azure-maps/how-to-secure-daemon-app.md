---
title: Guide pratique pour sécuriser une application démon dans Microsoft Azure Maps
titleSuffix: Azure Maps
description: Cet article explique comment héberger des applications démon, telles que des processus en arrière-plan, des minuteurs et des travaux dans un environnement approuvé et sécurisé dans Microsoft Azure Maps.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
custom.ms: subject-rbac-steps
ms.openlocfilehash: e64645f7bdcbfb40cbee0fd29d1df2464a5d4f3e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450853"
---
# <a name="secure-a-daemon-application"></a>Sécuriser une application démon

Cet article explique comment héberger des applications démon dans un environnement approuvé et sécurisé dans Microsoft Azure Maps.

Voici des exemples d’applications démon :

- Travail web Azure
- Application Azure Function
- Service Windows
- Service en arrière-plan opérationnel et fiable

## <a name="view-azure-maps-authentication-details"></a>Afficher les détails de l’authentification Azure Maps

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

>[!IMPORTANT]
>Pour les applications de production, nous vous recommandons d’implémenter Azure AD et le contrôle d’accès en fonction du rôle Azure (Azure RBAC). Pour obtenir une vue d’ensemble des concepts Azure AD, consultez [Authentification avec Azure Maps](azure-maps-authentication.md).

## <a name="scenario-shared-key-authentication-with-azure-key-vault"></a>Scénario : Authentification par clé partagée avec Azure Key Vault

Les applications qui utilisent l’authentification par clé partagée doivent stocker les clés dans un magasin sécurisé. Ce scénario explique comment stocker en lieu sûr votre clé d’application en tant que secret dans Azure Key Vault. Au lieu de stocker la clé partagée dans la configuration de l’application, l’application peut récupérer la clé partagée sous la forme d’un secret Azure Key Vault. Pour simplifier la regénération des clés, nous recommandons de faire en sorte que les applications utilisent une seule clé à la fois. Les applications peuvent ensuite regénérer la clé inutilisée et déployer la clé regénérée dans Azure Key Vault tout en conservant les connexions actuelles avec une seule clé. Pour comprendre comment configurer Azure Key Vault, consultez le [Guide du développeur Azure Key Vault](../key-vault/general/developers-guide.md).

>[!IMPORTANT]
>Ce scénario accède indirectement à Azure Active Directory via Azure Key Vault. Toutefois, nous vous recommandons d’utiliser l’authentification Azure AD directement. L’utilisation directe d’Azure AD évite la complexité et les exigences opérationnelles supplémentaires liées à l’utilisation de l’authentification par clé partagée et à la configuration de Key Vault.

Les étapes suivantes décrivent ce processus :

1. [Créez un coffre de clés Azure](../key-vault/general/quick-create-portal.md).
2. Créez un [principal de service Azure AD](../active-directory/fundamentals/service-accounts-principal.md) en créant une inscription d’application ou une identité managée. Le principal créé est chargé d’accéder à Azure Key Vault.
3. Accordez au principal du service l’autorisation d’accès `get` aux secrets de clé Azure. Pour obtenir des détails sur la façon de définir des autorisations, consultez [Attribuer une stratégie d’accès Key Vault à l’aide du portail Azure](../key-vault/general/assign-access-policy-portal.md).
4. Attribuez-vous temporairement (en tant que développeur) l’autorisation d’accès `set` aux secrets.
5. Définissez la clé partagée dans les secrets Key Vault et référencez l’ID de secret en tant que configuration pour l’application démon.
6. Supprimez votre autorisation `set` sur les secrets.
7. Pour récupérer le secret de clé partagée à partir d’Azure Key Vault, implémentez l’authentification Azure Active Directory dans l’application démon.
8. Créez une demande d’API REST Azure Maps avec la clé partagée.
À présent, l’application démon peut récupérer la clé partagée à partir du coffre Key Vault.

> [!TIP]
> Si l’application est hébergée dans l’environnement Azure, nous vous recommandons d’utiliser une identité managée pour réduire le coût et la complexité de la gestion d’un secret pour l’authentification. Pour savoir comment configurer une identité managée, consultez [Tutoriel : Utiliser une identité managée pour connecter Key Vault à une application web Azure dans .NET](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).

## <a name="scenario-azure-ad-role-based-access-control"></a>Scénario : Contrôle d’accès en fonction du rôle Azure AD

Une fois qu’un compte Azure Maps a été créé, la valeur `Client ID` d’Azure Maps est présente dans la page des détails d’authentification du portail Azure. Cette valeur représente le compte qui doit être utilisé pour les demandes d’API REST. Elle doit être stockée dans la configuration de l’application et récupérée avant d’effectuer des requêtes HTTP. L’objectif du scénario est de permettre à l’application démon de s’authentifier auprès d’Azure AD et d’appeler les API REST Azure Maps.

> [!TIP]
>Pour bénéficier des avantages offerts par les composants d’identité managée, nous vous recommandons d’héberger sur les machines virtuelles Azure, les groupes de machines virtuelles identiques ou App Services.

### <a name="host-a-daemon-on-azure-resources"></a>Héberger un démon sur des ressources Azure

Lorsque vous exécutez des ressources Azure, vous pouvez configurer des identités managées Azure pour favoriser un effort de gestion des informations d’identification minimal et à moindre coût.

Pour permettre à l’application d’accéder à une identité managée, consultez [Vue d’ensemble des identités managées](../active-directory/managed-identities-azure-resources/overview.md).

Les identités managées présentent, entre autres, les avantages suivants :

- Authentification de chiffrement à clé publique par certificat X509 managée par le système Azure
- Sécurité Azure AD avec certificats X509 au lieu de clés secrètes client
- Azure gère et renouvelle tous les certificats associés à la ressource Managed Identity
- La gestion opérationnelle des informations d’identification est simplifiée car l’identité managée supprime le besoin d’avoir recours à un service de magasin de secrets sécurisé, tel qu’Azure Key Vault.

### <a name="host-a-daemon-on-non-azure-resources"></a>Héberger un démon sur des ressources non-Azure

En cas d’exécution dans un environnement autre qu’Azure, les identités managées ne sont pas disponibles. Ainsi, vous devez configurer un principal de service par le biais d’une inscription d’application Azure AD pour l’application démon.

#### <a name="create-new-application-registration"></a>Créer une nouvelle inscription d’application

Si vous avez déjà créé votre inscription d’application, accédez à [Affecter des autorisations d’API déléguées](#assign-delegated-api-permissions).

Pour créer une nouvelle inscription d’application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez **Azure Active Directory**.

3. Sous **Gérer** dans le volet de gauche, sélectionnez **Inscriptions d’applications**.

4. Sélectionnez l’onglet **+ Nouvelle inscription**.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/app-registration.png" alt-text="Affichage des inscriptions d’applications":::

5. Entrez un **Nom**, puis sélectionnez un **Type de compte pris en charge**.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-create.png" alt-text="Création d’une inscription d’application":::

6. Sélectionnez **Inscription**.  

#### <a name="assign-delegated-api-permissions"></a>Attribuer des autorisations d’API déléguées

Pour attribuer des autorisations d’API déléguées à Azure Maps :

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez **Azure Active Directory**.

3. Sous **Gérer** dans le volet de gauche, sélectionnez **Inscriptions d’applications**.

4. Sélectionnez votre application.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-select.png" alt-text="Sélection d’inscriptions d’applications":::

5. Sous **Gérer** dans le volet de gauche, sélectionnez **Autorisations d’API**.

6. Sélectionnez **Ajouter une autorisation**.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/app-add-permissions.png" alt-text="Ajout d’une autorisation d’application":::

7. Sélectionnez l’onglet **API utilisées par mon organisation**.

8. Dans la zone de recherche, entrez **Azure Maps**.

9. Sélectionnez **Azure Maps**.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/app-permissions.png" alt-text="Demande d’autorisation d’application":::

10. Cochez la case **Accéder à Azure Maps**.

11. Sélectionnez **Ajouter des autorisations**.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/select-app-permissions.png" alt-text="Sélection d’autorisations d’API d’application":::

#### <a name="create-a-client-secret-or-configure-certificate"></a>Créer un secret client ou configurer un certificat

Pour implémenter l’authentification basée sur un serveur ou une application dans votre application, vous pouvez choisir l’une des deux options suivantes :

- Charger un certificat à clé publique
- Créer un secret client.

##### <a name="upload-a-public-key-certificate"></a>Charger un certificat à clé publique

Pour charger un certificat à clé publique :

1. Sous **Gérer** dans le volet de gauche, sélectionnez **Certificats et secrets**.

2. Sélectionnez **Charger un certificat**.
   :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate.png" alt-text="Chargement d’un certificat":::

3. À droite de la zone de texte, sélectionnez l’icône de fichier.

4. Sélectionnez un fichier *.crt*, *.cer* ou *.pem*, puis sélectionnez **Ajouter**.

    :::image type="content" border="true" source="./media/how-to-manage-authentication/upload-certificate-file.png" alt-text="Chargement du fichier de certificat":::

##### <a name="create-a-client-secret"></a>Créer une clé secrète client

Pour créer un secret client :

1. Sous **Gérer** dans le volet de gauche, sélectionnez **Certificats et secrets**.

2. Sélectionnez **+ Nouveau secret client**.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret.png" alt-text="Nouveau secret client":::

3. Entrez une description pour le secret client.

4. Sélectionnez **Ajouter**.

   :::image type="content" border="true" source="./media/how-to-manage-authentication/new-client-secret-add.png" alt-text="Ajout d’un nouveau secret client":::

5. Copiez le secret et stockez-le de manière sécurisée dans un service tel qu’Azure Key Vault. De plus, nous utiliserons le secret figurant dans la section [Demander un jeton avec identité managée](#request-a-token-with-managed-identity) de cet article.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/copy-client-secret.png" alt-text="Copie du secret client":::

     >[!IMPORTANT]
     >Pour stocker de manière sécurisée le certificat ou le secret, consultez le [Guide du développeur Azure Key Vault](../key-vault/general/developers-guide.md). Vous utiliserez ce secret pour obtenir des jetons auprès d’Azure AD.

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

### <a name="request-a-token-with-managed-identity"></a>Demander un jeton avec identité managée

Une fois qu’une identité managée a été configurée pour la ressource d’hébergement, vous pouvez utiliser le kit SDK Azure ou l’API REST pour acquérir un jeton pour Azure Maps. Pour savoir comment acquérir un jeton d’accès, consultez [Acquérir un jeton d’accès](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

### <a name="request-token-with-application-registration"></a>Demander un jeton avec l’inscription d’application

Une fois que votre application est inscrite et associée à Azure Maps, vous devez demander un jeton d’accès.

Pour acquérir le jeton d’accès :

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez **Azure Active Directory**.

3. Sous **Gérer** dans le volet de gauche, sélectionnez **Inscriptions d’applications**.

4. Sélectionnez votre application.

5. Vous devez voir la page Vue d’ensemble. Copiez les valeurs de l’ID d’application (client) et de l’ID de l’annuaire (locataire).

      :::image type="content" border="true" source="./media/how-to-manage-authentication/get-token-params.png" alt-text="Copie des paramètres du jeton":::

Nous allons utiliser l’application [Postman](https://www.postman.com/) pour créer la demande de jeton, mais vous pouvez utiliser un autre environnement de développement d’API.

1. Dans l’application Postman, sélectionnez **New** (Nouveau).

2. Dans la fenêtre **Create New** (Créer), sélectionnez **HTTP Request** (Requête HTTP).

3. Entrez un **Nom de demande** pour la demande, tel que *POST Token Request*.

4. Sélectionnez la méthode HTTP **POST**.

5. Entrez l’URL suivante dans la barre d’adresse (remplacez `{Tenant-ID}` par l’ID de l’annuaire (locataire), `{Client-ID}` par l’ID de l’application (client) et `{Client-Secret}` par votre secret client) :

    ```http
    https://login.microsoftonline.com/{Tenant-ID}/oauth2/v2.0/token?response_type=token&grant_type=client_credentials&client_id={Client-ID}&client_secret={Client-Secret}%3D&scope=api%3A%2F%2Fazmaps.fundamentals%2F.default
    ```

6. Sélectionnez **Envoyer**.

7. Vous devez voir la réponse JSON suivante :

```json
{
    "token_type": "Bearer",
    "expires_in": 86399,
    "ext_expires_in": 86399,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5PbzNaRHJPRFhFSzFq..."
}
```

Pour plus d’informations sur le flux d’authentification, consultez la page relative au [flux des informations d’identification du client OAuth 2.0 sur la plateforme d’identités Microsoft](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des exemples plus détaillés :
> [!div class="nextstepaction"]
> [Scénarios d’authentification pour Azure AD](../active-directory/develop/authentication-vs-authorization.md)

Recherchez les métriques d’utilisation de l’API pour votre compte Azure Maps :
> [!div class="nextstepaction"]
> [Afficher les métriques d’utilisation](how-to-view-api-usage.md)

Explorez des exemples qui montrent comment intégrer Azure AD à Azure Maps :
> [!div class="nextstepaction"]
> [Exemples Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
