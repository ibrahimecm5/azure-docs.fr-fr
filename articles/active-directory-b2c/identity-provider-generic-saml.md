---
title: Configurer l’inscription et la connexion avec le fournisseur d’identité SAML
titleSuffix: Azure Active Directory B2C
description: Configurez l’inscription et la connexion avec un fournisseur d’identité SAML dans Azure Active Directory B2C.
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
ms.openlocfilehash: e76176c74405422fc871bc2bfb05e4dada01774d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "132133753"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un fournisseur d’identité SAML dans Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) prend en charge la fédération avec des fournisseurs d’identité SAML 2.0. Cet article explique comment activer la connexion avec un compte d’utilisateur de fournisseur d’identité SAML, ce qui permet aux utilisateurs de se connecter avec leurs identités sociales ou d’entreprise existantes, comme [ADFS](./identity-provider-adfs.md) ou [Salesforce](identity-provider-salesforce-saml.md).

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>Présentation du scénario

Vous pouvez configurer Azure AD B2C pour permettre aux utilisateurs de se connecter à votre application avec des informations d’identification de fournisseurs d’identité SAML de réseau social ou d’entreprise externes. Quand Azure AD B2C fédère avec un fournisseur d’identité SAML, il fait office de **fournisseur de services** qui envoie une demande SAML au **fournisseur d’identité** SAML et qui attend une réponse SAML. Dans le schéma suivant :

1. L’application envoie une demande d’autorisation à Azure AD B2C. L’application peut être une application [OAuth 2.0](protocols-overview.md) ou [OpenID Connect](openid-connect.md), ou un [fournisseur de services SAML](saml-service-provider.md). 
1. Dans la page de connexion Azure AD B2C, l’utilisateur choisit de se connecter avec un compte de fournisseur d’identité SAML (par exemple, *contoso*). Azure AD B2C envoie une demande d’autorisation SAML et dirige l’utilisateur vers le fournisseur d’identité SAML pour effectuer la connexion.
1. Le fournisseur d’identité SAML retourne une réponse SAML.
1. Azure AD B2C valide le jeton SAML, extrait les revendications, émet son propre jeton et ramène l’utilisateur à l’application.  

