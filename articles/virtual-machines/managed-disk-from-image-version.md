---
title: Exporter une version d’image vers un disque managé
description: Exporter une version d’image depuis une galerie Azure Compute Gallery vers un disque managé.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7984cd086864d069363fa65ab7dbaecf4b62165f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448858"
---
# <a name="export-an-image-version-to-a-managed-disk"></a>Exporter une version d’image vers un disque managé

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Vous pouvez exporter le système d’exploitation ou un disque de données unique à partir d’une version d’image en tant que disque managé depuis une version d’image stockée dans Azure Compute Gallery (anciennement Shared Image Gallery).


## <a name="cli"></a>Interface de ligne de commande

Listez les versions d’image dans une galerie avec [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list). Dans cet exemple, nous recherchons toutes les versions d’image qui font partie de la définition d’image *myImageDefinition* dans la galerie *myGallery*.

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Affectez à la variable `source` l’ID de la version de l’image, puis utilisez [az disk create](/cli/azure/disk#az_disk_create) pour créer le disque géré. 

Dans cet exemple, nous exportons le disque du système d’exploitation de la version d’image pour créer un disque géré nommé *myManagedOSDisk*, dans la région *EastUS*, dans un groupe de ressources nommé *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Si vous souhaitez exporter un disque de données à partir de la version d’image, ajoutez `--gallery-image-reference-lun` pour spécifier l’emplacement LUN du disque de données à exporter. 

Dans cet exemple, nous exportons le disque de données situé sur le LUN 0 de la version d’image pour créer un disque géré nommé *myManagedDataDisk*, dans la région *EastUS*, dans un groupe de ressources nommé *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

Listez les versions d’image dans une galerie avec [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

Une fois que vous disposez de toutes les informations dont vous avez besoin, vous pouvez utiliser [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) pour obtenir la version de l’image source que vous souhaitez utiliser et l’affecter à une variable. Dans cet exemple, nous obtenons la version d’image `1.0.0`, de la définition `myImageDefinition`, dans la galerie source `myGallery`, dans le groupe de ressources `myResourceGroup`.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Après avoir défini la variable `source` sur l’ID de la version d’image, utilisez [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) pour créer une configuration de disque, et [New-AzDisk](/powershell/module/az.compute/new-azdisk) pour créer le disque. 

Dans cet exemple, nous exportons le disque du système d’exploitation de la version d’image pour créer un disque géré nommé *myManagedOSDisk*, dans la région *EastUS*, dans un groupe de ressources nommé *myResourceGroup*. 

Créez une configuration de disque.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Créez le disque.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Si vous souhaitez exporter un disque de données sur la version d’image, ajoutez un LUN à la configuration de disque pour spécifier l’emplacement LUN du disque de données à exporter. 

Dans cet exemple, nous exportons le disque de données situé sur le LUN 0 de la version d’image pour créer un disque géré nommé *myManagedDataDisk*, dans la région *EastUS*, dans un groupe de ressources nommé *myResourceGroup*. 

Créez une configuration de disque.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Créez le disque.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer une [version d’image](image-version.md) à partir d’un disque managé.


