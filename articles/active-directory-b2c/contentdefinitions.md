---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Spécifiez l’élément ContentDefinitions d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/12/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: af77df3d3839a019e4977b32c6b8b138b375b4f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007445"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vous pouvez personnaliser l’apparence de tout [profil technique autodéclaré](self-asserted-technical-profile.md). Azure Active Directory B2C (Azure AD B2C) exécute le code dans le navigateur de votre client et utilise une approche moderne appelée Partage des ressources Cross-Origin (CORS).

Pour personnaliser l’interface utilisateur, vous spécifiez une URL dans l’élément **ContentDefinition**, avec du contenu HTML personnalisé. Dans le profil technique autodéclaré ou **OrchestrationStep**, vous pointez vers cet identificateur de définition de contenu. La définition de contenu peut contenir un élément **LocalizedResourcesReferences** qui spécifie une liste de ressources localisées à charger. Azure AD B2C fusionne des éléments d’interface utilisateur avec le contenu HTML chargé à partir de votre URL, puis affiche la page à l’utilisateur.

L’élément **ContentDefinitions** contient des URL de modèles HTML5 utilisables dans un parcours utilisateur. L’URI de page HTML5 est utilisé pour une étape d’interface utilisateur spécifiée. Il peut s’agir, par exemple, de pages de connexion ou d’inscription, de réinitialisation de mot de passe ou d’erreur. Vous pouvez modifier l’apparence en remplaçant l’URI LoadUri du fichier HTML5. Vous pouvez aussi créer des définitions de contenu correspondant à vos besoins. Cet élément peut contenir une référence de ressources localisées à l’identificateur de localisation spécifié dans l’élément [Localization](localization.md).

L’exemple suivant montre l’identificateur de définition de contenu et la définition de ressources localisées :

```xml
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Les métadonnées du profil technique autodéclaré **LocalAccountSignUpWithLogonEmail** contiennent l’identificateur de définition de contenu **ContentDefinitionReferenceId** défini sur `api.localaccountsignup`.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

L’élément **ContentDefinitionParameter** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Id | Oui | Un identificateur de définition de contenu. La valeur est l’une de celles spécifiées dans la section **ID de définition de contenu** plus loin dans cette page. |

L’élément **ContentDefinition** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Chaîne contenant l’URL de la page HTML5 de la définition de contenu. |
| RecoveryUri | 1:1 | Chaîne contenant l’URL de la page HTML pour l’affichage d’une erreur liée à la définition de contenu. Non utilisé actuellement. La valeur doit être `~/common/default_page_error.html`. |
| DataUri | 1:1 | Chaîne contenant l’URL relative d’un fichier HTML qui fournit l’expérience utilisateur à appeler pour l’étape. |
| Métadonnées | 0:1 | Collection de paires clé/valeur contenant les métadonnées utilisées par la définition de contenu. |
| LocalizedResourcesReferences | 0:1 | Collection de références de ressources localisées. Cet élément permet de personnaliser la localisation d’une interface utilisateur et d’un attribut de revendications. |

### <a name="loaduri"></a>LoadUri

L’élément **LoadUri** est utilisé pour spécifier l’URL de la page HTML5 pour la définition du contenu. Les [packs de démarrage de stratégie personnalisée](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack) Azure AD B2C sont fournis avec des définitions de contenu qui utilisent des pages HTML d’Azure AD B2C. L’élément **LoadUri** commence par `~`, qui est un chemin d’accès relatif vers votre locataire Azure AD B2C.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  ...
</ContentDefinition>
```

Vous pouvez [personnaliser l’interface utilisateur avec des modèles HTML](customize-ui-with-html.md). Lorsque vous utilisez des modèles HTML, indiquez une URL absolue. L’exemple suivant illustre une définition de contenu avec un modèle HTML :

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="datauri"></a>DataUri

