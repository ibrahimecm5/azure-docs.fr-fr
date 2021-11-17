---
title: Tutoriel pour configurer Azure Active Directory B2C avec BlokSec
titleSuffix: Azure AD B2C
description: Découvrez comment intégrer l’authentification Azure AD B2C avec BlokSec pour une authentification sans mot de passe
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8e955a50ed85710c0a3a33680b1edc4ef5873da6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135517"
---
# <a name="tutorial-configure-azure-active-directory-b2c-with-bloksec-for-passwordless-authentication"></a>Tutoriel : configurer Azure Active Directory B2C avec BlokSec pour une authentification sans mot de passe

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

::: zone-end

Dans cet exemple de tutoriel, découvrez comment intégrer l’authentification Azure Active Directory (AD) B2C avec [BlokSec](https://bloksec.com/). BlokSec simplifie l’expérience de connexion de l’utilisateur final en fournissant une authentification sans mot de passe et une authentification multifacteur (MFA) sans jeton pour les clients. BlokSec protège les clients contre les cyberattaques axées sur l’identité, telles que l’usurpation de mot de passe, l’hameçonnage et les attaques de l’intercepteur.

## <a name="scenario-description"></a>Description du scénario

L’intégration BlokSec inclut les composants suivants :

- **Azure AD B2C** : configuré en tant que serveur d’autorisation/fournisseur d’identité pour toute application B2C.

- **Routeur d’identité décentralisé BlokSec** : agit en tant que passerelle pour les services qui souhaitent appliquer DIaaS™ de BlokSec afin d’acheminer les requêtes d’authentification et d’autorisation aux applications PIdP (Personal Identity Provider) des utilisateurs finaux. Elles sont configurées en tant que fournisseur d’identité OpenID Connect (OIDC) dans Azure AD B2C.

- **Application mobile basée sur le kit de développement logiciel (SDK) BlokSec** : agit en tant que PIdP des utilisateurs dans le scénario d’authentification décentralisée. L’application [BlokSec yuID](https://play.google.com/store/apps/details?id=com.bloksec) téléchargeable gratuitement peut être utilisée si votre organisation préfère ne pas développer vos propres applications mobiles à l’aide des kits de développement logiciel (SDK) BlokSec.
Le diagramme d’architecture suivant illustre l’implémentation.

![l’image montre le diagramme d’architecture](./media/partner-bloksec/partner-bloksec-architecture-diagram.png)

|Étapes| Description|
|:---------------|:----------------|
|1.| L’utilisateur tente de se connecter à une application Azure AD B2C et est dirigé vers une stratégie de connexion et d’inscription combinée d’Azure AD B2C.|
|2.| Azure AD B2C redirige l’utilisateur vers le routeur d’identité décentralisé BlokSec à l’aide du flux de code d’autorisation OIDC.|
|3.| Le routeur décentralisé BlokSec envoie une notification push à l’application mobile de l’utilisateur contenant tous les détails du contexte de la requête d’authentification et d’autorisation.|
|4.| L’utilisateur passe en revue la demande d’authentification et, si elle est acceptée, l’utilisateur est invité à fournir une biométrie comme une empreinte digitale ou une numérisation faciale disponible sur son appareil, prouvant ainsi l’identité de l’utilisateur.|
|5.| La réponse est signée numériquement avec la clé numérique unique de l’utilisateur. La réponse d’authentification finale fournit la preuve de possession, de présence et de consentement. La réponse est retournée au routeur d’identité décentralisé BlokSec.|
|6.| Le routeur d’identité décentralisé BlokSec vérifie la signature numérique par rapport à la clé publique unique invariable de l’utilisateur qui est stockée dans un registre distribué, puis répond à Azure AD B2C avec le résultat de l’authentification.|
|7.| En fonction du résultat de l’authentification, l’utilisateur se voit accorder ou refuser l’accès.|

## <a name="onboard-to-bloksec"></a>Intégration à BlokSec

Demandez un locataire de démonstration avec BlokSec en remplissant [le formulaire](https://bloksec.com/). Dans champ du message, indiquez que vous souhaitez intégrer à Azure AD B2C. Téléchargez et installez l’application mobile BlokSec yuID gratuite depuis l’App Store. Lorsque votre locataire de démonstration est préparé, vous recevez un e-mail. Sur votre appareil mobile sur lequel l’application BlokSec est installée, sélectionnez le lien pour inscrire votre compte administrateur auprès de votre application yuID.

::: zone pivot="b2c-user-flow"

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](./tutorial-create-tenant.md) lié à votre abonnement Azure.

- Un [compte d’évaluation](https://bloksec.com/) BlokSec.

- Si ce n’est déjà fait, [inscrivez](./tutorial-register-applications.md) une application web et [activez l’octroi implicite de jeton d’ID](./tutorial-register-applications.md#enable-id-token-implicit-grant).
::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](./tutorial-create-tenant.md) lié à votre abonnement Azure.

- Un [compte d’évaluation](https://bloksec.com/) BlokSec.

- Si ce n’est déjà fait, [inscrivez](./tutorial-register-applications.md) une application web et [activez l’octroi implicite de jeton d’ID](./tutorial-register-applications.md#enable-id-token-implicit-grant).

- Suivez les étapes dans [**Prise en main des stratégies personnalisées dans Azure Active Directory B2C**](./tutorial-create-user-flows.md?pivots=b2c-custom-policy).
::: zone-end

### <a name="part-1---create-an-application-registration-in-bloksec"></a>Partie 1 : Créer une inscription d’application dans BlokSec

1. Connectez-vous au portail d’administration BlokSec. Un lien sera inclus dans l’e-mail d’inscription de votre compte reçu lors de l’intégration à BlokSec.

2. Sur le tableau de bord principal, sélectionnez **Ajouter une application > Créer personnalisé**

3. Renseignez les détails de l’application comme suit et soumettez :  

   |Propriété  |Valeur  |
   |---------|---------|
   |  Name         |Azure AD B2C ou le nom de l’application souhaitée|
   |Type d’authentification unique         | OIDC|
   |URL du logo     |[https://bloksec.io/assets/AzureB2C.png](https://bloksec.io/assets/AzureB2C.png) un lien vers l’image de votre choix|
   |URI de redirection     | https://**nom-de-votre-locataire-B2C**.b2clogin.com/**nom-de-votre-locataire-B2C**.onmicrosoft.com/oauth2/authresp<BR>**Par exemple** :      'https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp ' <BR><BR>Si vous utilisez un domaine personnalisé, entrez https://**nom-de-votre-domaine**/**nom-de-votre-locataire**.onmicrosoft.com/oauth2/authresp. <BR> Remplacez nom-de-votre-domaine par votre domaine personnalisé, et nom-de-votre-locataire par le nom de votre locataire.         |
   |URI de redirection de la déconnexion après la déconnexion  |https://**nom-de-votre-locataire-B2C**.b2clogin.com/**nom-de-votre-locataire-B2C**.onmicrosoft.com/ **{policy}** /oauth2/v2.0/logout <BR> [Envoyez une requête de déconnexion](./openid-connect.md#send-a-sign-out-request). |

4. Une fois l’enregistrement terminé, sélectionnez l’application Azure AD B2C nouvellement créée pour ouvrir la configuration de l’application, puis sélectionnez **Générer le secret d’application**.

>[!NOTE]
>Vous aurez besoin d’un ID d’application et d’un secret d’application ultérieurement pour configurer le fournisseur d’identité dans Azure AD B2C.

::: zone pivot="b2c-user-flow"

### <a name="part-2---add-a-new-identity-provider-in-azure-ad-b2c"></a>Partie 2 : Ajouter un nouveau fournisseur d’identité dans Azure AD B2C

1. Connectez-vous au [portail Azure](https://portal.azure.com/#home) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Accédez à **Tableau de bord** > **Azure Active Directory B2C** > **Fournisseurs d’identité**.
1. Sélectionnez **Nouveau fournisseur OpenID Connect**.
1. Sélectionnez **Ajouter**.

### <a name="part-3---configure-an-identity-provider"></a>Partie 3 : Configurer un fournisseur d’identité

1. Sélectionnez **Type de fournisseur d’identité > OpenID Connect**.

1. Remplissez le formulaire pour configurer le fournisseur d’identité :

|Propriété  |Valeur  |
|:---------|:---------|
|Name     |Entrez BlokSec yuID – Sans mot de passe ou le nom de votre choix|
|URL de métadonnées| `https://api.bloksec.io/oidc/.well-known/openid-configuration` |
|ID client|ID d’application de l’interface utilisateur d’administration BlokSec capturé dans la **Partie 1**|
|Clé secrète client|Secret d’application de l’interface utilisateur d’administration BlokSec capturé dans la **Partie 1**|
|Étendue|Profil de messagerie OpenID|
|Type de réponse|Code|
|Indication du domaine|yuID|

1. Sélectionnez **OK**.

1. Sélectionnez **Mapper les revendications de ce fournisseur d’identité**.

1. Remplissez le formulaire pour mapper le fournisseur d’identité :

|Propriété  |Value  |
|:---------|:---------|
|ID d'utilisateur|sub|
|Nom d’affichage|name|
|Prénom|given_name|
|Surname|family_name|
|E-mail|email|

1. Sélectionnez **Enregistrer** pour terminer la configuration de votre nouveau fournisseur d’identité OIDC.  

### <a name="part-4---user-registration"></a>Partie 4 : Inscription de l’utilisateur

1. Connectez-vous à la console d’administration BlokSec avec les informations d’identification fournies précédemment.

1. Accédez à l’application Azure AD B2C créée précédemment. Sélectionnez l’icône d’engrenage en haut à droite, puis sélectionnez **Créer un compte**.  

1. Entrez les informations de l’utilisateur dans le formulaire de création de compte, en prenant note du nom du compte, puis sélectionnez **Soumettre**.  

L’utilisateur recevra un **e-mail d’inscription de compte** à l’adresse e-mail indiquée. Demandez à l’utilisateur de suivre le lien d’inscription sur l’appareil mobile sur lequel l’application BlokSec yuID est installée,

### <a name="part-5---create-a-user-flow-policy"></a>Partie 5 : créer une stratégie de flux d’utilisateur

Vous devriez maintenant voir BlokSec répertorié comme nouveau fournisseur d’identité OIDC parmi vos fournisseurs d’identité B2C.  

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs** sous **Stratégies**.  

1. Sélectionnez **Nouveau flux d’utilisateur**.

1. Sélectionnez **Inscription et connexion** > **Version** > **Créer**.

1. Sous **Nom**, spécifiez un nom pour votre stratégie.

1. Dans la section Fournisseurs d’identité, sélectionnez le fournisseur d’identité BlokSec que vous venez de créer.  

1. Sélectionnez **Aucun** pour les comptes locaux afin de désactiver l’authentification basée sur un e-mail et un mot de passe.

1. Sélectionnez **Exécuter le flux d’utilisateur**.

1. Dans le formulaire, entrez l’URL de réponse, par exemple `https://jwt.ms`.

1. Le navigateur est redirigé vers la page de connexion BlokSec. Entrez le nom du compte inscrit lors de l’inscription de l’utilisateur. L’utilisateur recevra une notification push sur son appareil mobile sur lequel l’application BlokSec yuID est installée. Lors de l’ouverture de la notification, l’utilisateur recevra une demande d’authentification.

1. Une fois la demande d’authentification acceptée, le navigateur redirige l’utilisateur vers l’URL de réponse.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Stratégies personnalisées dans Azure AD B2C](./custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end

::: zone pivot="b2c-custom-policy"

>[!NOTE]
>Dans Azure Active Directory B2C, les [**stratégies personnalisées**](./user-flow-overview.md) sont principalement conçues pour gérer des scénarios complexes. Pour la plupart des scénarios, nous vous recommandons de recourir à des [**flux d’utilisateurs**](./user-flow-overview.md) intégrés.

### <a name="part-2---create-a-policy-key"></a>Partie 2 : Créer une clé de stratégie

Stockez la clé secrète client que vous avez enregistrée dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
1. Pour **Options**, choisissez `Manual`.
1. Entrez un **nom** pour la clé de stratégie. Par exemple : `BlokSecAppSecret`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
1. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.
1. Pour **Utilisation de la clé**, sélectionnez `Signature`.
1. Sélectionnez **Create** (Créer).

### <a name="part-3---configure-bloksec-as-an-identity-provider"></a>Partie 3 : Configurer BlokSec en tant que fournisseur d’identité

Pour permettre aux utilisateurs de se connecter avec une identité décentralisée BlokSec, vous devez définir BlokSec en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer via un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié à l’aide d’une biométrie telle qu’une empreinte digitale ou une numérisation faciale disponible sur son appareil, prouvant ainsi l’identité de l’utilisateur.

Vous pouvez définir BlokSec comme fournisseur de revendications en l’ajoutant à l’élément **ClaimsProvider** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier `TrustFrameworkExtensions.xml`.

2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.

3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>bloksec</Domain>
      <DisplayName>BlokSec</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="BlokSec-OpenIdConnect">
          <DisplayName>BlokSec</DisplayName>
          <Description>Login with your BlokSec decentriled identity</Description>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://api.bloksec.io/oidc/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the BlokSec Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://api.bloksec.io/oidc</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_BlokSecAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Définissez **client_id** sur l’ID d’application de l’inscription de l’application.

5. Enregistrez le fichier .

### <a name="part-4---add-a-user-journey"></a>Partie 4 : Ajouter un parcours utilisateur

À ce stade, le fournisseur d’identité a été configuré, mais il n’est encore disponible dans aucune des pages de connexion. Si vous n’avez pas votre propre parcours utilisateur personnalisé, créez un doublon d’un modèle de parcours utilisateur existant ; sinon, passez à l’étape suivante.  

1. Ouvrez le fichier `TrustFrameworkBase.xml` à partir du pack de démarrage.

2. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourneys** comprenant l’ID `SignUpOrSignIn`.

3. Ouvrez `TrustFrameworkExtensions.xml` et recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.

4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.

5. Renommez l’ID du parcours utilisateur. Par exemple, ID = `CustomSignUpSignIn`.  

### <a name="part-5---add-the-identity-provider-to-a-user-journey"></a>Partie 5 : Ajouter le fournisseur d’identité à un parcours utilisateur

Maintenant que vous disposez d’un parcours utilisateur, ajoutez-y le nouveau fournisseur d’identité. Ajoutez d’abord un bouton de connexion, puis liez le bouton à une action. L’action représente le profil technique que vous avez créé plus haut.  

1. Recherchez l’élément d’étape d’orchestration comprenant Type = `CombinedSignInAndSignUp` ou Type = `ClaimsProviderSelection` dans le parcours utilisateur. Il s’agit généralement de la première étape d’orchestration. L’élément **ClaimsProviderSelections** contient une liste de fournisseurs d’identité auxquels un utilisateur peut se connecter. L’ordre des éléments détermine l’ordre des boutons de connexion présentés à l’utilisateur. Ajoutez un élément XML **ClaimsProviderSelection**. Définissez la valeur **TargetClaimsExchangeId** sur un nom convivial.

2. À la prochaine étape d’orchestration, ajoutez un élément **ClaimsExchange**. Définissez l’**ID** sur la valeur de l’ID cible d’échange des revendications. Mettez à jour la valeur de **TechnicalProfileReferenceId** sur l’ID du profil technique que vous avez créé.

Le code XML suivant montre les deux premières étapes d’orchestration d’un parcours utilisateur avec le fournisseur d’identité :

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="BlokSecExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="BlokSecExchange" TechnicalProfileReferenceId="BlokSec-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>Partie 6 : Configurer la stratégie de partie de confiance

La stratégie de partie de confiance, par exemple [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), spécifie le parcours utilisateur à partir duquel Azure AD B2C s’exécutera. Recherchez l’élément **DefaultUserJourney** dans la partie de confiance. Mettez à jour la valeur **ReferenceId** afin qu’elle corresponde à l’ID du parcours utilisateur auquel vous avez ajouté le fournisseur d'identité.

Dans l’exemple suivant, pour le parcours utilisateur `CustomSignUpOrSignIn`, la valeur ReferenceId est définie sur `CustomSignUpOrSignIn`.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>Partie 7 : Charger la stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com/#home).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le [portail Azure](https://portal.azure.com/#home), recherchez et sélectionnez **Azure AD B2C**.
1. Sous Stratégies, sélectionnez **Identity Experience Framework**.
Sélectionnez **Charger une stratégie personnalisée**, puis chargez les deux fichiers de stratégie que vous avez modifiés, dans l’ordre suivant : la stratégie d’extension, par exemple `TrustFrameworkExtensions.xml`, puis la stratégie de la partie de confiance, par exemple `SignUpSignIn.xml`.

### <a name="part-8---test-your-custom-policy"></a>Partie 8 : Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](./tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **Google** pour vous connecter avec un compte Google.

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Stratégies personnalisées dans Azure AD B2C](./custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end
