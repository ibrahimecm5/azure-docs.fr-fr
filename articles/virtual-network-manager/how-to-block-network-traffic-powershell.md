---
title: Blocage du trafic réseau avec Azure Virtual Network Manager (préversion) -Azure PowerShell
description: Découvrez comment bloquer le trafic réseau à l’aide de règles de sécurité dans Azure Virtual Network Manager avec Azure PowerShell.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: d2e1a79891c0061dd49749fa2e27a2725a0b922f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098063"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-powershell"></a>Blocage du trafic réseau avec Azure Virtual Network Manager (préversion) -Azure PowerShell

Cet article explique comment créer une règle de sécurité pour bloquer le trafic réseau entrant sur les ports 80 et 443 que vous pouvez ajouter à une collection de règles. Pour plus d’informations, consultez [Règles d’administration de la sécurité](concept-security-admins.md).

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Avant de commencer la configuration des règles de sécurité, procédez comme suit :

* Vous comprenez chaque élément dans une [règle d’administration de la sécurité](concept-security-admins.md).
* Vous avez créé une [instance Azure Virtual Network Manager](create-virtual-network-manager-powershell.md).

## <a name="create-a-securityadmin-configuration"></a>Créer une configuration SecurityAdmin

1. Créez une nouvelle configuration SecurityAdmin [New-AzNetworkManagerSecurityAdminConfiguration](/powershell/module/az.network/new-aznetworkmanagersecurityadminconfiguration).

    ```azurepowershell-interactive
    $config = @{
        Name = 'SecurityConfig'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $securityconfig = New-AzNetworkManagerSecurityAdminConfiguration @config 
    ```

1. Stockez le groupe réseau sur une variable avec la fonction [Get-AzNetworkManagerGroup](/powershell/module/az.network/get-aznetworkmanagergroup).

    ```azurepowershell-interactive
    $ng = @{
        Name = 'myNetworkGroup'
        ResoureceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
    }
    $networkgroup = Get-AzNetworkManagerGroup @ng   
    ```

1. Créez un élément de groupe de connectivité pour ajouter un groupe de réseaux à avec [New-AzNetworkManagerSecurityGroupItem](/powershell/module/az.network/new-aznetworkmanagersecuritygroupitem).

    ```azurepowershell-interactive
    $gi = @{
        NetworkGroupId = '$networkgroup.Id'
    }
    $groupItem = New-AzNetworkManagerSecurityGroupItem @gi
    ```

1. Créez un groupe de configurations et ajoutez l’élément de groupe à partir de l’étape précédente.

    ```azurepowershell-interactive
    [System.Collections.Generic.List[Microsoft.Azure.Commands.Network.Models.PSNetworkManagerSecurityGroupItem]]$configGroup = @()  
    $configGroup.Add($groupItem) 
    ```

1. Créez une collection de règles d’administration de la sécurité avec [New-AzNetworkManagerSecurityAdminRuleCollection](/powershell/module/az.network/new-aznetworkmanagersecurityadminrulecollection).

    ```azurepowershell-interactive
    $collection = @{
        Name = 'myRuleCollection'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManager = 'myAVNM'
        ConfigName = 'SecurityConfig'
        AppliesToGroup = $configGroup
    }
    $rulecollection = New-AzNetworkManagerSecurityAdminRuleCollection @collection
    ```

1. Définissez les variables pour les préfixes d’adresse source et de destination et les ports avec [New-AzNetworkManagerAddressPrefixItem](/powershell/module/az.network/new-aznetworkmanageraddressprefixitem).

    ```azurepowershell-interactive
    $sourceip = @{
        AddressPrefix = 'Internet'
        AddressPrefixType = 'ServiceTag'
    }
    $sourceprefix = New-AzNetworkManagerAddressPrefixItem @sourceip

    $destinationip = @{
        AddressPrefix = '10.0.0.0/24'
        AddressPrefixType = 'IPPrefix'
    }
    $destinationprefix = New-AzNetworkManagerAddressPrefixItem @destinationip

    [System.Collections.Generic.List[string]]$sourcePortList = @() 
    $sourcePortList.Add("65500”) 

    [System.Collections.Generic.List[string]]$destinationPortList = @() 
    $destinationPortList.Add("80”)
    $destinationPortList.Add("443”)
    ```

