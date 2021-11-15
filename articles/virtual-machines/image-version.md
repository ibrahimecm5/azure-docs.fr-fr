---
title: Créer une définition d’image et une version d’image
description: Découvrez comment créer une image dans une galerie Azure Compute Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: e52457a67e2c653d1fa4e191ee568b35cb7e365f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471513"
---
# <a name="create-an-image-definition-and-an-image-version"></a>Créer une définition d’image et une version d’image 

Une galerie [Azure Compute Gallery](shared-image-galleries.md) (anciennement Shared Image Gallery) simplifie considérablement le partage d’images personnalisées dans votre organisation. Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Les images personnalisées peuvent être utilisées pour amorcer des tâches de déploiement comme le préchargement des applications, les configurations d’application et d’autres configurations de système d’exploitation. 

La galerie Azure Compute Gallery vous permet de partager vos images de machine virtuelle personnalisées avec d’autres personnes de votre organisation, dans la même région ou des régions différentes, au sein d’un locataire AAD. Choisissez les images à partager, les régions dans lesquelles vous souhaitez les rendre disponibles et les personnes avec lesquelles vous voulez les partager. Vous pouvez créer plusieurs galeries afin de regrouper logiquement les images. 

La fonctionnalité Azure Compute Gallery présente plusieurs types de ressources :

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>Avant de commencer

