---
title: Autoriser l’accès à Azure Web PubSub Service avec Azure Active Directory
description: Cet article fournit des informations sur l’autorisation d’accès aux ressources Azure Web PubSub Service avec Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 386ef4d27a8fc2fbe4d9c3f209d2016fa1911761
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997454"
---
# <a name="authorize-access-to-web-pubsub-resources-using-azure-active-directory"></a>Autoriser l’accès aux ressources Web PubSub à l’aide d’Azure Active Directory
Le service Azure Web PubSub prend en charge l’utilisation d’Azure Active Directory (Azure AD) pour autoriser les requêtes aux ressources Web PubSub. Azure AD vous permet d’utiliser le contrôle d’accès en fonction du rôle (RBAC) pour accorder des autorisations à un principal de sécurité<sup>[<a href="#security-principal">1</a>]</sup>. Le principal de sécurité est authentifié par Azure AD, qui retourne un jeton OAuth 2.0. Le jeton peut ensuite être utilisé pour autoriser une requête à la ressource Web PubSub.

Autoriser les demandes auprès de Web PubSub avec Azure AD offre davantage de sécurité et de facilité d’utilisation sur l’autorisation de la clé d’accès. Microsoft recommande d’utiliser l’autorisation Azure AD avec vos ressources Web PubSub dans la mesure du possible pour garantir l’accès avec les privilèges minimaux requis.

<a id="security-principal"></a>
 *[1] principal de sécurité : un groupe d’utilisateurs/ressources, une application ou un principal du service, comme des identités affectées par le système et des identités affectées par l’utilisateur.*

## <a name="overview-of-azure-ad-for-web-pubsub"></a>Présentation d’Azure AD pour Web PubSub

Lorsqu’un principal de sécurité tente d’accéder à une ressource Web PubSub, la requête doit être autorisée. Avec Azure AD, l’accès à une ressource requiert deux étapes. 

1. D’abord, le principal de sécurité est authentifié par Azure, qui retourne un jeton OAuth 2.0. 
2. Ensuite, le jeton est passé dans le cadre d’une requête adressée à la ressource Web PubSub et utilisé par le service pour autoriser l’accès à la ressource spécifiée.

### <a name="client-side-authentication-while-using-azure-ad"></a>Authentification côté client lors de l’utilisation d’Azure AD

Lorsque vous utilisez la clé d’accès, la clé est partagée entre votre serveur de négociation (ou application de fonction) et la ressource Web PubSub, ce qui signifie que le service Web PubSub peut authentifier la requête de connexion du client avec la clé partagée. Toutefois, il n’y a aucune clé partagée lors de l’utilisation d’Azure AD pour autoriser. 

Pour résoudre ce problème, nous avons fourni une API REST pour générer le jeton client qui peut être utilisé pour se connecter au service Azure Web PubSub.

1. Tout d’abord, le serveur de négociation requiert un **jeton Aad** d’Azure pour s’authentifier.
1. Deuxièmement, le serveur de négociation appelle l’API d’authentification Web PubSub avec le **jeton AAD** pour obtenir un **jeton client** et le retourne au client.
1. Enfin, le client utilise le **jeton client** pour se connecter au service Azure Web PubSub.

Nous avons fourni des fonctions d’assistance (par exemple, « GenerateClientAccessUri ») pour les langages de programmation pris en charge.

## <a name="assign-azure-roles-for-access-rights"></a>Attribuer des rôles Azure pour les droits d’accès

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées par le biais du [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md). Azure Web PubSub définit un ensemble de rôles intégrés Azure qui englobent les ensembles communs d’autorisations permettant d’accéder aux ressources Web PubSub. Vous pouvez également définir des rôles personnalisés pour l’accès aux ressources Web PubSub.

### <a name="resource-scope"></a>Étendue des ressources

Avant d’attribuer un rôle RBAC Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Il est recommandé d’accorder uniquement l’étendue la plus étroite possible. Les rôles RBAC Azure définis au niveau d’une étendue plus large sont hérités par les ressources qui sont sous eux.

Vous pouvez étendre l’accès aux ressources Azure SignalR aux niveaux suivants, en commençant par l’étendue la plus restreinte :

- **Une ressource individuelle.** 

  Dans cette étendue, une attribution de rôle s’applique uniquement à la ressource cible.

- **Un groupe de ressources.** 

  Pour cette étendue, l’attribution de rôle s’applique à toutes les ressources dans le groupe de ressources.

- **Un abonnement.**

  Pour cette étendue, l’attribution de rôle s’applique à toutes les ressources dans tous les groupes de ressources de l’abonnement.

- **Un groupe d’administration.** 

  Dans cette étendue, une attribution de rôle s’applique à toutes les ressources dans tous les groupes de ressources de tous les abonnements dans le groupe d’administration.

## <a name="azure-built-in-roles-for-web-pubsub-resources"></a>Rôles intégrés Azure pour les ressources Web PubSub.

- `Web PubSub Service Owner`

    Accès complet aux autorisations de plan de données, y compris les API REST en lecture/écriture et les API d’authentification.

    Il s’agit du rôle le plus couramment utilisé pour créer un serveur en amont.

- `Web PubSub Service Reader`

    Utilisez-le pour accorder des autorisations d’API REST en lecture seule aux ressources Web PubSub.

    Il est généralement utilisé lorsque vous souhaitez écrire un outil d’analyse qui appelle **UNIQUEMENT** les API REST de plan de données en **LECTURE SEULE** de Web PubSub.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment créer une application Azure et utiliser l’authentification AAD, consultez
- [Autoriser la requête aux ressources Web PubSub avec Azure AD à partir d’applications Azure](howto-authorize-from-application.md)

Pour savoir comment configurer une identité managée et utiliser l’authentification AAD, consultez
- [Autoriser la requête aux ressources Web PubSub avec Azure AD à partir d’identités managées](howto-authorize-from-managed-identity.md)

Pour en savoir plus sur les rôles et attributions de rôle, consultez 
- [Présentation du contrôle d’accès en fonction du rôle Azure](../role-based-access-control/overview.md).

Pour savoir comment créer des rôles personnalisés, consultez 
- [Procédure de création d’un rôle personnalisé](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)