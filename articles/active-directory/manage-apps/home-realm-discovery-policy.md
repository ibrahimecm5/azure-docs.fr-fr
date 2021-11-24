---
title: Stratégie de découverte du domaine d’accueil
titleSuffix: Azure AD
description: Découvrez comment contrôler l’accélération automatique d’une application à l’aide de la stratégie de découverte du domaine d’accueil.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.openlocfilehash: 8ac93a42c4911a6694770ee320f84b88d6ea6aa5
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547443"
---
# <a name="home-realm-discovery-for-an-application"></a>Découverte de domaine d’accueil pour une application

La découverte du domaine d’accueil est le processus qui permet à Azure AD d’identifier le fournisseur d’identité (« IdP ») avec lequel un utilisateur doit s’authentifier au moment de la connexion.  Quand un utilisateur se connecte à un locataire Azure AD pour accéder à une ressource ou à la page de connexion courante d’Azure AD, il tape un nom d’utilisateur (UPN). Azure AD l’utilise pour découvrir où l’utilisateur doit se connecter.

L’utilisateur est dirigé vers l’un des fournisseurs d’identité suivants pour être authentifié :

- Le locataire de base de l’utilisateur (peut être le même locataire que la ressource à laquelle l’utilisateur tente d’accéder).

- Compte Microsoft.  L’utilisateur est un invité dans le locataire de ressources qui utilise un compte de consommateur pour l’authentification.

- Un fournisseur d’identité local tel que les services de fédération Active Directory (AD FS).

- Un autre fournisseur d’identité qui est fédéré avec le locataire Azure AD.

## <a name="auto-acceleration"></a>Accélération automatique

Certaines organisations configurent les domaines dans leur locataire Azure AD afin de les fédérer avec un autre IdP, par exemple AD FS pour l’authentification des utilisateurs.

Quand un utilisateur se connecte à une application, il se voit d’abord présenter une page de connexion Azure AD. Après avoir entré son UPN, s’il se trouve dans un domaine fédéré, il est alors dirigé vers la page de connexion de l’IdP desservant ce domaine. Dans certaines circonstances, les administrateurs peuvent vouloir diriger les utilisateurs vers la page de connexion quand ils se connectent à des applications spécifiques.

En conséquence, les utilisateurs peuvent ignorer la page Azure Active Directory initiale. Ce processus est dénommé « accélération automatique de la connexion ».

Dans les cas où le locataire est fédéré avec un autre fournisseur d’identité pour la connexion, l’accélération automatique simplifie la connexion de l’utilisateur.  Vous pouvez configurer l’accélération automatique pour des applications individuelles.

> [!NOTE]
> Si vous configurez une application pour l’accélération automatique, les utilisateurs ne peuvent pas utiliser les informations d’identification gérées (telles que FIDO) et les utilisateurs invités ne peuvent pas se connecter. Si vous dirigez un utilisateur directement vers un IdP fédéré pour l’authentification, il lui est impossible de revenir à la page de connexion Azure Active Directory. Les utilisateurs invités, qui doivent éventuellement être dirigés vers d’autres locataires ou un fournisseur d’identité externe tel qu’un compte Microsoft, ne peuvent pas se connecter à cette application, car ils ignorent l’étape de la découverte du domaine d’accueil.

Il existe trois façons de contrôler l’accélération automatique vers un fournisseur d’identité :

- Utiliser une indication de domaine sur les demandes d’authentification pour une application.
- Configurer une stratégie de découverte du domaine d’accueil pour [forcer l’accélération automatique]()(configure-authentication-for-federated-users-portal.md)
- Configurez une stratégie de découverte du domaine d’accueil pour [ignorer les indications de domaine](prevent-domain-hints-with-home-realm-discovery.md) provenant d’applications spécifiques ou pour certains domaines.

## <a name="domain-hints"></a>Indications de domaine

Les indications de domaine sont des directives qui sont incluses dans la demande d’authentification émise par une application. Elles peuvent servir à accélérer l’utilisateur vers la page de connexion de son fournisseur d’identité fédéré. Ou elles peuvent être utilisées par une application multilocataire pour accélérer l’utilisateur directement vers la page de connexion Azure AD personnalisée pour son locataire.

Par exemple, l’application « largeapp.com » peut permettre à ses clients d’accéder à l’application depuis une URL « contoso.largeapp.com » personnalisée. L’application peut également inclure une indication de domaine pour contoso.com dans la demande d’authentification.

La syntaxe des indicateurs de domaine varie selon le protocole utilisé. Elle est généralement configurée dans l’application de l’une des manières suivantes :

