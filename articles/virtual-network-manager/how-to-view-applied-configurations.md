---
title: Afficher les configurations appliquées par Azure Virtual Network Manager (préversion)
description: Découvrez comment afficher les configurations appliquées par Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0d0f0d0ed5eb100aff4df02a65ab4902da1b7c16
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096663"
---
# <a name="view-configurations-applied-by-azure-virtual-network-manager-preview"></a>Afficher les configurations appliquées par Azure Virtual Network Manager (préversion)

Azure Virtual Network Manager vous offre différentes méthodes pour vérifier si les configurations sont appliquées correctement. Dans cet article, nous allons voir comment vérifier que les configurations sont appliquées au niveau du réseau virtuel et de la machine virtuelle. Nous allons également parler des opérations que vous verrez dans le journal d’activité.

## <a name="virtual-network-visibility"></a>Visibilité du réseau virtuel

Une fois que votre configuration a été déployée par Virtual Network Manager, vous pouvez voir la configuration appliquée à partir de la ressource de réseau virtuel. 

1. Accédez à votre ressource de réseau virtuel et sélectionnez **Network Manager** sous *Paramètres*. Sous l’onglet Connectivité, vous pouvez voir toutes les configurations de connectivité auxquelles le réseau virtuel est associé. 

    :::image type="content" source="./media/how-to-view-applied-configurations/vnet-connectivity.png" alt-text="Capture d’écran de la configuration de connectivité associée au réseau virtuel.":::

2. Sélectionnez l’onglet **Administration de la sécurité** pour voir toutes les règles de sécurité actuellement appliquées à votre réseau virtuel.

    :::image type="content" source="./media/how-to-view-applied-configurations/vnet-security.png" alt-text="Capture d’écran des règles de sécurité associées à un réseau virtuel.":::

## <a name="virtual-machine-visibility"></a>Visibilité de la machine virtuelle

Au niveau de la machine virtuelle, vous pouvez voir les règles de sécurité appliquées par Virtual Network Manager et les routes effectives pour les configurations de connectivité.

### <a name="applied-security-rules"></a>Règles de sécurité appliquées

1. Accédez à une machine virtuelle dans un réseau virtuel qui a une configuration appliquée par Virtual Network Manager. Sous *Paramètres* dans le menu de gauche, sélectionnez **Réseaux**.

    :::image type="content" source="./media/how-to-view-applied-configurations/virtual-machine.png" alt-text="Capture d’écran de la page de vue d’ensemble de la machine virtuelle.":::

1. Vous voyez une liste de groupes de sécurité réseau entrants, ainsi qu’une section pour les règles de sécurité de trafic entrant appliquées par Virtual Network Manager.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-inbound-rules.png" alt-text="Capture d’écran des règles de sécurité de trafic sortant de la machine virtuelle.":::

1. Sélectionnez l’onglet **Règles de port de trafic sortant** pour voir les règles de sécurité de trafic sortant de la machine virtuelle.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-outbound-rules.png" alt-text="Capture d’écran des règles de sécurité de trafic entrant de la machine virtuelle.":::

### <a name="effective-routes"></a>Itinéraires effectifs

1. Pour voir les routages effectifs de la configuration de connectivité appliquée, sélectionnez le nom de l’interface réseau sous les paramètres *Réseaux* de la machine virtuelle.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-network-interface.png" alt-text="Capture d’écran de la sélection de la carte d’interface réseau de la machine virtuelle.":::

1. Sélectionnez ensuite **Routages effectifs** sous *Support + Dépannage*.

    :::image type="content" source="./media/how-to-view-applied-configurations/network-interface.png" alt-text="Capture d’écran du bouton Routages effectifs d’une carte d’interface réseau de machine virtuelle.":::

