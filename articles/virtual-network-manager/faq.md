---
title: Questions fréquemment posées sur Azure Virtual Network Manager
description: Trouvez des réponses aux questions fréquemment posées sur Azure Virtual Network Manager.
services: virtual-network-manager
author: duongau
ms.service: virtual-network-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: duau
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: f25a2f21713684024dcacee18e5666dfbb33a1b2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098074"
---
# <a name="azure-virtual-network-manager-faq"></a>FAQ sur Azure Virtual Network Manager

## <a name="general"></a>Général

### <a name="what-regions-are-available-in-public-preview"></a>Quelles sont les régions disponibles en préversion publique ?

* USA Centre-Nord

* USA Ouest

* USA Ouest 2

* USA Est

* USA Est 2

* Europe Nord

* Europe Ouest

* France Centre

### <a name="what-are-common-use-cases-for-using-azure-virtual-network-manager"></a>Quels sont les cas d’usage courants d’Azure Virtual Network Manager ?

* En tant que responsable de la sécurité informatique, vous pouvez créer différents groupes réseau pour répondre aux besoins de votre environnement et de ses fonctions. Par exemple, vous pouvez créer des groupes réseau pour les environnements réseau de production et de test, les équipes de développement, le service finance, etc. pour gérer leurs règles de connectivité et de sécurité à grande échelle. 

* Vous pouvez appliquer des configurations de connectivité pour créer un maillage ou une topologie réseau hub-and-spoke pour un grand nombre de réseaux virtuels dans les abonnements de votre organisation. 

* Vous pouvez refuser le trafic à haut risque : en tant qu’administrateur d’une entreprise, vous pouvez bloquer des protocoles ou sources spécifiques qui remplacent les règles NSG qui autorisent normalement le trafic.   

* Toujours autoriser le trafic : vous souhaitez autoriser un analyseur de sécurité spécifique à toujours avoir une connectivité entrante à toutes vos ressources, même si des règles NSG sont configurées pour refuser le trafic.   

## <a name="technical"></a>Technique

### <a name="can-a-virtual-network-belong-to-multiple-azure-virtual-network-managers"></a>Un réseau virtuel peut-il appartenir à plusieurs instances d’Azure Virtual Network Manager ?

Oui, un réseau virtuel peut appartenir à plusieurs instances d’Azure Virtual Network Manager.

### <a name="what-is-a-global-mesh-network-topology"></a>Qu’est-ce qu’une topologie de maillage en réseau global ?

Un maillage global permet aux réseaux virtuels de différentes régions de communiquer les uns avec les autres. Les effets sont similaires à l’appairage de réseaux virtuels globaux.

### <a name="is-there-a-limit-to-how-many-network-groups-can-be-created"></a>Le nombre de groupes réseau pouvant être créés est-il limité ?

Le nombre de groupes réseau pouvant être créés n’est pas limité.

### <a name="how-do-i-remove-the-deployment-of-all-applied-configurations"></a>Comment supprimer le déploiement de toutes les configurations appliquées ?

Vous devez déployer une configuration **None** dans toutes les régions auxquelles une configuration est appliquée.

### <a name="can-i-add-virtual-networks-from-another-subscription-not-managed-by-myself"></a>Est-ce que je peux ajouter les réseaux virtuels d’un autre abonnement que je ne gère pas ?

Oui, vous devez disposer des autorisations appropriées pour accéder à ces réseaux virtuels.

### <a name="what-is-dynamic-group-membership"></a>Qu’est-ce que l’appartenance de groupe dynamique ?

Pour plus d’informations, consultez [Appartenance dynamique](concept-network-groups.md#dynamic-membership).

### <a name="how-does-the-deployment-of-configuration-differ-for-dynamic-membership-and-static-membership"></a>En quoi le déploiement de la configuration diffère-t-il entre l’appartenance dynamique et l’appartenance statique ?

Pour plus d’informations, consultez [Déploiement sur les types d’appartenance](concept-deployments.md#deployment).

### <a name="how-do-i-delete-an-azure-virtual-network-manager-component"></a>Comment supprimer un composant Azure Virtual Network Manager ?

Pour plus d’informations, consultez la [check-list pour supprimer des composants](concept-remove-components-checklist.md).

### <a name="how-can-i-see-what-configurations-are-applied-to-help-me-troubleshoot"></a>Comment voir quelles sont les configurations appliquées pour m’aider à résoudre les problèmes ?

Vous pouvez voir les paramètres Azure Virtual Network Manager d’un réseau virtuel sous **Network Manager**. Vous pouvez voir les configurations Connectivité et Administration de la sécurité qui sont appliquées. Pour plus d’informations, consultez [Voir la configuration appliquée](how-to-view-applied-configurations.md).

### <a name="can-a-virtual-network-managed-by-azure-virtual-network-manager-be-peered-to-a-non-managed-virtual-network"></a>Un réseau virtuel managé par Azure Virtual Network Manager peut-il appairé à un réseau virtuel non managé ?

Oui, vous pouvez choisir de remplacer ou de supprimer un appairage existant déjà créé.

## <a name="limits"></a>limites

### <a name="what-are-the-service-limitation-of-azure-virtual-network-manager"></a>Quelle est la limitation de service d’Azure Virtual Network Manager ?

* Un hub dans une topologie hub-and-spoke peut être appairé avec jusqu’à 500 spokes. 

* Les sous-réseaux d’un réseau virtuel ne peuvent pas communiquer entre eux s’ils ont le même espace d’adressage dans une configuration de maillage. 

* Azure Virtual Network Manager autorise uniquement 500 connexions d’appairage de réseaux virtuels sur toute la configuration Connectivité pour un réseau virtuel donné. Vous pouvez également gérer l’appairage hérité de manière autonome. 

* Le nombre maximal de préfixes IP dans toutes les règles d’administration combinées est de 1 000. 

* Le nombre maximal de règles d’administration dans un niveau d’Azure Virtual Network Manager est de 100. 

* Azure Virtual Network Manager n’offre pas de prise en charge multilocataire dans la préversion publique.

* Un réseau virtuel peut faire partie d’un maximum de cinq configurations de maillage. 

## <a name="next-steps"></a>Étapes suivantes

Créez une instance [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) en utilisant le portail Azure.
