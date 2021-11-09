---
title: Configurer un flux d’informations d’identification par mot de passe du propriétaire de ressource
titleSuffix: Azure AD B2C
description: Découvrez comment configurer le flux des informations d’identification par mot de passe de propriétaire de ressource dans Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/12/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0acca4a87ea72157a11862ec448483008af87526
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008222"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>Configurer le flux des informations d’identification par mot de passe de propriétaire de ressource dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Dans Azure Active Directory B2C (Azure AD B2C), le flux des informations d’identification par mot de passe du propriétaire de ressource est un flux d’authentification standard OAuth. Dans ce flux, une application, également appelée partie de confiance, échange des informations d’identification valides pour des jetons. Les informations d’identification incluent un ID utilisateur et un mot de passe. Les jetons retournés sont un jeton d’ID, un jeton d’accès et un jeton d’actualisation.

## <a name="ropc-flow-notes"></a>Notes relatives au flux ROPC

Les options suivantes sont prises en charge dans Azure Active Directory B2C (Azure AD B2C) :

- **Client natif** : une interaction de l’utilisateur lors de l’authentification se produit quand le code s’exécute sur un appareil côté utilisateur. L’appareil peut être une application mobile qui s’exécute dans un système d’exploitation natif, comme Android et iOS.
- **Flux client public** : seules les informations d’identification de l’utilisateur, collectées par une application, sont envoyées dans l’appel d’API. Les informations d’identification de l’application ne sont pas envoyées.
- **Ajouter de nouvelles revendications** : il est possible de changer le contenu du jeton de l’ID pour ajouter de nouvelles revendications.

Les flux suivants ne sont pas pris en charge :

- **Serveur à serveur** : le système de protection des identités a besoin d’une adresse IP fiable collectée auprès de l’appelant (le client natif) dans le cadre de l’interaction. Dans un appel d’API côté serveur, seule l’adresse IP du serveur est utilisée. Si un seuil dynamique d’échecs d’authentification est dépassé, le système de protection d’identité peut identifier une adresse IP répétée en tant qu’attaquant.
- **Flux client confidentiel** : l’ID du client d’application est validé, mais le secret d’application ne l’est pas.

Lors de l'utilisation du flux ROPC, tenez compte de ce qui suit :

