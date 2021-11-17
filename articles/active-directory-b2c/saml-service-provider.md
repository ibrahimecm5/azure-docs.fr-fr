---
title: Configurer Azure AD B2C en tant que fournisseur d’identité SAML pour vos applications
title-suffix: Azure Active Directory B2C
description: Découvrez comment configurer Azure Active Directory B2C pour fournir des assertions de protocole SAML à vos applications (fournisseurs de services).
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4dee0d3730c74218e236c5d6b51afedc496e02fe
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522799"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Inscrire une application SAML dans Azure AD B2C

Cet article explique comment connecter vos applications Security Assertion Markup Language (SAML) (fournisseurs de services) à Azure Active Directory B2C (Azure AD B2C) pour l’authentification.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Vue d'ensemble

Les organisations qui utilisent Azure AD B2C comme solution de gestion des identités et des accès des clients peuvent nécessiter une interaction avec des applications qui s’authentifient à l’aide du protocole SAML. Le diagramme suivant montre comment Azure AD B2C fait office de *fournisseur d’identité* (IdP) pour effectuer une authentification unique (SSO) avec des applications SAML.

![Diagramme montrant Azure Active Directory B2C comme fournisseur d’identité à gauche et fournisseur de services à droite.](media/saml-service-provider/saml-service-provider-integration.png)

1. L’application crée une demande d’authentification SAML qui est envoyée au point de terminaison de connexion SAML pour Azure AD B2C.
2. L’utilisateur peut utiliser un compte local Azure AD B2C ou tout autre fournisseur d’identité fédéré (s’il est configuré) pour s’authentifier.
3. Si l’utilisateur se connecte à l’aide d’un fournisseur d’identité fédéré, une réponse de jeton est envoyée à Azure AD B2C.
4. Azure AD B2C génère une assertion SAML et l’envoie à l’application.

Regardez cette vidéo pour apprendre à intégrer des applications SAML avec Azure AD B2C. 

>[!Video https://www.youtube.com/embed/r2TIVBCm7v4]

## <a name="prerequisites"></a>Prérequis

Pour le scénario décrit dans cet article, vous avez besoin de ce qui suit :

* La stratégie personnalisée *SocialAndLocalAccounts* d’un pack de démarrage de stratégie personnalisée. Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy). 
* Une compréhension de base du protocole SAML et un bonne connaissance de l’implémentation SAML de l’application.
* Une application web configurée en tant qu’application SAML. Elle doit être capable d’envoyer des demandes d’authentification SAML, ainsi que de recevoir, décoder et vérifier des répondes SAML d’Azure AD B2C. L’application SAML est également connue en tant qu’application par partie de confiance ou fournisseur de services. 
* Le document XML ou le *point de terminaison de métadonnées SAML* disponible publiquement de l’application SAML.
* Un [locataire Azure AD B2C](tutorial-create-tenant.md).

Si vous ne disposez pas encore d’une application SAML et d’un point de terminaison de métadonnées associé, vous pouvez utiliser l’[application de test SAML][samltest] que nous avons mise à disposition à des fins de test.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="set-up-certificates"></a>Configurer des certificats

Pour créer une relation d’approbation entre votre application et Azure AD B2C, les deux services doivent être en mesure de créer des signatures et de valider celles de l’autre. Configurez des certificats X509 dans votre application et dans Azure AD B2C.

**Certificats d’application**

| Usage | Obligatoire | Description |
| --------- | -------- | ----------- |
| Signature de demande SAML  | Non | Un certificat avec une clé privée stockée dans votre application web. Votre application utilise le certificat pour signer les demandes SAML envoyées à Azure AD B2C. L’application web doit exposer la clé publique via son point de terminaison de métadonnées SAML. Azure AD B2C valide la signature de la demande SAML à l’aide de la clé publique extraite des métadonnées de l’application.|
| Chiffrement d’assertion SAML  | Non | Un certificat avec une clé privée stockée dans votre application web. L’application web doit exposer la clé publique via son point de terminaison de métadonnées SAML. Azure AD B2C peut chiffrer des assertions à l’adresse de votre application à l’aide de la clé publique. L’application utilise la clé privée pour déchiffrer l’assertion.|

