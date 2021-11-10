---
title: Ajouter des connecteurs d’API à des flux d’utilisateurs lors de l’inscription
description: Configurez un connecteur d’API à utiliser dans un flux d’utilisateur lors de l’inscription.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 11/09/2021
ms.author: kengaderdus
author: kengaderdus
manager: CelesteDG
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 665f914d19f0ab73c8ed6ab6af75ba485c1d35bf
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053367"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow"></a>Ajouter un connecteur d’API à un workflow d’utilisateur d’inscription

En tant que développeur ou administrateur informatique, vous pouvez utiliser des connecteurs d’API pour intégrer vos flux d’utilisateurs d’inscription à des API REST pour personnaliser l’inscription et l’intégrer à des systèmes externes. À la fin de cette procédure pas à pas, vous pourrez créer un flux d’utilisateur Azure AD B2C qui interagira avec des [services API REST](api-connectors-overview.md) pour modifier vos expériences d’inscription. 

::: zone pivot="b2c-user-flow"
Vous pouvez créer un point de terminaison d’API à l’aide de l’un de nos [exemples](api-connector-samples.md#api-connector-rest-api-samples).
::: zone-end

::: zone pivot="b2c-custom-policy"

Dans ce scénario, nous allons ajouter la possibilité pour les utilisateurs d’entrer un numéro de fidélité dans la page d’inscription Azure AD B2C. L’API REST valide si la combinaison de l’adresse e-mail et du numéro de fidélité est mappée à un code promotionnel. Si l’API REST trouve un code promotionnel pour cet utilisateur, celui-ci est retourné à Azure AD B2C. Enfin, le code promotionnel sera inséré dans les revendications de jeton que l’application utilisera.

Vous pouvez aussi concevoir l’interaction comme une étape d’orchestration. Cela convient lorsque l’API REST ne valide pas les données à l’écran et qu’elle renvoie toujours des revendications. Pour plus d’informations, consultez [Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme étape d’orchestration](add-api-connector-token-enrichment.md).

::: zone-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="create-an-api-connector"></a>Créer un connecteur d'API

Pour utiliser un [connecteur d’API](api-connectors-overview.md), vous devez d’abord créer le connecteur d’API, puis l’activer dans un workflow utilisateur.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**.
1. Sélectionnez **Connecteurs d’API**, puis **Nouveau connecteur d’API**.

   ![Capture d’écran de la configuration de base d’un connecteur d’API](media/add-api-connector/api-connector-new.png)

1. Indiquez un nom d’affichage pour l’appel. Par exemple, **Valider les informations utilisateur**.
1. Indiquez l’**URL du point de terminaison** pour l’appel d’API.
1. Choisissez le **Type d'authentification** et configurez les informations d'authentification pour appeler votre API. Découvrez comment [sécuriser votre connecteur d’API](secure-rest-api.md).

   ![Capture d’écran de la configuration de l’authentification pour un connecteur d’API](media/add-api-connector/api-connector-config.png)

1. Sélectionnez **Enregistrer**.

## <a name="the-request-sent-to-your-api"></a>Demande envoyée à votre API
Un connecteur d’API est matérialisé en tant que requête **HTTP POST**, en envoyant les attributs utilisateur (« revendications ») en tant que paires clé-valeur dans un corps JSON. Les attributs sont sérialisés de la même façon que les propriétés utilisateur [Microsoft Graph](/graph/api/resources/user#properties). 

**Exemple de requête**
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
 "objectId": "11111111-0000-0000-0000-000000000000"
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "step": "<step-name>",
 "client_id":"93fd07aa-333c-409d-955d-96008fd08dd9",
 "ui_locales":"en-US"
}
```

Seules les propriétés utilisateur et les attributs personnalisés répertoriés dans l’expérience **Azure AD B2C** > **Attributs utilisateur** peuvent être envoyés dans la demande.

Les attributs personnalisés existent au format **extension_\<extensions-app-id>_CustomAttribute** dans le répertoire. Votre API doit s’attendre à recevoir des revendications dans ce même format sérialisé. Pour plus d’informations sur les attributs personnalisés, consultez [Définir des attributs personnalisés dans Azure AD B2C](user-flow-custom-attributes.md).

De plus, ces revendications sont généralement envoyées dans toutes les demandes :
- **Paramètres régionaux de l’interface utilisateur (« ui_locales »)** – Paramètres régionaux d’un utilisateur final comme configurés sur son appareil. Ils peuvent être utilisés par votre API pour retourner des réponses internationalisées.
- **Étape (« step »)** – Étape ou point du flux d’utilisateur pour lequel le connecteur d’API a été appelé. Ces valeurs comprennent :
  - `PostFederationSignup` – Correspond à « Après la fédération avec un fournisseur d’identité lors de l’inscription ».
  - `PostAttributeCollection` – Correspond à « Avant la création de l’utilisateur ».
  - `PreTokenIssuance` – Correspond à « Avant l’envoi du jeton (préversion) ». [Plus d’informations sur cette tâche](add-api-connector-token-enrichment.md)
- **ID client (« client_id »)** – Valeur `appId` de l’application auprès de laquelle un utilisateur final s’authentifie dans un flux d’utilisateur. Il *ne s’agit pas* du paramètre `appId` de l’application de ressource dans les jetons d’accès.
- **Adresse e-mail (« e-mail »)** ou [**identités (« identities »)**](/graph/api/resources/objectidentity) – Ces revendications peuvent être utilisées par votre API pour identifier l’utilisateur final qui s’authentifie auprès de l’application.
  
> [!IMPORTANT]
> Si une revendication n’a pas de valeur au moment où le point de terminaison de l’API est appelé, la revendication n’est pas envoyée à l’API. Votre API doit être conçue pour vérifier et gérer explicitement le cas où une revendication ne figure pas dans la requête.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Activer le connecteur d’API dans un workflow utilisateur

Procédez comme suit pour ajouter un connecteur d’API à un workflow d’utilisateur d’inscription.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**, puis sélectionnez le flux utilisateur auquel vous souhaitez ajouter le connecteur d’API.
1. Sélectionnez **Connecteurs d’API**, puis sélectionnez les points de terminaison d’API que vous souhaitez appeler aux étapes suivantes dans le workflow d’utilisateur :

   - **Après la fédération avec un fournisseur d’identité lors de l’inscription**
   - **Avant de créer l’utilisateur**
   - **Avant d’envoyer le jeton (préversion)**

   ![Sélection d’un connecteur d’API pour une étape dans le flux d’utilisateur](media/add-api-connector/api-connectors-user-flow-select.png)

1. Sélectionnez **Enregistrer**.

Ces étapes existent uniquement pour les flux d’utilisateur **S’inscrire et se connecter (recommandé)** et **S’inscrire (recommandé)** , mais s’appliquent uniquement à la partie inscription de l’expérience.

## <a name="after-federating-with-an-identity-provider-during-sign-up"></a>Après la fédération avec un fournisseur d’identité lors de l’inscription

Un connecteur d’API à cette étape du processus d’inscription est appelé immédiatement après que l’utilisateur s’est authentifié auprès d’un fournisseur d’identité (comme Google, Facebook et Azure AD). Cette étape précède la ***page de collection d’attributs***, qui est le formulaire présenté à l’utilisateur pour collecter des attributs utilisateur. Cette étape n’est pas appelée si un utilisateur s’inscrit auprès d’un compte local.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exemple de demande envoyée à l’API à cette étape
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
 "objectId": "11111111-0000-0000-0000-000000000000",
 "givenName":"John",
 "lastName":"Smith",
 "step": "PostFederationSignup",
 "client_id":"<guid>",
 "ui_locales":"en-US"
}
```

Les revendications exactes envoyées à l’API dépendent des informations fournies par le fournisseur d’identité. « email » est toujours envoyé.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Types de réponses attendus de l’API Web à cette étape

Lorsque l’API Web reçoit une requête HTTP de Azure AD pendant un workflow utilisateur, elle peut renvoyer les réponses suivantes :

- Réponse de continuation
- Réponse de blocage

#### <a name="continuation-response"></a>Réponse de continuation

Une réponse de continuation indique que le flux de l’utilisateur doit passer à l’étape suivante : la page de collecte d’attribut.

Dans une réponse de continuation, l’API peut renvoyer des revendications. Si une revendication est retournée par l’API, la revendication effectue les opérations suivantes :

- Renseigne le champ d’entrée dans la page de collections d’attributs.

Consultez un exemple de [réponse de continuation](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Réponse de blocage

Une réponse de blocage quitte le workflow de l’utilisateur. Il peut être intentionnellement émis par l’API pour arrêter la continuation du workflow de l’utilisateur en affichant une page de blocage à l’utilisateur. La page de blocage affiche les `userMessage` fournies par l’API.

Consultez un exemple de [réponse de blocage](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>avant la création de l’utilisateur

Un connecteur d’API à cette étape du processus d’inscription est appelé après la page de collection d’attributs, si elle est inclue. Cette étape est toujours appelée avant qu’un compte d’utilisateur ne soit créé.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exemple de demande envoyée à l’API à cette étape

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
 "objectId": "11111111-0000-0000-0000-000000000000",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "step": "PostAttributeCollection",
 "client_id":"93fd07aa-333c-409d-955d-96008fd08dd9",
 "ui_locales":"en-US"
}
```

Les revendications qui sont envoyées à l’API dépendent des informations collectées à partir de l’utilisateur ou fournies par le fournisseur d’identité.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Types de réponses attendus de l’API Web à cette étape

Lorsque l’API Web reçoit une requête HTTP de Azure AD pendant un workflow utilisateur, elle peut renvoyer les réponses suivantes :

- Réponse de continuation
- Réponse de blocage
- Réponse de validation

#### <a name="continuation-response"></a>Réponse de continuation

Une réponse de continuation indique que le flux de l’utilisateur doit passer à l’étape suivante : créer l’utilisateur dans le répertoire.

Dans une réponse de continuation, l’API peut renvoyer des revendications. Si une revendication est retournée par l’API, la revendication effectue les opérations suivantes :

- Remplace toute valeur qui a déjà été fournie par un utilisateur dans la page de collecte d’attributs.

Pour écrire des revendications dans le répertoire lors de l’inscription qui ne doivent pas être collectées auprès de l’utilisateur, vous devez toujours sélectionner les revendications sous les **attributs utilisateur** du flux d’utilisateur, qui, par défaut, demande à l’utilisateur des valeurs, mais vous pouvez utiliser un [code JavaScript ou CSS personnalisé](customize-ui-with-html.md) pour masquer les champs d’entrée d’un utilisateur final.

Consultez un exemple de [réponse de continuation](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Réponse de blocage
Une réponse de blocage quitte le workflow de l’utilisateur. Il peut être intentionnellement émis par l’API pour arrêter la continuation du workflow de l’utilisateur en affichant une page de blocage à l’utilisateur. La page de blocage affiche les `userMessage` fournies par l’API.

Consultez un exemple de [réponse de blocage](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Validation-réponse d’erreur
 Lorsque l’API répond avec une réponse d’erreur de validation, le workflow de l’utilisateur reste sur la page de collection d’attributs et un `userMessage` est présenté à l’utilisateur. L’utilisateur peut ensuite modifier et renvoyer le formulaire. Ce type de réponse peut être utilisé pour la validation d’entrée.

Consultez un exemple de [réponse d’erreur de validation](#example-of-a-validation-error-response).

## <a name="before-sending-the-token-preview"></a>Avant d’envoyer le jeton (préversion)

> [!IMPORTANT]
> Les connecteurs d’API utilisés dans cette étape sont en préversion. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un connecteur d’API à cette étape est appelé lorsqu’un jeton est sur le point d’être émis pendant des connexions et des inscriptions. Un connecteur d’API pour cette étape peut être utilisé pour enrichir le jeton avec des valeurs de revendication provenant de sources externes.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exemple de demande envoyée à l’API à cette étape

```http
POST <API-endpoint>
Content-type: application/json

{
 "clientId": "231c70e8-8424-48ac-9b5d-5623b9e4ccf3",
 "step": "PreTokenApplicationClaims",
 "ui_locales":"en-US"
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
}
```

Les revendications envoyées à l’API dépendent des informations définies pour l’utilisateur.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Types de réponses attendus de l’API Web à cette étape

Lorsque l’API Web reçoit une requête HTTP de Azure AD pendant un workflow utilisateur, elle peut renvoyer les réponses suivantes :

- Réponse de continuation

#### <a name="continuation-response"></a>Réponse de continuation

Une réponse de continuation indique que le flux d’utilisateur doit passer à l’étape suivante : l’émission du jeton.

Dans une réponse de continuation, l’API peut retourner des revendications supplémentaires. Une revendication retournée par l’API que vous souhaitez retourner dans le jeton doit être une revendication intégrée ou [définie comme un attribut personnalisé](user-flow-custom-attributes.md) et elle doit être sélectionnée dans la configuration **Revendications d’application** du flux d’utilisateur. 

La valeur de revendication dans le jeton sera celle retournée par l’API, et non la valeur figurant dans le répertoire. Certaines valeurs de revendication ne peuvent pas être remplacées par la réponse de l’API. Les revendications qui peuvent être retournées par l’API correspondent à l’ensemble trouvé sous **Attributs utilisateur**, à l’exception de `email`.

Consultez un exemple de [réponse de continuation](#example-of-a-continuation-response).

> [!NOTE]
> L’API est appelée uniquement lors d’une authentification initiale. Lorsque vous utilisez des jetons d’actualisation pour obtenir silencieusement de nouveaux jetons d’accès ou d’ID, le jeton inclut les valeurs évaluées lors de l’authentification initiale. 

## <a name="example-responses"></a>Exemples de réponses

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
| \<builtInUserAttribute>                            | \<attribute-type> | Non       | Les valeurs retournées peuvent remplacer des valeurs collectées à partir d’un utilisateur.                    |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Non       | La revendication n’a pas besoin de contenir `_<extensions-app-id>_`, cela est *facultatif*. Les valeurs retournées peuvent remplacer des valeurs collectées à partir d’un utilisateur. |

### <a name="example-of-a-blocking-response"></a>Exemple de réponse de blocage

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Paramètre   | Type   | Obligatoire | Description                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Oui      | Version de votre API.                                                    |
| action      | String | Oui      | La valeur doit être `ShowBlockPage`                                              |
| userMessage | String | Oui      | Message à afficher à l’utilisateur.                                            |

**Expérience de l’utilisateur final avec une réponse de blocage**

![Exemple de réponse de blocage](media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Exemple de réponse d’erreur de validation

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| Paramètre   | Type    | Obligatoire | Description                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Oui      | Version de votre API.                                                    |
| action      | String  | Oui      | La valeur doit être `ValidationError`.                                           |
| status      | Entier / Chaîne | Oui      | La valeur doit être `400`, ou `"400"` pour une réponse ValidationError.  |
| userMessage | String  | Oui      | Message à afficher à l’utilisateur.                                            |

> [!NOTE]
> Le code d’état HTTP doit être « 400 » en plus de la valeur « Status » dans le corps de la réponse.

**Expérience de l’utilisateur final avec une réponse d’erreur de validation**

![Exemple de réponse d’erreur de validation](media/add-api-connector/validation-error-postal-code.png)

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prepare-a-rest-api-endpoint"></a>Préparer un point de terminaison d’API REST

Pour cette procédure pas à pas, vous devez disposer d’une API REST qui vérifie si une adresse e-mail est inscrite dans votre système principal avec un ID de fidélité. Si elle est inscrite, l’API REST doit retourner un code promotionnel d’inscription que le client peut utiliser pour acheter des marchandises sur votre application. Dans le cas contraire, l’API REST doit renvoyer un message d’erreur HTTP 409 : « L’ID de fidélité "{loyalty ID}" n’est pas associé à l’adresse e-mail "{email}". »

Le code JSON suivant illustre les données qu’Azure AD B2C enverra à votre point de terminaison d’API REST. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Une fois que votre API REST valide les données, elle doit retourner un message HTTP 200 (OK), avec les données JSON suivantes :

```json
{
    "promoCode": "24534"
}
```

En cas d’échec de la validation, l’API REST doit retourner un message d’erreur HTTP 409 (Conflit) avec l’élément JSON `userMessage`. Identity Experience Framework attend la revendication `userMessage` retournée par l’API REST. Cette revendication est présentée sous forme de chaîne à l’utilisateur en cas d’échec de la validation.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Ajouter le profil technique de l’API RESTful 

Un [profil technique RESTful](restful-technical-profile.md) prend en charge la création d’une interface avec votre propre service RESTful. Azure Active Directory B2C envoie des données au service RESTful dans une collection `InputClaims` et reçoit des données en retour dans une collection `OutputClaims`. Recherchez l’élément **ClaimsProviders** et ajoutez un fournisseur de revendications comme suit :

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Dans cet exemple, le `userLanguage` sera envoyé au service REST en tant que `lang` au sein de la charge utile JSON. La valeur de la revendication `userLanguage` contient l’ID de langue de l’utilisateur actuel. Pour plus d’informations, consultez [Programmes de résolution de revendication](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Configurer le profil technique de l’API RESTful 

Après avoir déployé votre API REST, définissez les métadonnées du profil technique `REST-ValidateProfile` pour refléter votre propre API REST, notamment :

- **ServiceUrl**. Définissez l’URL du point de terminaison de l’API REST.
- **SendClaimsIn**. Spécifiez la façon dont les revendications d’entrée sont envoyées au fournisseur de revendications RESTful.
- **AuthenticationType**. Définissez le type de l’authentification effectuée par le fournisseur de revendications RESTful. 
- **AllowInsecureAuthInProduction**. Dans un environnement de production, veillez à définir ces métadonnées sur `true`
    
Pour plus d’informations sur les configurations, consultez [Métadonnées du profil technique RESTful](restful-technical-profile.md#metadata).

Les commentaires ci -dessus `AuthenticationType` et `AllowInsecureAuthInProduction` indiquent les modifications que vous devez effectuer lorsque vous passez à un environnement de production. Pour savoir comment sécuriser vos API RESTful pour la production, consultez [Sécuriser une API RESTful](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Valider l’entrée de l’utilisateur

Pour obtenir le numéro de fidélité de l’utilisateur lors de l’inscription, vous devez permettre à l’utilisateur d’entrer ces données à l’écran. Ajoutez la revendication de sortie **loyaltyId** à la page d’inscription en l’ajoutant à l’élément `OutputClaims` de la section du profil technique d’inscription existant. Spécifiez la liste complète des revendications de sortie pour contrôler l’ordre dans lequel les revendications sont présentées à l’écran.  

Ajoutez la référence du profil technique de validation au profil technique d’inscription, qui appelle le `REST-ValidateProfile`. Le nouveau profil technique de validation sera ajouté au début de la collection `<ValidationTechnicalProfiles>` définie dans la stratégie de base. Ce comportement signifie qu’une fois la validation réussie, Azure AD B2C poursuit le processus en créant le compte dans le répertoire.   

1. Recherchez l’élément **ClaimsProviders**. Ajoutez un fournisseur de revendications comme suit :

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Inclure une revendication dans le jeton 

Pour retourner la revendication de code promotionnel à l’application par partie de confiance, ajoutez une revendication de sortie au fichier <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em>. La revendication de sortie permettra l’ajout de la revendication au jeton après un parcours utilisateur réussi et l’enverra à l’application. Modifiez l’élément de profil technique dans la section de partie de confiance pour ajouter `promoCode` en tant que revendication de sortie.
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Tester la stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser le répertoire qui contient votre locataire Azure AD en sélectionnant l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire Azure AD dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Charger une stratégie personnalisée**, puis chargez les fichiers de stratégie que vous avez modifiés : *TrustFrameworkExtensions.xml* et *SignUpOrSignin.xml*. 
1. Sélectionnez la stratégie d’inscription et de connexion que vous avez chargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire à l’aide d’une adresse de messagerie.
1. Cliquez sur le lien **S’inscrire maintenant**.
1. Dans le champ **Votre ID de fidélité**, saisissez 1234, puis cliquez sur **Continuer**. À ce stade, vous devez obtenir un message d’erreur de validation.
1. Remplacez par une autre valeur et cliquez sur **Continuer**.
1. Le jeton envoyé à votre application inclut la revendication `promoCode`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

::: zone-end

## <a name="best-practices-and-how-to-troubleshoot"></a>Meilleures pratiques et résolution des problèmes

::: zone pivot="b2c-user-flow"

### <a name="using-serverless-cloud-functions"></a>Utilisation des fonctions cloud serverless

Les fonctions serverless, comme les [déclencheurs HTTP dans Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md), fournissent une façon de créer des points de terminaison d’API à utiliser avec le connecteur d’API. Vous pouvez utiliser la fonction cloud serverless pour, [par exemple](api-connector-samples.md#api-connector-rest-api-samples), effectuer une logique de validation et limiter les inscriptions à des domaines de courrier spécifiques. La fonction cloud serverless peut également appeler et invoquer d’autres API web, magasins de données et d’autres services cloud dans le cade de scénarios plus complexes.

### <a name="best-practices"></a>Meilleures pratiques
Assurez-vous que :
* Votre API suit les contrats de demande et de réponse d’API comme indiqué ci-dessus. 
* L’**URL du point de terminaison** du connecteur d’API pointe vers le point de terminaison d’API approprié.
* Votre API recherche explicitement les valeurs null des revendications reçues dont elle dépend.
* Votre API implémente une méthode d’authentification décrite dans [Sécuriser votre connecteur d’API](secure-rest-api.md).
* Votre API répond aussi rapidement que possible pour garantir une expérience utilisateur fluide.
    * Azure AD B2C attendra un maximum de *20 secondes* pour recevoir une réponse. Si aucune réponse n’est reçue, le service effectuera *une tentative de plus* pour appeler votre API.
    * Si vous utilisez une fonction serverless ou un service web scalable, utilisez un plan d’hébergement qui conserve l’API dans un état « de veille » ou « dynamique » en production. Pour Azure Functions, il est recommandé d’utiliser au minimum le [plan Premium](../azure-functions/functions-scale.md) en production.
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
1. Accédez à **Azure AD B2C**.
2. Sous **Activités**, sélectionnez **Journaux d’audit**.
3. Filtrez la vue Liste : pour **Date**, sélectionnez l’intervalle de temps souhaité, et pour **Activité**, sélectionnez **Une API a été appelée dans le cadre d’un flux d’utilisateur**.
4. Inspectez les journaux individuels. Chaque ligne représente un connecteur d’API qui tente d’être appelé pendant un flux d’utilisateur. Si un appel d’API échoue et qu’une nouvelle tentative a lieu, il est toujours représenté par une seule ligne. `numberOfAttempts` indique le nombre de fois où votre API a été appelée. Cette valeur peut être `1` ou `2`. D’autres informations sur l’appel d’API sont détaillées dans les journaux.

![Exemple de transaction de connecteur d’API pendant l’authentification de l’utilisateur](media/add-api-connector/example-anonymized-audit-log.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="using-serverless-cloud-functions"></a>Utilisation des fonctions cloud serverless

Les fonctions cloud serverless, comme les [déclencheurs HTTP dans Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md), fournissent une méthode simple, hautement disponible et très performante pour créer des points de terminaison d’API à utiliser comme connecteurs d’API.

### <a name="best-practices"></a>Meilleures pratiques
Assurez-vous que :
* Votre API recherche explicitement les valeurs null des revendications reçues dont elle dépend.
* Votre API implémente une méthode d’authentification décrite dans [Sécuriser votre connecteur d’API](secure-rest-api.md).
* Votre API répond aussi rapidement que possible pour garantir une expérience utilisateur fluide. 
    * Si vous utilisez une fonction serverless ou un service web scalable, utilisez un plan d’hébergement qui conserve l’API dans un état « de veille » ou « dynamique » en production. Pour Azure Functions, il est recommandé d’utiliser au minimum le [plan Premium](../azure-functions/functions-scale.md).
* Assurez la haute disponibilité de votre API.
* Analysez et optimisez les performances des API en aval, des bases de données ou d’autres dépendances de votre API.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]
 
### <a name="use-logging"></a>Utiliser la journalisation

En général, il est judicieux d’utiliser les outils de journalisation activés par votre service API Web, comme [Application Insights](../azure-functions/functions-monitoring.md), pour surveiller votre API en cas de codes d’erreur inattendus, d’exceptions et de performances médiocres.
* Analysez les codes d’état HTTP autres que HTTP 200 ou 400.
* Un code d’état HTTP 401 ou 403 indique généralement un problème avec votre authentification. Vérifiez la couche d’authentification de votre API et la configuration correspondante dans le connecteur d’API.
* Si nécessaire, utilisez des niveaux de journalisation plus agressifs (par exemple, « trace » ou « debug ») lors du développement.
* Surveillez votre API en cas de temps de réponse longs.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

::: zone pivot="b2c-user-flow"

- Bien commencer avec nos [exemples](api-connector-samples.md#api-connector-rest-api-samples).
- [Sécuriser votre connecteur d’API](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

- [Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme étape d’orchestration](add-api-connector-token-enrichment.md)
- [Sécuriser votre connecteur d’API](secure-rest-api.md)
- [Reference : Profil technique RESTful](restful-technical-profile.md)

::: zone-end
