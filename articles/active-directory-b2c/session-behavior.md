---
title: Configurer le comportement de session - Azure Active Directory B2C
description: Découvrez comment configurer le comportement de session dans Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/05/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 59d14afdbe6f4949f2761ddccb3a48a4770c3bd1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028174"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurer le comportement de session dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Lorsque les utilisateurs s’authentifient auprès d’applications dans Azure Active Directory B2C (Azure AD B2C), l’authentification unique (SSO) constitue la méthode la plus sécurisée et la plus pratique. Cet article décrit les différentes méthodes d’authentification unique utilisées dans Azure AD B2C et vous aide à choisir la méthode SSO la plus appropriée lors de la configuration de votre stratégie.

Avec l’authentification unique, les utilisateurs se connectent une seule fois avec un seul compte et accèdent à plusieurs applications. L’application peut être une application web, mobile ou monopage, indépendamment du nom de la plateforme ou du domaine.

Lorsque l’utilisateur se connecte pour la première fois à une application, Azure AD B2C maintient une session basée sur un cookie. Lors des demandes d’authentification suivantes, Azure AD B2C lit et valide la session basée sur un cookie et émet un jeton d’accès sans inviter l’utilisateur à se reconnecter. Si la session basée sur un cookie expire ou devient non valide, l’utilisateur est invité à se connecter à nouveau.  

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Vue d’ensemble de la session Azure AD B2C

L’intégration avec Azure AD B2C implique trois types de sessions SSO :

- **Azure AD B2C** : session gérée par Azure AD B2C
- **Fournisseur d’identité fédéré** : session gérée par le fournisseur d’identité, par exemple Facebook, Salesforce ou compte Microsoft
- **Application** : session gérée par l’application web, mobile ou monopage

