---
title: Ajouter des attributs utilisateur et personnaliser l’entrée utilisateur
titleSuffix: Azure AD B2C
description: Découvrez comment personnaliser l’entrée utilisateur et ajouter des attributs utilisateur au parcours d’inscription ou de connexion dans Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 042829ac525ed911f1a9ec984843931e8029b0a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007464"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Ajouter des attributs utilisateur et personnaliser l’entrée utilisateur dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Dans cet article, vous collectez un nouvel attribut lors de votre parcours d’inscription dans Azure Active Directory B2C (Azure AD B2C). Vous obtenez la ville des utilisateurs, vous la configurez en tant que liste déroulante et vous indiquez si elle doit être obligatoirement fournie.

> [!IMPORTANT]
> Cet exemple utilise la revendication intégrée « city ». Au lieu de cela, vous pouvez choisir l’un des [attributs intégrés Azure AD B2C](user-profile-attributes.md) pris en charge ou un attribut personnalisé. Pour utiliser un attribut personnalisé, [activez les attributs personnalisés](user-flow-custom-attributes.md). Pour utiliser un autre attribut intégré ou personnalisé, remplacez « city » par l’attribut de votre choix, par exemple l’attribut intégré *jobTitle* ou un attribut personnalisé comme *extension_loyaltyId*.  

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>Ajouter des attributs utilisateur à votre flux d’utilisateur

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez aussi utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Sélectionnez votre stratégie (par exemple, « B2C_1_SignupSignin ») pour l’ouvrir.
1. Sélectionnez **Attributs utilisateur**, puis sélectionnez l’attribut utilisateur (par exemple, « City »). 
1. Sélectionnez **Enregistrer**.

## <a name="provide-optional-claims-to-your-app"></a>Fournir des revendications facultatives à votre application

Les revendications d’application sont des valeurs qui sont retournées à l’application. Mettez à jour votre flux d’utilisateurs pour qu’il contienne les revendications souhaitées.

1. Sélectionnez votre stratégie (par exemple, « B2C_1_SignupSignin ») pour l’ouvrir.
1. Cliquez sur **Revendications de l’application**.
1. Sélectionnez les attributs que vous souhaitez renvoyer à votre application (par exemple, « Ville »).
1. Sélectionnez **Enregistrer**.
 
## <a name="configure-user-attributes-input-type"></a>Configurer le type d’entrée des attributs utilisateur

1. Sélectionnez votre stratégie (par exemple, « B2C_1_SignupSignin ») pour l’ouvrir.
1. Sélectionnez **Mises en page**.
1. Sélectionnez **Page d’inscription à un compte Local**.
1. Sous **Attributs utilisateur**, sélectionnez **Ville**.
    1. Dans la liste déroulante **Type d’entrée utilisateur**, sélectionnez **DropdownSingleSelect**. Facultatif : utilisez les boutons « monter/descendre » pour réorganiser l’ordre du texte sur la page d’inscription.
    1. Dans la liste déroulante **Facultatif**, sélectionnez **Non**.
1. Sélectionnez **Enregistrer**. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>Fournir une liste de valeurs à l’aide de collections localisées

Pour fournir une liste définie de valeurs pour l’attribut Ville : 

