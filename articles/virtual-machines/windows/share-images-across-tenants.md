---
title: Partager des images de la bibliothèque entre tenants dans Azure
description: Découvrez comment partager des images de machine virtuelle entre des locataires Azure à l’aide des galeries Azure Compute et de PowerShell.
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/15/2019
ms.reviewer: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 029e926a7b3b2d932189d0c2b9aa046fabc30a28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444556"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-powershell"></a>Partager des images de machine virtuelle des galeries entre des locataires Azure à l’aide de PowerShell

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les galeries Azure Compute vous permettent de partager des images à l'aide du contrôle d'accès en fonction du rôle (Azure RBAC). Vous pouvez utiliser Azure RBAC pour partager des images au sein de votre locataire, voire avec d’autres personnes en dehors. Pour plus d’informations sur cette option de partage simple, voir [Partager la galerie](../share-gallery.md).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> Vous ne pouvez pas utiliser le portail pour déployer une machine virtuelle à partir d’une image dans un autre locataire Azure. Pour créer une machine virtuelle à partir d’une image partagée entre des locataires, vous devez utiliser l’interface [Azure CLI](../linux/share-images-across-tenants.md) ou PowerShell.

## <a name="create-a-vm-using-powershell"></a>Créer une machine virtuelle avec PowerShell

Ouvrez une session sur les deux clients avec l’ID d’application, le secret et l’ID de locataire. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Créez la machine virtuelle dans le groupe de ressources disposant de l’autorisation d’inscription d’applications. Remplacez les informations de cet exemple par les vôtres.



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Set a variable for the image version in Tenant 1 using the full image ID of the image version
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Networking pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using the $image variable to specify the image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $image | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="next-steps"></a>Étapes suivantes

Créez des [ressources Azure Compute Gallery](../image-version.md).