- Pour les applications qui utilisent **WS-Federation** : whr=contoso.com dans la chaîne de requête.

- Pour les applications qui utilisent le protocole **SAML** : demande d’authentification SAML contenant un indicateur de domaine ou une chaîne de requête whr=contoso.com.

- Pour les applications qui utilisent **Open ID Connect** : chaîne de requête domain_hint=contoso.com.

Par défaut, Azure AD tente de rediriger la connexion vers le fournisseur d’identité configuré pour un domaine si les **deux** conditions suivantes sont réunies :

- Une indication de domaine est incluse dans la demande d’authentification de l’application **et**
- Le locataire est fédéré avec ce domaine.

Si l'indication de domaine ne fait pas référence à un domaine fédéré vérifié, elle est ignorée.

> [!NOTE]
> Si une indication de domaine est incluse dans une requête d’authentification et [doit être respectée](#home-realm-discovery-policy-to-prevent-auto-acceleration), sa présence remplace toute accélération automatique définie pour l’application dans la stratégie de découverte du domaine d’accueil.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Stratégie de découverte du domaine d’accueil pour l’accélération automatique

Certaines applications n’offrent pas la possibilité de configurer la demande d’authentification qu’elles émettent. Dans ce cas, il est impossible d'utiliser des indications de domaine pour contrôler l'accélération automatique. Vous pouvez configurer l’accélération automatique via une [stratégie de découverte du domaine d’accueil](configure-authentication-for-federated-users-portal.md) pour obtenir le même comportement.

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Stratégie de découverte du domaine d’accueil pour empêcher l’accélération automatique

Certaines applications Microsoft et SaaS incluent automatiquement des indications de domaine (par exemple, `https://outlook.com/contoso.com` génère une demande de connexion avec l’ajout de `&domain_hint=contoso.com`), ce qui peut perturber le déploiement d’informations d’identification gérées telles que FIDO.  Vous pouvez utiliser la [stratégie de découverte du domaine d’accueil pour ignorer les indicateurs de domaine](prevent-domain-hints-with-home-realm-discovery.md) de certaines applications ou pour certains domaines, pendant le déploiement des informations d’identification managées.

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Activer l’authentification ROPC directe des utilisateurs fédérés pour les applications héritées

Pour les applications, la bonne pratique consiste à utiliser les bibliothèques Azure AD et la connexion interactive afin d’authentifier les utilisateurs. Les bibliothèques gèrent les flux d’utilisateurs fédérés.  Parfois, les applications héritées, en particulier celles qui utilisent l’octroi d’informations ROPC (informations d’identification de mot de passe du propriétaire de la ressource), envoient le nom d’utilisateur et le mot de passe directement à Azure AD et ne sont pas écrites pour comprendre la fédération. Elles n’effectuent pas de découverte du domaine d’accueil et n’interagissent pas avec le point de terminaison fédéré approprié pour authentifier un utilisateur. Si vous le souhaitez, vous pouvez utiliser la [stratégie de découverte du domaine d’accueil pour permettre à des applications héritées spécifiques](configure-authentication-for-federated-users-portal.md) qui envoient des informations d’identification de nom d’utilisateur/mot de passe avec l’octroi ROPC à s’authentifier directement auprès d’Azure Active Directory. La synchronisation du code de hachage de mot de passe doit être activée.

> [!IMPORTANT]
> Activez uniquement l’authentification directe si vous avez activé la synchronisation de hachage de mot de passe et que vous pouvez authentifier cette application sans aucune stratégie implémentée par votre fournisseur d’identité locale. Si vous désactivez la synchronisation de hachage de mot de passe ou la synchronisation d’annuaires avec AD Connect pour une raison quelconque, vous devez supprimer cette stratégie pour empêcher la possibilité d’authentification directe à l’aide d’un hachage de mot de passe obsolète.

## <a name="set-hrd-policy"></a>Définir une stratégie de découverte du domaine d’accueil

Il existe trois étapes de définition de la stratégie de découverte du domaine d’accueil sur une application pour l’accélération automatique de connexion fédérée ou les applications cloud directes :

1. Créez une stratégie de découverte du domaine d’accueil.

2. Localisez le principal de service auquel attacher la stratégie.

3. Attachez la stratégie au principal de service.

Les stratégies prennent effet pour une application spécifique uniquement lorsqu’elles sont attachées à un principal de service.

Une seule stratégie de découverte du domaine d’accueil peut être active sur un principal de service à tout moment.

Vous pouvez utiliser les cmdlets PowerShell Azure Active Directory pour créer et gérer la stratégie de découverte du domaine d’accueil.

Voici un exemple de définition de stratégie pour la découverte du domaine d’accueil :

```json
{  
  "HomeRealmDiscoveryPolicy":
  {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
  }
}
```

Le type de stratégie est « [HomeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy) ».

**AccelerateToFederatedDomain** est facultatif. Si **AccelerateToFederatedDomain** a la valeur false, la stratégie n’a aucun effet sur l’accélération automatique. Si **AccelerateToFederatedDomain** a la valeur true et qu’il existe un seul domaine vérifié et fédéré dans le client, les utilisateurs seront directement dirigés vers le fournisseur d'identité fédéré pour la connexion. S’il a la valeur true et qu’il existe plusieurs domaines vérifiés dans le client, **PreferredDomain** doit être spécifié.

**PreferredDomain** est facultatif. **PreferredDomain** doit indiquer un domaine vers lequel vous souhaitez accélérer. Il peut être omis si le locataire n’a qu’un seul domaine fédéré.  S’il est omis et qu’il existe plusieurs domaines fédérés vérifiés, la stratégie n’a aucun effet.

 Si **PreferredDomain** est spécifié, il doit correspondre à un domaine fédéré vérifié pour le locataire. Tous les utilisateurs de l’application doivent être en mesure de se connecter à ce domaine : les utilisateurs qui ne peuvent pas se connecter au domaine fédéré sont bloqués et ne peuvent pas terminer la connexion.

**AllowCloudPasswordValidation** est facultatif. Si **AllowCloudPasswordValidation** est true, l’application est autorisée à authentifier un utilisateur fédéré à l’aide des informations d’identification de nom d’utilisateur/mot de passe directement auprès du point de terminaison de jeton Azure Active Directory. Cela fonctionne uniquement si la synchronisation de hachage de mot de passe est activée.

En outre, il existe deux options de découverte du domaine d’accueil au niveau du locataire, non indiquées ci-dessus :

- **AlternateIdLogin** est facultatif.  Si cette option est activée, [les utilisateurs peuvent se connecter avec leurs adresses e-mail au lieu de leur nom d’utilisateur principal](../authentication/howto-authentication-use-email-signin.md) à la page de connexion Azure AD.  Les autres ID supposent que l’utilisateur n’est pas accéléré automatiquement vers un fournisseur d’identité fédéré.

- **DomainHintPolicy** est un objet complexe facultatif qui [*empêche* les indications de domaine d’accélérer automatiquement les utilisateurs aux domaines fédérés](prevent-domain-hints-with-home-realm-discovery.md). Ce paramètre à l’échelle du locataire est utilisé pour s’assurer que les applications qui envoient des indications de domaine n’empêchent pas les utilisateurs de se connecter avec les informations d’identification gérées dans le cloud.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorité et évaluation des stratégies de découverte du domaine d’accueil

Les stratégies de découverte du domaine d’accueil peuvent être créées, puis affectées à des organisations et principaux de service spécifiques. Cela signifie qu’il est possible que plusieurs stratégies s’appliquent à une application spécifique. Par conséquent, Azure AD doit décider laquelle d’entre elles est prioritaire. Un ensemble de règles détermine la stratégie de découverte du domaine d’accueil (parmi les nombreuses stratégies appliquées) qui prend effet :

- Si une indication de domaine est présente dans la demande d’authentification, la stratégie de découverte du domaine d’accueil pour le locataire (stratégie définie par défaut pour le locataire) est contrôlée pour déterminer si les indications de domaine doivent être ignorées. Si les indications de domaine sont autorisées, le comportement spécifié par l’indication de domaine est utilisé.

- Sinon, si une stratégie est explicitement affectée au principal de service, elle est appliquée.

- S’il n’existe aucune indication de domaine et qu’aucune stratégie n’est explicitement affectée au principal de service, une stratégie qui est explicitement affectée à l’organisation parente du principal de service est appliquée.

- S’il n’existe aucune indication de domaine et qu’aucune stratégie n’a été affectée au principal de service ou à l’organisation, le comportement de découverte du domaine d’accueil par défaut est utilisé.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer le comportement de connexion d’une application à l’aide d’une stratégie de découverte du domaine d’accueil](configure-authentication-for-federated-users-portal.md)
- [Désactiver l’accélération automatique vers un fournisseur d’identité fédéré lors d’une connexion utilisateur avec la stratégie Découverte de domaine d’accueil](prevent-domain-hints-with-home-realm-discovery.md)
- Pour plus d’informations sur le fonctionnement de l’authentification dans Azure AD, consultez la section [Scénarios d’authentification pour Azure AD](../develop/authentication-vs-authorization.md).
