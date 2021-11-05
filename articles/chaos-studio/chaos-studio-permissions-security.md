---
title: Autorisations et sécurité pour Azure Chaos Studio
description: Comprenez comment les autorisations fonctionnent dans Azure Chaos Studio, et comment vous pouvez sécuriser des ressources à partir d’une injection d’erreurs accidentelles.
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 8502000dde601f6b42a8f770513e13f171dcf5d2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096918"
---
# <a name="permissions-and-security-in-azure-chaos-studio"></a>Autorisations et sécurité dans Azure Chaos Studio

Azure Chaos Studio vous permet d’améliorer la résilience des services en injectant systématiquement des erreurs dans vos ressources Azure. L’injection d’erreurs est un moyen puissant qui permet d’améliorer la résilience des services, mais elle peut également s’avérer dangereuse. Le fait de provoquer des défaillances dans votre application peut avoir plus d’impact que prévu et offrir à des acteurs malveillants des opportunités d’infiltrer vos applications. Chaos Studio dispose d’un modèle d’autorisation robuste qui empêche les erreurs de s’exécuter involontairement, ou à l’initiative d’une personne malintentionnée. Dans cet article, vous allez découvrir comment sécuriser les ressources ciblées pour l’injection d’erreurs à l’aide de Chaos Studio.

## <a name="how-can-i-restrict-the-ability-to-inject-faults-with-chaos-studio"></a>Comment puis-je limiter la capacité à injecter des erreurs avec Chaos Studio ?

Chaos Studio offre trois niveaux de sécurité qui vous permettent de contrôler comment et quand l’injection d’erreurs peut se produire sur une ressource.

Avant tout, une expérience de chaos est une ressource Azure qui est déployée sur une région, un groupe de ressources et un abonnement. Les utilisateurs doivent disposer des autorisations Azure Resource Manager appropriées pour créer, mettre à jour, démarrer, annuler, supprimer ou afficher une expérience. Chaque autorisation représente une opération ARM pouvant être affectée de manière précise à une identité, ou dans le cadre d’un rôle avec des autorisations génériques. Par exemple, le rôle de contributeur dans Azure dispose de l’autorisation */write sur l’étendue attribuée, qui inclut l’autorisation Microsoft.Chaos/experiments/write. Lorsque vous tentez de contrôler la capacité à injecter des erreurs sur une ressource, l’opération la plus importante à limiter est Microsoft.Chaos/experiments/start/action, car cette opération démarre une expérience de chaos qui injecte des erreurs.

Par ailleurs, une expérience de chaos dispose d’une [identité managée affectée par le système](../active-directory/managed-identities-azure-resources/overview.md) qui exécute des erreurs sur une ressource. Lorsque vous créez une expérience, l’identité managée affectée par le système est créée dans votre locataire Azure Active Directory sans autorisation. Avant d’exécuter votre expérience de chaos, vous devez accorder à son identité les [autorisations appropriées](chaos-studio-fault-providers.md) pour toutes les ressources cibles. Si l’identité de l’expérience ne dispose pas de l’autorisation appropriée pour une ressource, elle ne pourra pas exécuter d’erreur sur cette ressource.

Enfin, chaque ressource doit être intégrée à Chaos Studio en tant que [cible avec les fonctionnalités correspondantes activées](chaos-studio-targets-capabilities.md). Si une cible ou la fonctionnalité pour l’erreur en cours d’exécution n’existe pas, l’expérience échoue sans affecter la ressource.

## <a name="agent-authentication"></a>Authentification de l’agent

Lorsque vous exécutez des erreurs basées sur l’agent, vous devez installer l’agent Chaos Studio sur votre machine virtuelle ou votre groupe de machines virtuelles identiques. L’agent utilise une [identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/overview.md) pour s’authentifier sur Chaos Studio, et un *profil d’agent* pour établir une relation avec une ressource de machine virtuelle spécifique. Lors de l’intégration d’une machine virtuelle ou d’un groupe de machines virtuelles identiques pour les erreurs basées sur l’agent, vous créez au préalable une cible d’agent. La cible d’agent doit disposer d’une référence à l’identité managée affectée par l’utilisateur qui sera utilisée pour l’authentification. La cible d’agent contient un *ID de profil d’agent* qui est fourni sous forme de configuration lors de l’installation de l’agent. Les profils d’agent sont uniques à chaque cible, et les cibles sont uniques à chaque ressource.

