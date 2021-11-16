---
title: Créer une galerie Azure Compute Gallery pour le partage de ressources
description: Découvrez comment créer une galerie Azure Compute Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/05/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 924cce1b23cf86a6e29ef4fd28f862bf2fa12363
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459688"
---
# <a name="create-a-gallery-for-storing-and-sharing-resources"></a>Créer une galerie pour le stockage et le partage de ressources


Une [galerie Azure Compute Gallery](./shared-image-galleries.md) (anciennement Shared Image Gallery) simplifie le partage de ressources telles que des images et des packages d’application dans votre organisation.  

La galerie Azure Compute Gallery vous permet de partager des images de machine virtuelle personnalisées et des packages d’application avec d’autres personnes de votre organisation, dans une même région ou des régions différentes, au sein d’un locataire AAD. Choisissez ce que vous souhaitez partager, les régions dans lesquelles ce doit être disponible et les personnes avec lesquelles vous voulez partager. Vous pouvez créer plusieurs galeries afin de pouvoir regrouper logiquement des ressources. 

La galerie est une ressource de niveau supérieur qui fournit le contrôle d'accès en fonction du rôle Azure (Azure RBAC) complet. 

## <a name="create-a-gallery"></a>Créer une galerie

Les caractères autorisés pour le nom de galerie sont les lettres majuscules ou minuscules, les chiffres et les points. Le nom de galerie ne peut pas contenir de tirets. Les noms de galerie doivent être uniques dans votre abonnement. 

Choisissez une option ci-dessous pour créer votre galerie :

### <a name="portal"></a>[Portail](#tab/portal)

L’exemple suivant crée une galerie nommée *myGallery* dans le groupe de ressources *myGalleryRG*.

1. Connectez-vous au portail Azure sur https://portal.azure.com.
1. Utilisez le type **Azure Compute Gallery** dans la zone de recherche, puis sélectionnez **Azure Compute Gallery** dans les résultats.
1. Dans la page **Azure Compute Gallery**, cliquez sur **Ajouter**.
1. Dans la page **Créer une galerie Azure Compute Gallery**, sélectionnez l’abonnement approprié.
1. Dans **Groupe de ressources**, sélectionnez **Créer** et saisissez *myResourceGroup* comme nom.
1. Dans **Nom**, saisissez *myGallery* comme nom de la galerie.
1. Laissez la valeur par défaut pour **Région**.
1. Vous pouvez saisir une brève description de la galerie, par exemple, *Ma galerie pour le test*. Cliquez ensuite sur **Vérifier + créer**.
1. Une fois la validation réussie, sélectionnez **Créer**.
1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Créez une galerie à l’aide de la commande [az sig create](/cli/azure/sig#az_sig_create). L’exemple suivant crée un groupe de ressources nommé *myGalleryRG* dans *USA Est* et une galerie nommée *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Créez une galerie d’images à l’aide de la commande [New-AzGallery](/powershell/module/az.compute/new-azgallery). L’exemple suivant crée une galerie nommée *myGallery* dans le groupe de ressources *myGalleryRG*.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Azure Compute Gallery for my organization' 
```


### <a name="rest"></a>[REST](#tab/rest)

Utilisez l'[API REST](/rest/api/resources/resource-groups/create-or-update) pour créer un groupe de ressources.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2021-04-01

{
  "location": "eastus"
}
```

Utilisez l'[API REST](/rest/api/compute/galleries/create-or-update) pour créer une galerie.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}?api-version=2019-12-01

{
  "properties": {
    "description": "Azure Compute Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>Étapes suivantes

- Créez une [définition d’image et une version d’image](image-version.md).

- [Créez une application de machine virtuelle](vm-applications-how-to.md) dans votre galerie.

- Vous pouvez également créer une galerie Azure Compute Gallery [créer une galerie Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-create/) à l’aide d’un modèle.

- Le [Générateur d’image Azure](./image-builder-overview.md) peut vous aider à automatiser la création de version d’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version d’image à partir d’une version d’image existante](./windows/image-builder-gallery-update-image-version.md). 