Pour suivre cet article, vous devez disposer d’une galerie Azure Compute Gallery existante et d’une source pour votre image disponible dans Azure. Les sources d’images peuvent être :
- Une machine virtuelle dans votre abonnement. Vous pouvez capturer une image à partir de machines virtuelles [spécialisées et généralisées](shared-image-galleries.md#generalized-and-specialized-images). 
- Une image managée.
- Des disques de système d’exploitation et de données managés.
- Des disques de système d’exploitation et de données en tant que disques durs virtuels dans un compte de stockage.
- D’autres versions de l’image, soit dans la même galerie, soit dans une autre galerie du même abonnement.

Si l’image contient des disques de données, la taille du disque de données ne peut pas être supérieure à 1 To.

Les noms de définition d’image peuvent contenir des lettres majuscules ou minuscules, des chiffres, des tirets et des points. Pour plus d’informations sur les valeurs que vous pouvez spécifier pour une définition d’image, consultez [Définitions d’image](shared-image-galleries.md#image-definitions).

Les caractères autorisés pour la version d’image sont les chiffres et les points. Les nombres doivent être un entier 32 bits. Format: *MajorVersion*.*MinorVersion*.*Patch*.

Dans cet article, remplacez les noms de ressources si nécessaire.

## <a name="create-an-image"></a>Créer une image 

Choisissez une option ci-dessous pour créer votre définition d’image et votre version d’image :

### <a name="portal"></a>[Portail](#tab/portal)

Pour créer une image à partir d’une machine virtuelle dans le portail, consultez [Capturer une image d’une machine virtuelle](capture-image-portal.md). 

Pour créer une image à l’aide d’une source autre qu’une machine virtuelle, procédez comme suit.

1. Accédez au [portail Azure](https://portal.azure.com), puis recherchez et sélectionnez **Azure Compute Gallery**.
1. Sélectionnez la galerie à utiliser dans la liste.
1. Sur la page de votre nouvelle galerie d’images, sélectionnez **Ajouter** en haut de la page, puis **Définition d’image de machine virtuelle** dans le menu déroulant. 
1. Sur la page **Ajouter une nouvelle définition d’image à Azure Compute Gallery**, dans l’onglet **Informations de base**, sélectionnez une **Région**. 
1. Pour **Nom de la définition d’image**, tapez un nom comme *myImageDefinition*.
1. Pour **Système d’exploitation**, sélectionnez l’option appropriée en fonction de votre source.  
1. Pour **Génération de machine virtuelle**, sélectionnez l’option en fonction de votre source. Dans la plupart des cas, ce sera *Gen 1*. Pour plus d'informations, voir [Prise en charge des machines virtuelles de 2e génération](generation-2.md).
1. Pour **État du système d’exploitation**, sélectionnez l’option appropriée en fonction de votre source. Pour plus d’informations, voir [Machines virtuelles généralisées et spécialisées](shared-image-galleries.md#generalized-and-specialized-images).
1. Pour **Éditeur**, entrez un nom unique, par exemple *myPublisher*. 
1. Pour **Offre**, saisissez un nom unique, par exemple *myOffer*.
1. Pour **SKU**, saisissez un nom unique, par exemple *mySKU*.
1. En bas de la page, sélectionnez **Revoir + Créer**.
1. Une fois la définition de l’image validée, sélectionnez **Créer**.
1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.
1. Dans la page de votre définition d’image, dans l’onglet **Prise en main**, sélectionnez **Créer une version**.
1. Dans **Région**, sélectionnez la région où créer l’image. Dans certains cas, la source doit se trouver dans la même région que celle où l’image est créée. Si votre source ne figure pas dans les listes déroulantes ultérieures, essayez de modifier la région de l’image. Vous pourrez toujours répliquer l’image dans d’autres régions par la suite.
1. Pour **Numéro de version**, tapez un nombre comme *1.0.0*. Le nom de version d’image doit respecter le format *version majeure*.*version mineure*.*correctif* en utilisant des nombres entiers. 
1. Dans **Source**, sélectionnez le type de fichier que vous utilisez pour votre source dans la liste déroulante. Consultez le tableau ci-dessous pour obtenir des détails spécifiques pour chaque type de source.

    | Source | Autres champs |
    |---|---|
    | Disques ou instantanés | - Pour **Disque de système d’exploitation**, sélectionnez le disque ou l’instantané dans la liste déroulante. <br> - Pour ajouter un disque de données, tapez le numéro de LUN, puis sélectionnez le disque de données dans la liste déroulante. |
    | Version d’image | - Sélectionnez la **galerie source** dans la liste déroulante. <br> - Sélectionnez la définition d’image appropriée dans la liste déroulante. <br>- Sélectionnez la version de l’image existante que vous souhaitez utiliser dans la liste déroulante. |
    | Image managée | Sélectionnez l’**image source** dans la liste déroulante. <br>L’image managée doit se trouver dans la même région que celle que vous avez choisie dans **Détails de l’instance**.
    | VHD dans un compte de stockage | Sélectionnez **Parcourir** pour choisir le compte de stockage du VHD. |

1. Dans **Exclure de la plus récente**, laissez la valeur par défaut *Non*, sauf si vous ne souhaitez pas que cette version soit utilisée lors de la création d’une machine virtuelle à l’aide de `latest` au lieu d’un numéro de version.
1. Pour **Date de fin de vie**, sélectionnez dans le calendrier une date à laquelle vous pensez que cette version devrait cesser d’être utilisée.
1. Dans l’onglet **Réplication**, sélectionnez le type de stockage dans la liste déroulante.
1. Définissez le **nombre de réplicas par défaut**. Vous pouvez le remplacer pour chaque région que vous ajoutez. 
1. Vous devez effectuer une réplication vers la région source, de sorte que le premier réplica de la liste sera dans la région où vous avez créé l’image. Vous pouvez ajouter d’autres réplicas en sélectionnant la région dans la liste déroulante et en ajustant le nombre de réplicas si nécessaire.
1. Quand vous avez terminé, sélectionnez **Vérifier + créer**. Azure validera la configuration.
1. Une fois la définition d’image validée, sélectionnez **Créer**.
1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

La réplication de l’image à l’ensemble des régions cibles peut prendre un certain temps.

Vous pouvez également capturer une machine virtuelle existante sous forme d’image à partir du portail. Pour plus d’informations, consultez [Créer une image d’une machine virtuelle dans le portail](capture-image-portal.md).

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Les définitions d’image créent un regroupement logique des images. Elles sont utilisées pour gérer les informations sur les versions d’image créées au sein de celles-ci.

Créez une définition d’image dans une galerie avec la commande [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create). Assurez-vous que le type de votre définition d’image est approprié. Si vous avez généralisé la machine virtuelle (à l’aide de Sysprep pour Windows ou de waagent -deprovision pour Linux), vous devez créer une définition d’image généralisée à l’aide de la commande `--os-state generalized`. Si vous souhaitez utiliser la machine virtuelle sans supprimer de comptes d’utilisateur, créez une définition d’image spécialisée à l’aide de la commande `--os-state specialized`.

Pour plus d’informations sur les paramètres que vous pouvez spécifier pour une définition d’image, consultez [Définitions d’image](shared-image-galleries.md#image-definitions).

Dans cet exemple, la définition d’image est nommée *myImageDefinition* et est destinée à une image de système d’exploitation Linux [spécialisée](shared-image-galleries.md#generalized-and-specialized-images). Pour créer une définition pour des images utilisant un système d’exploitation Windows, utilisez `--os-type Windows`. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!NOTE]
> Pour les définitions d’images qui contiennent des images provenant d’images de marketplaces tierces, les informations de plan doivent correspondre exactement aux informations de plan de l’image tierce. Incluez les informations de plan dans la définition de l’image en ajoutant `--plan-name`, `--plan-product` et `--plan-publisher` lors de la création de la définition de l’image.
>

**Créer la version de l’image**

Créez une version de l’image avec [az sig image version create](/cli/azure/sig/image-version#az_sig_image_version_create).  

La syntaxe de création de l’image change en fonction de la source que vous utilisez. Vous pouvez mélanger les types de sources, tant que vous n’avez qu’une seule source de système d’exploitation. Vous pouvez également avoir des sources différentes pour chaque disque de données.

| Source  | Jeu de paramètres |
|---|---|
| **Disque du système d’exploitation**| |
| Machine virtuelle utilisant l’ID de machine virtuelle| `--managed-image <Resource ID of the VM>` |
| Image managée ou une autre version de l’image | `--managed-image <Resource ID of the managed image or image version` |
| Instantané ou disque managé | `--os-snapshot <Resource ID of the snapshot or managed disk>` |
| VHD dans un compte de stockage | `--os-vhd-uri <URI> --os-vhd-storage-account <storage account name>`.  | 
| **Disque de données** |
| Instantané ou disque managé | `--data-snapshots <Resource ID of the snapshot or managed disk> --data-snapshot-luns <LUN number>` |
| VHD dans un compte de stockage | `--data-vhds-sa <storageaccountname> --data-vhds-uris <URI> --data-vhds-luns <LUN number>` |

Pour obtenir des exemples détaillés de la façon de spécifier différentes sources pour votre image, consultez les [exemples de la commande az sig image-version create](/cli/azure/sig/image-version#az_sig_image_version_create-examples).

Dans l’exemple ci-dessous, nous créons une image à partir d’une **machine virtuelle**. La version de notre image est *1.0.0* et nous allons créer 2 réplicas dans la région *USA Centre-Ouest*, 1 réplica dans la région *USA Centre Sud* et 1 réplica dans la région *USA Est 2* à l’aide du stockage redondant interzone. Les régions de réplication doivent inclure la région dans laquelle se trouve la machine virtuelle source.

Il est recommandé d’arrêter ou de désallouer la machine virtuelle avant de créer une image.

Remplacez la valeur `--managed-image` dans cet exemple par l’ID de votre machine virtuelle.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Vous devez attendre que la version d’image soit totalement intégrée et répliquée avant de pouvoir utiliser la même image managée pour créer une autre version d’image.
>
> Vous pouvez également stocker votre image dans le Stockage Premium, en ajoutant `--storage-account-type  premium_lrs`, ou dans le [stockage redondant interzone](../storage/common/storage-redundancy.md), en ajoutant `--storage-account-type  standard_zrs`, quand vous créez la version de l’image.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Les définitions d’image créent un regroupement logique des images. Lors de la définition de votre image, assurez-vous de disposer de toutes les informations correctes. Si vous avez généralisé la source de l’image (à l’aide de Sysprep pour Windows ou de waagent -deprovision pour Linux), vous devez créer une définition d’image à l’aide de `-OsState generalized`. Si vous n’avez pas généralisé la source, créez une définition d’image à l’aide de `-OsState specialized`.

Pour plus d’informations sur les valeurs que vous pouvez spécifier pour une définition d’image, consultez [Définitions d’image](./shared-image-galleries.md#image-definitions).

Créez la définition d’image à l’aide de [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). 

Dans cet exemple, la définition d’image se nomme *myImageDefinition* et est destinée à une machine virtuelle spécialisée exécutant Windows. Pour créer une définition pour des images à l’aide de Linux, utilisez `-OsType Linux`. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

> [!NOTE]
> Pour les définitions d’images qui contiennent des images provenant d’images tierces, les informations de plan doivent correspondre exactement aux informations de plan de l’image tierce. Incluez les informations de plan dans la définition de l’image en ajoutant `-PurchasePlanName`, `-PurchasePlanProduct` et `-PurchasePlanPublisher` lors de la création de la définition de l’image.
>

**Créer une version d’image**

Créez une version d’image à l’aide de [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

La syntaxe de création de l’image change en fonction de la source que vous utilisez. 

| Source  | Jeu de paramètres |
|---|---|
| **Disque du système d’exploitation**| |
| Machine virtuelle utilisant l’ID de machine virtuelle| `-SourceImageId <Resource ID of the VM>` |
| Image managée ou une autre version de l’image | `-SourceImageId <Resource ID of the managed image or image version` |
| Instantané ou disque managé | `-OSDiskImage <Resource ID of the snapshot or managed disk>` |
| **Disque de données** |
| Instantané ou disque managé | `-DataDiskImage @{Source = @{Id=<source_id>}; Lun=<LUN>; SizeInGB = <Size in GB>; HostCaching = <Caching> }` |


Dans l’exemple ci-dessous, nous créons une version d’image à partir d’une machine virtuelle. Il est recommandé d’arrêter\désallouer la machine virtuelle avant de créer une image à l’aide de la commande [Stop-AzVM](/powershell/module/az.compute/stop-azvm).

Dans cet exemple, la version d'image, *1.0.0*, elle est répliquée dans les deux centres de données *USA Centre-Ouest* et *USA Centre Sud*. Lors du choix des régions cibles pour la réplication, n’oubliez pas que vous devez également inclure la région *source* en tant que cible pour la réplication.


```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

La réplication de l'image dans toutes les régions cibles peut nécessiter un certain temps et nous avons donc créé une tâche pour en suivre la progression. Pour afficher la progression de la tâche, entrez `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Vous devez attendre que la version d’image soit totalement intégrée et répliquée avant de pouvoir utiliser la même image managée pour créer une autre version d’image.
>
> Vous pouvez également stocker votre image dans le Stockage Premium, en ajoutant `-StorageAccountType Premium_LRS`, ou dans le [stockage redondant interzone](../storage/common/storage-redundancy.md), en ajoutant `-StorageAccountType Standard_ZRS`, quand vous créez la version de l’image.
>

### <a name="rest"></a>[REST](#tab/rest)

Créez une définition d’image à l’aide de l’[API REST](/rest/api/compute/gallery-images/create-or-update).

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}?api-version=2019-12-01

{
    "location": "eastus",
    "properties": {
        "hyperVGeneration": "V1",
        "identifier": {
            "offer": "myOffer",
            "publisher": "myPublisher",
            "sku": "mySKU"
        },
        "osState": "Specialized",
        "osType": "Linux",
    },
}
```

Créez une version d’image à l’aide de l’[API REST](/rest/api/compute/gallery-image-versions/create-or-update). Dans cet exemple, nous créons une version d’image à partir d’une machine virtuelle. Pour utiliser une autre source, transmettez l’ID de ressource de la source (par exemple, transmettez l’ID de l’instantané du disque de système d’exploitation).

```rest
# @name imageVersion
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}/versions/{galleryImageVersionName}?api-version=2019-12-01

{
    "location": "{region}",
    "properties": {
        "publishingProfile": {
            "endOfLifeDate": "2024-12-02T00:00:00+00:00",
            "replicaCount": 1,
            "storageAccountType": "Standard_ZRS",
            "targetRegions": [
                {
                    "name": "eastus",
                    "regionalReplicaCount": 2,
                    "storageAccountType": "Standard_LRS",
                },
                {
                    "name": "westus2",
                }
            ]
        },
        "storageProfile": {
            "source": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}"
            }
        }
    }
}
```

---


## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment fournir des informations sur le plan d’achat, consultez [Donner des informations sur le plan d’achat de la Place de marché Azure lors de la création d’images](marketplace-images.md).
