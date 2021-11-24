---
title: Empêcher l’accélération automatique de la connexion à l’aide de la stratégie de découverte du domaine d’accueil
titleSuffix: Azure AD
description: Découvrez comment empêcher l’accélération automatique de domain_hint vers des fournisseurs d’identité fédérés.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.openlocfilehash: 912299553e48bbaec6c815e44f91a6ac0b42441e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547466"
---
# <a name="disable-auto-acceleration-sign-in"></a>Désactiver la connexion d’auto-accélération

La [stratégie Découverte de domaine d’accueil](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) offre aux administrateurs plusieurs moyens de contrôler le mode et l’emplacement d’authentification de leurs utilisateurs. La section `domainHintPolicy` de la stratégie HRD est utilisée pour faciliter la migration d’utilisateurs fédérés vers des informations d’identification managées dans le cloud telles que [FIDO](../authentication/howto-authentication-passwordless-security-key.md), en veillant à ce qu’ils visitent toujours la page de connexion à Azure AD et ne soient pas automatiquement accélérés vers un fournisseur d’identité fédéré en raison d’indications de domaine.

Cette stratégie est nécessaire dans les situations où des applications qu’un administrateur ne peut ni contrôler ni mettre à jour ajoutent des indications de domaine lors de la connexion.  Par exemple, `outlook.com/contoso.com` envoie l’utilisateur vers une page de connexion avec le paramètre `&domain_hint=contoso.com` ajouté, afin d’accélérer automatiquement l’utilisateur directement vers le fournisseur d’identité fédéré pour le domaine `contoso.com`. Les utilisateurs disposant d’informations d’identification managées envoyées à un fournisseur d’identité fédéré ne peuvent pas se connecter à l’aide de ces informations, ce qui a pour effet de réduire la sécurité et de frustrer les utilisateurs avec des expériences de connexion aléatoires. Les administrateurs déployant des informations d’identification managées [doivent également configurer cette stratégie](#suggested-use-within-a-tenant) pour s’assurer que les utilisateurs peuvent toujours utiliser ces informations.

## <a name="domainhintpolicy-details"></a>Détails de DomainHintPolicy

La section DomainHintPolicy de la stratégie HRD est un objet JSON qui permet à un administrateur de refuser à certains domaines et applications l’utilisation d’indications de domaine.  Sur le plan fonctionnel, cela indique à la page de connexion à Azure AD de se comporter comme si un paramètre `domain_hint` dans la demande de connexion était introuvable.

### <a name="the-respect-and-ignore-policy-sections"></a>Sections Respect et Ignore de la stratégie

|Section | Signification | Valeurs |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Si cette indication de domaine est envoyée dans la demande, l’ignorer. |Tableau d’adresses de domaine (par exemple, `contoso.com`). Prend également en charge `all_domains`.|
|`RespectDomainHintForDomains`| Si cet indicateur de domaine est envoyé dans la demande, le respecter même si `IgnoreDomainHintForApps` indique que l’application dans la demande ne doit pas accélérer automatiquement. Utilisée pour ralentir le déploiement d’indications de domaine en cours de dépréciation au sein de votre réseau. Vous pouvez indiquer que certains domaines doivent toujours être accélérés. | Tableau d’adresses de domaine (par exemple, `contoso.com`). Prend également en charge `all_domains`.|
|`IgnoreDomainHintForApps`| Si une demande de cette application inclut une indication de domaine, l’ignorer. | Tableau d’ID d’application (GUID). Prend également en charge `all_apps`.|
|`RespectDomainHintForApps` |Si une demande de cette application inclut une indication de domaine, la respecter même si la section `IgnoreDomainHintForDomains` inclut ce domaine. Utilisée pour garantir que certaines applications continuent de fonctionner si vous découvrez qu’elles s’arrêtent sans indication de domaine. | Tableau d’ID d’application (GUID). Prend également en charge `all_apps`.|

### <a name="policy-evaluation"></a>Évaluation de la stratégie

La logique DomainHintPolicy s’exécute sur chaque demande entrante contenant une indication de domaine, et accélère en fonction de deux éléments de données dans la demande : le domaine dans l’indication de domaine et l’ID client (l’application). En bref, l’instruction « Respect » pour un domaine ou une application prend le pas sur l’instruction « Ignore » relative à une indication de domaine pour un domaine ou une application donnés.

1. En l’absence de toute stratégie d’indication de domaine, ou si aucune des 4 sections ne référence l’indication d’application ou de domaine mentionnée, [le reste de la stratégie HRD est évalué](home-realm-discovery-policy.md#priority-and-evaluation-of-hrd-policies).
1. Si au moins l’une des instructions `RespectDomainHintForApps` ou `RespectDomainHintForDomains` de la section inclut l’indication d’application ou de domaine dans la demande, l’utilisateur est automatiquement accéléré vers le fournisseur d’identité fédéré comme demandé.
1. Si au moins l’une des instructions `IgnoreDomainHintsForApps` ou `IgnoreDomainHintsForDomains` référence l’indication d’application ou de domaine dans la demande, et si elles ne sont pas référencées par les sections « Respect », la demande n’est pas accélérée automatiquement et l’utilisateur reste sur la page de connexion à Azure AD pour fournir un nom d’utilisateur.

Une fois qu’un utilisateur a entré un nom d’utilisateur sur la page de connexion, il peut utiliser ses informations d’identification managées s’il les a inscrites.  S’il choisit de ne pas utiliser d’informations d’identification managées, ou s’il n’en a inscrite aucune, il est redirigé vers son fournisseur d’identité fédéré pour entrer ses informations d’identification comme d’habitude.

## <a name="suggested-use-within-a-tenant"></a>Usage suggéré dans un locataire

Les administrateurs de domaines fédérés doivent configurer cette section de la stratégie HRD dans un plan en quatre phases. L’objectif de ce plan est d’aboutir à ce que tous les utilisateurs au sein d’un locataire aient la possibilité d’utiliser leurs informations d’identification managées indépendamment du domaine ou de l’application, à l’exception des applications qui ont des dépendances dures par rapport à l’usage de `domain_hint`.  Ce plan permet aux administrateurs de trouver ces applications, de les exempter de la nouvelle stratégie, et de continuer à déployer la modification sur le reste du locataire.

1. Sélectionnez un domaine sur lequel déployer initialement cette modification.  Comme ce sera votre domaine de test, choisissez-en un qui peut être plus réceptif aux modifications apportées à l’expérience utilisateur (par exemple, affichage d’une page de connexion différente).  Cela aura pour effet d’ignorer toutes les indications de domaine de toutes les applications qui utilisent ce nom de domaine. [Définissez](#configuring-policy-through-graph-explorer) cette stratégie dans votre stratégie HRD par défaut de locataire :

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Recueillez les commentaires des utilisateurs du domaine de test. Collectez les détails des applications qui se sont arrêtées à la suite de cette modification. Elles ont une dépendance par rapport à l’usage d’indication de domaine et nécessitent une mise à jour. Pour le moment, ajoutez-les à la section `RespectDomainHintForApps` :

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Continuez à étendre le déploiement de la stratégie à de nouveaux domaines, en recueillant davantage de commentaires.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Terminez votre déploiement. Ciblez tous les domaines, en exemptant ceux qui doivent continuer à être accélérés :

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Une fois l’étape 4 accomplie, tous les utilisateurs, à l’exception de ceux figurant dans `guestHandlingDomain.com`, peuvent se connecter via la page de connexion à Azure AD, même si des indications de domaine risquent de provoquer une accélération automatique vers un fournisseur d’identité fédéré.  La seule exception à cela est si l’application demandant la connexion est l’une des applications exemptées. Pour toutes ces applications, les indications de domaine continueront d’être acceptées.

## <a name="configuring-policy-through-graph-explorer"></a>Configuration de stratégie via l’Explorateur graphique

Définissez la [stratégie HRD](/graph/api/resources/homeRealmDiscoveryPolicy) comme d’habitude, à l’aide de Microsoft Graph.  

1. Accordez l’autorisation Policy.ReadWrite.ApplicationConfiguration dans l’[Explorateur graphique](https://developer.microsoft.com/graph/graph-explorer).  
1. Utilisez l’URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`.
1. Publiez (POST) la nouvelle stratégie sur cette URL, ou corrigez-la (PATCH) en `/policies/homerealmdiscoveryPolicies/{policyID}` si vous modifiez une stratégie existante.

Contenu des opérations POST et PATCH :

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Veillez à utiliser des barres obliques pour échapper la section JSON `Definition` lors de l’utilisation de Graph.  

La valeur de `isOrganizationDefault` doit être true, mais le nom d’affichage et la définition peuvent changer.

## <a name="next-steps"></a>Étapes suivantes

* [Activer la connexion par clé de sécurité sans mot de passe](../authentication/howto-authentication-passwordless-security-key.md)
* [Activer la connexion sans mot de passe avec l’application Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)