![Se connecter avec un flux de fournisseur d’identité SAML](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>Composants de la solution

Pour ce scénario, les composants suivants sont nécessaires :

* Un **fournisseur de services** SAML avec la possibilité de recevoir et de décoder des demandes SAML, ainsi que d’y répondre à partir d’Azure AD B2C.
* Un **point de terminaison de métadonnées** SAML disponible publiquement pour votre fournisseur d’identité.
* Un [locataire Azure AD B2C](tutorial-create-tenant.md).

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Pour créer une confiance entre Azure AD B2C et votre fournisseur d’identité SAML, vous devez fournir un certificat X509 valide avec la clé privée. Azure AD B2C signe les demandes SAML à l’aide de la clé privée du certificat. Le fournisseur d’identité valide la demande à l’aide de la clé publique du certificat. La clé publique est accessible via les métadonnées du profil technique. En guise d’alternative, vous pouvez charger manuellement le fichier .cer vers votre fournisseur d’identité SAML.

Un certificat auto-signé est acceptable pour la plupart des scénarios. Pour les environnements de production, nous vous recommandons d’utiliser un certificat X509 émis par une autorité de certification. En outre, comme décrit plus loin dans ce document, pour un environnement hors production, vous pouvez désactiver la signature SAML des deux côtés.

### <a name="obtain-a-certificate"></a>Obtenir un certificat

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Téléchargement du certificat

Vous devez enregistrer votre certificat dans votre client Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Upload`.
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `SAMLSigningCert`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
1. Recherchez et sélectionnez votre fichier de certificat .pfx contenant la clé privée.
1. Cliquez sur **Créer**.

## <a name="configure-the-saml-technical-profile"></a>Configurer le profil technique SAML

Définissez le fournisseur d’identité SAML en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie. Les fournisseurs de revendications ont un profil technique SAML qui détermine les points de terminaison et les protocoles nécessaires pour communiquer avec le fournisseur d’identité SAML. Pour ajouter un fournisseur de revendications avec un profil technique SAML :

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
1. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
1. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Mettez à jour les éléments XML suivants avec la valeur appropriée :

|Élément XML  |Value  |
|---------|---------|
|ClaimsProvider\Domain  | Nom de domaine utilisé pour la [connexion directe](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Entrez le nom de domaine que vous souhaitez utiliser pour la connexion directe. Par exemple, *Contoso.com*. |
|TechnicalProfile\DisplayName|Cette valeur s’affiche sur le bouton Se connecter dans votre écran de connexion. Par exemple, *Contoso*. |
|Metadata\PartnerEntity|URL des métadonnées du fournisseur d’identité SAML. Vous pouvez également copier les métadonnées du fournisseur d’identité et les ajouter à l’intérieur de l’élément CDATA `<![CDATA[Your IDP metadata]]>`.|

## <a name="map-the-claims"></a>Mapper les revendications

L’élément **OutputClaims** contient une liste de revendications que le fournisseur d’identité SAML retourne. Mappez le nom de la revendication qui est définie dans votre stratégie sur le nom d’assertion qui est défini dans le fournisseur d’identité. Contactez votre fournisseur d’identité pour obtenir la liste des revendications (assertions). Pour plus d’informations, consultez [Mappage des revendications](identity-provider-generic-saml-options.md#claims-mapping).

Dans l’exemple ci-dessus, *Contoso-SAML2* comprend les revendications retournées par un fournisseur d’identité SAML :

* La revendication **issuerUserId** est mappée à la revendication **assertionSubjectName**.
* Revendication **first_name** mappée à la revendication **givenName**.
* Revendication **last_name** mappée à la revendication **surname**.
* La revendication **displayName** est mappée à la revendication `http://schemas.microsoft.com/identity/claims/displayname`.
* La revendication **email** sans mappage de nom.

Le profil technique retourne également des revendications qui ne sont pas retournées par le fournisseur d’identité :

* La revendication **identityProvider** contenant le nom du fournisseur d’identité.
* La revendication **authenticationSource** avec la valeur par défaut **socialIdpAuthentication**.
 
### <a name="add-the-saml-session-technical-profile"></a>Ajouter le profil technique de session SAML

Si vous n’avez pas encore de profil technique de session SAML `SM-Saml-idp`, ajoutez-en un à votre stratégie d’extension. Recherchez la section `<ClaimsProviders>` et ajoutez l’extrait de code XML suivant. Si votre stratégie contient déjà le profil technique `SM-Saml-idp`, passez à l’étape suivante. Pour plus d’informations, voir [Gestion de sessions d’authentification unique](custom-policy-reference-sso.md).

```xml
<ClaimsProvider>
  <DisplayName>Session Management</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SM-Saml-idp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

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
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>Configurer votre fournisseur d’identité SAML

Une fois votre stratégie configurée, vous devez configurer votre fournisseur d’identité SAML avec les métadonnées SAML Azure AD B2C. Les métadonnées SAML sont des informations qui sont utilisées dans le protocole SAML pour exposer la configuration de votre stratégie (le **fournisseur de services**). Elles définissent l’emplacement des services, tels que la connexion et la déconnexion, les certificats, la méthode de connexion et plus encore.

Les étapes de configuration d’un fournisseur de services sont différentes pour chaque fournisseur d’identité SAML. Certains fournisseurs d’identité SAML demandent les métadonnées Azure AD B2C, tandis que d’autres vous demandent de parcourir le fichier de métadonnées manuellement et de fournir les informations nécessaires. Pour obtenir des indications, consultez la documentation de votre fournisseur d’identité.

L’exemple suivant montre l’adresse URL des métadonnées SAML d’un profil technique Azure AD B2C :

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Avec un [domaine personnalisé](custom-domain.md), utilisez le format suivant :

```
https://your-domain-name/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Remplacez les valeurs suivantes :

- **your-tenant-name** avec le nom de votre locataire, par exemple your-tenant.onmicrosoft.com.
- **your-domain-name** avec votre nom de domaine personnalisé, tel que login.contoso.com.
- **your-policy** par le nom de votre stratégie. Par exemple, B2C_1A_signup_signin_adfs.
- **your-technical-profile** par le nom du profil technique de votre fournisseur d’identité SAML. Par exemple, Contoso-SAML2.

Ouvrez un navigateur et accédez à l’URL. Veillez à taper l’URL est correcte pour avoir accès au fichier de métadonnées XML.

## <a name="test-your-custom-policy"></a>Tester votre stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Contoso** pour vous connecter avec un compte Contoso.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les options de fournisseur d’identité SAML avec Azure Active Directory B2C](identity-provider-generic-saml-options.md)

::: zone-end