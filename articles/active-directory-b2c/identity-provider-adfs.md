---
title: Ajouter AD FS en tant que fournisseur d’identité OpenID Connect en utilisant des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Configurer AD FS 2016 à l’aide du protocole OpenID Connect et de stratégies personnalisées dans Azure Active Directory B2C
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ad00a74871373338a2b4e8b557297a808e8186fc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007293"
---
# <a name="add-ad-fs-as-an-openid-connect-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Ajouter AD FS en tant que fournisseur d’identité OpenID Connect à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]


## <a name="prerequisites"></a>Configuration requise

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-an-ad-fs-application"></a>Créer une application AD FS

Pour autoriser la connexion des utilisateurs avec un compte AD FS dans Azure Active Directory B2C (Azure AD B2C), créez un groupe d’applications dans votre compte AD FS. Pour plus d’informations, consultez [Créer une application web à l’aide d’OpenID Connect avec AD FS 2016 et versions ultérieures](/windows-server/identity/ad-fs/development/enabling-openid-connect-with-ad-fs).

Pour créer un groupe d’applications, procédez comme suit :

1. Dans **Gestionnaire de serveur**, sélectionnez **Outils**, puis **Gestion AD FS**.
1. Dans Gestion AD FS, cliquez avec le bouton droit sur **Groupes d’applications** et sélectionnez **Ajouter un groupe d’applications**.
1. Sur l’écran **Bienvenue** de l’Assistant de groupe d’applications :
    1. Entrez le **nom** de votre application. Par exemple, *Application Azure AD B2C*.
    1. Sous **Applications client-serveur**, sélectionnez le modèle **Navigateur web accédant à une application web**.
    1. Sélectionnez **Suivant**.
1. Sur l’écran **Application native** de l’Assistant de groupe d’applications :
    1. Copiez la valeur d’**Identificateur du client**. L’identificateur du client est votre **ID d’application** AD FS. Vous aurez besoin de l’ID d’application plus loin dans cet article.
    1. Dans **URI de redirection**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Si vous utilisez un [domaine personnalisé](custom-domain.md), entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-tenant-name` par le nom de votre locataire et `your-domain-name` par le nom de votre domaine personnalisé.
    1. Sélectionnez **Suivant**, puis encore **Suivant** pour terminer l’Assistant d’inscription d’application. 
    1. Sélectionnez **Fermer**.


### <a name="configure-the-app-claims"></a>Configurer les réclamations de l’application

Dans cette étape, configurez les réclamations que l’application AD FS renvoie à Azure AD B2C.  

1. Parmi les **groupes d’applications**, sélectionnez l’application que vous avez créée.
1. Dans la fenêtre des propriétés de l’application, sous **Applications**, sélectionnez l’**application web**. Sélectionnez ensuite **Modifier**.
    :::image type="content" source="./media/identity-provider-adfs/ad-fs-edit-app.png" alt-text="Capture d’écran montrant comment modifier une application web.":::
1. Sélectionnez l’onglet **Règles de transformation d’émission**, puis sélectionnez **Ajouter une règle**.
1. Dans **Modèle de règle de revendication**, sélectionnez **Envoyer les attributs LDAP en tant que revendications**.
1. Fournissez un **Nom de règle de revendication**. Pour le **Magasin d’attributs**, choisissez **Sélectionner Active Directory** et ajoutez les revendications suivantes.

    | Attribut LDAP | Type de revendication sortante |
    | -------------- | ------------------- |
    | User-Principal-Name | upn |
    | Surname | family_name |
    | Given-Name | given_name |
    | Display-Name | name |

    Notez que certains des noms ne s’afficheront pas dans la liste déroulante Type de revendication sortante. Vous devez les saisir manuellement. (La liste déroulante est modifiable).

1. Sélectionnez **Terminer**, puis **Fermer**.


::: zone pivot="b2c-user-flow"

## <a name="configure-ad-fs-as-an-identity-provider"></a>Configurer AD FS en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Nouveau fournisseur OpenID Connect**.
1. Saisissez un **Nom**. Par exemple, *Contoso*.
1. Pour l’**URL de métadonnées**, entrez l’URL du [document de configuration d’AD FS OpenID Connect](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints). Par exemple :

    ```http
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```

1. Dans **ID client**, entrez l’ID d’application que vous avez enregistré précédemment.
1. Pour l'**Étendue**, entrez `openid`.
1. Pour **Type de réponse**, sélectionnez **id_token**.
1. (Facultatif) Pour l'**Indication de domaine**, entrez `contoso.com`. Pour plus d’informations, consultez [Configurer la connexion directe avec Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Sous **Mappage des revendications du fournisseur d’identité**, sélectionnez les revendications suivantes :

    - **ID d’utilisateur** : `upn`
    - **Nom d’affichage** : `unique_name`
    - **Prénom** : `given_name`
    - **Nom de famille** : `family_name`

1. Sélectionnez **Enregistrer**.

## <a name="add-ad-fs-identity-provider-to-a-user-flow"></a>Ajouter le fournisseur d’identité AD FS à un flux d’utilisateur 

À ce stade, le fournisseur d’identité AD FS (Contoso) a été configuré, mais il n’est encore disponible dans aucune des pages de connexion. Pour ajouter le fournisseur d’identité AD FS à un flux d’utilisateur :

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Sélectionnez le flux d’utilisateur que vous souhaitez ajouter au fournisseur d’identité AD FS (Contoso).
1. Sous **Fournisseurs d’identité sociale**, sélectionnez **Contoso**.
1. Sélectionnez **Enregistrer**.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter le flux d’utilisateur**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Contoso** pour vous connecter avec le compte Contoso.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="configure-ad-fs-as-an-identity-provider"></a>Configurer AD FS en tant que fournisseur d’identité

Pour permettre aux utilisateurs de se connecter avec un compte AD FS, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer via un point de terminaison. 

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://your-adfs-domain/adfs/.well-known/openid-configuration</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your AD FS application ID</Item>
          </Metadata>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="unique_name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Pour l’**URL de métadonnées**, entrez l’URL du [document de configuration d’AD FS OpenID Connect](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints). Par exemple :

    ```
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```
5. Définissez **client_id** sur l’ID d’application de l’inscription de l’application.
6. Enregistrez le fichier .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Contoso** pour vous connecter avec un compte Contoso.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.


::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transmettre un jeton AD FS à votre application](idp-pass-through-user-flow.md).
