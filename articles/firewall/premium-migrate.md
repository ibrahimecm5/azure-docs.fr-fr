---
title: Migrer vers le Pare-feu Azure Premium
description: Découvrez comment migrer du Pare-feu Azure Standard vers le Pare-feu Azure Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 11/02/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 574c1f7f84ae34b1a1158b61206135de79ee73af
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548061"
---
# <a name="migrate-to-azure-firewall-premium"></a>Migrer vers le Pare-feu Azure Premium

Vous pouvez migrer du Pare-feu Azure Standard vers le Pare-feu Azure Premium pour tirer parti des nouvelles fonctionnalités Premium. Pour plus d’informations sur les fonctionnalités du Pare-feu Azure Premium, consultez [Fonctionnalités du Pare-feu Azure Premium](premium-features.md).

Les exemples suivants montrent comment :
- Migrer une stratégie standard existante à l’aide d’Azure PowerShell
- Migrez un pare-feu standard existant (avec les règles classiques) vers le Pare-feu Azure Premium avec une stratégie Premium.

Si vous utilisez Terraform pour déployer le Pare-feu Azure, vous pouvez utiliser Terraform pour migrer vers le Pare-feu Azure Premium. Pour plus d'informations, consultez [Migrer le Pare-feu Azure Standard vers la version Premium à l'aide de Terraform](/azure/developer/terraform/firewall-upgrade-premium?toc=/azure/firewall/toc.json&bc=/azure/firewall/breadcrumb/toc.json).

## <a name="performance-considerations"></a>Considérations relatives aux performances

Les performances sont un facteur à prendre en compte lors de la migration à partir de la référence SKU standard. L’inspection IDPS et TLS sont des opérations gourmandes en ressources de calcul. La référence SKU Premium utilise une référence SKU de machine virtuelle plus puissante qui s’adapte à un débit maximal de 30 Gbits/s comparables à la référence SKU standard. Le débit de 30 Gbits/s est pris en charge lorsqu’il est configuré avec IDPS en mode alerte. L’utilisation d’IDPS en mode de refus et de l’inspection TLS augmente la consommation du processeur. Une dégradation du débit maximal peut se produire. 

Le débit du pare-feu peut être inférieur à 30 Gbits/s quand une ou plusieurs signatures sont définies sur **Alerter et refuser** ou l’**inspection TLS** est activée pour des règles d’application. Microsoft recommande aux clients d’effectuer des tests à grande échelle lors de leur déploiement Azure pour s’assurer que les performances du service de pare-feu répondent aux attentes.

## <a name="downtime"></a>Temps d’arrêt

Effectuez la migration de votre pare-feu pendant une période de maintenance planifiée, car il y aura un certain temps d’arrêt pendant la migration.

## <a name="migrate-classic-rules-to-standard-policy"></a>Migrer les règles classiques vers une stratégie standard

Pendant votre processus de migration, vous devrez peut-être migrer vos règles de pare-feu classiques vers une stratégie standard. Vous pouvez réaliser cette opération à l’aide du Portail Azure :

1. Dans le portail Azure, sélectionnez votre pare-feu standard. Sur la page **Vue d’ensemble**, sélectionnez **Migrer vers une stratégie de pare-feu**.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migrer vers une stratégie de pare-feu":::

1. Sur la page **Migrer vers une stratégie de pare-feu**, sélectionnez **Vérifier + créer**.
1. Sélectionnez **Create** (Créer).

   Le déploiement prend quelques minutes.

Vous pouvez également créer des stratégies en migrant des règles classiques existantes du Pare-feu Azure àl’aide d’Azure PowerShell. Pour plus d’informations, consultez [Migrer des configurations Pare-feu Azure vers une stratégie Pare-feu Azure à l’aide de PowerShell](../firewall-manager/migrate-to-policy.md).

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Migrer une stratégie existante à l’aide d’Azure PowerShell

`Transform-Policy.ps1` est un script Azure PowerShell qui crée une stratégie Premium à partir d’une stratégie standard existante.

En fonction d’un ID de stratégie de pare-feu standard, le script le transforme en stratégie de Pare-feu Azure Premium. Le script se connecte d’abord à votre compte Azure, extrait la stratégie, transforme/ajoute différents paramètres, puis charge une nouvelle stratégie Premium. La nouvelle stratégie Premium est appelée `<previous_policy_name>_premium`. Dans le cas d'une transformation de stratégie enfant, le lien vers la stratégie parente est conservé.

Exemple d’utilisation :

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> Le script ne migre pas les paramètres Threat Intelligence. Vous devez noter ces paramètres avant de continuer et les migrer manuellement.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy.Id 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -MinimumVersion 4.5 -ErrorAction SilentlyContinue
    if ($null -eq $networkModule) {
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    $resourceModule = Get-InstalledModule -Name "Az.Resources" -MinimumVersion 4.2 -ErrorAction SilentlyContinue
    if ($null -eq $resourceModule) {
        Write-Host "Please install Az.Resources module version 4.2.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
    Import-Module Az.Resources -MinimumVersion 4.2.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy

```

## <a name="migrate-azure-firewall-using-stopstart"></a>Migrer le pare-feu Azure à l’aide d’une opération d’arrêt/démarrage

Si vous utilisez la référence SKU standard du Pare-feu Azure avec la stratégie de pare-feu, vous pouvez utiliser la méthode Allouer/Libérer pour migrer votre référence SKU de pare-feu vers Premium. Cette approche de migration est prise en charge sur les pare-feu de hub sécurisé et de hub de réseau virtuel. Lorsque vous migrez un déploiement de hub sécurisé, l’adresse IP publique du pare-feu est conservée.

La version minimale requise de Azure PowerShell est 6.5.0. Pour plus d’informations, consultez [Az 6.5.0](https://www.powershellgallery.com/packages/Az/6.5.0).

 
### <a name="migrate-a-vnet-hub-firewall"></a>Migrer un pare-feu de hub de réseau virtuel

- Libérer le pare-feu standard 

   ```azurepowershell
   $azfw = Get-AzFirewall -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Deallocate()
   Set-AzFirewall -AzureFirewall $azfw
   ```


- Allouer le pare-feu Premium

   ```azurepowershell
   $azfw = Get-AzFirewall -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Sku.Tier="Premium"
   $azfw.Allocate($vnet,$pip, $mgmtpip)
   Set-AzFirewall -AzureFirewall $azfw
   ```

### <a name="migrate-a-secure-hub-firewall"></a>Migrer un pare-feu de hub sécurisé


- Libérer le pare-feu standard

   ```azurepowershell
   $azfw = Get-AzFirewall -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Deallocate()
   Set-AzFirewall -AzureFirewall $azfw
   ```

- Allouer le pare-feu Premium

   ```azurepowershell
   $azfw = Get-AzFirewall -Name -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Sku.Tier="Premium"
   $azfw.Allocate($hub.id)
   Set-AzFirewall -AzureFirewall $azfw
   ```

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les fonctionnalités du Pare-feu Azure Premium](premium-features.md)
