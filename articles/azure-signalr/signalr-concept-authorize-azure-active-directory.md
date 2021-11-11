---
title: Autoriser l’accès à Azure SignalR Service avec Azure Active Directory
description: Cet article fournit des informations sur l’autorisation d’accès aux ressources Azure SignalR Service avec Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 0e644f792e47243f3c6924cd8546efb5661a9193
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479009"
---
# <a name="authorize-access-with-azure-active-directory-for-azure-signalr-service"></a>Autoriser l’accès à Azure SignalR Service avec Azure Active Directory

Azure SignalR Service prend en charge l’utilisation d’Azure Active Directory (Azure AD) pour autoriser les requêtes aux ressources SignalR. Azure AD vous permet d’utiliser le contrôle d’accès en fonction du rôle (RBAC) pour accorder des autorisations à un principal de sécurité<sup>[<a href="#security-principal">1</a>]</sup>. Le principal de sécurité est authentifié par Azure AD, qui retourne un jeton OAuth 2.0. Le jeton peut ensuite être utilisé pour autoriser une requête à la ressource SignalR.

Autoriser les demandes auprès de SignalR avec Azure AD offre davantage de sécurité et de facilité d’utilisation sur l’autorisation de la clé d’accès. Microsoft recommande d’utiliser l’autorisation Azure AD avec vos ressources SignalR dans la mesure du possible pour garantir l’accès avec les privilèges minimaux requis.

<a id="security-principal"></a>
 *[1] principal de sécurité : un groupe d’utilisateurs/ressources, une application ou un principal du service, comme des identités affectées par le système et des identités affectées par l’utilisateur.*

## <a name="overview-of-azure-ad-for-signalr"></a>Vue d’ensemble d’Azure AD pour SignalR

Lorsqu’un principal de sécurité tente d’accéder à une ressource SignalR, la requête doit être autorisée. Avec Azure AD, l’accès à une ressource requiert 2 étapes. 

1. D’abord, le principal de sécurité est authentifié par Azure, qui retourne un jeton OAuth 2.0. 
2. Ensuite, le jeton est passé dans le cadre d’une requête adressée à la ressource SignalR et utilisé par le service pour autoriser l’accès à la ressource spécifiée.

### <a name="client-side-authentication-while-using-azure-ad"></a>Authentification côté client lors de l’utilisation d’Azure AD

Lorsque vous utilisez la clé d’accès, la clé est partagée entre votre serveur d’applications (ou application de fonction) et la ressource SignalR, les résultats dans le service SignalR peuvent authentifier la requête de connexion du client avec la clé partagée. Toutefois, il n’y a aucune clé partagée lors de l’utilisation d’Azure AD pour autoriser. 

Pour résoudre ce problème, nous avons introduit une **clé d’accès temporaire** qui peut être utilisée pour signer des jetons pour les connexions clientes. Le workflow contient quatre étapes.

1. Tout d’abord, le principal de sécurité requiert un jeton OAuth 2.0 d’Azure pour s’authentifier.
2. Deuxièmement, le principal de sécurité appelle l’API d’authentification SignalR pour obtenir une **clé d’accès temporaire**.
3. Ensuite, le principal de sécurité signe un jeton client avec la **clé d’accès temporaire** pour les connexions clientes lors de la négociation.
4. Enfin, le client utilise le jeton client pour se connecter aux ressources Azure SignalR.

La **clé d’accès temporaire** expirera dans 90 minutes, ce qui signifie qu’il est recommandé d’en obtenir une nouvelle et de faire pivoter l’ancienne une fois par heure. 

Le workflow est créé dans le [Kit de développement logiciel (SDK) du serveur](https://github.com/Azure/azure-signalr).

## <a name="assign-azure-roles-for-access-rights"></a>Attribuer des rôles Azure pour les droits d’accès

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées par le biais du [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md). Azure SignalR définit un ensemble de rôles intégrés Azure qui englobent les ensembles communs d’autorisations permettant d’accéder aux ressources SignalR. Vous pouvez également définir des rôles personnalisés pour l’accès aux ressources SignalR.

### <a name="resource-scope"></a>Étendue des ressources

Avant d’attribuer un rôle RBAC Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Il est recommandé d’accorder uniquement l’étendue la plus étroite possible. Les rôles RBAC Azure définis au niveau d’une étendue plus large sont hérités par les ressources qui sont sous eux.

Vous pouvez étendre l’accès aux ressources Azure SignalR aux niveaux suivants, en commençant par l’étendue la plus restreinte :

- **Un résultat individuel.** 

  Dans cette étendue, une attribution de rôle s’applique uniquement à la ressource cible.

- **Un groupe de ressources.** 

  Pour cette étendue, l’attribution de rôle s’applique à toutes les ressources dans le groupe de ressources.

- **Un abonnement.**

  Pour cette étendue, l’attribution de rôle s’applique à toutes les ressources dans tous les groupes de ressources de l’abonnement.

- **Un groupe d’administration.** 
  
  Dans cette étendue, une attribution de rôle s’applique à toutes les ressources dans tous les groupes de ressources de tous les abonnements dans le groupe d’administration.

## <a name="azure-built-in-roles-for-signalr-resources"></a>Rôles intégrés Azure pour les ressources SignalR

- [Serveur d’application SignalR](../role-based-access-control/built-in-roles.md#signalr-app-server-preview)

    Accès à l’API de création de connexion WebSocket et aux API d’authentification.
    
    Il s’agit du rôle le plus couramment utilisé pour un serveur d’applications.

- [Propriétaire SignalR Service](../role-based-access-control/built-in-roles.md#signalr-service-owner)

    Accès complet à toutes les API de plan de données, y compris toutes les API REST, l’API de création de connexion WebSocket et les API d’authentification.

    Le **mode sans serveur** doit utiliser ce rôle pour prendre en charge l’autorisation avec Azure AD car il nécessite des autorisations d’API REST et d’API d’authentification.

- [Propriétaire de l’API REST SignalR](../role-based-access-control/built-in-roles.md#signalr-rest-api-owner)

    Accès complet aux API REST de plan de données.

    Il est généralement utilisé lorsque vous souhaitez écrire un outil de gestion qui gère les connexions et les groupes, mais qui n’effectue **PAS** de connexions ou appelle des API d’authentification.

- [Lecteur de l’API REST SignalR](../role-based-access-control/built-in-roles.md#signalr-rest-api-reader)

    Accès en lecture seule aux API REST de plan de données.

    Il est généralement utilisé lorsque vous souhaitez écrire un outil d’analyse qui appelle **UNIQUEMENT** les API REST de plan de données en **LECTURE SEULE** de SignalR.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment créer une application Azure et utiliser l’authentification Azure AD, consultez
- [Autoriser la requête aux ressources SignalR avec Azure AD à partir d’applications Azure](signalr-howto-authorize-application.md)

Pour savoir comment configurer une identité managée et utiliser l’authentification Azure AD, consultez
- [Autoriser les requêtes aux ressources SignalR avec Azure AD à partir d’identités managées](signalr-howto-authorize-managed-identity.md)

Pour en savoir plus sur les rôles et attributions de rôle, consultez 
- [Présentation du contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md).

Pour savoir comment créer des rôles personnalisés, consultez 
- [Procédure de création d’un rôle personnalisé](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)