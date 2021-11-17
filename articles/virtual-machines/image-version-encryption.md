---
title: Créer une version d’image chiffrée avec vos propres clés
description: Créez une version d’image dans une instance Azure Compute Gallery avec des clés de chiffrement gérées par le client.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 7/1/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bcd214413eb4880219e18c4bb03e4430f31690fd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452050"
---
# <a name="use-customer-managed-keys-for-encrypting-images"></a>Utiliser des clés gérées par le client pour le chiffrement d’images

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les images d’une instance Azure Compute Gallery (anciennement Shared Image Gallery) étant stockées sous forme d’instantanés, elles sont automatiquement chiffrées côté serveur. Le chiffrement côté serveur utilise le [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, l’un des chiffrements par blocs les plus puissants actuellement disponibles. Le chiffrement côté serveur est également conforme à la norme FIPS 140-2. Pour plus d’informations sur les modules cryptographiques des disque managés Azure, consultez [API de chiffrement : nouvelle génération](/windows/desktop/seccng/cng-portal).

Vous pouvez vous appuyer sur des clés gérées par la plateforme pour le chiffrement de vos images ou utiliser vos propres clés. Vous pouvez aussi combiner les deux pour bénéficier d’un double chiffrement. Si vous choisissez de gérer le chiffrement avec vos propres clés, vous pouvez spécifier une *clé gérée par le client* à utiliser pour chiffrer et déchiffrer tous les disques de vos images. 

Le chiffrement côté serveur à l’aide de clés gérées par le client utilise Azure Key Vault. Vous pouvez importer [vos clés RSA](../key-vault/keys/hsm-protected-keys.md) vers votre coffre de clés ou générer de nouvelles clés RSA dans Azure Key Vault.

## <a name="prerequisites"></a>Prérequis

Cet article nécessite que vous disposiez déjà d’un chiffrement de disque défini dans chaque région vers laquelle vous souhaitez répliquer votre image :

- Pour utiliser uniquement une clé gérée par le client, consultez les articles sur l’activation des clés gérées par le client avec un chiffrement côté serveur à l’aide du [portail Azure](./disks-enable-customer-managed-keys-portal.md) ou de [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-optionally-with-automatic-key-rotation).

- Pour utiliser des clés gérées par la plateforme et des clés gérées par le client (à des fins de double chiffrement), consultez les articles sur l’activation du double chiffrement au repos à l’aide du [portail Azure](./disks-enable-double-encryption-at-rest-portal.md) ou de [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).

   > [!IMPORTANT]
   > Pour accéder au portail Azure, vous devez utiliser le lien [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates). Le double chiffrement au repos n’est actuellement pas visible dans le portail Azure public sans utiliser ce lien.

## <a name="limitations"></a>Limites

Quand vous utilisez des clés gérées par le client pour chiffrer des images dans une instance Azure Compute Gallery, des limitations s’appliquent : 

- Les jeux de clés de chiffrement doivent se trouver dans le même abonnement que votre image.

- Les jeux de clés de chiffrement étant des ressources régionales, chaque région nécessite un jeu de clés de chiffrement différent.

- Vous ne pouvez pas partager ou copier des images qui utilisent des clés gérées par le client. 

- Après avoir utilisé vos propres clés pour chiffrer un disque ou une image, vous ne pouvez pas revenir à l’utilisation de clés gérées par la plateforme pour chiffrer ces disques ou images.


## <a name="powershell"></a>PowerShell

Afin de spécifier un jeu de chiffrement de disque pour une version d’image, utilisez [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) avec le paramètre `-TargetRegion` : 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Créer une machine virtuelle

Vous pouvez créer une machine virtuelle à partir d’une instance Azure Compute Gallery et utiliser des clés gérées par le client pour chiffrer les disques. La syntaxe est la même que celle utilisée pour créer une machine virtuelle [généralisée](vm-generalized-image-version.md) ou [spécialisée](vm-specialized-image-version.md) à partir d’une image. Utilisez le jeu de paramètres étendu et ajoutez `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` à la configuration de la machine virtuelle.

Pour les disques de données, ajoutez le paramètre `-DiskEncryptionSetId $setID` quand vous utilisez [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>Interface de ligne de commande 

Afin de spécifier un jeu de chiffrement de disque pour une version d’image, utilisez [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create) avec le paramètre `--target-region-encryption`. Le format pour `--target-region-encryption` est une liste de clés, séparée par des virgules, pour le chiffrement des disques de système d’exploitation et de données. Ce que vous obtenez doit ressembler à ceci : `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`. 

Si la source du disque du système d’exploitation est un disque managé ou une machine virtuelle, utilisez `--managed-image` pour spécifier la source de la version de l’image. Dans cet exemple, la source est une image managée qui possède un disque de système d’exploitation et un disque de données au numéro d’unité logique 0. Le disque du système d’exploitation sera chiffré avec DiskEncryptionSet1 et le disque de données avec DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Si la source du disque du système d’exploitation est un instantané, utilisez `--os-snapshot` pour spécifier le disque du système d’exploitation. Si des instantanés de disque de données doivent également faire partie de la version de l’image, ajoutez-les. Utilisez `--data-snapshot-luns` pour spécifier le numéro d’unité logique et `--data-snapshots` pour spécifier les instantanés.

Dans cet exemple, les sources sont des instantanés de disque. Le numéro d’unité logique 0 comporte un disque de système d’exploitation et un disque de données. Le disque du système d’exploitation sera chiffré avec DiskEncryptionSet1 et le disque de données avec DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Création de la machine virtuelle

Vous pouvez créer une machine virtuelle à partir d’une instance Azure Compute Gallery et utiliser des clés gérées par le client pour chiffrer les disques. La syntaxe est la même que celle utilisée pour créer une machine virtuelle [généralisée](vm-generalized-image-version.md) ou [spécialisée](vm-specialized-image-version.md) à partir d’une image. Ajoutez simplement le paramètre `--os-disk-encryption-set` avec l’ID du jeu de chiffrement. Pour les disques de données, ajoutez `--data-disk-encryption-sets` avec une liste délimitée par des espaces des jeux de chiffrement de disque.


## <a name="portal"></a>Portail

Quand vous créez votre version d’image dans le portail, vous pouvez utiliser l’onglet **Chiffrement** pour apliquer vos jeux de chiffrement de stockage.

1. Dans la page **Créer une version d’image**, sélectionnez l’onglet **Chiffrement**.
2. Dans **Type de chiffrement**, sélectionnez **Chiffrement au repos avec une clé gérée par le client** ou **Double chiffrement avec les clés gérées par la plateforme et gérées par le client**. 
3. Pour chaque disque de l’image, sélectionnez un jeu de chiffrement dans la liste déroulante **Jeu de chiffrement de disque**. 

### <a name="create-the-vm"></a>Création de la machine virtuelle

Vous pouvez créer une machine virtuelle à partir d’une version d’image et utiliser des clés gérées par le client pour chiffrer les disques. Quand vous créez la machine virtuelle dans le portail, sous l’onglet **Disques**, sélectionnez **Chiffrement au repos avec des clés gérées par le client** ou **Double chiffrement avec les clés gérées par la plateforme et gérées par le client** pour le **Type de chiffrement**. Vous pouvez ensuite sélectionner le jeu de chiffrement dans la liste déroulante.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [chiffrement de disque côté serveur](./disk-encryption.md).

Pour savoir comment fournir des informations sur le plan d'achat, consultez [Supply Azure Marketplace purchase plan information when creating images](marketplace-images.md) (Donner des informations sur le plan d'achat de la place de marché Azure lors de la création d’images).