1. Les routes avec le tronçon suivant de type *ConnectedGroup* font partie de la configuration du maillage ou lorsque la [*connectivité directe*](concept-connectivity-configuration.md#direct-connectivity) est activée pour un groupe réseau. Les routes entre les réseaux virtuels hub et spoke apparaissent en tant que tronçon suivant de type *VNetPeering* ou *GlobalVNetPeering*.

    :::image type="content" source="./media/how-to-view-applied-configurations/effective-routes.png" alt-text="Capture d’écran des routages effectifs qui montre les groupes connectés et les routes du hub." lightbox="./media/how-to-view-applied-configurations/effective-routes-expanded.png":::

    > [!NOTE]
    > L’espace d’adressage du réseau virtuel hub est également **compris** dans le *ConnectedGroup*. C’est pourquoi, si l’appairage de réseaux virtuels échoue entre les réseaux virtuels hub et spoke, ces derniers peuvent quand même communiquer entre eux parce qu’ils sont dans un groupe connecté.
    > 

### <a name="effective-security-rules"></a>Règles de sécurité effectives

1. Pour voir les règles de sécurité effectives d’une configuration de règle de sécurité appliquée, sélectionnez le nom de l’interface réseau sous les paramètres *Réseaux* de la machine virtuelle.

    :::image type="content" source="./media/how-to-view-applied-configurations/vm-network-interface.png" alt-text="Capture d’écran de la sélection de la carte d’interface réseau de la machine virtuelle pour les règles de sécurité.":::

1. Sélectionnez ensuite **Règles de sécurité effectives** sous *Support + dépannage*.

    :::image type="content" source="./media/how-to-view-applied-configurations/network-interface-security-rules.png" alt-text="Capture d’écran du bouton Règles de sécurité effectives pour une carte d’interface réseau de machine virtuelle.":::

1. Sélectionnez le nom de l’instance Azure Virtual Network Manager pour voir les règles d’administration de la sécurité associées à la machine virtuelle.

    :::image type="content" source="./media/how-to-view-applied-configurations/effective-security-rules.png" alt-text="Capture d’écran des règles de sécurité effectives associées à la machine virtuelle.":::

## <a name="activity-log"></a>Journal d’activité

Vous pouvez afficher le journal d’activité de votre ressource Azure Virtual Network Manager pour voir les changements que vous ou votre administrateur réseau avez apportés. Pour afficher le journal d’activité, accédez à votre ressource Network Manager dans le portail Azure. Sélectionnez **Journal d’activité** dans le menu du volet de gauche. Si nécessaire, ajustez l’*intervalle de temps* et ajoutez des filtres pour affiner la liste des opérations. Vous pouvez également afficher le *Journal d’activité* en recherchant le service en haut du portail Azure.

:::image type="content" source="./media/how-to-view-applied-configurations/activity-log.png" alt-text="Capture d’écran de la page du journal d’activité pour Network Manager.":::

### <a name="list-of-operations"></a>Liste des opérations

La liste suivante contient les opérations que vous verrez dans le journal d’activité :

| Nom | Description |
| ---- | ----------- |
| Commit | Le déploiement d’une configuration a été validé dans une ou plusieurs régions. |
| Supprimer ConnectivityConfiguration | Suppression d’une configuration de connectivité de Network Manager. |
| Supprimer NetworkGroups | Suppression d’un groupe réseau de Network Manager.|
| Supprimer des règles | Suppression d’une règle d’une collection de règles. |
| Supprimer RuleCollections | Suppression d’une collection de règles d’une configuration d’administration de la sécurité. |
| Supprimer SecurityAdminConfigurations | Suppression d’une configuration d’administration de la sécurité de Network Manager. |
| ListDeploymentStatus | Affichage de l’état de déploiement d’une configuration de connectivité et d’administration de la sécurité. |
| ListActiveConnectivityConfiguration | Affichage de la liste de configurations de connectivité appliquées au réseau virtuel.|
| ListActiveSecurityAdminRules | Affichage de la liste de configurations d’administration de la sécurité appliquées au réseau virtuel. |
| Écrire ConnectivityConfiguration | Création d’une configuration de connectivité. |
| Écrire NetworkGroups | Création d’un groupe réseau. |
| Écrire NetworkManager | Création d’une instance Azure Virtual Network Manager. |
| Écrire des règles | Création d’une règle de sécurité à ajouter à une collection de règles. |
| Écrire RuleCollections | Création d’une collection de règles à ajouter à une configuration d’administration de la sécurité. |
| Écrire SecurityAdminConfiguration | Création d’une configuration d’administration de la sécurité. |

## <a name="next-steps"></a>Étapes suivantes

- Créez une instance [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) en utilisant le portail Azure.
- Consultez [FAQ sur Network Manager](faq.md) pour découvrir les questions fréquentes.
