---
title: À propos des connecteurs d’API dans Azure AD B2C
description: Utilisez des connecteurs d’API Azure Active Directory (Azure AD) pour personnaliser et étendre vos flux d’utilisateurs à l’aide d’API REST ou de webhooks sortants vers des sources de données d’identité externes.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 11/02/2021
ms.author: kengaderdus
author: kengaderdus
manager: CelesteDG
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9b5d3c49019d1953dd0deb5d498291d92af4aba1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131436932"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows-with-external-identity-data-sources"></a>Utiliser des connecteurs d’API pour personnaliser et étendre des flux d’inscription d’utilisateurs avec des sources de données d’identité externes 

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

## <a name="overview"></a>Vue d’ensemble 

En tant que développeur ou administrateur informatique, vous pouvez utiliser des connecteurs d’API pour intégrer vos flux d’utilisateurs d’inscription à des API REST pour personnaliser l’inscription et l’intégrer à des systèmes externes. Par exemple, avec les connecteurs d’API, vous pouvez :

- **Valider des données d’entrée utilisateur**. Validez par rapport à des données utilisateur incorrectes ou non valides. Par exemple, vous pouvez valider les données fournies par l’utilisateur par rapport à des données existantes dans une banque de données externe ou une liste de valeurs autorisées. En cas d’invalidité, vous pouvez demander à un utilisateur de fournir des données valides ou empêcher l’utilisateur de continuer le processus d’inscription.
- **Vérifier l’identité de l’utilisateur**. Utilisez un service de vérification d’identité ou des sources de données d’identité externes pour ajouter un niveau supplémentaire de sécurité aux décisions de création de compte.
- **Intégrer à un flux de travail d’approbation personnalisé**. Connectez-vous à un système d’approbation personnalisé pour gérer et limiter la création de comptes.
- **Augmentez les jetons avec des attributs de sources externes**. Augmentez le nombre de jetons, avec des attributs relatifs à l’utilisateur, issus de sources externes à Azure AD B2C telles que les systèmes Cloud, les magasins d’utilisateurs personnalisés, les systèmes d’autorisation personnalisés, les services d’identité hérités, et bien plus.
- **Remplacer des attributs utilisateur**. Reformatez ou attribuez une valeur à un attribut collecté auprès de l’utilisateur. Par exemple, si un utilisateur entre le prénom entier en lettres minuscules ou majuscules, vous pouvez modifier sa mise en forme en utilisant une majuscule uniquement pour la première lettre. 
- **Exécuter une logique métier personnalisée**. Vous pouvez déclencher des événements en aval dans vos systèmes ce cloud pour envoyer des notifications Push, mettre à jour des bases de données d’entreprise, gérer des autorisations, auditer des bases de données et effectuer d’autres actions personnalisées.

Un connecteur d’API fournit à Azure AD B2C les informations nécessaires pour appeler le point de terminaison d’API en définissant l’URL de point de terminaison HTTP et l’authentification pour l’appel d’API. Une fois que vous avez configuré un connecteur d’API, vous pouvez l’activer pour une étape spécifique dans un flux utilisateur. Quand un utilisateur atteint cette étape dans le flux d’inscription, le connecteur d’API est appelé et se matérialise en tant que requête HTTP POST à votre API, en envoyant des informations utilisateur (« revendications ») en tant que paires clé-valeur dans un corps JSON. La réponse d’API peut affecter l’exécution du flux utilisateur. Par exemple, la réponse d’API peut empêcher un utilisateur de s’inscrire, demander à l’utilisateur d’entrer à nouveau des informations ou écraser les attributs de l’utilisateur.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Où activer un connecteur d’API dans un flux utilisateur

Il existe trois emplacements dans un flux utilisateur où vous pouvez activer un connecteur d’API :

- **Après la fédération avec un fournisseur d’identité lors de l’inscription** (s’applique uniquement aux expériences d’inscription)
- **Avant de créer l’utilisateur** (s’applique aux expériences d’inscription uniquement)
- **Avant d’envoyer le jeton (préversion)** (s’applique aux inscriptions et aux connexions)

### <a name="after-federating-with-an-identity-provider-during-sign-up"></a>Après la fédération avec un fournisseur d’identité lors de l’inscription

Un connecteur d’API à cette étape du processus d’inscription est appelé immédiatement après que l’utilisateur s’est authentifié auprès d’un fournisseur d’identité (comme Google, Facebook et Azure AD). Cette étape précède la ***page de collection d’attributs***, qui est le formulaire présenté à l’utilisateur pour collecter des attributs utilisateur. Cette étape n’est pas appelée si un utilisateur s’inscrit auprès d’un compte local. Voici quelques exemples de scénarios de connecteur d’API que vous pouvez activer à cette étape :

