---
title: Enrichissement de jetons – Azure Active Directory B2C
description: Enrichissez les jetons avec des revendications provenant de sources externes en utilisant des API.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/29/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8f5bf34424754106c6d86195c37d23c3c9060c1c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008348"
---
# <a name="enrich-tokens-with-claims-from-external-sources-using-api-connectors"></a>Enrichir les jetons avec des revendications provenant de sources externes à l’aide de connecteurs API

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) permet aux développeurs d’identité d’intégrer une interaction avec une API RESTful dans leur flux d’utilisateur à l’aide de [connecteurs d’API](api-connectors-overview.md). À la fin de cette procédure pas à pas, vous serez en mesure de créer un flux d’utilisateur Azure AD B2C qui interagit avec les API pour enrichir les jetons avec des informations provenant de sources externes.

::: zone pivot="b2c-user-flow"

Vous pouvez utiliser les connecteurs d’API appliqués à l’étape **Avant d’envoyer le jeton (préversion)** pour enrichir les jetons de vos applications avec des informations provenant de sources externes. Lorsqu’un utilisateur s’inscrit ou se connecte, Azure AD B2C appelle le point de terminaison de l’API configuré dans le connecteur d’API, qui peut interroger les informations relatives à un utilisateur dans des services en aval tels que les services cloud, les magasins d’utilisateurs personnalisés, les systèmes d’autorisation personnalisés, les systèmes d’identité hérités, et bien plus encore.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Vous pouvez créer un point de terminaison d’API à l’aide de l’un de nos [exemples](api-connector-samples.md#api-connector-rest-api-samples).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-api-connector"></a>Créer un connecteur d'API

Pour utiliser un [connecteur d’API](api-connectors-overview.md), vous devez d’abord créer le connecteur d’API, puis l’activer dans un workflow utilisateur.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sous **Services Azure**, sélectionnez **Azure AD B2C**.
4. Sélectionnez **Connecteurs d’API**, puis **Nouveau connecteur d’API**.

   ![Capture d’écran de la configuration de base d’un connecteur d’API](media/add-api-connector-token-enrichment/api-connector-new.png)

5. Indiquez un nom d’affichage pour l’appel. Par exemple, **Enrichir le jeton à partir d’une source externe**.
6. Indiquez l’**URL du point de terminaison** pour l’appel d’API.
7. Choisissez le **Type d'authentification** et configurez les informations d'authentification pour appeler votre API. Découvrez comment [sécuriser votre connecteur d’API](secure-rest-api.md).

   ![Capture d’écran de la configuration de l’authentification pour un connecteur d’API](media/add-api-connector-token-enrichment/api-connector-config.png)

8. Sélectionnez **Enregistrer**.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Activer le connecteur d’API dans un workflow utilisateur

Procédez comme suit pour ajouter un connecteur d’API à un workflow d’utilisateur d’inscription.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sous **Services Azure**, sélectionnez **Azure AD B2C**.
4. Sélectionnez **Flux d’utilisateurs**, puis sélectionnez le flux utilisateur auquel vous souhaitez ajouter le connecteur d’API.
5. Sélectionnez **Connecteurs d’API**, puis sélectionnez les point de terminaison d’API que vous souhaitez appeler à l’étape **Avant d’envoyer le jeton (préversion)** du flux d’utilisateur :

   ![Capture d’écran de la sélection d’un connecteur d’API pour une étape du flux d’utilisateur](media/add-api-connector-token-enrichment/api-connectors-user-flow-select.png)

6. Sélectionnez **Enregistrer**.

Cette étape n’existe que pour les flux d’utilisateur **S’inscrire et se connecter (recommandé)** , **S’inscrire (recommandé)** et **Se connecter (recommandé)** .

## <a name="example-request-sent-to-the-api-at-this-step"></a>Exemple de demande envoyée à l’API à cette étape

Un connecteur d’API à cette étape est appelé lorsqu’un jeton est sur le point d’être émis pendant des connexions et des inscriptions. 

Un connecteur d’API est matérialisé en tant que requête **HTTP POST**, en envoyant les attributs utilisateur (« revendications ») en tant que paires clé-valeur dans un corps JSON. Les attributs sont sérialisés de la même façon que les propriétés utilisateur [Microsoft Graph](/graph/api/resources/user#properties). 

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "objectId": "ab3ec3b2-a435-45e4-b93a-56a005e88bb7",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "client_id": "231c70e8-8424-48ac-9b5d-5623b9e4ccf3",
 "step": "PreTokenIssuance",
 "ui_locales":"en-US"
}
```

Les revendications envoyées à l’API dépendent des informations définies pour l’utilisateur.

Seules les propriétés utilisateur et les attributs personnalisés répertoriés dans l’expérience **Azure AD B2C** > **Attributs utilisateur** peuvent être envoyés dans la demande.

Les attributs personnalisés existent au format **extension_\<extensions-app-id>_CustomAttribute** dans le répertoire. Votre API doit s’attendre à recevoir des revendications dans ce même format sérialisé. Pour plus d’informations sur les attributs personnalisés, consultez [Définir des attributs personnalisés dans Azure AD B2C](user-flow-custom-attributes.md).

En outre, ces revendications sont généralement envoyées dans toutes les requêtes de cette étape :
- **Paramètres régionaux de l’interface utilisateur (’ui_locales’)** : paramètres régionaux de l’utilisateur final comme configurés sur leur appareil. Ils peuvent être utilisés par votre API pour retourner des réponses internationalisées.
- **Étape (« step »)**  : étape ou point du flux d’utilisateur pour lequel le connecteur d’API a été appelé. La valeur de cette étape est `
- **ID client (« client_id »)**  : valeur `appId` de l’application sur laquelle un utilisateur final s’authentifie dans un flux d’utilisateur. Il ne s’agit *pas* de l’`appId` de la ressource d’application dans les jetons d’accès.
- **objectId** : identifiant de l’utilisateur. Vous pouvez l’utiliser pour interroger les services en aval dans le but d’obtenir des informations sur l’utilisateur.
  
> [!IMPORTANT]
> Si une revendication n’a pas de valeur au moment où le point de terminaison de l’API est appelé, la revendication n’est pas envoyée à l’API. Votre API doit être conçue pour vérifier et gérer explicitement le cas où une revendication ne figure pas dans la requête.

## <a name="expected-response-types-from-the-web-api-at-this-step"></a>Types de réponses attendus de l’API Web à cette étape

Lorsque l’API web reçoit une requête HTTP d’Azure AD pendant un flux d’utilisateur, elle peut renvoyer une « réponse de continuation ».

### <a name="continuation-response"></a>Réponse de continuation

Une réponse de continuation indique que le flux d’utilisateur doit passer à l’étape suivante : l’émission du jeton.

Dans une réponse de continuation, l’API peut renvoyer des revendications supplémentaires. Une revendication renvoyée par l’API que vous souhaitez renvoyer dans le jeton doit être une revendication intégrée ou [définie comme un attribut personnalisé](user-flow-custom-attributes.md) et elle doit être sélectionnée dans la configuration **Revendications d’application** du flux d’utilisateur. 

La valeur de la revendication dans le jeton sera celle renvoyée par l’API, et non la valeur dans le répertoire. Certaines valeurs de revendication ne peuvent pas être remplacées par la réponse de l’API. Les revendications qui peuvent être renvoyées par l’API correspondent à l’ensemble trouvé sous **Attributs utilisateur**, à l’exception de `email`.

> [!NOTE]
> L’API est appelée uniquement lors d’une authentification initiale. Lorsque vous utilisez des jetons d’actualisation pour obtenir silencieusement de nouveaux jetons d’accès ou d’ID, le jeton inclut les valeurs évaluées lors de l’authentification initiale. 

## <a name="example-response"></a>Exemple de réponse

### <a name="example-of-a-continuation-response"></a>Exemple de réponse de continuation

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Paramètre                                          | Type              | Obligatoire | Description                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version     | String | Oui      | Version de votre API.                                                    |
| action                                             | String            | Oui      | La valeur doit être `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Non       | Elles peuvent être renvoyées dans le jeton si elles sont sélectionnées en tant que **Revendication d’application**.                                        |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Non       | La revendication n’a pas besoin de contenir `_<extensions-app-id>_`, cela est *facultatif*. Elles peuvent être renvoyées dans le jeton si elles sont sélectionnées en tant que **Revendication d’application**.  |

::: zone-end

::: zone pivot="b2c-custom-policy"

Dans ce scénario, nous enrichissons les données de jeton de l’utilisateur en les intégrant à un workflow métier. Lors de l’inscription ou de la connexion avec un compte local ou fédéré, Azure AD B2C appelle une API REST pour obtenir les données de profil étendues de l’utilisateur à partir d’une source de données distante. Dans cet exemple, Azure AD B2C envoie l’identificateur unique de l’utilisateur, objectId. L’API REST retourne ensuite le solde du compte de l’utilisateur (un nombre aléatoire). Utilisez cet exemple comme point de départ pour une intégration avec votre propre système CRM, votre base de données marketing ou tout workflow métier.

Vous pouvez aussi concevoir l’interaction comme un profil technique de validation. Cela convient lorsque l’API REST valide les données à l’écran et renvoie des revendications. Pour plus d'informations, consultez [Procédure pas à pas : Ajouter un connecteur d'API à un flux d'utilisateur d'inscription](add-api-connector.md).

## <a name="prerequisites"></a>Prérequis

- Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy). Vous devez disposer d’une stratégie personnalisée fonctionnelle pour l’inscription et la connexion avec des comptes locaux.
- Découvrez comment [intégrer des échanges de revendications de l’API REST dans votre stratégie personnalisée Azure AD B2C](api-connectors-overview.md).

## <a name="prepare-a-rest-api-endpoint"></a>Préparer un point de terminaison d’API REST

Pour cette procédure pas à pas, vous devez disposer d’une API REST qui vérifie si l’objectId Azure AD B2C d’un utilisateur est inscrit dans votre système principal. S’il est inscrit, l’API REST renvoie le solde du compte d’utilisateur. Dans le cas contraire, l’API REST inscrit le nouveau compte dans le répertoire et retourne le solde initial de `50.00`.

Le code JSON suivant illustre les données qu’Azure AD B2C enverra à votre point de terminaison d’API REST. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Une fois que votre API REST valide les données, elle doit retourner un message HTTP 200 (OK), avec les données JSON suivantes :

```json
{
    "balance": "760.50"
}
```

Cet article ne traite pas de la configuration du point de terminaison d’API REST. Vous avez créé un exemple [Azure Functions](../azure-functions/functions-reference.md). Vous pouvez accéder au code complet de la fonction Azure sur le site de [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Définir des revendications

Une revendication fournit un stockage temporaire de données lors d’une exécution de stratégie Azure AD B2C. Vous pouvez déclarer des revendications dans la section [Schéma de revendications](claimsschema.md). 

1. Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Localisez l’élément [ClaimsSchema](claimsschema.md). Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez les revendications suivantes à l’élément **ClaimsSchema**.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Ajouter le profil technique de l’API RESTful 

Un [profil technique RESTful](restful-technical-profile.md) prend en charge la création d’une interface avec votre propre service RESTful. Azure Active Directory B2C envoie des données au service RESTful dans une collection `InputClaims` et reçoit des données en retour dans une collection `OutputClaims`. Recherchez l’élément **ClaimsProviders** dans votre fichier <em> **`TrustFrameworkExtensions.xml`**</em> et ajoutez un fournisseur de revendications comme suit :

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

Dans cet exemple, le `userLanguage` sera envoyé au service REST en tant que `lang` au sein de la charge utile JSON. La valeur de la revendication `userLanguage` contient l’ID de langue de l’utilisateur actuel. Pour plus d’informations, consultez [Programmes de résolution de revendication](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Configurer le profil technique de l’API RESTful 

Après avoir déployé votre API REST, définissez les métadonnées du profil technique `REST-GetProfile` pour refléter votre propre API REST, notamment :

- **ServiceUrl**. Définissez l’URL du point de terminaison de l’API REST.
- **SendClaimsIn**. Spécifiez la façon dont les revendications d’entrée sont envoyées au fournisseur de revendications RESTful.
- **AuthenticationType**. Définissez le type de l’authentification effectuée par le fournisseur de revendications RESTful. 
- **AllowInsecureAuthInProduction**. Dans un environnement de production, veillez à définir ces métadonnées sur `true`
    
Pour plus d’informations sur les configurations, consultez [Métadonnées du profil technique RESTful](restful-technical-profile.md#metadata).

Les commentaires ci -dessus `AuthenticationType` et `AllowInsecureAuthInProduction` indiquent les modifications que vous devez effectuer lorsque vous passez à un environnement de production. Pour savoir comment sécuriser vos API RESTful pour la production, consultez [Sécuriser votre API RESTful](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Ajouter une étape d’orchestration

Les [parcours utilisateur](userjourneys.md) spécifient des chemins explicites par le biais desquels une stratégie autorise une application par partie de confiance à obtenir les revendications souhaitées pour un utilisateur. Un parcours utilisateur est représenté en tant que séquence d’orchestration qui doit être suivie pour que la transaction réussisse. Vous pouvez ajouter ou soustraire des étapes d’orchestration. Dans ce cas, vous allez ajouter une étape d’orchestration qui est utilisée pour compléter les informations fournies à l’application après l’inscription ou la connexion de l’utilisateur via l’appel de l’API REST.

1. Ouvrez le fichier de base de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>.
1. Recherchez l’élément `<UserJourneys>`. Copiez l’élément dans son intégralité, puis supprimez-le.
1. Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Collez le `<UserJourneys>` dans le fichier des extensions après avoir fermé l’élément `<ClaimsProviders>`.
1. Localisez le `<UserJourney Id="SignUpOrSignIn">` et ajoutez l’étape d’orchestration suivante en avant-dernier.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refactorisez la dernière étape d’orchestration en changeant la valeur de `Order` en `8`. Les deux dernières étapes de l’orchestration doivent ressembler à ce qui suit :

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Répétez les deux dernières étapes pour les parcours utilisateur **ProfileEdit** et **PasswordReset**.


## <a name="include-a-claim-in-the-token"></a>Inclure une revendication dans le jeton 

Pour retourner la revendication `balance` à l’application par partie de confiance, ajoutez une revendication de sortie au fichier <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em>. L’ajout d’une revendication de sortie émettra la revendication dans le jeton après un parcours utilisateur réussi et l’enverra à l’application. Modifiez l’élément de profil technique dans la section de partie de confiance pour ajouter `balance` en tant que revendication de sortie.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Répétez cette étape pour les parcours utilisateur **ProfileEdit.xml** et **PasswordReset.xml**.

Enregistrez les fichiers que vous avez modifiés : *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* et *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Tester la stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser le répertoire qui contient votre locataire Azure AD en sélectionnant l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire Azure AD dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Charger une stratégie personnalisée**, puis chargez les fichiers de stratégie que vous avez modifiés : *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* et *PasswordReset.xml*. 
1. Sélectionnez la stratégie d’inscription et de connexion que vous avez chargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire au moyen d’une adresse e-mail ou d’un compte Facebook.
1. Le jeton envoyé à votre application inclut la revendication `balance`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="best-practices-and-how-to-troubleshoot"></a>Meilleures pratiques et résolution des problèmes

### <a name="using-serverless-cloud-functions"></a>Utilisation des fonctions cloud serverless

Les fonctions serverless, comme les [déclencheurs HTTP dans Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md), fournissent une façon de créer des points de terminaison d’API à utiliser avec le connecteur d’API. La fonction cloud serverless peut également appeler et invoquer d’autres API web, magasins de données et d’autres services cloud dans le cade de scénarios plus complexes.

### <a name="best-practices"></a>Meilleures pratiques

Assurez-vous que :
* Votre API suit les contrats de demande et de réponse d’API comme indiqué ci-dessus. 
* L’**URL du point de terminaison** du connecteur d’API pointe vers le point de terminaison d’API approprié.
* Votre API recherche explicitement les valeurs null des revendications reçues dont elle dépend.
* Votre API implémente une méthode d’authentification décrite dans [Sécuriser votre connecteur d’API](secure-rest-api.md).
* Votre API répond aussi rapidement que possible pour garantir une expérience utilisateur fluide.
    * Azure AD B2C attendra un maximum de *20 secondes* pour recevoir une réponse. Si aucune réponse n’est reçue, le service effectuera *une tentative de plus* pour appeler votre API.
    * Si vous utilisez une fonction serverless ou un service web évolutif, utilisez un plan d’hébergement qui conserve l’API dans un état « de veille » ou « dynamique » en production. Pour Azure Functions, il est recommandé d’utiliser au minimum le [plan Premium](../azure-functions/functions-scale.md) en production.
* Assurez la haute disponibilité de votre API.
* Analysez et optimisez les performances des API en aval, des bases de données ou d’autres dépendances de votre API.
  
[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

### <a name="use-logging"></a>Utiliser la journalisation

En général, il est judicieux d’utiliser les outils de journalisation activés par votre service API Web, comme [Application Insights](../azure-functions/functions-monitoring.md), pour surveiller votre API en cas de codes d’erreur inattendus, d’exceptions et de performances médiocres.
* Analysez les codes d’état HTTP autres que HTTP 200 ou 400.
* Un code d’état HTTP 401 ou 403 indique généralement un problème avec votre authentification. Vérifiez la couche d’authentification de votre API et la configuration correspondante dans le connecteur d’API.
* Si nécessaire, utilisez des niveaux de journalisation plus agressifs (par exemple, « trace » ou « debug ») lors du développement.
* Surveillez votre API en cas de temps de réponse longs. 

De plus, Azure AD B2C journalise les métadonnées relatives aux transactions d’API qui se produisent lors de l’authentification des utilisateurs via un flux d’utilisateur. Pour les rechercher :
1. Accédez à **Azure AD B2C**.
2. Sous **Activités**, sélectionnez **Journaux d’audit**.
3. Filtrez la vue Liste : pour **Date**, sélectionnez l’intervalle de temps souhaité, et pour **Activité**, sélectionnez **Une API a été appelée dans le cadre d’un flux d’utilisateur**.
4. Inspectez les journaux individuels. Chaque ligne représente un connecteur d’API qui tente d’être appelé pendant un flux d’utilisateur. Si un appel d’API échoue et qu’une nouvelle tentative a lieu, il est toujours représenté par une seule ligne. `numberOfAttempts` indique le nombre de fois où votre API a été appelée. Cette valeur peut être `1` ou `2`. D’autres informations sur l’appel d’API sont détaillées dans les journaux.

   ![Capture d’écran d’un exemple de journal d’audit avec transaction de connecteur d’API](media/add-api-connector-token-enrichment/example-anonymized-audit-log.png)

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

::: zone pivot="b2c-user-flow"

- Bien commencer avec nos [exemples](api-connector-samples.md#api-connector-rest-api-samples).
- [Sécuriser votre connecteur d’API](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

Pour en savoir plus sur la sécurisation de vos API, consultez les articles suivants :

- [Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme étape d’orchestration](add-api-connector-token-enrichment.md)
- [Sécuriser votre API RESTful](secure-rest-api.md)
- [Reference : Profil technique RESTful](restful-technical-profile.md)

::: zone-end
