---
title: Spécifier des informations sur le plan d’achat de la Place de marché à l’aide d’Azure PowerShell
description: Découvrez comment spécifier les détails du plan d’achat de la Place de marché Azure lors de la création d’images dans Azure Compute Gallery (anciennement appelée Shared Image Gallery).
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d50aa76f80205df1d34215a020691c51ca58d4b0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474607"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Fournir des informations sur le plan d’achat de la Place de marché Azure lors de la création d’images

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Si vous créez une image dans une galerie partagée, à l’aide d’une source qui a été créée à l’origine à partir d’une image de la Place de marché Azure, vous devrez peut-être effectuer le suivi des informations sur le plan d’achat. Cet article montre comment rechercher des informations sur le plan d’achat pour une machine virtuelle, puis utiliser ces informations lors de la création d’une définition d’image. Nous abordons également l’utilisation des informations de la définition de l’image pour simplifier la fourniture des informations sur le plan d’achat lors de la création d’une machine virtuelle pour une image.

Pour plus d’informations sur la recherche et l’utilisation d’images de la Place de marché, consultez [Rechercher et utiliser des images de la Place de marché Azure](./windows/cli-ps-findimage.md).


## <a name="get-the-source-vm-information"></a>Obtenir les informations sur la machine virtuelle source
Si vous avez toujours la machine virtuelle d’origine, vous pouvez obtenir le nom du plan, l’éditeur et les informations sur le produit à l’aide de Get-AzVM. Cet exemple obtient une machine virtuelle nommée *myVM* dans le groupe de ressources *myResourceGroup*, puis affiche les informations du plan d’achat pour la machine virtuelle.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>Créer la définition d’image

Obtenez la galerie que vous souhaitez utiliser pour stocker l’image. Vous pouvez commencer par lister toutes les galeries.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Ensuite, créez des variables pour la galerie que vous souhaitez utiliser. Dans cet exemple, nous créons une variable nommée `$gallery` pour *myGallery* dans le groupe de ressources *myGalleryRG*.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

Créez la définition de l’image en utilisant les paramètres `-PurchasePlanPublisher`, `-PurchasePlanProduct` et `-PurchasePlanName`.

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Ensuite, créez votre [version d’image](image-version.md) à l’aide de l’applet de commande [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion).  


## <a name="create-the-vm"></a>Création de la machine virtuelle

Quand vous procédez à la création d’une machine virtuelle à partir de l’image, vous pouvez utiliser les informations de la définition de l’image pour transmettre les informations du serveur de publication à l’aide de la commande [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan).


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la recherche et l’utilisation d’images de la Place de marché, consultez [Rechercher et utiliser des images de la Place de marché Azure](./windows/cli-ps-findimage.md).