- Utilisez l’adresse e-mail ou l’identité fédérée que l’utilisateur a fournie pour rechercher des revendications dans un système existant. Retournez ces revendications à partir du système existant, complétez la page de collection d’attributs, puis rendez-les disponibles pour retourner dans le jeton.
- Implémentez une liste d’autorisation ou de refus en fonction de l’identité sociale.

### <a name="before-creating-the-user"></a>avant la création de l’utilisateur

Un connecteur d’API à cette étape du processus d’inscription est appelé après la page de collection d’attributs, si elle est inclue. Cette étape est toujours appelée avant qu’un compte d’utilisateur ne soit créé. Voici quelques exemples de scénarios que vous pouvez activer à cette étape au cours de l’inscription :

- Validez les données d’entrée utilisateur et demandez à un utilisateur de renvoyer des données.
- Bloquer l’inscription utilisateur en fonction des données entrées par l’utilisateur.
- Vérifier l’identité de l’utilisateur.
- Interrogez des systèmes externes pour obtenir des données existantes sur l’utilisateur afin de les retourner dans le jeton d’application ou de les stocker dans Azure AD.

### <a name="before-sending-the-token-preview"></a>Avant d’envoyer le jeton (préversion)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Un connecteur d’API à cette étape du processus d’inscription ou de connexion est appelé avant l’émission d’un jeton. Voici quelques exemples de scénarios que vous pouvez activer à cette étape :
- Enrichissement du jeton avec des attributs relatifs à l’utilisateur provenant de sources différentes du répertoire, y compris les systèmes d’identité hérités, les systèmes RH, les magasins d’utilisateurs externes, etc.
- Enrichissement du jeton avec des attributs de groupe ou de rôle que vous stockez et gérez dans votre propre système d’autorisation. 
- Application de transformations ou manipulations de revendications à des valeurs de revendications dans le répertoire.

::: zone-end

::: zone pivot="b2c-custom-policy"

La plateforme Identity Experience Framework sur laquelle repose Azure Active Directory B2C (Azure AD B2C) peut s’intégrer avec des API RESTful à l’intérieur d’un parcours utilisateur. Cet article explique comment créer un parcours utilisateur interagissant avec un service RESTful à l’aide d’un [profil technique RESTful](restful-technical-profile.md).

Azure AD B2C vous permet d’ajouter votre propre logique métier à un parcours utilisateur en appelant votre propre service RESTful. La plateforme Identity Experience Framework peut envoyer et recevoir des données de votre service RESTful pour échanger des revendications. Vous pouvez par exemple :

- **Utiliser une source de données d’identité externe pour valider les données d’entrée utilisateur**. Par exemple, vous pouvez vérifier que l’adresse e-mail fournie par l’utilisateur existe dans la base de données de votre client et, si ce n’est pas le cas, présenter une erreur. Vous pouvez également considérer les connecteurs d’API comme un moyen de prendre en charge des webhooks sortants, car l’appel est effectué quand un événement se produit, par exemple une inscription.
- **Traiter des revendications**. Si un utilisateur entre son prénom entièrement en minuscules ou majuscules, votre API REST peut modifier la mise en forme en utilisant une majuscule uniquement pour la première lettre avant de le renvoyer à Azure AD B2C. Toutefois, lors de l’utilisation d’une stratégie personnalisée, [ClaimsTransformations](claimstransformations.md) est préférable à l’appel d’une API RESTful. 
- **Enrichir les données utilisateur de façon dynamique en les intégrant davantage avec des applications métier d’entreprise**. votre service RESTful peut recevoir l’adresse e-mail de l’utilisateur, interroger la base de données de clients et retourner le numéro de fidélité de l’utilisateur à Azure AD B2C. Les revendications retournées peuvent alors être stockées dans le compte Azure AD de l’utilisateur, évaluées dans les étapes d’orchestration suivantes ou incluses dans le jeton d’accès.
- **Exécuter une logique métier personnalisée**. Vous pouvez envoyer des notifications Push, mettre à jour des bases de données d’entreprise, exécuter un processus de migration utilisateur, gérer les autorisations, auditer des bases de données et effectuer d’autres flux de travail.

![Diagramme d’un échange de revendications de service RESTful](media/api-connectors-overview/restful-service-claims-exchange.png)

