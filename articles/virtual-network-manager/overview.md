---
title: Qu’est-ce qu’Azure Virtual Network Manager (préversion) ?
description: Découvrez comment Azure Virtual Network Manager peut simplifier la gestion et la scalabilité de vos réseaux virtuels.
services: virtual-network-manager
author: duongau
ms.service: virtual-network-manager
ms.topic: overview
ms.date: 11/02/2021
ms.author: duau
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 05c43d5e21c637fef321dc39cd341befedfba81e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028834"
---
# <a name="what-is-azure-virtual-network-manager-preview"></a>Qu’est-ce qu’Azure Virtual Network Manager (préversion) ?

Azure Virtual Network Manager est un service de gestion qui vous permet de regrouper, de configurer, de déployer et de gérer des réseaux virtuels dans l’ensemble des abonnements. Avec Virtual Network Manager, vous pouvez définir des groupes réseau pour identifier et segmenter de manière logique vos réseaux virtuels. Vous pouvez ensuite déterminer les configurations de connectivité et de sécurité de votre choix, et les appliquer à tous les réseaux virtuels sélectionnés des groupes réseau. 

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-azure-virtual-network-manager-work"></a>Comment fonctionne Azure Virtual Network Manager ?

:::image type="content" source="./media/overview/management-group.png" alt-text="Diagramme du groupe d’administration dans Virtual Network Manager.":::

Pendant le processus de création, vous définissez l’étendue de gestion de votre instance Azure Virtual Network Manager. L’étendue peut être définie au niveau de l’abonnement ou du [groupe d’administration](../governance/management-groups/overview.md). Après avoir défini l’étendue, vous pouvez activer des fonctionnalités comme la *Connectivité* et le rôle *SecurityAdmin* pour l’instance Virtual Network Manager.

Après avoir déployé l’instance Virtual Network Manager, vous créez un *groupe réseau* à l’aide d’instructions conditionnelles pour sélectionner des réseaux virtuels par nom, étiquette ou ID (appartenance dynamique). Vous pouvez également sélectionner des réseaux virtuels spécifiques (appartenance statique). Les règles de groupe réseau définies apparaissent dans Azure Policy sous la forme d’une définition d’initiative personnalisée et d’une attribution correspondante qui illustrent les règles que vous avez définies pour l’appartenance au réseau virtuel. Pour plus d’informations sur les initiatives Azure Policy, consultez [Structure des initiatives Azure Policy](../governance/policy/concepts/initiative-definition-structure.md). Ces stratégies sont disponibles en mode Lecture seule. Pour plus d’informations sur la création, la mise à jour et la suppression de ces stratégies, consultez [Groupes réseau et Azure Policy](concept-network-groups.md#network-group-and-azure-policy). Vous créez ensuite des configurations de connectivité et/ou de sécurité à appliquer à ces groupes réseau en fonction de vos besoins en matière de topologie et de sécurité. 

Une configuration de connectivité vous permet de créer une topologie de réseau de type maillage ou hub-and-spoke. Une configuration de sécurité vous permet de définir une collection de règles que vous pouvez appliquer à un ou plusieurs groupes réseau au niveau global. Une fois que vous avez créé les groupes et les configurations de réseau souhaités, vous pouvez déployer les configurations dans n’importe quelle région.

## <a name="key-benefits"></a>Principaux avantages

* Gérez les stratégies de sécurité et de connectivité de manière centralisée pour l’ensemble des régions et des abonnements.

* Activez la communication transitive entre spokes dans une configuration hub-and-spoke, sans la complexité liée à la gestion d’un réseau maillé.

* Service hautement scalable et hautement disponible avec redondance et réplication dans le monde entier.

* Possibilité de créer des règles de sécurité réseau globales qui remplacent les règles de groupe de sécurité réseau.

* Faible latence et bande passante élevée entre les ressources des différents réseaux virtuels grâce à l’appairage des réseaux virtuels.

* Déployez les modifications réseau via la séquence et la fréquence de région de votre choix.

## <a name="public-preview-regions"></a>Régions avec préversion publique

* Centre-Nord des États-Unis

* USA Ouest

* USA Ouest 2

* USA Est

* USA Est 2

* Europe Nord

* Europe Ouest

* France Centre

## <a name="next-steps"></a>Étapes suivantes

- Créez une instance [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) en utilisant le portail Azure.
- En savoir plus sur les [groupes réseau](concept-network-groups.md) dans Azure Virtual Network Manager.
- Découvrez ce que vous pouvez faire avec une [configuration de connectivité](concept-connectivity-configuration.md).
- En savoir plus sur les [configurations d’administration de sécurité](concept-security-admins.md).
