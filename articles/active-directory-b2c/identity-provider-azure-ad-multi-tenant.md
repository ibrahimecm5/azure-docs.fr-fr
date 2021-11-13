---
title: Configurer la connexion pour les annuaires Azure AD multilocataires par des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Ajoutez un fournisseur d’identité Azure AD multilocataire en utilisant des stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 553608a5574edaf904e9c9ac0986a3d0f8af9278
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227958"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer une connexion pour un service Azure Active Directory mutualisé à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Cet article explique comment autoriser la connexion d’utilisateurs à l’aide du point de terminaison multi-locataire pour Azure Active Directory (Azure AD). Autorisation d’utilisateurs de différents locataires Azure AD à se connecter avec Azure AD B2C sans avoir à configurer un fournisseur d’identité pour chaque locataire. Toutefois, les membres invités dans ces locataires **ne pourront pas** se connecter. Pour ce faire, vous devez [configurer individuellement chaque locataire](identity-provider-azure-ad-single-tenant.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

> [!NOTE]
> Dans cet article, nous partons du principe que le pack de démarrage **SocialAndLocalAccounts** est utilisé dans les étapes précédentes mentionnées dans les prérequis.  

## <a name="register-an-azure-ad-app"></a>Inscrire une application Azure AD

Pour autoriser la connexion des utilisateurs avec un compte Azure AD dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application dans le [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez [Inscrire une application auprès de la plateforme d’identités Microsoft](../active-directory/develop/quickstart-register-app.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser le répertoire qui contient votre locataire Azure AD de l’organisation (par exemple, Contoso). Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire Azure AD dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Nouvelle inscription**.
1. Entrez un **nom** pour votre application. Par exemple : `Azure AD B2C App`.
1. Sélectionnez **Comptes dans un annuaire d’organisation (tout annuaire Azure AD - Multilocataire)** pour cette application.
1. Pour le champ **URI de redirection**, acceptez la valeur **Web**, puis entrez l’URL suivante en minuscules, où `your-B2C-tenant-name` est remplacé par le nom de votre locataire Azure AD B2C :

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Par exemple : `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-domain-name` par le nom de votre domaine personnalisé et `your-tenant-name` par le nom de votre locataire.

1. Sélectionnez **Inscription**. Enregistrez l’**ID d’application (client)** pour l’utiliser dans une étape ultérieure.
1. Sélectionnez **Certificats et secrets**, puis sélectionnez **Nouveau secret client**.
1. Entrez une **description** pour le secret, sélectionnez une date d’expiration, puis sélectionnez **Ajouter**. Enregistrez la **Valeur** du secret pour l’utiliser à une étape ultérieure.

### <a name="configuring-optional-claims"></a>Configuration des revendications facultatives

Si vous souhaitez obtenir les revendications `family_name` et `given_name` d’Azure AD, vous pouvez configurer des revendications facultatives pour votre application dans l’interface utilisateur du portail Azure ou dans le manifeste de l’application. Pour plus d'informations, consultez [Procédure : Fournir des revendications facultatives à votre application Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com). Recherchez et sélectionnez **Azure Active Directory**.
1. Dans la section **Gérer**, sélectionnez **Inscriptions d’applications**.
1. Sélectionnez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives.
1. Dans la section **Gérer**, sélectionnez **Configuration de jetons**.
1. Sélectionnez **Ajouter une revendication facultative**.
1. Dans **Type de jeton**, sélectionnez **ID**.
1. Sélectionnez les revendications facultatives à ajouter, `family_name` et `given_name`.
1. Sélectionnez **Ajouter**. Si l’option **Activer l’autorisation d’e-mail Microsoft Graph (nécessaire pour que les revendications apparaissent dans le jeton)** s’affiche, activez-la et sélectionnez une nouvelle fois **Ajouter**.

## <a name="optional-verify-your-app-authenticity"></a>[Facultatif] Vérifier l’authenticité de votre application

La [vérification de l’éditeur](../active-directory/develop/publisher-verification-overview.md) permet aux utilisateurs de s’assurer de l’authenticité de l’application que vous avez [inscrite](#register-an-azure-ad-app). Une application vérifiée signifie que l’éditeur de l’application a [vérifié](/partner-center/verification-responses) son identité à l’aide de son Microsoft Partner Network (MPN). Apprenez à [marquer votre application avec la mention « éditeur vérifié »](../active-directory/develop/mark-app-as-publisher-verified.md). 

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé d’application que vous avez créée dans votre locataire Azure AD B2C.

1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `AADAppSecret`.  Le préfixe `B2C_1A_` étant ajouté automatiquement au nom de votre clé lors de sa création, sa référence dans le code XML de la section suivante est à *B2C_1A_AADAppSecret*.
1. Dans **Secret**, entrez le secret client que vous avez enregistré précédemment.
1. Pour **Utilisation de la clé**, sélectionnez `Signature`.
1. Sélectionnez **Create** (Créer).

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurer Azure AD en tant que fournisseur d’identité

Pour permettre aux utilisateurs de se connecter à l’aide d’un compte Azure AD, vous devez définir Azure AD en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir Azure AD comme fournisseur de revendications en ajoutant Azure AD à l’élément **ClaimsProvider** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml***.
1. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Sous l’élément **ClaimsProvider**, mettez à jour la valeur de **Domaine** sur une valeur unique qui peut être utilisée pour le distinguer des autres fournisseurs d’identité.
1. Sous l’élément **TechnicalProfile**, mettez à jour la valeur de **DisplayName**, par exemple `Multi-Tenant AAD`. Cette valeur apparaît sur le bouton de connexion dans votre page de connexion.
1. Définissez **client_id** sur l’ID de l’application multilocataire Azure AD que vous avez inscrite précédemment.
1. Sous **CryptographicKeys**, mettez à jour la valeur de **StorageReferenceId** sur le nom de la clé de stratégie que vous avez créée précédemment. Par exemple : `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Restriction de l’accès

La valeur `https://login.microsoftonline.com/` pour **ValidTokenIssuerPrefixes** autorise tous les utilisateurs Azure AD à se connecter à votre application. Mettez à jour la liste des émetteurs de jetons valides et restreignez l’accès à une liste spécifique d’utilisateurs locataires Azure AD qui peuvent se connecter.

Pour obtenir les valeurs correspondantes, examinez les métadonnées de découverte OpenID Connect de chacun des locataires Azure AD à partir desquels les utilisateurs seront autorisés à se connecter. Le format de l’URL des métadonnées est semblable à `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`, où `your-tenant` est votre nom de locataire Azure AD. Par exemple :

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Effectuez ces étapes pour chaque locataire Azure AD qui doit être utilisé pour se connecter :

1. Ouvrez votre navigateur et accédez à l’URL des métadonnées OpenID Connect pour le locataire. Recherchez l’objet **issuer** et enregistrez sa valeur. Elle doit ressembler à `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/.well-known/openid-configuration`.
1. Copiez et collez la valeur dans la clé **ValidTokenIssuerPrefixes**. Séparez plusieurs émetteurs par une virgule. Un exemple avec deux émetteurs apparaît dans l’exemple XML `ClaimsProvider` précédent.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **AAD commun** pour vous connecter avec un compte Contoso Azure AD.

Pour tester la fonctionnalité de connexion multilocataire, effectuez les deux dernières étapes à l’aide des informations d’identification d’un utilisateur qui existe dans un autre locataire Azure AD. Copiez le point de terminaison **Exécuter maintenant** et ouvrez-le dans une fenêtre de navigation privée, par exemple en mode navigation privée dans Google Chrome ou dans une fenêtre InPrivate dans Microsoft Edge. L’ouverture dans une fenêtre de navigation privée vous permet de tester le parcours utilisateur complet en n’utilisant pas les informations d’identification Azure AD actuellement mises en cache.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transmettre le jeton Azure AD à votre application](idp-pass-through-user-flow.md).

::: zone-end
