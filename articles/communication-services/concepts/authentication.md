---
title: S’authentifier auprès d’Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les différentes façons dont une application ou un service peut s’authentifier auprès de Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: identity
ms.openlocfilehash: 657ea9b12f8bff633f1bcb87439e950a3aa5ce3b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672321"
---
# <a name="authenticate-to-azure-communication-services"></a>S’authentifier auprès d’Azure Communication Services

Chaque interaction client avec Azure Communication Services doit être authentifiée. Dans une architecture classique, l’[architecture client et serveur](./client-and-server-architecture.md), les *clés d’accès* ou les *identités managées* sont utilisées pour l’authentification.

Un autre type d’authentification utilise des *jetons d’accès utilisateur* pour s’authentifier auprès des services nécessitant la participation de l’utilisateur. Par exemple, le service de conversation ou d’appel utilise des *jetons d’accès utilisateur* pour permettre l’ajout d’utilisateurs à un thread et d’échanger.

## <a name="authentication-options"></a>Options d’authentification

Le tableau suivant répertorie les Kits de développement logiciel (SDK) Azure Communication Services ainsi que leurs options d’authentification :

| Kit SDK    | Option d'authentification                               |
| ----------------- | ----------------------------------------------------|
| Identité          | Clé d’accès ou identité managée                      |
| SMS               | Clé d’accès ou identité managée                      |
| Numéros de téléphone     | Clé d’accès ou identité managée                      |
| Appel           | Jeton d’accès utilisateur                                   |
| Conversation              | Jeton d’accès utilisateur                                   |

Chaque option d’autorisation est décrite brièvement ci-dessous :

### <a name="access-key"></a>Clé d’accès

L’authentification par clé d’accès est adaptée aux applications de service qui s’exécutent dans un environnement de service approuvé. La clé d’accès est accessible sur le Portail Azure Communication Services. L’application de service l’utilise comme informations d’identification pour initialiser les Kits de développement logiciel (SDK) correspondants. Consultez un exemple illustrant la manière dont il est utilisé dans le [Kit de développement logiciel (SDK) Identity](../quickstarts/access-tokens.md). 

Étant donné que la clé d’accès fait partie de la chaîne de connexion de votre ressource, l’authentification avec une chaîne de connexion est équivalente à l’authentification avec une clé d’accès.

Si vous souhaitez appeler des API ACS manuellement à l’aide d’une clé d’accès, vous devez signer la requête. La signature de la requête est expliquée en détail dans un [tutoriel](../tutorials/hmac-header-tutorial.md).

### <a name="managed-identity"></a>Identité managée

Les identités managées offrent davantage de sécurité et de facilité d’utilisation par rapport à d’autres options d’autorisation. Par exemple, Azure AD vous permet d’éviter de devoir stocker votre clé d’accès au compte avec votre code, comme vous le faites avec l’autorisation par clé d’accès. Bien qu’il vous soit possible de continuer à utiliser l’autorisation par clé d’accès avec les applications Communication Services, Microsoft recommande de migrer vers Azure AD dans la mesure du possible. 

Pour configurer une identité managée, [créez une application inscrite à partir d’Azure CLI](../quickstarts/identity/service-principal-from-cli.md). Le point de terminaison et les informations d’identification peuvent ensuite être utilisés pour authentifier les Kits de développement logiciel (SDK). Consultez des exemples d’utilisation de l' [identité managée](../quickstarts/identity/service-principal.md).

### <a name="user-access-tokens"></a>Jetons d’accès utilisateur

Les jetons d’accès utilisateur sont générés à l’aide du Kit de développement logiciel (SDK) Identity et associés aux utilisateurs créés dans ce Kit de développement logiciel (SDK) Identity. Consultez un exemple de création d'[utilisateurs et de génération de jetons](../quickstarts/access-tokens.md). Les jetons d’accès utilisateur sont ensuite utilisés pour authentifier les participants ajoutés aux conversations dans le kit de développement logiciel (SDK) de conversation ou d’appel. Pour plus d’informations, consultez [Ajouter la conversation à votre application](../quickstarts/chat/get-started.md). L’authentification par jeton d’accès utilisateur est différente de l’authentification par clé d’accès et identité managée, car elle est utilisée pour authentifier un utilisateur plutôt qu’une ressource Azure sécurisée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer et gérer des ressources Communication Services](../quickstarts/create-communication-resource.md)
> [Créer une application d’identité managée Azure Active Directory à partir d’Azure CLI](../quickstarts/identity/service-principal-from-cli.md)
> [Créer des jetons d’accès utilisateur](../quickstarts/access-tokens.md)

Pour plus d’informations, consultez les articles suivants :
- [En savoir plus sur l’architecture client et serveur](../concepts/client-and-server-architecture.md)
