---
title: Répertorier, mettre à jour et supprimer des ressources d’image
description: Répertoriez, mettez à jour et supprimez des ressources d’image dans votre galerie Azure Compute Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2c01020c266a357329c38023c9e9c34c59424088
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448764"
---
# <a name="list-update-and-delete-image-resources"></a>Répertorier, mettre à jour et supprimer des ressources d’image 

Vous pouvez gérer votre galerie Azure Compute Gallery (anciennement appelée Shared Image Gallery) à l’aide de l’interface Azure CLI ou d’Azure PowerShell.

## <a name="list-information"></a>Lister les informations

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Obtenez l’emplacement, l’état et d’autres informations sur les galeries d’images disponibles à l’aide de [az sig list](/cli/azure/sig#az_sig_list).

```azurecli-interactive 
az sig list -o table
```

Listez les définitions d’image dans une galerie, y compris les informations sur le type de système d’exploitation et l’état, avec [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Répertoriez les versions d’image dans une galerie à l’aide de [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Obtenez l’ID d’une version d’image avec [az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Répertorier toutes les galeries par leur nom.

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

Répertorier toutes les définitions d’image par leur nom.

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

Répertorier toutes les versions d’image par leur nom.

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

Supprimer une version d’image. Cet exemple supprime la version de l’image nommée *1.0.0*.

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```


---


## <a name="update-resources"></a>Mettre à jour les ressources

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)
Il existe certaines limitations concernant les éléments qui peuvent être mis à jour. Les éléments suivants peuvent être mis à jour : 

Azure Compute Gallery :
- Description

Définition d’image :
- Processeurs virtuels recommandés
- Mémoire recommandée
- Description
- Date de fin de vie

Version d’image :
- Nombre de réplicas régionaux
- Régions cibles
- Exclusion du dernier
- Date de fin de vie

Si vous prévoyez d’ajouter des régions de réplica, ne supprimez pas l’image managée source. En effet, elle est nécessaire pour la réplication de la version de l’image vers d’autres régions. 

Mettez à jour la description d’une galerie à l’aide de ([az sig update](/cli/azure/sig#az_sig_update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Mettez à jour la description d’une définition d’image à l’aide de [az sig image-definition update](/cli/azure/sig/image-definition#az_sig_image_definition_update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Mettez à jour une version de l’image pour ajouter une région à répliquer à l’aide de [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update). Cette modification prendra un certain temps, puisque l’image est répliquée vers la nouvelle région.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

Cet exemple montre comment utiliser [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) pour empêcher l’utilisation de cette version de l’image comme image *la plus récente*.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

Cet exemple montre comment utiliser [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) pour que cette version de l’image soit considérée comme l’image *la plus récente*.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


Il existe certaines limitations concernant les éléments qui peuvent être mis à jour. Les éléments suivants peuvent être mis à jour : 

Azure Compute Gallery :
- Description

Définition d’image :
- Processeurs virtuels recommandés
- Mémoire recommandée
- Description
- Date de fin de vie

Version d’image :
- Nombre de réplicas régionaux
- Régions cibles
- Exclusion du dernier
- Date de fin de vie

Si vous prévoyez d’ajouter des régions de réplica, ne supprimez pas l’image managée source. En effet, elle est nécessaire pour la réplication de la version de l’image vers d’autres régions. 

Mettez à jour la description d’une galerie à l’aide du paramètre [Update-AzGallery](/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Cet exemple montre comment utiliser [Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) pour mettre à jour la date de fin de vie de notre définition d’image.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Cet exemple montre comment utiliser le paramètre [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) pour empêcher que cette version de l’image soit utilisée en tant qu’image *la plus récente*.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

Cet exemple montre comment utiliser [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) pour que cette version de l’image soit considérée comme l’image *la plus récente*.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

---




## <a name="delete-resources"></a>Supprimer des ressources

Vous devez supprimer les ressources dans l’ordre inverse, en supprimant tout d’abord la version de l’image. Après avoir supprimé toutes les versions de l’image, vous pouvez supprimer la définition de l’image. Après avoir supprimé toutes les définitions de l’image, vous pouvez supprimer la galerie. 

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Supprimez une version de l’image avec [az sig image-version delete](/cli/azure/sig/image-version#az_sig_image_version_delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Supprimez une définition de l’image avec [az sig image-definition delete](/cli/azure/sig/image-definition#az_sig_image_definition_delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Supprimez une galerie à l’aide de [az sig delete](/cli/azure/sig#az_sig_delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

---

## <a name="next-steps"></a>Étapes suivantes

[Générateur d’image Azure (préversion)](./image-builder-overview.md) permet d’automatiser la création d’une version de l’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une image existante](./linux/image-builder-gallery-update-image-version.md).