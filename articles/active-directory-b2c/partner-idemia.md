---
title: Configurer IDEMIA avec Azure Active Directory B2C (Préversion)
titleSuffix: Azure AD B2C
description: Découvrez comment intégrer l’authentification Azure AD B2C à IDEMIA pour une partie de confiance afin de consommer des ID mobiles émis par IDEMIA ou par un état des États-Unis
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 27c83d2ade4552150793aa8fbe777e376a2ae762
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012874"
---
# <a name="tutorial-configure-idemia-with-azure-active-directory-b2c-for-relying-party-to-consume-idemia-or-us-state-issued-mobile-identity-credentials-preview"></a>Tutoriel : Configurer IDEMIA avec Azure Active Directory B2C pour une partie de confiance afin de consommer des informations d’identification d’identité mobiles émises par IDEMIA ou par un état des États-Unis (Préversion)
[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

Cette fonctionnalité est disponible uniquement pour les stratégies personnalisées. Pour accéder aux étapes de configuration, sélectionnez **Stratégie personnalisée** dans le sélecteur précédent.

::: zone-end

::: zone pivot="b2c-custom-policy"

Dans cet exemple du tutoriel, découvrez comment intégrer Azure Active Directory (Azure AD) B2C à [IDEMIA](https://www.idemia.com/). IDEMIA est un fournisseur d’authentification sans mot de passe, qui fournit des services basés sur le consentement en temps réel avec une authentification biométrique comme un ID de visage et une empreinte digitale, éliminant la fraude et la réutilisation des informations d’identification. L’ID mobile d’IDEMIA permet aux citoyens de tirer parti d’un ID numérique approuvé par l’administration, en complément de leur ID physique. Cette application est utilisée pour vérifier l’identité avec un code PIN ou un ID tactile/ID facial choisi par l’utilisateur. L’ID mobile permet aux citoyens de contrôler leur identité en leur permettant de partager seulement les informations nécessaires pour une transaction et en permettant une protection contre les fraudes.



## <a name="scenario-description"></a>Description du scénario

L’intégration d’IDEMIA inclut les composants suivants :

- **Azure AD B2C** : serveur d’autorisation chargé de vérifier les informations d’identification de l’utilisateur. Également appelé fournisseur d’identité.

- **IDEMIA mID** - Fournisseur OIDC (OpenID Connect) configuré en tant que [fournisseur externe Azure AD B2C](add-identity-provider.md)

- **[Application IDEMIA mID](https://idemia-mobile-id.com/)** - Une identité numérique approuvée par l’administration. L’ID mobile est une version numérique de votre permis de conduire ou de votre ID émis par l’administration, qui se trouve dans une application sur votre téléphone. [IDEMIA](https://idemia-mobile-id.com/).

IDEMIA fournit mID pour de nombreux départements des véhicules motorisés dans les états des États-Unis.

Le mID est la numérisation d’un document d’identification en un jeton d’identité mobile fort qui est hautement portable pour la vérification et qui sert d’index pour les **services mID** d’autorisation. Le service mID permet aux départements des véhicules motorisés de vérifier les identités des personnes avec une authentification des documents d’informations d’identification effectuée en utilisant leur permis de conduire et les services de reconnaissance faciale biométrique de **selfie** valant informations d’identification.  

Une fois créé, le mID est stocké sur le téléphone mobile de l’utilisateur final sous la forme d’une **identité sur la périphérie** signée numériquement. Les utilisateurs finaux peuvent désormais utiliser ces informations d’identification signées pour accéder à d’autres services sensibles à l’identité, comme la preuve d’âge, la reconnaissance de votre client pour des opérations financières et l’accès à des comptes là où la sécurité est primordiale.

L’offre pour Microsoft est la prise en charge de ces services en tant que partie de confiance qui va utiliser un mID émis par l’administration pour fournir des services en utilisant les attributs envoyés par le propriétaire du mID.

Le diagramme suivant montre l’implémentation pour des scénarios web ou locaux :

![Capture d’écran montrant la vérification locale](./media/partner-idemia/idemia-architecture-diagram.png)

| Étape | Description |
|:--------|:--------|
| 1. | L’utilisateur visite la page de connexion Azure AD B2C, qui est la partie de confiance dans ce cas sur son appareil pour effectuer une transaction et se connecter via son application mID. |
| 2. | Azure AD B2C demande une vérification d’ID et redirige pour cela l’utilisateur vers le routeur IDEMIA en utilisant le flux de code d’autorisation OIDC|
| 3. | Le routeur IDEMIA envoie une demande biométrique à l’application mobile de l’utilisateur contenant tous les détails du contexte de la demande d’authentification et d’autorisation.|
| 4. | En fonction du niveau de sécurité nécessaire, l’utilisateur peut avoir besoin de fournir des détails supplémentaires, d’entrer son code PIN, de prendre un selfie immédiatement, ou les deux.|
| 5. | La réponse d’authentification finale fournit la preuve de possession, de présence et de consentement. La réponse est retournée au routeur IDEMIA.|
| 6. | Le routeur IDEMIA vérifie les informations fournies par l’utilisateur et répond à Azure AD B2C avec le résultat de l’authentification.|
|7. | En fonction du résultat de l’authentification, l’utilisateur se voit accorder ou refuser l’accès. |

## <a name="onboard-with-idemia"></a>Intégration à IDEMIA

Contactez [IDEMIA](https://www.idemia.com/get-touch/) pour demander une démonstration. Quand vous remplissez le formulaire de demande, dans le champ Message, indiquez que vous souhaitez effectuer une intégration à Azure AD B2C.

## <a name="integrate-idemia-with-azure-ad-b2c"></a>Intégrer IDEMIA à Azure AD B2C

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Accès à des utilisateurs finaux qui ont des informations d’identification d’ID mobile émises par un état des États-Unis ou par IDEMIA ou, pendant la phase de test, l’application de démonstration mID fournie par [IDEMIA](https://www.idemia.com/).

- Un abonnement Azure AD Si vous n’en avez pas, créez un  [compte gratuit](https://azure.microsoft.com/free/).

- Un  [locataire Azure AD B2C](tutorial-create-tenant.md)  lié à votre abonnement Azure.

- Votre application web d’entreprise inscrite dans le locataire Azure AD B2C. À des fins de test, vous pouvez configurer https://jwt.ms, une application web appartenant à Microsoft qui affiche le contenu décodé d’un jeton.

>[!NOTE]
>Le contenu du jeton ne quitte jamais votre navigateur.


### <a name="part-1---submit-a-relying-party-application-on-boarding-for-mid"></a>Partie 1 : Soumettre une intégration d’application de partie de confiance pour mID

Dans le cadre de votre intégration à IDEMIA, vous allez recevoir les informations suivantes :

| Propriété | Description |
|:---------|:----------|
| Nom de l’application | Azure AD B2C ou le nom de l’application souhaitée |
| Client_ID | C’est l’identificateur unique fourni par le fournisseur d’identité |
| Clé secrète client | Mot de passe utilisé par l’application de la partie de confiance pour s’authentifier auprès du fournisseur d’identité IDEMIA |
| Point de terminaison de métadonnées | URL qui pointe vers un document de configuration d’émetteur de jeton, également appelé point de terminaison de configuration OpenID connu. |
|URI de redirection | `https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp`<br>Par exemple, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`<br><br>Si vous utilisez un domaine personnalisé, entrez `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`.<br>Remplacez nom-de-votre-domaine par votre domaine personnalisé, et nom-de-votre-locataire par le nom de votre locataire. |
|URI de redirection de la déconnexion après la déconnexion | `https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/{policy}/oauth2/v2.0/logout`<br>Envoyez une demande de déconnexion. |

>[!NOTE]
>Vous aurez besoin ultérieurement de l’ID client et de la clé secrète client IDEMIA pour configurer le fournisseur d’identité dans Azure AD B2C.

### <a name="part-2---create-a-policy-key"></a>Partie 2 : Créer une clé de stratégie

Stockez la clé secrète client IDEMIA que vous avez enregistrée précédemment dans votre locataire Azure AD B2C.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.

3. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.

4. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.

5. Dans la page **de présentation**, sélectionnez **Identity Experience Framework**.

6. Sélectionnez **Clés de stratégie**, puis **Ajouter**.

7. Dans **Options**, choisissez **Manuel**.

8. Entrez un **nom** pour la clé de stratégie. Par exemple, IdemiaAppSecret. Le préfixe B2C_1A_ est ajouté automatiquement au nom de votre clé.

9. Dans **Secret**, entrez la clé secrète client que vous avez enregistrée.

10. Pour **Utilisation de la clé**, sélectionnez **Signature**.

11. Sélectionnez **Create** (Créer).

### <a name="part-3---configure-idemia-as-an-external-idp"></a>Partie 3 : Configurer IDEMIA comme fournisseur d’identité externe

Pour permettre aux utilisateurs de se connecter avec une identité sans mot de passe d’ID mobile IDEMIA, vous devez définir IDEMIA en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer via un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié à l’aide d’une biométrie telle qu’une empreinte digitale ou une numérisation faciale disponible sur son appareil, prouvant ainsi l’identité de l’utilisateur.
Vous pouvez définir IDEMIA comme fournisseur de revendications en l’ajoutant à l’élément **ClaimsProvider** dans le fichier d’extension de votre stratégie.

```PowerShell
     <TechnicalProfile Id="Idemia-Oauth2">
          <DisplayName>IDEMIA</DisplayName>
          <Description>Login with your IDEMIA identity</Description>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="METADATA">https://idp.XXXX.net/oxauth/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid id_basic mt_scope</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="token_endpoint_auth_method">client_secret_basic</Item>
            <Item Key="ClaimsEndpoint">https://idp.XXXX.net/oxauth/restv1/userinfo</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdemiaAppSecret" />
</CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="acr" PartnerClaimType="acr_values" DefaultValue="loa-2" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName1" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="lastName1" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="idemia" />
            <OutputClaim ClaimTypeReferenceId="documentId" />
            <OutputClaim ClaimTypeReferenceId="address1" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
           
```

Définissez client_id sur l’ID d’application de l’inscription de l’application.

|Propriété | Description|
|:------|:-------|
|Étendue| Pour OIDC (OpenID Connect), la configuration minimale requise est que le paramètre d’étendue soit défini sur **openid**. Des étendues supplémentaires peuvent être ajoutées sous forme de liste délimitée par des espaces.|
|redirect_uri | Ceci définit l’emplacement à partir duquel l’agent utilisateur renvoie le code d’autorisation à Azure AD B2C.|
|response_type| Pour le flux de code d’autorisation, ceci est défini sur **code**.|
|acr_values| Ce paramètre contrôle les méthodes d’authentification que l’utilisateur doit effectuer lors le processus d’authentification. |

Une des valeurs suivantes doit être sélectionnée :

|Valeur du paramètre| Effet sur le processus d’authentification de l’utilisateur |
|:------|:-------|
|`loa-2`| Authentification multifacteur Azure AD basée sur le chiffrement uniquement|
|`loa-3`| Authentification multifacteur Azure AD basée sur le chiffrement plus un autre facteur|
|`loa-4`| Authentification multifacteur Azure AD basée sur le chiffrement avec l’obligation que l’utilisateur effectue également une authentification par code PIN et biométrique |

Le point de terminaison **/userinfo** fournit les revendications pour la ou les étendues demandées dans la demande d’autorisation. Pour **<mt_scope>** , ceci inclut des revendications comme le prénom, le nom et le numéro de permis de conduire, entre autres éléments.
Le jeu de revendications pour une étendue donnée est publié dans la section **scope_to_claims_mapping** de l’API de découverte.
Azure AD B2C demande des revendications auprès du point de terminaison des revendications et retourne ces revendications dans l’élément OutputClaims. Il peut être nécessaire de mapper le nom de la revendication définie dans votre stratégie sur le nom défini dans le fournisseur d’identité, en veillant à définir le type de revendication dans l’[élément ClaimSchema](claimsschema.md) :

```PowerShell
<ClaimType Id="documentId">
     <DisplayName>documentId</DisplayName>
     <DataType>string</DataType>
</ClaimType>
<ClaimType Id="address1">
     <DisplayName>address</DisplayName>
     <DataType>string</DataType>
</ClaimType>
```

### <a name="part-4---add-a-user-journey"></a>Partie 4 : Ajouter un parcours utilisateur

À ce stade, le fournisseur d’identité a été configuré, mais il n’est encore disponible dans les pages de connexion. Si vous n’avez pas votre propre parcours utilisateur personnalisé, créez un doublon d’un modèle de parcours utilisateur existant ; sinon, passez à l’étape suivante.

1. Ouvrez le fichier `TrustFrameworkBase.xml` à partir du pack de démarrage.

2. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourneys** qui inclut `ID=SignUpOrSignIn`.

3. Ouvrez `TrustFrameworkExtensions.xml` et recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.

4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément UserJourneys.

5. Renommez l’ID du parcours utilisateur. Par exemple : `ID=CustomSignUpSignIn`.

### <a name="part-5---add-the-idp-to-a-user-journey"></a>Partie 5 : Ajout du fournisseur d’identité à un parcours utilisateur

Maintenant que vous disposez d’un parcours utilisateur, ajoutez-y le nouveau fournisseur d’identité. Ajoutez d’abord un bouton de connexion, puis liez le bouton à une action. L’action représente le profil technique que vous avez créé plus haut.

1. Recherchez l’élément d’étape d’orchestration comprenant Type = `CombinedSignInAndSignUp` ou Type = `ClaimsProviderSelection` dans le parcours utilisateur. Il s’agit généralement de la première étape d’orchestration. L’élément **ClaimsProviderSelections** contient une liste de fournisseurs d’identité avec lesquels un utilisateur peut se connecter. L’ordre des éléments détermine l’ordre des boutons de connexion présentés à l’utilisateur. Ajoutez un élément XML **ClaimsProviderSelection**. Définissez la valeur **TargetClaimsExchangeId** sur un nom convivial.

2. À la prochaine étape d’orchestration, ajoutez un élément **ClaimsExchange**. Définissez l’**ID** sur la valeur de l’ID cible d’échange des revendications. Mettez à jour la valeur de **TechnicalProfileReferenceId** sur l’ID du profil technique que vous avez créé.

Le code XML suivant montre les deux premières étapes de l’orchestration d’un parcours utilisateur avec le fournisseur d’identité :

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="IdemiaExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="IdemiaExchange" TechnicalProfileReferenceId="Idemia-Oauth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>Partie 6 : Configurer la stratégie de partie de confiance

La stratégie de partie de confiance, par exemple [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), spécifie le parcours utilisateur à partir duquel Azure AD B2C s’exécutera. Recherchez l’élément **DefaultUserJourney** dans la partie de confiance. Mettez à jour **ReferenceId** afin qu’il corresponde à l’ID du parcours utilisateur auquel vous avez ajouté le fournisseur d’identité.

Dans l’exemple suivant, pour le parcours utilisateur `CustomSignUpOrSignIn`, la **ReferenceId** est défini sur `CustomSignUpOrSignIn`.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>Partie 7 : Charger la stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com/#home).

2. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.

3. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.

4. Dans le [portail Azure](https://portal.azure.com/#home), recherchez et sélectionnez **Azure AD B2C**.

5. Sous Stratégies, sélectionnez **Identity Experience Framework**.

Sélectionnez **Charger une stratégie personnalisée**, puis chargez les deux fichiers de stratégie que vous avez modifiés, dans l’ordre suivant : la stratégie d’extension, par exemple `TrustFrameworkExtensions.xml`, puis la stratégie de la partie de confiance, par exemple `SignUpSignIn.xml`.

### <a name="part-8---test-your-custom-policy"></a>Partie 8 : Tester votre stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.

2. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](./tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.

3. Sélectionnez le bouton **Exécuter maintenant**.

4. Dans la page d’inscription ou de connexion, sélectionnez **IDEMIA** pour vous connecter avec un mID émis par un état des États-Unis ou par IDEMIA (Informations d’identification de l’ID mobile).

Si le processus de connexion réussit, votre navigateur est redirigé vers `https://jwt.ms`, qui affiche le contenu du jeton retourné par Azure AD B2C.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Stratégies personnalisées dans Azure AD B2C](custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

- [En savoir plus sur le mID IDEMIA](https://www.idemia.com/mobile-id)

::: zone-end
