---
title: 'Azure ExpressRoute : ajouter la prise en charge d’IPv6 à l’aide d’Azure PowerShell'
description: Découvrez comment ajouter la prise en charge d’IPv6 pour se connecter à des déploiements Azure à l’aide d’Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 04248f1cf0e0d9ecb659ca3271000a0a28a6a97b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242309"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell"></a>Ajouter la prise en charge d’IPv6 pour le Peering privé à l’aide d’Azure PowerShell

Cet article explique comment ajouter la prise en charge d’IPv6 pour se connecter via ExpressRoute à vos ressources dans Azure à l’aide d’Azure PowerShell.

## <a name="working-with-azure-powershell"></a>Utilisation d’Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Ajouter le peering privé IPv6 à votre circuit ExpressRoute

1. [Créez un circuit ExpressRoute](./expressroute-howto-circuit-arm.md) ou utilisez un circuit existant. Récupérez le circuit en exécutant la commande **Get-AzExpressRouteCircuit** :

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Récupérez la configuration de Peering privé pour le circuit en exécutant la commande **AzExpressRouteCircuitPeeringConfig** :

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Ajoutez un Peering privé IPv6 à votre configuration de Peering privé IPv4. Indiquez une paire de sous-réseaux IPv6 /126 dont vous êtes propriétaire pour votre lien principal et vos liens secondaires. À partir de chacun de ces sous-réseaux, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième IP utilisable pour son routeur.

    > [!Note]
    > L’ASN et le VlanId de l’homologue doivent correspondre à ceux de votre configuration de Peering privé IPv4.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Une fois que la configuration a été enregistrée avec succès, récupérez à nouveau le circuit en exécutant la commande **Az-ExpressRouteCircuit**. La réponse doit être similaire à l’exemple suivant :

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Mettre à jour votre connexion à un réseau virtuel existant

Suivez les étapes ci-dessous si vous disposez d’un environnement de ressources Azure existant que vous souhaitez utiliser avec votre Peering privé IPv6.

1. Récupérez réseau virtuel auquel votre circuit ExpressRoute est connecté.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Ajoutez un espace d’adressage IPv6 à votre réseau virtuel.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Ajoutez un espace d’adressage IPv6 à votre sous-réseau de passerelle. Le sous-réseau IPv6 de passerelle doit être /64 ou plus.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Si vous disposez d’une passerelle existante redondante dans une zone, exécutez la commande suivante pour activer la connectivité IPv6. (Notez que les changements peuvent nécessiter 1 heure pour être appliqués.) Sinon, [créez la passerelle de réseau virtuel](./expressroute-howto-add-gateway-resource-manager.md) à l’aide d’une référence SKU. Si vous envisagez d’utiliser FastPath, utilisez UltraPerformance ou ErGw3AZ (notez que celle-ci n’est disponible que pour des circuits utilisant ExpressRoute Direct).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```
>[!NOTE]
> Si vous disposez déjà d’une passerelle qui n’est pas redondante interzone (c’est-à-dire qu’il s’agit d’un niveau tarifaire Standard, Haute performance ou Ultra-performance), vous devez supprimer et [recréer la passerelle](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) avec n’importe quel niveau tarifaire et une IP publique statique standard.

## <a name="create-a-connection-to-a-new-virtual-network"></a>Créer une connexion à un nouveau réseau virtuel

Suivez les étapes ci-dessous si vous envisagez de vous connecter à un nouvel ensemble de ressources Azure à l’aide de votre Peering privé IPv6.

1. Créez un réseau virtuel à double pile avec l’espace d’adressage IPv4 et IPv6. Pour plus d’informations, consultez [Créer un réseau virtuel](../virtual-network/quick-create-portal.md#create-a-virtual-network).

2. [Créez le sous-réseau de passerelle à double pile](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

3. [Créez la passerelle de réseau virtuel](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) à l’aide d’une référence SKU. Si vous envisagez d’utiliser FastPath, utilisez la référence UltraPerformance ou ErGw3AZ (notez que celle-ci n’est disponible que pour des circuits utilisant ExpressRoute Direct).

4. [Liez votre réseau virtuel à votre circuit ExpressRoute](./expressroute-howto-linkvnet-arm.md).

## <a name="limitations"></a>Limites
Bien que la prise en charge IPV6 soit disponible pour les connexions à des déploiements dans des régions Azure publiques, elle ne prend pas en charge les cas d’utilisation suivants :

* Connexions à des passerelles ExpressRoute *existantes* qui ne sont pas redondantes interzones. Notez que les passerelles ExpressRoute *nouvellement* créées de n’importe quel niveau tarifaire (redondant interzone ou non) à l’aide d’une adresse IP statique standard peuvent être utilisées pour les connexions ExpressRoute à double pile
* Connexions Global Reach entre des circuits ExpressRoute
* Utiliser ExpressRoute avec un WAN virtuel
* FastPath avec circuits non ExpressRoute Direct
* FastPath avec circuits dans les emplacements de Peering suivants : Dubaï
* Coexistence avec une passerelle VPN

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre des problèmes liés à ExpressRoute, consultez les articles suivants :

* [Vérification de la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Résolution des problèmes de performances réseau](expressroute-troubleshooting-network-performance.md)
