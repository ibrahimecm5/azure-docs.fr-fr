---
title: Créer une image managée dans Azure
description: Créer une image managée d’une machine virtuelle ou d’un disque dur virtuel généralisé(e) dans Azure. Les images peuvent être utilisées pour créer différentes machines virtuelles utilisant des disques managés.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.collection: windows
ms.openlocfilehash: 95f57b01f2c9e6bffd0cfc1f1d563605e320d6ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437008"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Créer une image managée d’une machine virtuelle généralisée dans Azure

**S’applique à :** :heavy_check_mark : Machines virtuelles Windows 


Une ressource d’image managée peut être créée à partir d’une machine virtuelle généralisée stockée en tant que disque managé ou non managé dans un compte de stockage. L’image peut ensuite être utilisée pour créer plusieurs machines virtuelles. Pour plus d'informations sur la facturation des images managées, reportez-vous à [Tarification de la fonctionnalité Disques managés](https://azure.microsoft.com/pricing/details/managed-disks/). 

Une image managée prend en charge jusqu’à 20 déploiements simultanés. Une tentative de création simultanée de plus de 20 machines virtuelles à partir de la même image managée peut entraîner l’expiration des délais d’approvisionnement en raison des limites de performances de stockage d’un disque dur virtuel unique. Pour créer plus de 20 machines virtuelles simultanément, utilisez une image de [Azure Compute Gallery](../shared-image-galleries.md) (anciennement Shared Image Gallery) configurée avec 1 réplica tous les 20 déploiements simultanés de machines virtuelles.

## <a name="prerequisites"></a>Prérequis

Pour créer une image, vous avez besoin d’une machine virtuelle [généralisée](../generalize.md).

## <a name="create-a-managed-image-in-the-portal"></a>Créer une image managée dans le portail 

1. Accédez au [Portail Azure](https://portal.azure.com) pour gérer l’image de machine virtuelle. Recherchez et sélectionnez **Machines virtuelles**.

2. Sélectionnez votre machine virtuelle dans la liste.

3. Dans la page **Machine virtuelle** relative à la machine virtuelle, dans le menu supérieur, sélectionnez **Capturer**.

   La page **Créer une image** s’affiche.

4. Pour **nom**, acceptez le nom prérempli ou entrez un nom à utiliser pour l’image.

5. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez un nom, ou sélectionnez un groupe de ressources à utiliser dans la liste déroulante.

6. Pour supprimer la machine virtuelle source une fois que l’image a été créée, sélectionnez **Supprimer automatiquement cette machine virtuelle après avoir créé l’image**.

7. Si vous souhaitez pouvoir utiliser l’image dans toute [zone de disponibilité](../../availability-zones/az-overview.md), sélectionnez **Activée** pour l’option **Résilience aux zones**.

8. Sélectionnez **Créer** pour créer l’image.

Une fois l’image créée, elle apparaît en tant que ressource **Image** dans la liste des ressources du groupe de ressources.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Création d’une image de machine virtuelle avec PowerShell

 

La création d’une image directement à partir de la machine virtuelle permet de s’assurer que celle-ci comprend tous les disques associés à la machine virtuelle, y compris le disque du système d’exploitation et tous les disques de données. Cet exemple montre comment créer une image gérée à partir d’une machine virtuelle qui utilise des disques managés.

Avant de commencer, assurez-vous que vous disposez de la dernière version du module Azure PowerShell. Pour trouver la version, exécutez `Get-Module -ListAvailable Az` dans PowerShell. Si vous devez procéder à une mise à niveau, voir [Installer Azure PowerShell sur Windows avec PowerShellGet](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, exécutez `Connect-AzAccount` pour créer une connexion avec Azure.


> [!NOTE]
> Si vous voulez stocker votre image dans un stockage redondant interzone, vous devez la créer dans une région qui prend en charge les [zones de disponibilité](../../availability-zones/az-overview.md) et inclut le paramètre `-ZoneResilient` dans la configuration de l’image (commande `New-AzImageConfig`).

Pour créer une image de machine virtuelle, procédez comme suit :

1. Définissez des variables.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Assurez-vous que la machine virtuelle a été libérée.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Définissez l’état de la machine virtuelle sur **Généralisé**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Accédez à la machine virtuelle. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Créez la configuration de l’image.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Créez l’image.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Créer une image à partir d’un disque géré à l’aide de PowerShell

Si vous souhaitez créer une image uniquement du disque du système d’exploitation, spécifiez l’ID de disque managé en tant que le disque du système d’exploitation :

    
1. Définissez des variables. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Obtenez la machine virtuelle.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Obtenez l’ID du disque géré.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Créez la configuration de l’image.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Créez l’image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Création d’une image à partir d’une capture instantanée avec PowerShell

Vous pouvez créer une image gérée à partir d’une capture instantanée d’une machine virtuelle généralisée en procédant comme suit :

    
1. Définissez des variables. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Accédez à la capture instantanée.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Créez la configuration de l’image.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Créez l’image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Créer une image à partir d’une machine virtuelle qui utilise un compte de stockage

Pour créer une image managée à partir d’une machine virtuelle qui n’utilise pas de disques managés, vous devez disposer de l’URI du VHD (disque dur virtuel) de système d’exploitation dans le compte de stockage, au format suivant : https://*moncomptedestockage*.blob.core.windows.net/*conteneurvhd*/*nomdefichiervhd.vhd*. Dans cet exemple, le disque dur virtuel se trouve dans *mystorageaccount*, dans un conteneur nommé *vhdcontainer*, et le nom de fichier du disque dur virtuel est *vhdfilename.vhd*.


1.  Définissez des variables.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Arrêtez/libérez la machine virtuelle.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Indiquez que la machine virtuelle est généralisée.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Créez l’image en utilisant votre disque dur virtuel généralisé de système d’exploitation.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Étapes suivantes
- [Créer une machine virtuelle à partir d’une image gérée](create-vm-generalized-managed.md). 