## <a name="arm-operations-and-roles"></a>Rôles et opérations ARM

Chaos Studio comporte les opérations suivantes :

| Opération | Description |
| -- | -- |
| Microsoft.Chaos/targets/[Read,Write,Delete] | Obtenir, créer, mettre à jour ou supprimer une cible. |
| Microsoft.Chaos/targets/capabilities/[Read,Write,Delete] | Obtenir, créer, mettre à jour ou supprimer une fonctionnalité. |
| Microsoft.Chaos/locations/targetTypes/Read | Obtenir tous les types de cibles. |
| Microsoft.Chaos/locations/targetTypes/capabilityTypes/Read | Obtenir tous les types de fonctionnalités. |
| Microsoft.Chaos/experiments/[Read,Write,Delete] | Obtenir, créer, mettre à jour ou supprimer une expérience de chaos. |
| Microsoft.Chaos/experiments/start/action | Démarrer une expérience de chaos. |
| Microsoft.Chaos/experiments/cancel/action | Arrêter une expérience de chaos. |
| Microsoft.Chaos/experiments/statuses/Read | Obtenir l’état d’exécution d’une expérience de chaos. |
| Microsoft.Chaos/experiments/executionDetails/Read | Obtenir les détails de l’exécution (état et erreurs de chaque action) d’une expérience de chaos. |

Pour affecter de façon précise ces autorisations, vous pouvez [créer un rôle personnalisé](../role-based-access-control/custom-roles.md).

## <a name="network-security"></a>Sécurité du réseau

Toutes les interactions utilisateur avec Chaos Studio se produisent par l’intermédiaire d’Azure Resource Manager. Si un utilisateur démarre une expérience, l’expérience peut interagir avec d’autres points de terminaison que Resource Manager, en fonction de l’erreur.
* Erreurs de service direct : la plupart des erreurs de service direct sont exécutées via Azure Resource Manager. Les ressources cibles ne nécessitent aucun point de terminaison réseau de la liste d’autorisations.
* Erreurs de service direct AKS Chaos Mesh : les erreurs de service direct pour Azure Kubernetes Service utilisant Chaos Mesh exigent que le cluster AKS ait accès au serveur d’API Kubernetes publiquement exposé. [Vous pouvez apprendre à limiter l’accès réseau AKS à un ensemble de plages d’adresses IP ici.](../aks/api-server-authorized-ip-ranges.md)
* Erreurs basées sur l’agent : les erreurs basées sur l’agent exigent l’accès de l’agent au service d’agent Chaos Studio. Une machine virtuelle ou un groupe de machines virtuelles identiques doit avoir un accès sortant à http://agentcommunicationservice-frontdoor-canary.trafficmanager.net pour que l’agent se connecte correctement.

Azure Chaos Studio ne prend pas en charge les étiquettes de service ni Private Link.

## <a name="data-encryption"></a>Chiffrement des données

Chaos Studio chiffre toutes les données par défaut. Chaos Studio accepte uniquement les entrées pour les propriétés système, telles que les ID d’objets d’identités managées, les noms d’expérience/d’étape/de branche et les paramètres d’erreur (par exemple, la plage de ports réseau à bloquer dans une erreur de déconnexion réseau). Ces propriétés ne doivent pas être utilisées pour stocker des données sensibles, telles que des informations de paiement ou des mots de passe. Pour plus d’informations sur la façon dont Chaos Studio protège vos données, consultez [l’article sur la protection des données client Azure](../security/fundamentals/protection-customer-data.md).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous comprenez comment sécuriser votre expérience de chaos, vous êtes prêt à :
- [Créer et exécuter votre première expérience](chaos-studio-tutorial-service-direct.md)
- [Créer et exécuter votre première expérience Azure Kubernetes Service](chaos-studio-tutorial-aks.md)