L’élément **DataUri** est utilisé pour spécifier l’identificateur de page. Azure AD B2C utilise l’identificateur de page pour charger et lancer des éléments d’interface utilisateur et du code JavaScript côté client. Le format de la valeur est `urn:com:microsoft:aad:b2c:elements:page-name:version`. Le tableau suivant répertorie des identificateurs de page que vous pouvez utiliser.

| Identificateur de page | Description |
| ----- | ----------- |
| `globalexception` | Affiche une page d’erreur quand une exception ou une erreur sont rencontrées. |
| `providerselection`, `idpselection` | Répertorie les fournisseurs d’identité parmi lesquels les utilisateurs peuvent choisir au moment de la connexion.  |
| `unifiedssp` | Affiche un formulaire pour la connexion avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Cette valeur fournit également la fonctionnalité de maintien de la connexion et le lien « Vous avez oublié votre mot de passe ? » . |
| `unifiedssd` | Affiche un formulaire pour la connexion avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Cet identificateur de page est déconseillé. Utiliser l’identificateur de page `unifiedssp` à la place.  |
| `multifactor` | Vérifie des numéros de téléphone (par voie textuelle ou vocale) au cours d’une inscription ou d’une connexion. |
| `selfasserted` | Affiche un formulaire permettant de collecter des données auprès d'un utilisateur. Par exemple, permet aux utilisateurs de créer ou de mettre à jour leur profil. |

### <a name="select-a-page-layout"></a>Sélectionner une mise en page

Vous pouvez activer le [code JavaScript côté client](javascript-and-page-layout.md) en insérant `contract` entre `elements` et le type de page. Par exemple : `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

La partie [version](page-layout.md) de `DataUri` spécifie le package de contenu contenant du code HTML, CSS et JavaScript pour les éléments d’interface utilisateur de votre stratégie. Si vous prévoyez d’activer le code JavaScript côté client, les éléments sur lesquels vous basez votre JavaScript doivent être immuables. S’ils ne le sont pas, toute modification pourrait provoquer un comportement inattendu sur vos pages d’utilisateurs. Pour éviter ces problèmes, imposez l’utilisation d’une mise en page et spécifiez une version de mise en page. Toutes les définitions de contenu sur lesquelles vous avez basé votre code JavaScript sont ainsi immuables. Même si vous ne prévoyez pas d’activer JavaScript, vous devez néanmoins toujours spécifier la version de la disposition de page pour vos pages.

L’exemple suivant montre le **DataUri** de `selfasserted` version `1.2.0` :

```xml
<!-- 
<BuildingBlocks> 
  <ContentDefinitions>-->
    <ContentDefinition Id="api.localaccountpasswordreset">
      <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
      <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
      <Metadata>
        <Item Key="DisplayName">Local account change password page</Item>
      </Metadata>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions> 
</BuildingBlocks> -->
```

#### <a name="migrating-to-page-layout"></a>Migration vers la mise en page

Pour migrer de l’ancienne valeur **DataUri** (sans contrat de page) vers la version de mise en page, ajoutez le mot `contract` suivi par une version de page. Utilisez le tableau suivant pour migrer de l’ancienne valeur **DataUri** vers la version de mise en page.

| Ancienne valeur DataUri | Nouvelle valeur DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.5` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.5` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.5` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.5` |

L’exemple suivant montre les identificateurs de définition de contenu et les **DataUri** correspondants pour la [dernière version de la page](page-layout.md) :

```xml
<!-- 
<BuildingBlocks> -->
  <ContentDefinitions>
    <ContentDefinition Id="api.error">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections.signup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.5</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted.profileupdate">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.7</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.phonefactor">
      <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.5</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!-- 