![Session SSO](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Session Azure AD B2C 

Quand un utilisateur réussit à s’authentifier avec un compte local ou social, Azure AD B2C stocke une session basée sur un cookie sur le navigateur de l’utilisateur. Le cookie est stocké sous le nom de domaine du locataire Azure AD B2C, par exemple, `https://contoso.b2clogin.com`.

Si un utilisateur se connecte initialement avec un compte fédéré, puis, pendant la fenêtre de temps de la session (durée de vie ou TTL), se connecte à la même application ou à une autre application, Azure AD B2C essaie d’acquérir un nouveau jeton d’accès auprès du fournisseur d’identité fédéré. Si la session du fournisseur d’identité fédéré a expiré ou n’est pas valide, le fournisseur d’identité fédéré invite l’utilisateur à entrer ses informations d’identification. Si la session est toujours active (ou si l’utilisateur s’est connecté avec un compte local au lieu d’un compte fédéré), Azure AD B2C autorise l’utilisateur et élimine les invites supplémentaires.

Vous pouvez configurer le comportement de la session, dont sa durée de vie et la façon dont Azure AD B2C partage la session entre les stratégies et les applications.

### <a name="federated-identity-provider-session"></a>Session du fournisseur d’identité fédéré

Un fournisseur d’identité de réseaux sociaux ou d’entreprise gère sa propre session. Le cookie est stocké sous le nom de domaine du fournisseur d’identité, par exemple `https://login.salesforce.com`. Azure AD B2C ne contrôle pas la session du fournisseur d’identité fédéré. Au lieu de cela, le comportement de session est déterminé par le fournisseur d’identité fédéré. 

Examinez le cas suivant :

1. Un utilisateur se connecte à Facebook pour vérifier son flux.
2. Plus tard, l’utilisateur ouvre votre application et démarre le processus de connexion. L’application redirige l’utilisateur vers Azure AD B2C pour terminer le processus de connexion.
3. Sur la page d’inscription ou de connexion d’Azure AD B2C, l’utilisateur choisit de se connecter avec son compte Facebook. L’utilisateur est redirigé vers Facebook. S’il existe une session active sur Facebook, l’utilisateur n’est pas invité à fournir ses informations d’identification et est immédiatement redirigé vers Azure AD B2C avec un jeton Facebook.

### <a name="application-session"></a>Session d’application

Une application web, mobile ou monopage peut être protégée par un jeton d’accès OAuth2, un jeton d’ID ou un jeton SAML. Quand un utilisateur tente d’accéder à une ressource protégée sur l’application, celle-ci vérifie s’il existe une session active côté application. S’il n’y a pas de session d’application ou si la session a expiré, l’application dirige l’utilisateur vers la page de connexion d’Azure AD B2C.

La session d’application peut être une session basée sur un cookie stockée sous le nom de domaine de l’application, par exemple `https://contoso.com`. Les applications mobiles peuvent stocker la session d’une façon différente, mais dont l’approche est similaire.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Configurer le comportement de la session Azure AD B2C

Vous pouvez configurer le comportement de la session Azure AD B2C, notamment les éléments suivants :

- **Durée de vie de la session de l’application web (minutes)**  : durée pendant laquelle le cookie de session Azure AD B2C est stocké sur le navigateur de l’utilisateur après une authentification réussie. Vous pouvez définir la durée de vie de la session à 24 heures maximum.

- **Délai d’expiration de session d’application web** : indique comment une session est étendue par le paramètre de durée de vie de la session ou par le paramètre Maintenir la connexion.
  - **Continue** : indique que la session est étendue chaque fois que l’utilisateur effectue une authentification basée sur un cookie (valeur par défaut).
  - **Absolue** : indique que l’utilisateur est obligé de se réauthentifier après la période spécifiée.

- **Configuration de l’authentification unique** : vous pouvez configurer la session Azure AD B2C avec les étendues suivantes :
  - **Client** : il s’agit du paramètre par défaut. L’utilisation de ce paramètre permet à plusieurs applications et flux d’utilisateur dans votre locataire B2C de partager la même session utilisateur. Par exemple, quand un utilisateur se connecte à une application, il peut également se connecter sans problème à une autre en y accédant.
  - **Application**: ce paramètre vous permet de maintenir une session utilisateur exclusivement pour une application, indépendamment des autres applications. Par exemple, vous pouvez utiliser ce paramètre si vous voulez que l’utilisateur se connecte à Contoso Pharmacy, que l’utilisateur y soit ou non déjà connecté.
  - **Stratégie** : ce paramètre vous permet de maintenir une session utilisateur exclusivement pour un flux d’utilisateur, indépendamment des applications qui l’utilisent. Par exemple, si l’utilisateur s’est déjà connecté et a effectué une étape d’authentification multifacteur, il peut obtenir l’accès à des parties plus sécurisées de plusieurs applications tant que la session liée au flux d’utilisateur n’expire pas.
  - **Supprimé** : ce paramètre oblige l’utilisateur à réexécuter tout le flux d’utilisateur à chaque exécution de la stratégie.
- **Maintenir la connexion** : étend la durée de vie de la session à l’aide d’un cookie persistant. Si cette fonctionnalité est activée et que l’utilisateur la sélectionne, la session reste active même après que l’utilisateur a fermé et rouvert le navigateur. La session est révoquée uniquement quand un utilisateur se déconnecte. La fonctionnalité Maintenir la connexion s’applique uniquement à la connexion avec des comptes locaux. La fonctionnalité Maintenir la connexion est prioritaire sur la durée de vie de la session.

::: zone pivot="b2c-user-flow"

Pour configurer le comportement de la session :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Ouvrez le flux utilisateur que vous avez créé précédemment.
1. Sélectionner **Propriétés**.
1. Configurez **Durée de vie de la session de l’application web (minutes)** , **Délai d’expiration de la session de l’application web** , **Configuration de l’authentification unique** et **Exiger un jeton d’ID dans les demandes de déconnexion**, selon les besoins.
1. Cliquez sur **Enregistrer**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Pour changer le comportement des sessions et les configurations de SSO, vous devez ajouter un élément **UserJourneyBehaviors** à l’intérieur de l’élément [RelyingParty](relyingparty.md).  L’élément **UserJourneyBehaviors** doit suivre immédiatement l’élément **DefaultUserJourney**. Votre élément **UserJourneyBehaviors** devrait ressembler à l’exemple suivant :

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>Activer le maintien de la connexion (KMSI)

Vous pouvez activer la fonctionnalité Maintenir la connexion pour les utilisateurs de vos applications web et natives qui ont des comptes locaux dans votre répertoire Azure AD B2C. Lorsque vous activez la fonctionnalité, les utilisateurs peuvent choisir de rester connectés afin que la session reste active après avoir fermé le navigateur. La session est maintenue en définissant un [cookie persistant](cookie-definitions.md). Les utilisateurs qui sélectionnent KMSI peuvent ensuite rouvrir le navigateur sans avoir à entrer à nouveau leur nom d’utilisateur et leur mot de passe. Cet accès (cookie persistent) est révoqué lorsque l’utilisateur se déconnecte. 

![Exemple de page de connexion avec la case à cocher Maintenir la connexion](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

La fonction Maintenir la connexion est configurable au niveau du flux de l’utilisateur individuel. Avant d’activer la fonction Maintenir la connexion pour vos flux d’utilisateurs, prenez en compte les éléments suivants :

- Maintenir la connexion est pris en charge uniquement pour les versions **recommandées** des flux d’utilisateur d’inscription et de connexion (SUSI), de connexion et de modification de profil. Si vous disposez actuellement de versions **Standard (hérité)** ou **Préversion héritée (v2)** de ces flux d’utilisateur et que vous voulez activer Maintenir la connexion, vous devrez créer des versions **recommandées** de ces flux.
- Maintenir la connexion n’est pas pris en charge avec les flux d’utilisateur de réinitialisation de mot de passe ou d’inscription.
- Si vous souhaitez activer Maintenir la connexion pour toutes les applications de votre locataire, nous vous recommandons d’activer cette fonctionnalité pour tous les flux d’utilisateurs de votre locataire. Étant donné qu’un utilisateur peut être présenté avec plusieurs stratégies au cours d’une session, il est possible qu’un utilisateur n’ait pas la fonction Maintenir la connexion activée, ce qui supprime le cookie Maintenir la connexion de la session.
- La fonction Maintenir la connexion ne doit pas être activée sur les ordinateurs publics.

### <a name="configure-kmsi-for-a-user-flow"></a>Configurer Maintenir la connexion pour un flux utilisateur

Pour activer Maintenir la connexion pour votre flux utilisateur :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez  **Tous les services**  dans le coin supérieur gauche du Portail Azure, puis recherchez et sélectionnez  **Azure AD B2C**.
1. Sélectionnez  **Flux utilisateur (stratégies)** .
1. Ouvrez le flux utilisateur que vous avez créé précédemment.
1. Sélectionnez  **Propriétés**.

1. Sous  **Comportement de session**, sélectionnez **Activer la session Maintenir la connexion**. En regard de **Maintenir la session de connexion (jours)** , entrez une valeur comprise entre 1 et 90 pour indiquer le nombre de jours pendant lesquels une session peut rester ouverte.

   ![Activer Maintenir ma connexion dans la session](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Vous ne devez pas activer cette option sur les ordinateurs publics.

### <a name="configure-the-page-identifier"></a>Configurer l’identificateur de page

Pour activer la fonctionnalité « Maintenir la connexion », définissez l’élément `DataUri` de définition de contenu sur l’[identificateur de page](contentdefinitions.md#datauri) `unifiedssp` et la [version de page](page-layout.md) *1.1.0* ou ultérieure.

1. Ouvrez le fichier d’extension de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>. Ce fichier d’extension est un des fichiers de stratégie inclus dans le pack de démarrage des stratégies personnalisées, que vous avez dû obtenir en suivant la rubrique prérequise [Bien démarrer avec les stratégies personnalisées](tutorial-create-user-flows.md?pivots=b2c-custom-policy).
1. Recherchez l’élément **BuildingBlocks**. Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez l’élément **ContentDefinitions** à l’élément **BuildingBlocks** de la stratégie.

    Votre stratégie personnalisée doit se présenter comme l’extrait de code suivant :

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Ajouter les métadonnées au profil technique autodéclaré

Pour ajouter la case à cocher Maintenir la connexion dans les pages d’inscription et de connexion, définissez les métadonnées `setting.enableRememberMe` sur true. Remplacez les profils techniques SelfAsserted-LocalAccountSignin-Email dans le fichier d’extension.

1. Recherchez l’élément ClaimsProviders. Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez le fournisseur de revendications suivant à l’élément ClaimsProviders :

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Enregistrez le fichier d’extensions.

### <a name="configure-a-relying-party-file"></a>Configurer un fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé. Le paramètre keepAliveInDays vous permet de configurer la façon dont le cookie de session de maintien de la connexion (KMSI) doit persister. Par exemple, si vous définissez la valeur sur 30, le cookie de session KMSI sera conservé pendant 30 jours. La plage de la valeur est comprise entre 1 et 90 jours. Affecter la valeur 0 désactive la fonctionnalité KMSI.

1. Ouvrez votre fichier de stratégie personnalisée. Par exemple *SignUpOrSignin.xml*.
1. S’il n’existe pas déjà, ajoutez un nœud enfant `<UserJourneyBehaviors>` au nœud `<RelyingParty>`. Il doit être placé immédiatement après `<DefaultUserJourney ReferenceId="User journey Id" />`, par exemple : `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`.
1. Ajoutez le nœud suivant en tant qu’enfant de l’élément `<UserJourneyBehaviors>`.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

Nous vous recommandons de définir la valeur de SessionExpiryInSeconds sur une courte période (1 200 secondes), tandis que vous pouvez définir la valeur de KeepAliveInDays sur une période relativement longue (30 jours), comme l’illustre l’exemple suivant :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>Se déconnecter

Quand vous souhaitez déconnecter l’utilisateur de l’application, il ne suffit pas de supprimer les cookies de l’application ou d’arrêter la session de l’utilisateur. Vous devez rediriger l’utilisateur vers Azure AD B2C pour le déconnecter. Autrement, l’utilisateur peut être en mesure de se réauthentifier auprès de votre application sans entrer à nouveau ses informations d’identification.

Lors d’une demande de déconnexion, Azure AD B2C effectue les opérations suivantes :

::: zone pivot="b2c-user-flow"
1. Invalide la session Azure AD B2C basée sur un cookie.
1. Tente de se déconnecter des fournisseurs d’identité fédérés.
::: zone-end

::: zone pivot="b2c-custom-policy"
1. Invalide la session Azure AD B2C basée sur un cookie.
1. Tente de se déconnecter des fournisseurs d’identité fédérés :
   - OpenId Connect : si le point de terminaison de configuration connu du fournisseur d’identité spécifie un emplacement `end_session_endpoint`. La demande de déconnexion ne passe pas le paramètre `id_token_hint`. Si le fournisseur d’identité fédérée requiert ce paramètre, la demande de déconnexion échoue.
   - OAuth2 : si les [métadonnées du fournisseur d’identité](oauth2-technical-profile.md#metadata) contiennent l’emplacement `end_session_endpoint`.
   - SAML : si les [métadonnées du fournisseur d’identité](identity-provider-generic-saml.md) contiennent l’emplacement `SingleLogoutService`.
1. Si vous le souhaitez, se déconnecte d’autres applications. Pour plus d’informations, consultez la section [Déconnexion unique](#single-sign-out).

> [!NOTE]
> Vous pouvez désactiver la déconnexion des fournisseurs d’identité fédérés en définissant les métadonnées du profil technique du fournisseur d’identité `SingleLogoutEnabled` sur `false`.
::: zone-end

La déconnexion efface l’état d’authentification unique de l’utilisateur auprès d’Azure AD B2C, mais il est possible qu’elle ne déconnecte pas l’utilisateur de sa session de fournisseur d’identité sociale. Si l’utilisateur sélectionne le même fournisseur d’identité lors d’une connexion ultérieure, il pourrait se réauthentifier sans avoir à entrer ses informations d’identification. Si un utilisateur veut se déconnecter de l’application, cela ne signifie pas nécessairement qu’il souhaite se déconnecter de son compte Facebook. Toutefois, si les comptes locaux sont utilisés, la session de l’utilisateur se termine correctement.

::: zone pivot="b2c-custom-policy"

## <a name="single-sign-out"></a>Déconnexion unique 

Quand vous redirigez l’utilisateur vers le [point de terminaison de déconnexion Azure AD B2C](openid-connect.md#send-a-sign-out-request) (pour OAuth2 et OpenID Connect) ou que vous envoyez une `LogoutRequest` (pour SAML), Azure AD B2C efface la session de l’utilisateur du navigateur. Toutefois, l’utilisateur pourrait rester connecté à d’autres applications utilisant Azure AD B2C pour l’authentification. Pour déconnecter l’utilisateur de toutes les applications qui ont une session active, Azure AD B2C prend en charge la *déconnexion unique*, également appelée *SLO (Single Log-Out)* .

Lors de la déconnexion, Azure AD B2C envoie simultanément une requête HTTP à l’URL de déconnexion inscrite de toutes les applications auxquelles l’utilisateur est actuellement connecté.

### <a name="configure-your-custom-policy"></a>Configurer votre stratégie personnalisée

Pour prendre en charge la déconnexion unique, les profils techniques de l’émetteur de jetons pour JWT et SAML doivent spécifier les éléments suivants :

- Le nom du protocole, tel que `<Protocol Name="OpenIdConnect" />`
- La référence au profil technique de session, tel que `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />`.

L’exemple suivant illustre les émetteurs de jetons JWT et SAML avec la fonctionnalité de déconnexion unique :

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="configure-your-application"></a>Configuration de votre application

Pour qu’une application participe à la déconnexion unique :

- Pour les [fournisseurs de services SAML](saml-service-provider.md), configurez l’application avec l’[emplacement SingleLogoutService dans son document de métadonnées SAML](saml-service-provider.md#override-or-set-the-logout-url-optional). Vous pouvez aussi configurer la `logoutUrl` de l’inscription de l’application. Pour plus d’informations, consultez [Défini l’URL de déconnexion](saml-service-provider.md#override-or-set-the-logout-url-optional).
- Pour les applications OpenID Connect ou OAuth2, définissez l’attribut `logoutUrl` du manifeste d’inscription de votre application. Pour configurer l’URL de déconnexion :
    1. Dans le menu Azure AD B2C, sélectionnez **Inscriptions d’applications**.
    1. Sélectionnez votre inscription d’application.
    1. Sous **Gérer**, sélectionnez **Authentification**.
    1. Sous l’**URL de déconnexion du canal frontal**, configurez votre URL de déconnexion.

### <a name="handling-single-sign-out-requests"></a>Gestion des demandes de déconnexion unique

Quand Azure AD B2C reçoit la demande de déconnexion, il utilise un iframe HTML de canal frontal pour envoyer une requête HTTP à l’URL de déconnexion inscrite de chaque application participante à laquelle l’utilisateur est actuellement connecté. Notez que l’application qui déclenche la demande de déconnexion ne recevra pas ce message de déconnexion. Vos applications doivent répondre à la demande de déconnexion en effaçant la session d’application qui identifie l’utilisateur.

- Pour les applications OpenID Connect et OAuth2, Azure AD B2C envoie une requête HTTP GET à l’URL de déconnexion inscrite.
- Pour les applications SAML, Azure AD B2C envoie une requête de déconnexion SAML à l’URL de déconnexion inscrite.

Quand toutes les applications ont été informées de la déconnexion, Azure AD B2C effectue une des opérations suivantes :

- Pour les applications OpenID Connect ou OAuth2, l’utilisateur est redirigé vers l’URI `post_logout_redirect_uri` demandée incluant le paramètre (facultatif) `state` spécifié dans la demande initiale. Par exemple, `https://contoso.com/logout?state=foo`.
- Pour les applications SAML, une réponse de déconnexion SAML est envoyée via HTTP POST à l’application qui a initialement envoyé la demande de déconnexion.

::: zone-end

### <a name="secure-your-logout-redirect"></a>Sécuriser la redirection de déconnexion

Après la déconnexion, l’utilisateur est redirigé vers l’URI spécifié dans le paramètre `post_logout_redirect_uri`, quelles que soient les URL de réponse qui ont été spécifiées pour l’application. Cependant, si un `id_token_hint` valide est transmis et que l’option **Exiger un jeton d’ID dans les demandes de déconnexion** est activée, Azure AD B2C vérifie que la valeur de `post_logout_redirect_uri` correspond à l’un des URI de redirection configurés de l’application avant d’effectuer la redirection. Si aucune URL de réponse correspondante n’a été configurée pour l’application, un message d’erreur s’affiche et l’utilisateur n’est pas redirigé. 

::: zone pivot="b2c-user-flow"

Pour exiger un jeton d’ID dans les demandes de déconnexion :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Ouvrez le flux utilisateur que vous avez créé précédemment.
1. Sélectionner **Propriétés**.
1. Activez l’option **Exiger un jeton d’ID dans les demandes de déconnexion**.
1. Revenez à **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis sélectionnez votre application.
1. Sélectionnez **Authentification**.
1. Dans la zone de texte **URL de déconnexion**, saisissez votre URI de redirection après déconnexion, puis sélectionnez **Enregistrer**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Pour exiger un jeton d’ID dans les demandes de déconnexion, ajoutez un élément **UserJourneyBehaviors** à l’intérieur de l’élément [RelyingParty](relyingparty.md). Définissez ensuite **EnforceIdTokenHintOnLogout** dans l’élément **SingleSignOn** sur `true`. Votre élément **UserJourneyBehaviors** devrait ressembler à l’exemple suivant :

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

Pour configurer l’URL de déconnexion de votre application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis sélectionnez votre application.
1. Sélectionnez **Authentification**.
1. Dans la zone de texte **URL de déconnexion**, saisissez votre URI de redirection après déconnexion, puis sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [configurer les jetons dans Azure AD B2C](configure-tokens.md).
