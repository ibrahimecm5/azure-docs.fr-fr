---
title: 'Créer une topologie de réseau de maillage avec Azure Virtual Network Manager (préversion) : Azure PowerShell'
description: Découvrez comment créer une topologie de réseau de maillage avec Azure Virtual Network Manager en utilisant Azure PowerShell.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d763a0914a624d21d3845a05c1f2c0e6ef7e1ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096673"
---
# <a name="create-a-mesh-network-topology-with-azure-virtual-network-manager-preview---azure-powershell"></a>Créer une topologie de réseau de maillage avec Azure Virtual Network Manager (préversion) : Azure PowerShell

Dans cet article, vous allez apprendre comment créer une topologie de réseau de maillage avec Azure Virtual Network Manager en utilisant Azure PowerShell. Avec cette configuration, tous les réseaux virtuels présent dans la même région du même groupe réseau peuvent communiquer les uns avec les autres. Vous pouvez activer la connectivité inter-régions en activant le paramètre de maillage global dans la configuration de la connectivité.

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* En savoir plus sur la topologie du réseau de [maillage](concept-connectivity-configuration.md#mesh-network-topology).
* Créé une [instance Azure Virtual Network Manager](create-virtual-network-manager-powershell.md#create-virtual-network-manager).
* Identifiez les réseaux virtuels que vous souhaitez utiliser dans la configuration de maillage ou créez de nouveaux [réseaux virtuels](../virtual-network/quick-create-powershell.md).

## <a name="create-a-network-group"></a>Créer un groupe réseau

Cette section vous aidera à créer un groupe réseau contenant les réseaux virtuels que vous utiliserez pour la topologie de réseau hub-and-spoke.

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

## <a name="create-a-mesh-connectivity-configuration"></a>Créer une configuration de connectivité de maillage

Cette section vous guide tout au long de la création d’une configuration de maillage avec le groupe réseau que vous avez créé dans la section précédente.

1. Créez un élément de groupe de connectivité pour ajouter un groupe de réseaux à avec [New-AzNetworkManagerConnectivityGroupItem](/powershell/module/az.network/new-aznetworkmanagerconnectivitygroupitem).

    ```azurepowershell-interactive
    $gi = @{
        NetworkGroupId = $networkgroup.Id
    }
    $groupItem = New-AzNetworkManagerConnectivityGroupItem @gi
    ```

1. Créez un groupe de configurations et ajoutez l’élément de groupe à partir de l’étape précédente.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.PSNetworkManagerConnectivityGroupItem]]$configGroup = @()
    $configGroup.Add($groupItem)
    ```

1. Créez la configuration de connectivité avec [New-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/new-aznetworkmanagerconnectivityconfiguration).

    ```azurepowershell-interactive
    $config = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        ConnectivityTopology = 'Mesh'
        AppliesToGroup = $configGroup
    }
    $connectivityconfig = New-AzNetworkManagerConnectivityConfiguration @config
     ```

## <a name="deploy-the-mesh-configuration"></a>Déployer la configuration de maillage

Validez la configuration dans les régions cibles avec [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($connectivityconfig.id) 
[System.Collections.Generic.List[string]]$target = @()   
$target.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $target
    CommitType = 'Connectivity'
}
Deploy-AzNetworkManagerCommit @deployment
```

## <a name="confirm-deployment"></a>Confirmer le déploiement

1. Accédez à l’un des réseaux virtuels dans le portail, puis sélectionnez **Gestionnaire de réseau** sous *Paramètres*. Vous devez voir la configuration indiquée sur cette page.

1. Pour tester la connectivité entre les réseaux virtuels, déployez une machine virtuelle de test dans chaque réseau virtuel et démarrez une requête ICMP entre elles.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Règles de sécurité administrateur](concept-security-admins.md)
- Découvrez comment bloquer le trafic réseau avec une [Configuration de l’administration de la sécurité](how-to-block-network-traffic-powershell.md).
