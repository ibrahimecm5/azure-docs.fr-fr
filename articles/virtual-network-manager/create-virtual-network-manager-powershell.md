---
title: 'Démarrage rapide : Créer un réseau maillé avec Azure Virtual Network Manager en utilisant Azure PowerShell'
description: Utilisez ce guide de démarrage rapide pour apprendre à créer un réseau maillé avec le gestionnaire de réseau virtuel à l’aide de Azure PowerShell.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: template-quickstart, ignite-fall-2021
ms.openlocfilehash: effbdd0482e40535793e2c60dcdce3b4cdb518e6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466459"
---
# <a name="quickstart-create-a-mesh-network-with-azure-virtual-network-manager-using-azure-powershell"></a>Démarrage rapide : Créer un réseau maillé avec Azure Virtual Network Manager en utilisant Azure PowerShell

Démarrez avec Azure Virtual Network Manager en utilisant Azure PowerShell pour gérer la connectivité de vos réseaux virtuels.

Dans ce guide de démarrage rapide, vous allez déployer trois réseaux virtuels et utiliser le gestionnaire de réseau virtuel Azure pour créer une topologie de réseau maillé.

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Assurez-vous que vous disposez des tout derniers modules PowerShell, sinon vous pouvez utiliser Azure Cloud Shell dans le portail.
* Si vous exécutez PowerShell en local, vous devez également exécuter `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="register-subscription-for-public-preview"></a>Inscrire un abonnement à la préversion publique

Utilisez la commande suivante pour inscrire votre abonnement Azure à la Préversion publique d’Azure Virtual Network Manager :

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowAzureNetworkManager -ProviderNamespace Microsoft.Network
```

## <a name="install-azure-powershell-module"></a>Installer le module Azure PowerShell

Installez la version la plus récente du module Azure PowerShell *Az.Network* à l’aide de cette commande :

```azurepowershell-interactive
Install-Module -Name Az.Network -AllowPrerelease
```

## <a name="create-virtual-network-manager"></a>Créer une liaison de réseau virtuel

1. Définissez l’étendue et le type d’accès de cette instance du gestionnaire de réseau virtuel Azure. Vous pouvez choisir de créer l’étendue avec un groupe d’abonnements ou un groupe d’administration, ou une combinaison des deux. Créez l’étendue à l’aide de [New-AzNetworkManagerScope](/powershell/module/az.network/new-aznetworkmanagerscope).

    ```azurepowershell-interactive
    [System.Collections.Generic.List[string]]$subGroup = @()  
    $group.Add("/subscriptions/abcdef12-3456-7890-abcd-ef1234567890")
    [System.Collections.Generic.List[string]]$mgGroup = @()  
    $group.Add("/managementGroups/abcdef12-3456-7890-abcd-ef1234567890")
    
    [System.Collections.Generic.List[String]]$access = @()  
    $access.Add("Connectivity");  
    $access.Add("Security"); 
 
    $scope = New-AzNetworkManagerScope -Subscription $subGroup  -ManagementGroup $mgGroup
    ```

1. Créez le gestionnaire de réseau virtuel avec [New-AzNetworkManager](/powershell/module/az.network/new-aznetworkmanager). Cet exemple crée un gestionnaire de réseau virtuel Azure nommé **myAVNM** à l’emplacement ouest des États-Unis.

    ```azurepowershell-interactive
    $avnm = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerScope = $scope.id
        NetworkManagerScopeAccess = $access
        Location = 'West US'
    }
    $networkmanager = New-AzNetworkManager @avnm
    ```