- ROPC ne fonctionne pas en cas d'interruption du flux d'authentification nécessitant une interaction de l'utilisateur. Par exemple, lorsqu’un mot de passe a expiré ou doit être modifié et qu’une [authentification multifacteur](multi-factor-authentication.md) est nécessaire, ou lorsque des informations supplémentaires doivent être collectées au moment de la connexion (comme le consentement de l’utilisateur).
- Seuls les comptes locaux sont pris en charge par ROPC. Les utilisateurs ne peuvent pas se connecter à des [fournisseurs d’identité fédérés](add-identity-provider.md) comme Microsoft, Google+, Twitter, AD-FS ou Facebook.
- La [gestion des sessions](session-behavior.md), y compris le [maintien de la connexion](session-behavior.md#enable-keep-me-signed-in-kmsi), n’est pas applicable.


## <a name="register-an-application"></a>Inscrire une application

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>Créer un flux d’utilisateur de propriétaire de ressource

1. Connectez-vous au [portail Azure](https://portal.azure.com) comme **administrateur général** de votre locataire Azure AD B2C.
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C :
    1. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
    1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateur**, puis sélectionnez **Nouveau flux d’utilisateur**.
1. Sélectionnez **Se connecter à l’aide des informations d’identification de mot de passe du propriétaire de la ressource (ROPC)** .
1. Sous **Version**, assurez-vous que **Préversion** est sélectionné, puis sélectionnez **Créer**.
1. Indiquez un nom pour le flux d’utilisateur, par exemple *ROPC_Auth*.
1. Sous **Revendications d’application**, cliquez sur **Afficher plus**.
1. Sélectionnez les revendications de l’application dont vous avez besoin pour votre application, comme Nom d’affichage, Adresse e-mail et Fournisseur d’identité.
1. Sélectionnez **OK**, puis **Créer**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="pre-requisite"></a>Conditions préalables 
Si vous ne l’avez pas fait, découvrez le Pack de démarrage de stratégie personnalisée dans [Prise en main des stratégies personnalisées dans Active Directory B2C](tutorial-create-user-flows.md).

##  <a name="create-a-resource-owner-policy"></a>Créer une stratégie de propriétaire de ressource

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Si elle n’existe pas encore, ajoutez un élément **ClaimsSchema** et ses éléments enfants en tant que premier élément sous l’élément **BuildingBlocks** :

    ```xml
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Après l’élément **ClaimsSchema**, ajoutez un élément **ClaimsTransformations** et ses éléments enfants à l’élément **BuildingBlocks** :

    ```xml
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>

      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Recherchez l’élément **ClaimsProvider** dont le **DisplayName** est `Local Account SignIn`, et ajoutez le profil technique de suivant :

    ```xml
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Remplacez la **DefaultValue** de **client_id** par l’ID d’application de l’application ProxyIdentityExperienceFramework que vous avez créée dans le didacticiel préalable. Remplacez ensuite la **DefaultValue** de **resource_id** par l’ID d’application de l’application IdentityExperienceFramework que vous avez également créée dans le didacticiel préalable.

5. Ajoutez les éléments **ClaimsProvider** suivants avec leurs profils techniques à l’élément **ClaimsProviders** :

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. Ajoutez un élément **UserJourneys** et ses éléments enfants à l’élément **TrustFrameworkPolicy** :

    ```xml
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Sur la page **Stratégies personnalisées** dans votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
8. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
9. Cliquez sur **Télécharger**.

## <a name="create-a-relying-party-file"></a>Créer un fichier de partie de confiance

Ensuite, mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé :

1. Faites une copie du fichier *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-la en *ROPC_Auth.xml*.
2. Ouvrez le nouveau fichier et changez la valeur de l’attribut **PolicyId** pour **TrustFrameworkPolicy** en valeur unique. L’ID de stratégie est le nom de votre stratégie. Par exemple, **B2C_1A_ROPC_Auth**.
3. Modifiez la valeur de l’attribut **ReferenceId** dans **DefaultUserJourney** en `ResourceOwnerPasswordCredentials`.
4. Modifiez l’élément **OutputClaims** afin qu’il ne contienne que les revendications suivantes :

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Sur la page **Stratégies personnalisées** dans votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
6. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *ROPC_Auth.xml*.
7. Cliquez sur **Télécharger**.


::: zone-end

## <a name="test-the-ropc-flow"></a>Tester le Flow ROPC

Utilisez votre application de développement d’API favorite pour générer un appel d’API et examinez la réponse pour déboguer votre stratégie. Construisez un appel similaire à cet exemple, avec les informations suivantes en tant que corps de la requête POST :

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Remplacez `<tenant-name>` par le nom de votre locataire Azure AD B2C.
- Remplacez `B2C_1A_ROPC_Auth` par le nom complet de votre stratégie d’informations d’identification de mot de passe du propriétaire de ressource.

| Clé | Valeur |
| --- | ----- |
| username | `user-account` |
| mot de passe | `password1` |
| grant_type | mot de passe |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | jeton id_token |

- Remplacez `user-account` par le nom d’un compte d’utilisateur de votre locataire.
- Remplacez `password1` par le mot de passe du compte d’utilisateur.
- Remplacez `application-id` par l’ID d’application extrait de l’inscription *ROPC_Auth_app*.
- *offline_access* est facultatif si vous voulez recevoir un jeton d’actualisation.

La requête POST réelle ressemble à l’exemple suivant :

```https
POST /<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Une réponse correcte avec accès hors connexion ressemble à l’exemple suivant :

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Échanger un jeton d’actualisation

Construire un appel POST comme celui illustré ici. Utilisez les informations du tableau suivant comme corps de la requête :

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Remplacez `<tenant-name>` par le nom de votre locataire Azure AD B2C.
- Remplacez `B2C_1A_ROPC_Auth` par le nom complet de votre stratégie d’informations d’identification de mot de passe du propriétaire de ressource.

| Clé | Valeur |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Remplacez `application-id` par l’ID d’application extrait de l’inscription *ROPC_Auth_app*.
- Remplacez `refresh-token` par le **refresh_token** renvoyé dans la réponse précédente.

Une réponse correcte ressemble à l’exemple suivant :

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="the-provided-application-is-not-configured-to-allow-the-oauth-implicit-flow"></a>L’application fournie n’est pas configurée pour autoriser le flux « OAuth » implicite

* **Symptôme** : vous exécutez le flux ROPC et vous recevez le message suivant : *AADB2C90057 : l’application fournie n’est pas configurée pour autoriser le flux implicite « OAuth »* .
* **Causes possibles** : le flux implicite n’est pas autorisé pour votre application.
* **Résolution** : lors de la création de votre [inscription d’application](#register-an-application) dans Azure AD B2C, vous devez modifier manuellement le manifeste de l'application et définir la valeur de la propriété `oauth2AllowImplicitFlow` sur `true`. Une fois la propriété `oauth2AllowImplicitFlow` configurée, il faut quelques minutes (généralement pas plus de 5 min) pour que la modification prenne effet. 

## <a name="use-a-native-sdk-or-app-auth"></a>Utiliser un kit de développement logiciel ou une authentification de l’application natifs

Azure AD B2C respecte les normes OAuth 2.0 ou les informations d’identification par mot de passe du propriétaire de ressource du client public, et doit être compatible avec la plupart des kits de développement logiciels (SDK) clients. Pour plus d’informations, consultez [Meilleures pratiques récentes d’implémentation du SDK d’application OAuth 2.0 natif et d’OpenID Connect](https://appauth.io/).

## <a name="next-steps"></a>Étapes suivantes

Téléchargez des exemples fonctionnels, configurés pour une utilisation avec Azure AD B2C à partir de GitHub, [pour Android](https://aka.ms/aadb2cappauthropc) et [pour iOS](https://aka.ms/aadb2ciosappauthropc).