</BuildingBlocks> -->
```

### <a name="metadata"></a>Métadonnées

Un élément **Metadata** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| Élément | 0:n | Métadonnées relatives à la définition de contenu. |

L’élément **Item** de l’élément **Metadata** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Clé | Oui | Clé de métadonnées.  |

#### <a name="metadata-keys"></a>Clés de métadonnées

La définition de contenu prend en charge les éléments de métadonnées suivants :

| Clé | Obligatoire | Description |
| --------- | -------- | ----------- |
| DisplayName | Non | Chaîne contenant le nom de la définition de contenu. |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

L’élément **LocalizedResourcesReferences** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | Liste de références de ressources localisées pour la définition de contenu. |

L’élément **LocalizedResourcesReference** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Language | Oui | Chaîne contenant une langue prise en charge pour la stratégie conformément à la spécification RFC 5646 - Tags pour l’identification des langues. |
| LocalizedResourcesReferenceId | Oui | Identificateur de l’élément **LocalizedResources**. |

L’exemple suivant montre une définition de contenu d’inscription ou de connexion avec une référence à la localisation pour l’anglais, le français et l’espagnol :

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Pour savoir comment ajouter la prise en charge de la localisation à vos définitions de contenu, voir [Localisation](localization.md).

## <a name="content-definition-ids"></a>ID de définition de contenu

L’attribut d’ID de l’élément **ContentDefinition** spécifie le type de page qui se rapporte à la définition de contenu. L’élément définit le contexte que va appliquer un modèle HTML5/CSS personnalisé. Le tableau suivant décrit l’ensemble d’ID de définition de contenu reconnus par l’Infrastructure d’expérience d’identité, ainsi que les types de pages associés. Vous pouvez créer vos propres définitions de contenu avec un identificateur arbitraire.

| id | Modèle par défaut | Description |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Page d’erreur** : affiche une page d’erreur quand une exception ou une erreur sont rencontrées. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Page de sélection de fournisseur d’identité** : répertorie les fournisseurs d’identité parmi lesquels les utilisateurs peuvent choisir au moment de la connexion. Il s’agit généralement de fournisseurs d’identité d’entreprise, de fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou de comptes locaux. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Sélection du fournisseur d’identité pour l’inscription** : répertorie les fournisseurs d’identité parmi lesquels les utilisateurs peuvent choisir au moment de l’inscription. Il s’agit généralement de fournisseurs d’identité d’entreprise, de fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou de comptes locaux. |
| **api.localaccountpasswordreset** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page d’oubli de mot de passe** : affiche un formulaire que les utilisateurs doivent remplir pour lancer une réinitialisation de mot de passe. |
| **api.localaccountsignin** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page de connexion de compte local** : affiche un formulaire pour la connexion avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir une zone de saisie de texte et une zone de saisie de mot de passe. |
| **api.localaccountsignup** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page d’inscription de compte local** : affiche un formulaire pour l’inscription avec un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir différentes commandes de saisie telles qu’une zone de saisie de texte, une zone de saisie de mot de passe, un bouton radio, des zones de liste déroulante à sélection unique ou des cases à sélection multiples. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Page d’authentification multifacteur** : vérifie des numéros de téléphone (par voie textuelle ou vocale) au cours d’une inscription ou d’une connexion. |
| **api.selfasserted** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Page d’inscription de compte social** : affiche un formulaire que les utilisateurs doivent remplir au moment de l’inscription à l’aide d’un compte existant d’un fournisseur d’identité de réseau social. Cette page est similaire à la page d’inscription au compte de réseau social ci-dessus, à l’exception des champs de saisie de mot de passe. |
| **api.selfasserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Page de mise à jour de profil** : affiche un formulaire auquel les utilisateurs peuvent accéder pour mettre à jour leur profil. Cette page est similaire à la page d’inscription au compte de réseau social, à l’exception des champs de saisie de mot de passe. |
| **api.signuporsignin** | [unified.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Page d’inscription ou de connexion unifiée** : gère le processus d’inscription et de connexion des utilisateurs. Les utilisateurs peuvent utiliser des fournisseurs d’identité d’entreprise, des fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou des comptes locaux. |

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un exemple de personnalisation de l’interface utilisateur à l’aide de définitions de contenu, voir :

[Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée](customize-ui-with-html.md)