## <a name="create-resource-group-and-virtual-networks"></a>Créer un groupe de ressources et un réseau virtuel

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de pouvoir créer un gestionnaire de réseau virtuel Azure, vous devez créer un groupe de ressources pour héberger le gestionnaire de réseau. Créez un groupe de ressources avec [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Cet exemple crée un groupe de ressources nommé **myAVNMResourceGroup** dans l'emplacement **WestUS**.

```azurepowershell-interactive
$rg = @{
    Name = 'myAVNMResourceGroup'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-three-virtual-networks"></a>Créer trois réseaux virtuels

Créez un réseau virtuel avec [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Cet exemple crée des réseaux virtuels nommés **VNetA**, **VNetB** et **VNetC** dans l’emplacement **ouest des États-Unis** . Si vous avez déjà des réseaux virtuels avec lesquels vous souhaitez créer un réseau maillé, vous pouvez passer à la section suivante.

```azurepowershell-interactive
$vnetA = @{
    Name = 'VNetA'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetworkA = New-AzVirtualNetwork @vnetA

$vnetB = @{
    Name = 'VNetB'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.1.0.0/16'    
}
$virtualNetworkB = New-AzVirtualNetwork @vnetB

$vnetC = @{
    Name = 'VNetC'
    ResourceGroupName = 'myAVNMResourceGroup'
    Location = 'West US'
    AddressPrefix = '10.2.0.0/16'    
}
$virtualNetworkC = New-AzVirtualNetwork @vnetC
```

### <a name="add-a-subnet-to-each-virtual-network"></a>Ajoutez un sous-réseau au réseau virtuel

Pour terminer la configuration des réseaux virtuels, ajoutez un sous-réseau/24 à chacun d’eux. Créez une configuration de sous-réseau nommée **Par défaut** à l’aide de la commande [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) :

```azurepowershell-interactive
$subnetA = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkA
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigA = Add-AzVirtualNetworkSubnetConfig @subnetA
$virtualnetworkA | Set-AzVirtualNetwork

$subnetB = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkB
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigC = Add-AzVirtualNetworkSubnetConfig @subnetB
$virtualnetworkB | Set-AzVirtualNetwork

$subnetC = @{
    Name = 'default'
    VirtualNetwork = $virtualNetworkC
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfigC = Add-AzVirtualNetworkSubnetConfig @subnetC
$virtualnetworkC | Set-AzVirtualNetwork
```

## <a name="create-a-network-group"></a>Créer un groupe réseau

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

1. Créez le groupe de réseaux à l’aide de l’instruction conditionnelle définie à la dernière étape à l’aide de [New-AzNetworkManagerGroup](/powershell/module/az.network/new-aznetworkmanagergroup).

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        GroupMember = $groupMembers
        ConditionalMembership = $conditionalMembership
        NetworkManagerName = 'myAVNM'
        MemberType = 'Microsoft.Network/VirtualNetwork'
    }
    $networkgroup = New-AzNetworkManagerGroup @ng
    ```

## <a name="create-a-configuration"></a>Créer une configuration

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

## <a name="commit-deployment"></a>Valider le déploiement

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

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du gestionnaire de réseau virtuel Azure, vous devez vous assurer que toutes les conditions suivantes sont remplies avant de pouvoir supprimer la ressource :

* Il n’existe aucun déploiement de configurations dans une région.
* Toutes les configurations ont été supprimées.
* Tous les groupes de réseaux ont été supprimés.

1. Supprimez le déploiement de la connectivité en déployant une configuration vide avec [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

    ```azurepowershell-interactive
    [System.Collections.Generic.List[string]]$configIds = @()
    [System.Collections.Generic.List[string]]$target = @()   
    $target.Add("westus")     
    $removedeployment = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
        ConfigurationId = $configIds
        Target = $target
        CommitType = 'Connectivity'
    }
    Deploy-AzNetworkManagerCommit @removedeployment
    ```

1. Supprimez la configuration de la connectivité avec [Remove-AzNetworkManagerConnectivityConfiguration](/powershell/module/az.network/remove-aznetworkmanagerconnectivityconfiguration)

    ```azurepowershell-interactive
    $removeconfig = @{
        Name = 'connectivityconfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    Remove-AzNetworkManagerConnectivityConfiguration @removeconfig   
    ```

1. Supprimez le groupe de réseaux avec [Remove-AzNetworkManagerGroup](/powershell/module/az.network/remove-aznetworkmanagergroup).

    ```azurepowershell-interactive
    $removegroup = @{
        Name = 'myNetworkGroup'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    Remove-AzNetworkManagerGroup @removegroup
    ```

1. Supprimez l’instance Network Manager avec [Remove-AzNetworkManager](/powershell/module/az.network/remove-aznetworkmanager).

    ```azurepowershell-interactive
    $removenetworkmanager = @{
        Name = 'myAVNM'
        ResourceGroupName = 'myAVNMResourceGroup'
    }
    Remove-AzNetworkManager @removenetworkmanager
    ```

1. Lorsque vous n'avez plus besoin des ressources que vous avez créées, utilisez la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources.

    ```azurepowershell-interactive
    Remove-AzResourceGroup -Name 'myAVNMResourceGroup'
    ```

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez créé le gestionnaire de réseau virtuel Azure, passez à la rubrique pour savoir comment bloquer le trafic réseau à l’aide de la configuration de l’administrateur de sécurité :

> [!div class="nextstepaction"]
> [Bloquer le trafic réseau avec les règles d’administration de sécurité](how-to-block-network-traffic-powershell.md)