**Certificats Azure AD B2C**

| Usage | Obligatoire | Description |
| --------- | -------- | ----------- |
| Signature de réponse SAML | Oui  | Un certificat avec une clé privée stockée dans Azure AD B2C. Azure AD B2C utilise ce certificat pour signer la réponse SAML envoyée à votre application. Votre application lit la clé publique des métadonnées dans Azure AD B2C pour valider la signature de la réponse SAML. |
| Signature d’assertion SAML | Oui | Un certificat avec une clé privée stockée dans Azure AD B2C. Azure AD B2C utilise ce certificat pour signer la partie `<saml:Assertion>` de la réponse SAML.  |

Dans un environnement de production, nous vous recommandons d’utiliser des certificats émis par une autorité de certification publique. Toutefois, vous pouvez suivre cette procédure avec des certificats auto-signés.

### <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Pour établir une relation d’approbation entre votre application et Azure AD B2C, créez un certificat de signature pour la réponse SAML. Azure AD B2C utilise ce certificat pour signer la réponse SAML envoyée à votre application. Votre application lit la clé publique des métadonnées pour Azure AD B2C afin de valider la signature de la réponse SAML. 

> [!TIP]
> Vous pouvez utiliser cette clé de stratégie à d’autres fins, telles que la signature de l’[assertion SAML](saml-service-provider-options.md#check-the-saml-assertion-signature). 

### <a name="obtain-a-certificate"></a>Obtenir un certificat

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Téléchargement du certificat

Vous devez enregistrer votre certificat dans votre client Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sélectionnez **Tous les services** dans l’angle supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page **de présentation**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, sélectionnez **Chargement**.
1. Pour **Nom**, entrez un nom pour la clé de stratégie. Par exemple, entrez **SamlIdpCert**. Le préfixe **B2C_1A_** est ajouté automatiquement au nom de votre clé.
1. Recherchez et sélectionnez votre fichier de certificat .pfx contenant la clé privée.
1. Sélectionnez **Create** (Créer).

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Activer votre stratégie pour la connexion à une application SAML

Pour se connecter à votre application SAML, Azure AD B2C doit être en mesure de créer des réponses SAML.

Ouvrez le fichier *SocialAndLocalAccounts\TrustFrameworkExtensions.xml* pack de démarrage de stratégie personnalisée.

Recherchez la section `<ClaimsProviders>`, puis ajoutez l’extrait de code XML suivant pour implémenter votre générateur de réponses SAML :

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML-based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issuer-uri-of-the-saml-response"></a>Configurer l’URI de l’émetteur de la réponse SAML

Vous pouvez modifier la valeur de l’élément de métadonnées `IssuerUri` dans le profil technique d’émetteur de jeton SAML. Cette modification sera reflétée dans l’attribut `issuerUri` retourné dans la réponse SAML d’Azure AD B2C. Configurez votre application pour accepter la même valeur `IssuerUri` lors de la validation de la réponse SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-your-policy-to-issue-a-saml-response"></a>Configurer votre stratégie pour émettre une réponse SAML

Maintenant que votre stratégie peut créer des réponses SAML, vous devez la configurer pour émettre une réponse SAML au lieu de la réponse JWT par défaut à votre application.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>Créer une stratégie d’inscription ou de connexion configurée pour SAML

1. Créez une copie du fichier *SignUpOrSignin.xml* dans le répertoire de travail de votre pack de démarrage, puis enregistrez celui-ci sous un nouveau nom. Cet article utilise le fichier *SignUpOrSigninSAML.xml* en guise d’exemple. Il s’agit de votre fichier de stratégie pour la partie de confiance. Il est configuré pour émettre une réponse JWT par défaut.

1. Ouvrez le fichier *SignUpOrSigninSAML.xml* dans votre éditeur préféré.

1. Modifiez les valeurs `PolicyId` et `PublicPolicyUri` de la stratégie en `B2C_1A_signup_signin_saml` et `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` .

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. À la fin du parcours utilisateur, Azure AD B2C contient une étape `SendClaims`. Cette étape fait référence au profil technique d’émetteur de jeton. Pour émettre une réponse SAML au lieu de la réponse JWT par défaut, modifiez l’étape `SendClaims` pour référencer le nouveau profil technique d’émetteur de jeton SAML, `Saml2AssertionIssuer`.

Ajoutez l’extrait de code XML suivant juste devant l’élément `<RelyingParty>`. Ce code XML remplace l’étape d’orchestration 7 du parcours utilisateur _SignUpOrSignIn_. 

Si vous avez commencé à partir d’un au dossier pack de démarrage ou si vous avez personnalisé le parcours utilisateur en ajoutant ou en supprimant des étapes d’orchestration, assurez-vous que le numéro de l’élément `order` correspond au numéro spécifié dans le parcours utilisateur pour l’étape de l’émetteur de jeton. Par exemple, dans les autres dossiers du pack de démarrage, le numéro d’étape correspondant est 4 pour `LocalAccounts`, 6 pour `SocialAccounts` et 9 pour `SocialAndLocalAccountsWithMfa`.

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

L’élément de partie de confiance détermine le protocole que votre application utilise. Par défaut, il s’agit de `OpenId`. La valeur de l’élément `Protocol` doit être modifiée en `SAML`. Les revendications de sortie créent le mappage de revendications à l’assertion SAML.

Remplacez la totalité de l’élément `<TechnicalProfile>` dans l’élément `<RelyingParty>` par le fichier XML de profil technique suivant. Mettez à jour `tenant-name` avec le nom de votre locataire Azure AD B2C.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

Votre fichier de stratégie final pour la partie de confiance final doit ressembler au code XML suivant :

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> Vous pouvez suivre le même processus pour implémenter d’autres types de flux utilisateur (par exemple, connexion, réinitialisation de mot de passe ou modification de profil).

### <a name="upload-your-policy"></a>Charger votre stratégie

Enregistrez vos modifications et chargez les nouveaux fichiers de stratégie *TrustFrameworkExtensions.xml* et *SignUpOrSigninSAML.xml* dans le portail Azure.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Tester les métadonnées SAML d’IdP Azure AD B2C

Une fois les fichiers de stratégie chargés, Azure AD B2C utilise les informations de configuration pour générer le document de métadonnées SAML du fournisseur d’identité que l’application va utiliser. Le document de métadonnées SAML contient les emplacements de services tels que les méthodes de connexion et déconnexion, les certificats, etc.

Les métadonnées de stratégie Azure AD B2C sont disponibles à l’URL suivante :

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Remplacez `<tenant-name>` par le nom de votre locataire Azure AD B2C. Remplacez `<policy-name>` par le nom (ID) de la stratégie. Voici un exemple :

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Inscrire votre application SAML dans Azure AD B2C

Pour qu’Azure AD B2C approuve votre application, vous créez une inscription d’application Azure AD B2C. L’inscription contient des informations de configuration telles que le point de terminaison des métadonnées de l’application.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, entrez **SAMLApp1**.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans cet annuaire organisationnel uniquement**.
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `https://localhost`. Vous modifierez cette valeur plus tard dans le manifeste de l’inscription d’application.
1. Sélectionnez **Inscription**.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Configurer votre application dans Azure AD B2C

Pour des applications SAML, vous devez configurer plusieurs propriétés dans le manifeste de l’inscription d’application.

1. Dans le [portail Azure](https://portal.azure.com), accédez à l’inscription d’application que vous avez créée dans la section précédente.
1. Sous **Gérer**, sélectionnez **Manifeste** pour ouvrir l’éditeur de manifeste. Modifiez ensuite les propriétés décrites dans les sections suivantes.

#### <a name="add-the-identifier"></a>Ajouter l’identificateur

Lorsque votre application SAML envoie une demande à Azure AD B2C, la demande d’authentification SAML inclut un attribut `Issuer`. La valeur de cet attribut est généralement identique à la valeur `entityID` des métadonnées de l’application. Azure AD B2C utilise cette valeur pour rechercher l’inscription d’application dans le répertoire et lire la configuration. Pour que cette recherche aboutisse, l’`identifierUri` dans l’inscription d’application doit contenir une valeur correspondant à l’attribut `Issuer`.

Dans le manifeste d’inscription, recherchez le paramètre `identifierURIs` et ajoutez la valeur appropriée. Cette valeur sera la même que celle configurée dans les demandes d’authentification SAML pour `EntityId` au niveau de l’application, et la valeur `entityID` dans les métadonnées de l’application. Vous devez également rechercher le paramètre `accessTokenAcceptedVersion` et lui attribuer la valeur `2`.

> [!IMPORTANT]
> Si vous ne remplacez pas la valeur de `accessTokenAcceptedVersion` par `2`, vous recevez un message d’erreur indiquant qu’un domaine vérifié est nécessaire.

L’exemple suivant illustre la valeur `entityID` dans les métadonnées SAML :

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

La propriété `identifierUris` accepte des URL uniquement sur le domaine `tenant-name.onmicrosoft.com`.

```json
"identifierUris":"https://tenant-name.onmicrosoft.com",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Partager les métadonnées de l’application avec Azure AD B2C

Une fois l’inscription d’application chargée par sa valeur `identifierUri`, Azure AD B2C utilise les métadonnées de l’application pour valider la demande d’authentification SAML et déterminer comment répondre.

Nous recommandons que votre application expose un point de terminaison de métadonnées accessible publiquement.

Si des propriétés sont spécifiées *à la fois* dans l’URL des métadonnées SAML et dans le manifeste de l’inscription d’application, elles sont *fusionnées*. Les propriétés spécifiées dans l’URL des métadonnées sont traitées en premier et sont prioritaires.

En utilisant l’application de test SAML comme exemple, vous utiliseriez la valeur suivante pour `samlMetadataUrl` dans le manifeste de l’application :

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Remplacer ou définir l’URL du consommateur d’assertion (facultatif)

Vous pouvez configurer l’URL de réponse à laquelle Azure AD B2C envoie les réponses SAML. Les URL peuvent être configurées dans le manifeste de l’application. Cette configuration est utile quand votre application n’expose pas de point de terminaison de métadonnées accessible publiquement.

L’URL de réponse pour une application SAML est le point de terminaison auquel l’application s’attend à recevoir des réponses SAML. L’application fournit généralement cette URL dans le document de métadonnées, dans l’attribut `Location` de l’élément `AssertionConsumerService`, comme le montre l’exemple suivant :

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Si l’élément `AssertionConsumerService` de métadonnées de l’application est manquant ou si vous souhaitez le remplacer, configurez la propriété `replyUrlsWithType` du manifeste d’inscription d’application. Azure AD B2C utilise `replyUrlsWithType` pour rediriger les utilisateurs une fois qu’ils sont connectés avec le type de liaison `HTTP-POST`.

En utilisant l’application de test SAML comme exemple, vous définiriez la propriété `url` de `replyUrlsWithType` sur la valeur indiquée dans l’extrait de code JSON suivant :

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Remplacer ou définir l’URL de déconnexion (facultatif)

L’URL de déconnexion définit l’emplacement de redirection de l’utilisateur après une demande de déconnexion. L’application fournit généralement cette URL dans le document de métadonnées dans l’attribut `Location` de l’élément `SingleLogoutService`, comme le montre l’exemple suivant :

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</SPSSODescriptor>
```

Si l’élément `SingleLogoutService` de métadonnées de l’application est manquant, configurez la propriété `logoutUrl` du manifeste d’inscription d’application. Azure AD B2C utilise `logoutURL` pour rediriger les utilisateurs une fois qu’ils sont déconnectés avec le type de liaison `HTTP-Redirect`.

Si l’on prend comme exemple l’application de test SAML, on attribue la valeur `https://samltestapp2.azurewebsites.net/logout` à la propriété `logoutUrl` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Si vous choisissez de configurer l’URL de réponse et l’URL de déconnexion dans le manifeste de l’application sans renseigner le point de terminaison des métadonnées de l’application via la propriété `samlMetadataUrl`, Azure AD B2C ne valide pas la signature de la demande SAML. Il ne chiffre pas non plus la réponse SAML.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Configurer Azure AD B2C en tant qu’IdP SAML dans votre application SAML

La dernière étape consiste à activer Azure AD B2C en tant qu’IdP SAML dans votre application SAML. Chaque application est différente et les étapes à suivre varient. Pour plus d’informations, consultez la documentation de votre application.

Vous pouvez configurer les métadonnées dans votre application en tant que *métadonnées statiques* ou *métadonnées dynamiques*. En mode statique, vous copiez tout ou partie des métadonnées à partir des métadonnées de la stratégie Azure AD B2C. En mode dynamique, fournissez l’URL d’accès aux métadonnées et laissez l’application lire les métadonnées de façon dynamique.

Certains ou l’ensemble des éléments suivants sont généralement requis :

* **Métadonnées**: utilisez le format `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata`.
* **Émetteur** : la valeur `issuer` de la demande SAML doit correspondre à l’un des URI configurés dans l’élément `identifierUris` du manifeste d’inscription de l’application. Si le nom `issuer` de la demande SAML n’existe pas dans l’élément `identifierUris`, [ajoutez-le au manifeste d’inscription de l’application](#add-the-identifier). Par exemple : `https://contoso.onmicrosoft.com/app-name`. 
* **URL de connexion, point de terminaison SAML, URL SAML** : vérifiez la valeur dans le fichier de métadonnées de stratégie SAML d’Azure AD B2C pour l’élément XML `<SingleSignOnService>`.
* **Certificat** : ce certificat est *B2C_1A_SamlIdpCert*, mais sans la clé privée. Pour obtenir la clé publique du certificat :

    1. Accédez à l’URL des métadonnées spécifiée ci-dessus.
    1. Copiez la valeur dans l’élément `<X509Certificate>`.
    1. Collez-la dans un fichier texte.
    1. Enregistrez le fichier texte sous la forme d’un fichier *.cer*.

### <a name="test-with-the-saml-test-app"></a>Tester avec l’application de test SAML

Vous pouvez utiliser notre [Application de test SAML][samltest] pour tester votre configuration :

* Mettez à jour le nom du locataire.
* Mettez à jour le nom de la stratégie. Par exemple, utilisez *B2C_1A_signup_signin_adfs*.
* Spécifiez l’URI de l’émetteur. Utilisez l’un des URI figurant dans l’élément `identifierUris` dans le manifeste d’inscription de l’application. Par exemple, utilisez `https://contoso.onmicrosoft.com/app-name`.

Sélectionnez **Connexion**. Un écran de connexion utilisateur doit s’afficher. Une fois que vous êtes connecté, une réponse SAML est renvoyée à l’exemple d’application.

## <a name="supported-and-unsupported-saml-modalities"></a>Modalités SAML prises en charge et non prises en charge

Les scénarios d’application SAML suivants sont pris en charge via votre propre point de terminaison de métadonnées :

* Spécifiez plusieurs URL de déconnexion ou une liaison POST pour l’URL de déconnexion dans l’objet de principal du service ou de l’application.
* Spécifiez une clé de signature pour vérifier les demandes de partie de confiance dans l’objet de principal du service ou de l’application.
* Spécifiez une clé de chiffrement de jeton dans l’objet de principal du service ou de l’application.
* Authentification initiée par le fournisseur d’identité, où le fournisseur d’identité est Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

- Récupérez l’application web SAML test à partir du [référentiel Azure AD B2C de la communauté GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).
- Consultez les [options d’inscription d’une application SAML dans Azure AD B2C](saml-service-provider-options.md).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
