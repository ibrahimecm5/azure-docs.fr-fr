---
title: Configurer des communautés BGP personnalisées pour le Peering privé Azure ExpressRoute (préversion)
description: Découvrez comment appliquer ou mettre à jour la valeur de communauté BGP pour un réseau virtuel nouveau ou existant.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/18/2021
ms.author: duau
ms.openlocfilehash: afcb09356c10a68b66870cf9594dd5fe9c6a18cb
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058465"
---
# <a name="configure-custom-bgp-communities-for-azure-expressroute-private-peering-preview"></a>Configurer des communautés BGP personnalisées pour le Peering privé Azure ExpressRoute (préversion)

Les communautés BGP sont des regroupements de préfixes IP marqués d’une valeur de communauté. Cette valeur peut être utilisée pour prendre des décisions de routage sur l’infrastructure du routeur. Vous pouvez appliquer des filtres ou spécifier des préférences de routage pour le trafic envoyé à votre site local à partir d’Azure avec des balises de communauté BGP. Cet article explique comment appliquer une valeur de communauté BGP personnalisée pour vos réseaux virtuels à l’aide d’Azure PowerShell. Une fois configurée, vous pouvez afficher la valeur de communauté BGP régionale et la valeur de communauté personnalisée de votre réseau virtuel. Cette valeur sera utilisée pour le trafic sortant envoyé via ExpressRoute en provenance de ce réseau virtuel.

## <a name="prerequisites"></a>Prérequis

* Avant de commencer la configuration, examinez les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md).

* Vous devez disposer d’un circuit ExpressRoute actif. 
  * Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le activer par votre fournisseur de service de connectivité. 
  * Vérifiez que le peering privé Azure est configuré pour votre circuit. Pour obtenir des instructions de routage, consultez l'article sur la [configuration du routage](expressroute-howto-routing-arm.md) . 
  * Assurez-vous que l’homologation privée Azure est configurée et établit une homologation BGP entre votre réseau et Microsoft pour une connectivité de bout en bout.
  
### <a name="working-with-azure-powershell"></a>Utilisation d’Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="apply-a-custom-bgp-community-value-for-a-new-virtual-network"></a>Appliquer une valeur de communauté BGP personnalisée pour un nouveau réseau virtuel

1. Pour commencer la configuration, connectez-vous à votre compte Azure et sélectionnez l’abonnement que vous souhaitez utiliser.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

1. Créez un groupe de ressources pour stocker le nouveau réseau virtuel.

    ```azurepowershell-interactive
    $rg = @{
        Name = 'myERRG'
        Location = 'WestUS'
    }
    New-AzResourceGroup @rg
    ```

1. Créez un réseau virtuel avec l'indicateur `-BgpCommunity` pour appliquer une valeur de communauté BGP.

    ```azurepowershell-interactive
    $vnet = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
        Location = 'WestUS'
        AddressPrefix = '10.0.0.0/16'
        BgpCommunity = '12076:20001'    
    }
    New-AzVirtualNetwork @vnet
    ```
    
    > [!NOTE]
    > `12076:` est requis avant votre valeur de communauté personnalisée.
    >

1. Récupérez votre réseau virtuel et passez en revue ses propriétés. Vous remarquerez une section *BgpCommunities* qui contient une valeur **RegionalCommunity** et une valeur **VirtualNetworkCommunity**. La valeur *RegionalCommunity* est prédéfinie en fonction de la région Azure du réseau virtuel. La valeur *VirtualNetworkCommunity* doit correspondre à votre définition personnalisée.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    Get-AzVirtualNewtork @virtualnetwork
    ```

## <a name="applying-or-updating-the-custom-bgp-value-for-an-existing-virtual-network"></a>Application ou mise à jour de la valeur BGP personnalisée pour un réseau virtuel existant

1. Récupérez le réseau virtuel que vous souhaitez appliquer ou mettez à jour la valeur de communauté BGP et stockez-la dans une variable.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    $vnet = Get-AzVirtualNetwork @virtualnetwork
    ```

1. Mettez à jour la valeur `VirtualNetworkCommunity` de votre réseau virtuel.

    ```azurepowershell-interactive
    $vnet.BgpCommunities.VirtualNetworkCommunity = '12076:20002'
    $vnet | Set-AzVirtualNetwork
    ```

    > [!NOTE]
    > `12076:` est requis avant votre valeur de communauté personnalisée.
    >

1. Récupérez votre réseau virtuel et passez en revue ses propriétés mises à jour. La valeur **RegionalCommunity** est prédéfinie en fonction de la région Azure du réseau virtuel. La valeur **VirtualNetworkCommunity** doit correspondre à votre définition personnalisée.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    Get-AzVirtualNetwork @virtualnetwork
    ```

> [!IMPORTANT]
>  Si votre réseau virtuel existant est déjà connecté à un circuit ExpressRoute, vous devez supprimer et recréer la connexion ExpressRoute après avoir appliqué la valeur de communauté BGP personnalisée. Consultez [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md) pour savoir comment faire.
>

## <a name="next-steps"></a>Étapes suivantes

- [Vérification de la connectivité ExpressRoute](expressroute-troubleshooting-expressroute-overview.md).
- [Résoudre les problèmes liés aux performances de votre réseau](expressroute-troubleshooting-network-performance.md)