1. [Activez la personnalisation de la langue sur le flux d’utilisateur](language-customization.md#support-requested-languages-for-ui_locales).
1. Sélectionnez votre stratégie (par exemple, « B2C_1_SignupSignin ») pour l’ouvrir.
1. Dans la page **Langues** du flux utilisateur, sélectionnez une langue que vous souhaitez personnaliser.
1. Sous **Fichiers de ressources de niveau page**, sélectionnez **Page d’inscription au compte local**.
1. Sélectionnez **Télécharger les valeurs par défaut** (ou **Télécharger les valeurs de remplacements** si vous avez déjà modifié cette langue).
1. Créez un attribut `LocalizedCollections`.

`LocalizedCollections` est un tableau de paires `Name` et `Value`. L’ordre des éléments sera l’ordre dans lequel ils apparaissent. 

* `ElementId` est l’attribut utilisateur auquel cet attribut `LocalizedCollections` apporte une réponse.
* `Name` est la valeur affichée à l’utilisateur.
* `Value` est ce qui est renvoyé dans la revendication lorsque cette option est sélectionnée.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": true,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>Charger vos modifications

1. Une fois que vous avez terminé de modifier votre fichier JSON, revenez à votre client B2C.
1. Sélectionnez **Flux d’utilisateurs** et votre stratégie (par exemple, « B2C_1_SignupSignin ») pour l’ouvrir.
1. Sélectionnez **Langues**.
1. Sélectionnez la langue dans laquelle vous voulez traduire.
1. Sous **Fichiers de ressources de niveau page**, sélectionnez **Page d’inscription au compte local**.
1. Sélectionnez l’icône de dossier et sélectionnez le fichier JSON à charger. Ces changements sont automatiquement enregistrés dans votre flux utilisateur.

## <a name="test-your-user-flow"></a>Tester votre flux d’utilisateur

1. Sélectionnez votre stratégie (par exemple, « B2C_1_SignupSignin ») pour l’ouvrir.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Vue d’ensemble

Vous pouvez collecter les données initiales auprès des utilisateurs par le biais du parcours utilisateur d’inscription ou de connexion. Des revendications supplémentaires peuvent être collectées par la suite au moyen des parcours utilisateur de modification de profil. Chaque fois qu’Azure AD B2C recueille de manière interactive des informations de l’utilisateur, il utilise le [profil technique autodéclaré](self-asserted-technical-profile.md). Dans cet exemple, vous allez :

1. Définir une revendication « city ». 
1. Demander à l’utilisateur la ville où il vit.
1. conserver la ville dans le profil utilisateur de l’annuaire Azure AD B2C ;
1. lire la revendication de ville dans l’annuaire Azure AD B2C à chaque connexion ;
1. retourner la ville à votre application par partie de confiance après vous être connecté ou inscrit.  

## <a name="define-a-claim"></a>Définir une revendication

Une revendication fournit un stockage temporaire de données lors d’une exécution de stratégie Azure AD B2C. Le [schéma de revendications](claimsschema.md) est l’endroit où vous déclarez vos revendications. Les éléments suivants sont utilisés pour définir la revendication :

- **DisplayName** : chaîne qui définit l’étiquette orientée utilisateur.
- [DataType](claimsschema.md#datatype) : type de revendication.
- **UserHelpText** : texte permettant à l’utilisateur de comprendre ce qui est requis.
- [UserInputType](claimsschema.md#userinputtype) : type de contrôle d’entrée, tel qu’une zone de texte, une case d’option, une liste déroulante ou des sélections multiples.

Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Localisez l’élément [ClaimsSchema](claimsschema.md). Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez la revendication de ville à l’élément **ClaimsSchema**.  

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="city">
      <DisplayName>City where you work</DisplayName>
      <DataType>string</DataType>
      <UserInputType>DropdownSingleSelect</UserInputType>
      <Restriction>
        <Enumeration Text="Berlin" Value="berlin" />
        <Enumeration Text="London" Value="london" />
        <Enumeration Text="Seattle" Value="seattle" />
      </Restriction>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks>-->
```

Incluez l’attribut [SelectByDefault](claimsschema.md#enumeration) sur un `Enumeration` élément que celui-ci soit sélectionné par défaut lors du premier chargement de la page. Par exemple, pour présélectionner l’élément *London*, modifiez l’élément `Enumeration` comme dans l’exemple suivant :

```xml
<Restriction>
  <Enumeration Text="Berlin" Value="berlin" />
  <Enumeration Text="London" Value="london" SelectByDefault="true" />
  <Enumeration Text="Seattle" Value="seattle" />
</Restriction>
```

## <a name="add-a-claim-to-the-user-interface"></a>Ajouter une revendication à l’interface utilisateur

Les profils techniques suivants sont [autodéclarés](self-asserted-technical-profile.md), appelés lorsqu’un utilisateur est supposé entrer des informations :

- **LocalAccountSignUpWithLogonEmail** : flux d’inscription de compte local.
- **SelfAsserted-social** : compte fédéré avec première connexion de l’utilisateur.
- **SelfAsserted-ProfileUpdate** :flux de modification de profil.

Pour collecter la revendication de ville au cours de l’inscription, celle-ci doit être ajoutée en tant que revendication de sortie au profil technique `LocalAccountSignUpWithLogonEmail`. Remplacez ce profil technique dans le fichier d’extension. Spécifiez la liste complète des revendications de sortie pour contrôler l’ordre dans lequel les revendications sont présentées à l’écran. Recherchez l’élément **ClaimsProviders**. Ajoutez un nouveau ClaimsProvider comme suit :

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Pour collecter la revendication de ville après la connexion initiale avec un compte fédéré, celle-ci doit être ajoutée en tant que revendication de sortie au profil technique `SelfAsserted-Social`. Pour que les utilisateurs de comptes locaux et de comptes fédérés puissent modifier leurs données de profil ultérieurement, ajoutez la revendication de sortie et d’entrée au profil technique `SelfAsserted-ProfileUpdate`. Remplacez ces profils techniques dans le fichier d’extension. Spécifiez la liste complète des revendications de sortie pour contrôler l’ordre dans lequel les revendications sont présentées à l’écran. Recherchez l’élément **ClaimsProviders**. Ajoutez un nouveau ClaimsProvider comme suit :

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Lire et écrire une revendication

Les profils techniques suivants sont des [profils techniques Active Directory](active-directory-technical-profile.md) qui lisent et écrivent des données dans Azure Active Directory.  
Dans les profils techniques Active Directory respectifs, utilisez `PersistedClaims` pour écrire des données dans le profil utilisateur, et `OutputClaims` pour lire des données depuis le profil utilisateur.

Remplacez ces profils techniques dans le fichier d’extension. Recherchez l’élément **ClaimsProviders**.  Ajoutez un nouveau ClaimsProvider comme suit :

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user resets the password. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Inclure une revendication dans le jeton 

Pour retourner la revendication de ville à l’application par partie de confiance, ajoutez une revendication de sortie au fichier <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em>. Après un parcours utilisateur réussi, la revendication de sortie est ajoutée au jeton et envoyée à l’application. Modifiez l’élément de profil technique dans la section de partie de confiance pour ajouter la ville en tant que revendication de sortie.
 
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="upload-and-test-your-updated-custom-policy"></a>Téléchargez et testez votre stratégie personnalisée mise à jour

1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Charger une stratégie personnalisée**.
1. Chargez les fichiers de stratégie que vous avez modifiés.

### <a name="test-the-custom-policy"></a>Tester la stratégie personnalisée

1. Sélectionnez votre stratégie de partie de confiance, par exemple `B2C_1A_signup_signin`.
1. Pour **Application**, sélectionnez une application web que vous avez [précédemment inscrite](tutorial-register-applications.md). L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez le bouton **Exécuter maintenant**.
1. Dans la page d’inscription ou de connexion, sélectionnez **S’inscrire maintenant**. Achevez d’entrer les informations utilisateur, dont le nom de la ville, puis cliquez sur **Créer**. Vous devez voir le contenu du jeton qui a été retourné.

::: zone-end

L’écran d’inscription doit ressembler à la capture d’écran suivante :

![Capture d’écran de l’option d’inscription modifiée](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

Le jeton envoyé à votre application inclut la revendication `city`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Berlin"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="optional-localize-the-ui"></a>[Facultatif] Localiser l’interface utilisateur

Azure AD B2C vous permet d’adapter votre stratégie à différentes langues. Pour plus d’informations, découvrez comment [personnaliser l’expérience linguistique](language-customization.md). Pour localiser la page d’inscription, [configurez la liste des langues prises en charge](language-customization.md#set-up-the-list-of-supported-languages) puis [fournissez des étiquettes spécifique de la langue](language-customization.md#provide-language-specific-labels).

> [!NOTE]
> Lorsque vous utilisez la commande `LocalizedCollection` avec les étiquettes spécifiques de la langue, vous pouvez supprimer la collection `Restriction` de la [définition de revendication](#define-a-claim).

L’exemple suivant montre comment fournir la liste de villes pour l’anglais et l’espagnol. La collection `Restriction` de la revendication *city* est définie avec une liste d’éléments pour l’anglais et l’espagnol. L’attribut [SelectByDefault](claimsschema.md#enumeration) a pour effet de sélectionner un élément par défaut lors du premier chargement de la page.
   
```xml
<!-- 
<BuildingBlocks>-->
  <Localization Enabled="true">
    <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
    </SupportedLanguages>
    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="city" TargetCollection="Restriction">
          <Item Text="Berlin" Value="Berlin"></Item>
          <Item Text="London" Value="London" SelectByDefault="true"></Item>
          <Item Text="Seattle" Value="Seattle"></Item>
        </LocalizedCollection>
      </LocalizedCollections>
    </LocalizedResources>
    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="city" TargetCollection="Restriction">
          <Item Text="Berlina" Value="Berlin"></Item>
          <Item Text="Londres" Value="London" SelectByDefault="true"></Item>
          <Item Text="Seattle" Value="Seattle"></Item>
        </LocalizedCollection>
      </LocalizedCollections>
    </LocalizedResources>
  </Localization>
<!-- 
</BuildingBlocks>-->
```

Après avoir ajouté l’élément localization, [modifiez la définition du contenu avec la localisation](language-customization.md#edit-the-content-definition-with-the-localization). Dans l’exemple suivant, les ressources personnalisées en anglais (en) et en espagnol (es) sont ajoutées à la page d’inscription :
   
```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
   <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
   </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks>-->
```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur l’élément [ClaimsSchema](claimsschema.md) dans la référence IEF.
- Découvrez comment [utiliser des attributs personnalisés dans Azure AD B2C](user-flow-custom-attributes.md).

::: zone-end
