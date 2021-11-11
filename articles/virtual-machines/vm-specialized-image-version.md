---
title: Créer une machine virtuelle à partir d’une version image spécialisée
description: Créez une machine virtuelle à l’aide d’une version image spécialisée dans Azure Compute Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b500a7e02167f5373eb2dbdeb4bdb708c5b69966
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474493"
---
# <a name="create-a-vm-using-a-specialized-image-version"></a>Créer une machine virtuelle à l’aide d’une version image spécialisée 

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows 

Créez une machine virtuelle à partir d’une [version d’image spécialisée](./shared-image-galleries.md#generalized-and-specialized-images) stockée dans une galerie Azure Compute Gallery (anciennement Shared Image Gallery). Si vous souhaitez créer une machine virtuelle à l’aide d’une version d’image généralisée, consultez [Créer une machine virtuelle à partir d’une version d’image généralisée](vm-generalized-image-version.md).

> [!IMPORTANT]
> 
> Lorsque vous créez une nouvelle machine virtuelle à partir d’une image spécialisée, la nouvelle machine conserve le nom d’ordinateur de la machine virtuelle d’origine. D’autres informations spécifiques à l’ordinateur (par exemple le CMID) sont également conservées. Dans certains cas, ces informations en double peuvent provoquer des problèmes. Quand vous copiez une machine virtuelle, vous devez connaître les types d’informations spécifiques à l’ordinateur dont vos applications dépendent.  

Remplacez les noms de ressources en fonction des besoins dans ces exemples. 

### <a name="portal"></a>[Portail](#tab/portal)

Vous pouvez maintenant créer une ou plusieurs machines virtuelles. Cet exemple crée une machine virtuelle nommée *myVM*, dans le groupe *myResourceGroup* du centre de données *USA Est*.

1. Accédez à la définition de votre image. Vous pouvez utiliser le filtre de ressources pour afficher toutes les définitions d’images disponibles.
1. Dans la page de définition de votre image, sélectionnez **Créer une machine virtuelle** dans le menu situé en haut.
1. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez *myResourceGroup* comme nom.
1. Dans **Nom de la machine virtuelle**, tapez *myVM*.
1. Pour **Région**, sélectionnez *USA Est*.
1. Pour **Options de disponibilité**, conservez la valeur par défaut *Aucune redondance d’infrastructure nécessaire*.
1. La valeur sous **Image** est automatiquement renseignée avec la version d’image `latest` si vous avez démarré à partir de la page de la définition d’image.
1. Pour **Taille**, choisissez une taille de machine virtuelle dans la liste des tailles disponibles, puis **Sélectionner**.
1. Sous **Compte administrateur**, le nom d’utilisateur est grisé, car le nom d’utilisateur et les informations d’identification de la machine virtuelle source sont utilisés.
1. Si vous souhaitez autoriser l’accès à distance à la machine virtuelle, sous **Ports d’entrée publics**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **SSH (22)** ou **RDP (3389)** dans la liste déroulante. Si vous ne souhaitez pas autoriser l’accès à distance à la machine virtuelle, conservez la sélection **Aucun** pour **Ports d’entrée publics**.
1. Lorsque vous avez terminé, sélectionnez le bouton **Vérifier + créer** en bas de la page.
1. Une fois la machine virtuelle validée, sélectionnez **Créer** en bas de la page pour démarrer le déploiement.


### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Répertoriez les définitions d’images d’une galerie avec la commande [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) pour voir le nom et l’ID des définitions.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Créez la machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create), en spécifiant le paramètre --specialized pour indiquer que l’image est une image spécialisée. 

Utilisez l’ID de définition d’image pour `--image` afin de créer la machine virtuelle à partir de la version la plus récente de l’image disponible. Vous pouvez également créer la machine virtuelle à partir d’une version spécifique en fournissant l’ID de version de l’image pour `--image`. 

Dans cet exemple, nous créons une machine virtuelle à partir de la dernière version de l’image *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Une fois que vous disposez d'une version d'image spécialisée, vous pouvez créer une ou plusieurs nouvelles machines virtuelles à l'aide de la cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm). 

Dans cet exemple, nous utilisons l’ID de définition d’image pour nous assurer que la nouvelle machine virtuelle utilise la version la plus récente d’une image. Vous pouvez également utiliser une version spécifique en utilisant l’ID de version de l’image pour `Set-AzVMSourceImage -Id`. Par exemple, pour utiliser la version de l’image *1.0.0* tapez : `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`. 

Souvenez-vous que l’utilisation d’une version d’image spécifique signifie que l’automatisation peut échouer si cette version d’image spécifique n’est pas disponible car elle a été effacée ou supprimée de la région. Nous vous recommandons d’utiliser l’ID de définition d’image pour créer votre machine virtuelle, sauf si une version d’image spécifique est requise.

Remplacez les noms de ressources en fonction des besoins dans cet exemple. 


```azurepowershell-interactive

# Create some variables for the new VM.

$resourceGroup = "mySIGSpecializedRG"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition


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

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig

```

---

**Étapes suivantes**

Vous pouvez également créer une ressource Azure Compute Gallery en utilisant des modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-create/)
- [Créer une définition d’image dans une galerie Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Créer une version d’image dans une galerie Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
