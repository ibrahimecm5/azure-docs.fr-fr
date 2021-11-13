---
title: 'Azure ExpressRoute : ajouter la prise en charge IPv6 à l’aide du Portail Azure'
description: Découvrez comment ajouter la prise en charge IPv6 pour se connecter à des déploiements Azure à l’aide du Portail Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: fb7f606b0427a5b3f5293fd82bfd9a0544bc945f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226335"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal"></a>Ajouter la prise en charge IPv6 pour le Peering privé à l’aide du Portail Azure

Cet article explique comment ajouter la prise en charge IPv6 pour se connecter via ExpressRoute à vos ressources dans Azure à l’aide du Portail Azure.

>[!NOTE]
> Certains aspects de l’expérience du portail sont encore en cours d’implémentation. Par conséquent, veuillez suivre l’ordre exact des instructions fournies dans ce document pour réussir à ajouter la prise en charge d’IPv6 via le portail. Plus précisément, veillez à créer votre réseau virtuel et sous-réseau ou à ajouter un espace d’adressage IPv6 à votre réseau virtuel existant et à GatewaySubnet *avant* de créer une nouvelle passerelle de réseau virtuel dans le portail.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Dans un navigateur, accédez au [Portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Ajouter le peering privé IPv6 à votre circuit ExpressRoute

1. [Créez un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) ou accédez au circuit existant que vous souhaitez modifier.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Capture d’écran de la liste des circuits ExpressRoute.":::

1. Sélectionnez la configuration de peering **privé Azure**.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Capture d’écran de la page de présentation d’ExpressRoute.":::

1. Ajoutez un peering privé IPv6 à votre configuration de peering privé IPv4 existante en sélectionnant « Les deux » pour les **sous-réseaux**, ou activez uniquement le Peering privé IPv6 sur votre nouveau circuit en sélectionnant « IPv6 ». Indiquez une paire de sous-réseaux IPv6 /126 dont vous êtes propriétaire pour votre lien principal et vos liens secondaires. À partir de chacun de ces sous-réseaux, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième IP utilisable pour son routeur. **Enregistrez** votre configuration de peering après avoir spécifié tous les paramètres.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Capture d’écran de l’ajout d’Ipv6 sur la page de Peering privé.":::

1. Une fois la configuration acceptée, vous devez voir quelque chose de similaire à l’exemple suivant.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Capture d’écran d’Ipv6 configuré pour le Peering privé.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Mettre à jour votre connexion à un réseau virtuel existant

Suivez les étapes ci-dessous si vous disposez d’un environnement de ressources Azure existant que vous souhaitez utiliser avec votre Peering privé IPv6.

1. Accédez au réseau virtuel auquel votre circuit ExpressRoute est connecté.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Capture d’écran de la liste des réseaux virtuels.":::

1. Accédez à l’onglet **Espace d’adressage** et ajoutez un espace d’adressage IPv6 à votre réseau virtuel. **Enregistrez** votre espace d’adressage.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Capture d’écran de l’ajout de l’espace d’adressage IPv6 au réseau virtuel.":::

1. Accédez à l’onglet **Sous-réseaux** et sélectionnez **GatewaySubnet**. Cochez la case **Ajouter un espace d’adressage IPv6** et indiquez un espace d’adressage IPv6 pour votre sous-réseau. Le sous-réseau IPv6 de la passerelle doit être /64 ou plus. **Enregistrez** votre configuration après avoir spécifié tous les paramètres.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Capture d’écran de l’ajout de l’espace d’adressage Ipv6 au sous-réseau.":::
    
1. Si vous disposez d’une passerelle existante redondante dans une zone, exécutez la commande suivante dans PowerShell pour activer la connectivité IPv6. (Notez que les changements peuvent nécessiter 1 heure pour être appliqués.) Sinon, [créez la passerelle de réseau virtuel](./expressroute-howto-add-gateway-portal-resource-manager.md) à l’aide d’une quelconque référence SKU et d’une adresse IP publique standard statique. Si vous envisagez d’utiliser FastPath, utilisez la référence UltraPerformance ou ErGw3AZ (notez que cette option n’est disponible que pour des circuits utilisant ExpressRoute Direct).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    
>[!NOTE]
> If you have an existing gateway that is not zone-redundant (meaning it is Standard, High Performance, or Ultra Performance SKU), you will need to delete and [recreate the gateway](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) using any SKU and a Standard, Static public IP address.

## Create a connection to a new virtual network

Follow the steps below if you plan to connect to a new set of Azure resources using your IPv6 Private Peering.

1. Create a dual-stack virtual network with both IPv4 and IPv6 address space. For more information, see [Create a virtual network](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Create the dual-stack gateway subnet](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Create the virtual network gateway](expressroute-howto-add-gateway-resource-manager.md) using any SKU and a Standard, Static public IP address. If you plan to use FastPath, use UltraPerformance or ErGw3AZ (note that this option is only available for circuits using ExpressRoute Direct). **NOTE:** Please use the PowerShell instructions for this step as the Azure portal experience is still under development.

1. [Link your virtual network to your ExpressRoute circuit](expressroute-howto-linkvnet-portal-resource-manager.md).

## Limitations
While IPv6 support is available for connections to deployments in Public Azure regions, it doesn't support the following use cases:

* Connections to *existing* ExpressRoute gateways that are not zone-redundant. Note that *newly* created ExpressRoute gateways of any SKU (both zone-redundant and not) using  a Standard, Static IP address can be used for dual-stack ExpressRoute connections
* Global Reach connections between ExpressRoute circuits
* Use of ExpressRoute with virtual WAN
* FastPath with non-ExpressRoute Direct circuits
* FastPath with circuits in the following peering locations: Dubai
* Coexistence with VPN Gateway

## Next steps

To troubleshoot ExpressRoute problems, see the following articles:

* [Verifying ExpressRoute connectivity](expressroute-troubleshooting-expressroute-overview.md)
* [Troubleshooting network performance](expressroute-troubleshooting-network-performance.md)
