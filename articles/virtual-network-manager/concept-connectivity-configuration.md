---
title: Configuration de la connectivité dans Azure Virtual Network Manager (préversion)
description: Découvrez les différents types de topologie réseau que vous pouvez créer avec une configuration de connectivité dans Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: face052462909e755771f12ff19fed8139675183
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484601"
---
# <a name="connectivity-configuration-in-azure-virtual-network-manager-preview"></a>Configuration de la connectivité dans Azure Virtual Network Manager (préversion)

Dans cet article, vous allez découvrir les différents types de configurations que vous pouvez créer et déployer en utilisant Azure Virtual Network Manager. Deux types de configurations sont actuellement disponibles : *Connectivité* et *Administration de la sécurité*. 

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connectivity-configuration"></a>Configuration de la connectivité

Les configurations de la *connectivité* vous permettent de créer différentes topologies réseau en fonction de vos besoins réseau. Vous avez le choix entre deux topologies, un *réseau maillé* et un *réseau hub-and-spoke*. Les connectivités entre réseaux virtuels sont définies dans les paramètres de configuration.

## <a name="mesh-network-topology"></a>Topologie du réseau maillé

Un réseau maillé est une topologie dans laquelle tous les réseaux virtuels du [groupe réseau](concept-network-groups.md) sont connectés les uns aux autres. Tous les réseaux virtuels sont connectés et peuvent se passer le trafic entre eux dans les deux sens. Par défaut, le maillage est un maillage régional, c’est pourquoi seuls les réseaux virtuels de la même région peuvent communiquer entre eux. Le **maillage global** peut être activé pour établir la connectivité des réseaux virtuels dans toutes les régions Azure. Un réseau virtuel peut faire partie d’un maximum de cinq groupes connectés. Les espaces d’adressage des réseaux virtuels ne peuvent pas se chevaucher dans une configuration, sinon vos ressources ne peuvent pas communiquer les unes avec les autres en raison de conflits réseau.

:::image type="content" source="./media/concept-configuration-types/mesh-topology.png" alt-text="Diagramme d’une topologie de réseau maillé.":::

### <a name="connected-group"></a><a name="connectedgroup"></a> Groupe connecté

Lorsque vous créez une topologie de maillage, une nouvelle construction de connectivité est créée, appelée *groupe connecté*. Dans un groupe connecté, les réseaux virtuels peuvent communiquer entre eux comme si vous deviez connecter les réseaux virtuels entre eux manuellement. Lorsque vous regardez les routes effectives pour une interface réseau, vous voyez un tronçon suivant de type **ConnectedGroup**. Le réseau virtuel connecté ensemble dans un groupe connecté n’a pas de configuration d’appairage indiquée sous *Appairages*.

> [!NOTE]
> * Si vous avez des sous-réseaux en conflit dans deux réseaux virtuels ou plus, les ressources de ces sous-réseaux *ne pourront pas* communiquer entre elles, même si elles font partie du même réseau maillé.
> * Un réseau virtuel peut faire partie d’un maximum de **deux** configurations de maillage.

## <a name="hub-and-spoke-topology"></a>Topologie hub-and-spoke

Hub-and-spoke est une topologie réseau dans laquelle vous avez un réseau virtuel sélectionné comme réseau virtuel hub. Ce réseau virtuel est appairé de manière bidirectionnelle à chaque réseau virtuel spoke de la configuration. Cette topologie est utile lorsque vous souhaitez isoler un réseau virtuel tout en lui laissant une connectivité aux ressources communes dans le réseau virtuel hub. 

Dans cette configuration, vous disposez de paramètres que vous pouvez activer, tels que la *connectivité directe* entre les réseaux virtuels spoke. Par défaut, cette connectivité concerne uniquement les réseaux virtuels d’une même région. Pour autoriser une connectivité entre différentes régions Azure, vous devez activer le *maillage global*. Vous pouvez également activer le transit par *passerelle* pour autoriser les réseaux virtuels spoke à utiliser la passerelle VPN ou ExpressRoute déployée dans le hub.

### <a name="direct-connectivity"></a>Connectivité directe