> [!NOTE]
> S’il n’y a pas de réponse ou que la réponse est lente depuis le service RESTful vers Azure AD B2C, le délai d’expiration est de 30 secondes et le nombre de tentatives est de 2 (ce qui signifie qu’il y a 3 tentatives au total). Les paramètres de délai d’expiration et de nombre de tentatives ne sont pas configurables actuellement.

## <a name="calling-a-restful-service"></a>Appel d’un service RESTful

Cette interaction inclut un échange de revendications d’informations entre les revendications de l’API REST et Azure AD B2C. Vous pouvez concevoir l’intégration aux services RESTful comme suit :

- **Profil technique de validation**. L’appel au service RESTful se produit à l’intérieur d’un [profil technique de validation](validation-technical-profile.md) du [profil technique autodéclaré](self-asserted-technical-profile.md) spécifié, ou d’un [contrôle d’affichage de vérification](display-control-verification.md) d’un [contrôle d’affichage](display-controls.md). Le profil technique de validation valide les données fournies par l’utilisateur avant la suite du parcours utilisateur. Avec le profil technique de validation, vous pouvez :

  - Envoyer des revendications à votre API REST.
  - Valider les revendications et lever des messages d’erreur personnalisés qui s’affichent pour l’utilisateur.
  - Renvoyer les revendications de l’API REST aux étapes suivantes de l’orchestration.

- **Échange de revendications**. Vous pouvez configurer un échange direct de revendications en appelant un profil technique d’API REST directement à partir d’une étape d’orchestration d’un [parcours utilisateur](userjourneys.md). Cette définition est limitée à :

  - Envoyer des revendications à votre API REST.
  - Valider les revendications et lever des messages d’erreur personnalisés qui sont retournés à l’application.
  - Renvoyer les revendications de l’API REST aux étapes suivantes de l’orchestration.

Vous pouvez ajouter un appel d’API REST à n’importe quelle étape du parcours utilisateur défini par une stratégie personnalisée. Par exemple, vous pouvez appeler une API REST :

- lors de la connexion, juste qu’Azure AD B2C valide les informations d’identification ;
- immédiatement après la connexion ;
- avant qu’Azure AD B2C crée un compte dans l’annuaire ;
- après qu’Azure AD B2C a créé un compte dans l’annuaire ;
- avant qu’Azure AD B2C émette un jeton d’accès.

![Collecte de profil technique de validation](media/api-connectors-overview/validation-technical-profile.png)

## <a name="sending-data"></a>Envoi de données

Dans le [profil technique RESTful](restful-technical-profile.md), l’élément `InputClaims` contient une liste de revendications à envoyer à votre service RESTful. Vous pouvez mapper le nom de votre revendication au nom défini dans le service RESTful, définir une valeur par défaut et utiliser des [programmes de résolution de revendications](claim-resolver-overview.md).

Vous pouvez configurer la façon dont les revendications d’entrée sont envoyées au fournisseur de revendications RESTful en utilisant l’attribut SendClaimsIn. Les valeurs possibles sont les suivantes :

- **Body**, envoyé dans le corps de la requête HTTP POST au format JSON.
- **Form**, envoyé dans le corps de la requête HTTP POST au format de valeur de clé séparée par des perluètes (« & »).
- **Header**, envoyé dans l’en-tête de requête HTTP GET.
- **QueryString**, envoyé dans la chaîne de requête HTTP GET.

