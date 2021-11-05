---
title: Créer une topologie en étoile avec Azure Virtual Network Manager (préversion) – Azure PowerShell
description: Découvrez comment créer une topologie de réseau en étoile avec Azure Virtual Network Manager en utilisant Azure PowerShell.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 8762a43608c52ceec3f8cb92e08f88a94c244e30
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097812"
---
# <a name="create-a-hub-and-spoke-topology-with-azure-virtual-network-manager-preview---azure-powershell"></a>Créer une topologie en étoile avec Azure Virtual Network Manager (préversion) – Azure PowerShell

Cet article explique comment créer une topologie de réseau en étoile avec Azure Virtual Network Manager. Avec cette configuration, vous sélectionnez un réseau virtuel qui agit en tant que hub. Par défaut, tous les réseaux virtuels constituant des branches de l’étoile auront un appairage (peering) bidirectionnel uniquement avec le hub. Vous pouvez également activer une connectivité directe entre les réseaux virtuels constituant les branches de l’étoile et leur permettre d’utiliser la passerelle de réseau virtuel dans le hub.

> [!IMPORTANT]
> *Azure Virtual Network Manager* est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [**Conditions supplémentaires relatives à l’utilisation des préversions de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Lire la topologie de réseau [en étoile (Hub and Spoke)](concept-connectivity-configuration.md#hub-and-spoke-topology) .
* Créé une [instance Azure Virtual Network Manager](create-virtual-network-manager-powershell.md#create-virtual-network-manager).
* Identifiez les réseaux virtuels que vous souhaitez utiliser dans la configuration en étoile, ou créez des [réseaux virtuels](../virtual-network/quick-create-powershell.md). 

## <a name="create-a-network-group"></a>Créer un groupe réseau

Cette section vous aidera à créer un groupe réseau contenant les réseaux virtuels que vous utiliserez pour la topologie de réseau hub-and-spoke.

### <a name="static-membership"></a>Appartenance statique

1. Créez un membre de réseau virtuel statique avec [New-AzNetworkManagerGroupMembersItem](/powershell/module/az.network/new-aznetworkmanagergroupmembersitem).

    ```azurepowershell-interactive
    $member = New-AzNetworkManagerGroupMembersItem –ResourceId "/subscriptions/abcdef12-3456-7890-abcd-ef1234567890/resourceGroups/myAVNMResourceGroup/providers/Microsoft.Network/virtualNetworks/VNetA"
    ```

1. Ajoutez le membre statique au groupe d’appartenance statique à l’aide des commandes suivantes :

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerGroupMembersItem]]$groupMembers = @()  
    $groupMembers.Add($member)
    ```

### <a name="dynamic-membership"></a>Adhésion dynamique

1. Définissez l’instruction conditionnelle et stockez-la dans une variable :

    ```azurepowershell-interactive
    $conditionalMembership = '{ 
        "allof":[ 
            { 
            "field": "name", 
            "contains": "VNet" 
            } 
        ] 
    }' 
    ```

1. Créez le groupe de réseaux à l’aide du groupe d’appartenance statique (GroupMember) ou du groupe d’appartenance dynamique (ConditionalMembership) définis précédemment à l’aide de [New-AzNetworkManagerGroup](/powershell/module/az.network/new-aznetworkmanagergroup).

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        GroupMember = $groupMembers
        ConditionalMembership = $conditionalMembership
        NetworkManagerName = 'myAVNM'
        MemberType = 'Microsoft.Network/VirtualNetworks
    }
    $networkgroup = New-AzNetworkManagerGroup @ng
    ```

## <a name="create-a-hub-and-spoke-connectivity-configuration"></a>Créer une configuration de connectivité de réseau en étoile

Cette section vous guide tout au long de la création d’une configuration en étoile avec le groupe de réseaux que vous avez créé dans la section précédente.

1. Créez un élément de groupe de connectivité de branche d’étoile pour ajouter un groupe de réseaux avec [New-AzNetworkManagerConnectivityGroupItem](/powershell/module/az.network/new-aznetworkmanagerconnectivitygroupitem). Vous pouvez activer une connectivité directe avec l’indicateur `-GroupConnectivity`, un maillage global avec l’indicateur `-IsGlobal` ou l’indicateur `-UseHubGateway` pour utiliser la passerelle dans le réseau virtuel hub :

    ```azurepowershell-interactive
    $spokes = @{
        NetworkGroupId = $networkgroup.Id
    }
    $spokesGroup = New-AzNetworkManagerConnectivityGroupItem @gi -UseHubGateway -GroupConnectivity 'None' -IsGlobal
    ```

1. Créez un groupe de connectivité de branche d’étoile et ajoutez l’élément de groupe de l’étape précédente.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerConnectivityGroupItem]]$configGroup = @()
    $configGroup.Add($spokesGroup) 
    ```

1. Créez un élément de groupe de connectivité hub et définissez le réseau virtuel que vous allez utiliser en tant que hub avec [New-AzNetworkManagerHub](/powershell/module/az.network/new-aznetworkmanagerhub).

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.NetworkManager.PSNetworkManagerHub]]$hubList = @()
    
    $hub = @{
        ResourceId = '/subscriptions/abcdef12-3456-7890-abcd-ef1234567890/resourceGroups/myAVNMResourceGroup/providers/Microsoft.Network/virtualNetworks/VNetA'
        ResourceType = 'Microsoft.Network/virtualNetworks'
    } 
    $hubvnet = New-AzNetworkManagerHub @hub

    $hubList.Add($hubvnet)
    ```

1. Créez la configuration de connectivité avec [New-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/new-aznetworkmanagerconnectivityconfiguration).

    ```azurepowershell-interactive
    $config = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        ConnectivityTopology = 'HubAndSpoke'
        Hub = $hubList
        AppliesToGroup = $configGroup
    }
    $connectivityconfig = New-AzNetworkManagerConnectivityConfiguration @config -DeleteExistingPeering -IsGlobal
     ```

## <a name="deploy-the-hub-and-spoke-configuration"></a>Déployer la configuration en étoile

Validez la configuration dans les régions cibles avec [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($connectivityconfig.id) 
[System.Collections.Generic.List[string]]$regions = @()   
$regions.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Connectivity'
}
Deploy-AzNetworkManagerCommit @deployment
```

## <a name="confirm-deployment"></a>Confirmer le déploiement

1. Accédez à l’un des réseaux virtuels dans le portail, puis sélectionnez **Peerings** sous *Paramètres*. Vous devriez voir la création d’une nouvelle connexion de peering entre le hub et les branches de l’étoile dont le nom contient *AVNM*.

1. Pour tester *la connectivité directe* entre les branches de l’étoile, déployez une machine virtuelle dans chacun de leurs réseaux virtuels. Ensuite, démarrez une requête ICMP d’une machine virtuelle à l’autre.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Règles de sécurité administrateur](concept-security-admins.md)
- Découvrez comment bloquer le trafic réseau avec une [Configuration de l’administration de la sécurité](how-to-block-network-traffic-powershell.md).