L’activation d’une *connectivité directe* crée un [*groupe connecté*](#connectedgroup) qui contient les réseaux virtuels spoke de la même région. Cette connexion est établie uniquement pour les réseaux virtuels du même groupe réseau. 

Par exemple, vous créez deux groupes réseau. Vous activez la connectivité directe pour le groupe réseau *Production*, mais pas pour le groupe réseau *Test*. Cette configuration permet uniquement aux réseaux virtuels du groupe réseau *Production* de communiquer les uns avec les autres, mais pas à ceux du groupe réseau *Test*. 

Consultez l’exemple de diagramme ci-dessous :

:::image type="content" source="./media/concept-configuration-types/hub-and-spoke.png" alt-text="Diagramme d’une topologie hub-and-spoke avec deux groupes réseau.":::

Lorsque vous regardez les routes effectives sur une machine virtuelle, la route entre le hub et les réseaux virtuels spoke a un tronçon suivant de type *VNetPeering* ou *GlobalVNetPeering*. Les routes entre les réseaux virtuels spoke apparaissent avec le type de tronçon suivant *ConnectedGroup*. Dans l’exemple ci-dessus, seul le groupe réseau *Production* a le type *ConnectedGroup* parce que la *connectivité directe* est activée pour lui.

> [!NOTE]
> L’espace d’adressage du réseau hub est ajouté au *ConnectedGroup* quand *Connectivité directe* est **activée**. Par conséquent, si l’appairage de réseaux virtuels entre le hub et le réseau virtuel spoke échoue, ils peuvent quand même communiquer par *ConnectedGroup*.

#### <a name="use-cases"></a>Cas d'utilisation

L’activation de la connectivité directe entre les réseaux virtuels spoke peut être utile lorsque vous voulez avoir une appliance virtuelle réseau ou un service commun dans le réseau virtuel hub, mais que vous n’avez pas toujours besoin d’accéder au hub. Vous avez plutôt besoin que vos réseaux virtuels spoke du groupe réseau communiquent entre eux. Par rapport aux réseaux hub-and-spoke classiques, cette topologie améliore les performances en supprimant le tronçon supplémentaire via le réseau virtuel hub.

### <a name="global-mesh"></a>Maillage global

Le maillage global est nécessaire lorsque vous souhaitez que vos réseaux virtuels spoke communiquent entre eux d’une région à l’autre. Cette connectivité est limitée au réseau virtuel du même groupe réseau. Pour activer la connectivité des réseaux virtuels entre les régions, vous devez **activer la connectivité du maillage entre les régions** du groupe réseau. Les connexions créées entre les réseaux virtuels spoke se trouvent dans un [*groupe connecté*](#connectedgroup). 

### <a name="use-hub-as-a-gateway"></a>Utiliser le hub comme passerelle

Une autre option que vous pouvez activer dans une configuration hub-and-spoke consiste à utiliser le hub comme passerelle. Ce paramètre permet à tous les réseaux virtuels du groupe réseau d’utiliser la passerelle VPN ou ExpressRoute dans le réseau virtuel hub pour passer le trafic. Consultez [Passerelles et connectivité locale](/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity).

Lorsque vous déployez une topologie hub-and-spoke à partir du portail Azure, l’option **Utiliser le hub comme passerelle** est activée par défaut pour les réseaux virtuels spoke du groupe réseau. Azure Virtual Network Manager tente de créer une connexion d’appairage de réseaux virtuels entre le hub et le réseau virtuel spoke dans le groupe de ressources. Si la passerelle n’existe pas dans le réseau virtuel hub, la création de l’appairage à partir du réseau virtuel spoke vers le hub échoue. La connexion d’appairage du hub vers le spoke est quand même créée sans connexion établie. 

> [!NOTE]
> Si vous activez la *connectivité directe* pour le groupe réseau spoke, le hub est également inclus dans le groupe connecté. C’est pourquoi, même si la connexion d’appairage du spoke vers le hub n’est pas créée, les réseaux virtuels spoke peuvent communiquer avec le réseau virtuel hub via le groupe connecté.
>

## <a name="next-steps"></a>Étapes suivantes

- Créez une instance [Azure Virtual Network Manager](create-virtual-network-manager-portal.md).
- Découvrez les [déploiements de configuration](concept-deployments.md) dans Azure Virtual Network Manager.
- Découvrez comment bloquer le trafic réseau avec une [Configuration de l’administration de la sécurité](how-to-block-network-traffic-portal.md).