Quand l’option **Body** est configurée, le profil technique de l’API REST vous permet d’envoyer une charge utile JSON complexe à un point de terminaison. Pour plus d’informations, consultez [Envoyer une charge utile JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Réception de données

L’élément `OutputClaims` du [profil technique RESTful](restful-technical-profile.md) contient une liste de revendications retournées par l’API REST. Il se peut que vous deviez mapper le nom de la revendication définie dans votre stratégie au nom défini dans l’API REST. Vous pouvez également inclure des revendications qui ne sont pas retournées par le fournisseur d’identité de l’API REST, pour autant que vous définissiez l’attribut DefaultValue.

Les revendications de sortie analysées par le fournisseur de revendications RESTful s’attendent toujours à analyser une réponse de corps JSON plate, par exemple :

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Les revendications de sortie doivent ressembler à l’extrait xml suivant :

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

### <a name="handling-null-values"></a>Traitement des valeurs Null 

Une valeur Null dans une base de données est utilisée lorsque la valeur d’une colonne est inconnue ou manquante.  N’utilisez pas de clés JSON avec la valeur `null`. Dans l’exemple suivant, l’e-mail renvoie la valeur `null` :

```json
{
  "name": "Emily Smith",
  "email": null,
  "loyaltyNumber":  1234
}
```

Lorsqu’un élément est Null, vous pouvez :

- omettre la paire clé-valeur du JSON.
- retourner une valeur qui correspond au type de données de revendication Azure AD B2C. Par exemple, le type de données `string` renvoie une chaîne vide `""`. Le type de données `integer` renvoie la valeur zéro `0`. Le type de données `dateTime` renvoie la valeur minimale `1970-00-00T00:00:00.0000000Z`.

L’exemple suivant montre comment gérer une valeur Null. L’e-mail est omis du JSON :

```json
{
  "name": "Emily Smith",
  "loyaltyNumber":  1234
}
```

### <a name="parse-a-nested-json-body"></a>Analyser un corps JSON imbriqué

Pour analyser une réponse de corps JSON imbriquée, définissez les métadonnées ResolveJsonPathsInJsonTokens sur true. Dans la revendication de sortie, définissez PartnerClaimType sur l’élément de chemin d’accès JSON que vous souhaitez générer.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


Les revendications de sortie doivent ressembler à l’extrait xml suivant :

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="localize-the-rest-api"></a>Localisez l’API REST

Dans un profil technique RESTful, vous pouvez envoyer la langue/les paramètres régionaux de la session active et, si nécessaire, déclencher un message d’erreur localisé. Le [programme de résolution des revendications](claim-resolver-overview.md) vous permet d’envoyer une revendication contextuelle, telle que la langue de l’utilisateur. L’exemple suivant présente un profil technique RESTful illustrant ce scénario.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Gestion des messages d’erreur

Il se peut que votre API REST doive retourner un message d’erreur tel que « Utilisateur introuvable dans le système CRM ». Quand une erreur se produit, l’API REST doit retourner un message d’erreur HTTP 409 (code d’état de réponse Conflit). Pour plus d’informations, consultez le [Profil technique RESTful ](restful-technical-profile.md#returning-validation-error-message).

Ce comportement ne peut être obtenu qu’en appelant un profil technique d’API REST à partir d’un profil technique de validation. Cela permet à l’utilisateur de corriger les données sur la page et de réexécuter la validation lors de l’envoi de la page.

Si vous référencez un profil technique d’API REST directement à partir d’un parcours utilisateur, l’utilisateur est redirigé vers l’application basée sur les revendications avec le message d’erreur approprié.

::: zone-end

## <a name="development-of-your-rest-api"></a>Développer votre API REST 

Votre API REST peut être développée sur n’importe quelle plateforme et écrite dans n’importe quel langage de programmation tant qu’elle est sécurisée et qu’elle peut envoyer et recevoir des revendications au format JSON.

La demande adressée à votre service d’API REST provient de serveurs Azure AD B2C. Le service de l’API REST doit être publié sur un point de terminaison HTTPS accessible publiquement. Les appels d’API REST proviennent d’une adresse IP de centre de données Azure.

Vous pouvez utiliser des fonctionnalités Cloud serverless comme les [déclencheurs HTTP dans Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md) pour un développement facile.

Nous vous conseillons de concevoir votre service d’API REST et ses composants sous-jacents (tels que la base de données et le système de fichiers) pour qu’ils soient hautement disponibles.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="next-steps"></a>Étapes suivantes

::: zone pivot="b2c-user-flow"

- Découvrez comment [Ajouter un connecteur d’API pour modifier les expériences d’inscription](add-api-connector.md)
- Découvrez comment [Ajouter un connecteur d’API pour enrichir des jetons avec des revendications externes](add-api-connector-token-enrichment.md)
- Découvrez comment [Sécuriser votre connecteur d’API](secure-rest-api.md)
- Bien commencer grâce à nos [exemples](api-connector-samples.md#api-connector-rest-api-samples)

::: zone-end

::: zone pivot="b2c-custom-policy"

Consultez les articles suivants pour obtenir des exemples d’utilisation d’un profil technique RESTful :

- [Procédure pas à pas : ajouter un connecteur d’API à un flux d’utilisateur d’inscription](add-api-connector.md)
- [Procédure pas à pas : Ajouter des échanges de revendications de l’API REST aux stratégies personnalisées dans Azure Active Directory B2C](add-api-connector-token-enrichment.md)
- [Sécuriser vos services d’API REST](secure-rest-api.md)
- [Reference : Profil technique RESTful](restful-technical-profile.md)

::: zone-end