1. Créez une règle de sécurité avec [New-AzNetworkManagerSecurityAdminRule](/powershell/module/az.network/new-aznetworkmanagersecurityadminrule).

    ```azurepowershell-interactive
    $rule = @{
        Name = 'Block_HTTP_HTTPS'
        ResourceGroupName = 'myAVNMResourceGroup'
        NetworkManagerName = 'myAVNM'
        SecurityAdminConfigurationName = 'SecurityConfig'
        RuleCollectionName = 'myRuleCollection'
        Protocol = 'TCP'
        Access = 'Deny'
        Priority = '100'
        Direction = 'Outbound'
        Source = $sourceprefix
        SourcePortRange = $sourcePortList
        Destination = $destinationprefix
        DestinationPortRange = $destinationPortList
    }
    $securityrule = New-AzNetworkManagerSecurityAdminRule @rule
    ```

## <a name="commit-deployment"></a>Valider le déploiement

Validez la configuration de sécurité dans les régions cibles avec [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()  
$configIds.add($securityconfig.id) 
[System.Collections.Generic.List[string]]regions = @()   
$regions.Add("westus")     

$deployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Security'
}
Deploy-AzNetworkManagerCommit @deployment 
```

## <a name="delete-security-configuration"></a>Supprimer une configuration de la sécurité

Si vous n’avez plus besoin de la configuration de sécurité, vous devez vous assurer que les critères suivants sont vrais avant de pouvoir supprimer la configuration de sécurité elle-même :

* Il n’existe aucun déploiement de configurations dans une région.
* Supprimez toutes les règles de sécurité dans une collection de règles associée à la configuration de sécurité.

### <a name="remove-security-configuration-deployment"></a>Supprimer le déploiement de la configuration de sécurité

Supprimez le déploiement de la sécurité en déployant une configuration vide avec [Deploy-AzNetworkManagerCommit](/powershell/module/az.network/deploy-aznetworkmanagercommit).

```azurepowershell-interactive
[System.Collections.Generic.List[string]]$configIds = @()
[System.Collections.Generic.List[string]]$regions = @()   
$regions.Add("westus")     
$removedeployment = @{
    Name = 'myAVNM'
    ResourceGroupName = 'myAVNMResourceGroup'
    ConfigurationId = $configIds
    TargetLocation = $regions
    CommitType = 'Security'
}
Deploy-AzNetworkManagerCommit @removedeployment
```

### <a name="remove-security-rules"></a>Supprimer des règles de sécurité

Supprimez une règle de sécurité avec [Remove-AzNetworkManagerSecurityAdminRule](/powershell/module/az.network/remove-aznetworkmanagersecurityadminrule).

```azurepowershell-interactive
$removerule = @{
    Name = 'Block80'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
    SecurityAdminConfigurationName = 'SecurityConfig'
}
Remove-AzNetworkManagerSecurityAdminRule @removerule
```

### <a name="remove-security-rule-collections"></a>Supprimer des collections de règles de sécurité

```azurepowershell-interactive
$removecollection = @{
    Name = 'myRuleCollection'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
    SecurityAdminConfigurationName = 'SecuritConfig'
}
Remove-AzNetworkManagerSecurityAdminRuleCollection @removecollection
```

### <a name="delete-configuration"></a>Supprimer une configuration

Supprimez la configuration de sécurité avec [New-AzNetworkManagerSecurityAdminConfiguration](/powershell/module/az.network/remove-aznetworkmanagersecurityadminconfiguration).

```azurepowershell-interactive
$removeconfig = @{
    Name = 'SecurityConfig'
    ResourceGroupName = 'myAVNMResourceGroup'
    NetworkManagerName = 'myAVNM'
}
Remove-AzNetworkManagerSecurityAdminConfiguration @removeconfig
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [Règles d’administration de la sécurité](concept-security-admins.md).
