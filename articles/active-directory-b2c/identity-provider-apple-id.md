---
title: Configurer l’inscription et la connexion avec un ID Apple
titleSuffix: Azure AD B2C
description: Autorisez les clients disposant d’un ID Apple de s’inscrire à vos applications et de s’y connecter à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c3d3fa84e615a60092d0f42acd401be0421bc3f3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448630"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un ID Apple à l’aide d’Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Créer une application ID Apple

Pour permettre la connexion des utilisateurs avec un ID Apple dans Azure Active Directory B2C (Azure AD B2C), vous devez créer une application dans [https://developer.apple.com](https://developer.apple.com). Pour en savoir plus, consultez [Se connecter avec Apple](https://developer.apple.com/sign-in-with-apple/get-started/). Si vous ne disposez pas encore d’un compte de développeur Apple, vous pouvez vous inscrire au programme [Apple Developer Program](https://developer.apple.com/programs/enroll/).

1. Connectez-vous au [Portail des développeurs Apple](https://developer.apple.com/account) avec les informations d’identification de votre compte.
1. Dans le menu, sélectionnez **Certificats, ID et profils**, puis sélectionnez **(+)** .
1. Pour **Inscrire un nouvel identificateur**, sélectionnez **ID d’application**, puis sélectionnez **Continuer**.
1. Pour **Sélectionner un type**, sélectionnez **Application**, puis cliquez sur **Continuer**.
1. Pour **Inscrire un ID d’application** :
    1. Entrez une **Description**. 
    1. Entrer **l’ID d’offre groupée**, par exemple `com.contoso.azure-ad-b2c`. 
    1. Pour les **Fonctionnalités**, sélectionnez **Se connecter avec Apple** dans la liste des fonctionnalités. 
    1. Notez votre **ID d’équipe** (préfixe d’ID d’application) à cette étape. Vous en aurez besoin ultérieurement.
    1. Sélectionnez **Continue**, puis **Register**.
1. Dans le menu, sélectionnez **Certificats, ID et profils**, puis sélectionnez **(+)** .
1. Pour **Inscrire un nouvel identificateur**, sélectionnez **ID de services**, puis sélectionnez **Continuer**.
1. Pour **Inscrire un ID de services** :
    1. Entrez une **Description**. La description s’affiche pour l’utilisateur sur l’écran de consentement.
    1. Entrez **l’Identificateur**, par exemple `com.consoto.azure-ad-b2c-service`. Prenez note de votre identificateur **ID de service**. L’identificateur est votre **ID client** pour le flux OpenID Connect.
    1. Sélectionnez **Continuer**, puis sélectionnez **S’inscrire**.
1. Dans **Identificateurs**, sélectionnez l’identificateur que vous avez créé.
1. Sélectionnez **Se connecter avec Apple**, puis sélectionnez **Configurer**.
    1. Sélectionnez **l’ID de l’application principale** pour laquelle vous souhaitez configurer la connexion avec Apple.
    1. Dans **Domaines et sous-domaines**, entrez `your-tenant-name.b2clogin.com`. Remplacez your-tenant-name par le nom de votre locataire. Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name`.
    1. Dans **URL de retour**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-tenant-name` par le nom de votre locataire et `your-domain-name` par le nom de votre domaine personnalisé. L’URL Retour doit être en lettres minuscules.
    1. Sélectionnez **Suivant**, puis sélectionnez **Terminé**.
    1. Quand la fenêtre contextuelle est fermée, sélectionnez **Continuer**, puis sélectionnez **Enregistrer**.

## <a name="creating-an-apple-client-secret"></a>Création d’une clé secrète client Apple

1. Dans le menu du portail des développeurs Apple, sélectionnez **Clés**, puis sélectionnez **(+)** .
1. Pour **Inscrire une nouvelle clé** :
    1. Tapez un **Nom de clé**.
    1. Sélectionnez **Se connecter avec Apple**, puis sélectionnez **Configurer**.
    1. Pour **l’ID d’application principale**, sélectionnez l’application que vous avez créée précédemment, puis sélectionnez **Enregistrer**.
    1. Sélectionnez **Configurer**, puis sélectionnez **S’inscrire** pour terminer le processus d’inscription de la clé. Notez l’**ID de clé**. Cette clé est requise lorsque vous configurez des flux d’utilisateur.
1. Pour **Télécharger votre clé**, sélectionnez **Télécharger** pour télécharger un fichier .p8 qui contient votre clé.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Configurer Apple en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Apple**.
1. Pour le **nom**, entrez **Se connecter avec Apple**. 
1. Entrez **l’ID de développeur Apple (ID d’équipe)** .
1. Entrez **l’ID de service Apple (ID client)** .
1. Entrez l’**ID de clé Apple** de l’étape [Création d’une clé secrète client Apple](#creating-an-apple-client-secret).
1. Sélectionnez et chargez les **Données de certificat Apple**.
1. Sélectionnez **Enregistrer**.


> [!IMPORTANT] 
> - En cas de connexion avec Apple, l’administrateur doit renouveler sa clé secrète client tous les 6 mois. 
> - La clé secrète client Apple sera automatiquement renouvelée lorsqu’elle expirera. Si vous devez renouveler la clé secrète manuellement, ouvrez Azure AD B2C dans le portail Azure, accédez à **Fournisseurs d’identité** > **Apple** et sélectionnez **Renouveler le secret**.
> - Suivez les instructions pour [proposer le bouton Se connecter avec Apple](#customize-your-user-interface).

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité Apple à un flux d’utilisateur

Pour permettre aux utilisateurs de se connecter à l’aide d’un ID Apple, vous devez ajouter le fournisseur d’identité Apple à un flux d’utilisateur. La connexion avec Apple peut être configurée uniquement pour la version **recommandée** des flux d’utilisateurs. Pour ajouter le fournisseur d’identité Apple à un flux d’utilisateur :

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Sélectionnez un flux d’utilisateur auquel vous souhaitez ajouter le fournisseur d’identité Apple. 
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **Apple**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux d’utilisateur**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Apple** pour vous connecter avec l’ID Apple.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>Signature de la clé secrète client

Utilisez le fichier .p8 téléchargé précédemment pour signer la clé secrète client dans un jeton JWT. De nombreuses bibliothèques peuvent créer et signer le JWT pour vous. Utilisez la [fonction Azure qui crée un jeton](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) pour vous. 

1. Créez une [fonction Azure](../azure-functions/functions-create-function-app-portal.md).
1. Sous **Développeur**, sélectionnez **Code + test**. 
1. Copiez le contenu du fichier [run.csx](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx), puis collez-le dans l’éditeur.
1. Sélectionnez **Enregistrer**.
1. Effectuez une requête HTTP `POST` et fournissez les informations suivantes :

    - **appleTeamId** : votre ID d’équipe de développeurs Apple
    - **appleServiceId** : ID de service Apple (ID client)
    - **appleKeyId** : Identifiant de clé à 10 chiffres stocké dans l’en-tête JWT (requis par Apple)
    - **p8key** : clé au format PEM. Vous pouvez obtenir la clé en ouvrant le fichier .p8 dans un éditeur de texte et en copiant tout ce qui se trouve entre `-----BEGIN PRIVATE KEY-----` et `-----END PRIVATE KEY-----`, sans sauts de ligne.
 
Le code JSON suivant est un exemple d’appel de la fonction Azure :

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleServiceId": "com.yourcompany.app1",
    "appleKeyId": "URKEYID001",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

La fonction Azure répond avec un jeton JWT de secret client correctement mis en forme et signé dans une réponse, par exemple :

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez stocker la clé secrète client que vous avez enregistrée dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Dans la page **de présentation**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Dans **Options**, choisissez **Manuel**.
1. Entrez un **nom** pour la clé de stratégie. Par exemple « AppleSecret ». Le préfixe « B2C_1A_ » est ajouté automatiquement au nom de votre clé.
1. Dans **Secret**, entrez la valeur d’un jeton retourné par la fonction Azure (jeton JWT).
1. Pour **Utilisation de la clé**, sélectionnez **Signature**.
1. Sélectionnez **Create** (Créer).

> [!IMPORTANT] 
> - En cas de connexion avec Apple, l’administrateur doit renouveler sa clé secrète client tous les 6 mois.
> - Vous devrez renouveler manuellement la clé secrète client Apple si elle expire et stocker la nouvelle valeur dans la clé de stratégie.
> - Nous vous recommandons de définir votre propre rappel dans les 6 mois pour générer une nouvelle clé secrète client. 
> - Suivez les instructions pour [proposer le bouton Se connecter avec Apple](#customize-your-user-interface).

## <a name="configure-apple-as-an-identity-provider"></a>Configurer Apple en tant que fournisseur d’identité

Pour permettre aux utilisateurs de se connecter avec un ID Apple, vous devez définir le compte comme fournisseur de revendications avec lequel Azure AD B2C peut communiquer via un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique est authentifié.

Vous pouvez définir un ID Apple en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Sign in with Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.name.firstName user.name.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Définissez **client_id** sur l’identificateur de service. Par exemple : `com.consoto.azure-ad-b2c-service`.
5. Enregistrez le fichier .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Apple** pour vous connecter avec l’ID Apple.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end

## <a name="customize-your-user-interface"></a>Personnaliser votre interface utilisateur

Suivez les instructions pour [proposer l’option Se connecter avec Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/introduction/). Apple fournit plusieurs boutons **Se connecter avec Apple** que vous pouvez utiliser pour permettre aux utilisateurs de configurer un compte et de se connecter. Si nécessaire, créez un bouton personnalisé pour proposer l’option Se connecter avec Apple. Découvrez comment [afficher un bouton Se connecter avec Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/buttons/).

Pour vous conformer aux instructions de l’interface utilisateur d’Apple :

- [Personnaliser l’interface utilisateur avec des modèles HTML](customize-ui-with-html.md)
- [Localisez](language-customization.md) le nom du fournisseur d’identité.

