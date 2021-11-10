---
title: 'Didacticiel : gérer les disques Azure avec Azure PowerShell'
description: Dans ce didacticiel, vous allez apprendre à utiliser Azure PowerShell afin de créer et gérer des disques Azure pour des machines virtuelles
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.date: 10/08/2021
ms.custom: template-tutorial, devx-track-azurepowershell
ms.openlocfilehash: a1ea898dd246977bbb7284a18349a265efd7655d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063115"
---
# <a name="tutorial-manage-disks-with-azure-powershell"></a>Tutoriel : Gérer les disques avec Azure PowerShell

Les machines virtuelles Azure utilisent des disques pour stocker les systèmes d’exploitation (OS), les applications et les données. Quand vous créez une machine virtuelle, il est important de choisir une taille de disque et une configuration adaptées à la charge de travail attendue.

Ce tutoriel couvre le déploiement et la gestion des disques de machine virtuelle. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer, attacher et initialiser un disque de données
> * Vérifier l’état d’un disque
> * Initialiser un disque
> * Étendre et mettre à niveau un disque
> * Détacher et supprimer un disque

## <a name="prerequisites"></a>Prérequis

Vous devez disposer d’un compte Azure avec un abonnement actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-vm"></a>Créer une machine virtuelle

Pour les exercices de ce tutoriel, une machine virtuelle est requise. Suivez les étapes de cette section pour en créer une.

Avant de commencer, recherchez la variable `$azRegion` située dans la première ligne de l’exemple de code et mettez à jour sa valeur pour refléter la région souhaitée. Par exemple, pour spécifier la région **USA Centre**, utilisez `$azRegion = "Central US"`. Utilisez ensuite le code pour déployer une machine virtuelle au sein d’un nouveau groupe de ressources. Vous êtes invité à entrer les valeurs du nom d’utilisateur et du mot de passe pour le compte administrateur local de la machine virtuelle.

```azurepowershell-interactive
$azRegion = "[Your Region]"
$azResourceGroup = "myDemoResourceGroup"
$azVMName = "myDemoVM"
$azDataDiskName = "myDemoDataDisk"

New-AzVm `
    -Location $azRegion `
    -ResourceGroupName $azResourceGroup `
    -Name $azVMName `
    -Size "Standard_D2s_v3" `
    -VirtualNetworkName "myDemoVnet" `
    -SubnetName "myDemoSubnet" `
    -SecurityGroupName "myDemoNetworkSecurityGroup" `
    -PublicIpAddressName "myDemoPublicIpAddress"
```

La sortie confirme la réussite de la création de la machine virtuelle.

```Output
ResourceGroupName        : myDemoResourceGroup
Id                       : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoTestVM
VmId                     : [{GUID}]
Name                     : myDemoVM
Type                     : Microsoft.Compute/virtualMachines
Location                 : centralus
Tags                     : {}
HardwareProfile          : {VmSize}
NetworkProfile           : {NetworkInterfaces}
OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, AllowExtensionOperations, RequireGuestProvisionSignal}
ProvisioningState        : Succeeded
StorageProfile           : {ImageReference, OsDisk, DataDisks}
FullyQualifiedDomainName : mydemovm-abc123.Central US.cloudapp.azure.com
```

La machine virtuelle est provisionnée, et deux disques sont automatiquement créés et attachés.

- Un **disque de système d’exploitation**, qui héberge le système d’exploitation de la machine virtuelle.
- Un **disque temporaire**, principalement utilisé pour des opérations telles que le traitement de données temporaires.

## <a name="add-a-data-disk"></a>Ajouter un disque de données

Nous vous recommandons de séparer les données d’application et les données utilisateur des données liées à l’OS, quand cela est possible. Si vous avez besoin de stocker des données utilisateur ou d’application sur votre machine virtuelle, vous devez généralement créer et attacher des disques de données supplémentaires.

Suivez les étapes de cette section pour créer, attacher et initialiser un disque de données sur la machine virtuelle.

### <a name="create-the-data-disk"></a>Créer le disque de données

Cette section vous guide tout au long de la création d’un disque de données.

1. Pour pouvoir créer un disque de données, vous devez d’abord créer un objet de disque. L’exemple de code suivant utilise l’applet de commande [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) pour configurer un objet de disque.

    ```azurepowershell-interactive
    $diskConfig = New-AzDiskConfig `
        -Location $azRegion `
        -CreateOption Empty `
        -DiskSizeGB 128 `
        -SkuName "Standard_LRS"
    ```

1. Une fois l’objet de disque créé, utilisez l’applet de commande [New-AzDisk](/powershell/module/az.compute/new-azdisk) pour provisionner un disque de données.

    ```azurepowershell-interactive
    $dataDisk = New-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName `
        -Disk $diskConfig
    ```

    Vous pouvez utiliser l’applet de commande [Get-AzDisk](/powershell/module/az.compute/get-azdisk) pour vérifier que le disque a été créé.

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName
    ```

    Dans cet exemple, la sortie confirme que le disque a été créé. Les valeurs de propriétés `DiskState` et `ManagedBy` confirment que le disque n’est pas encore attaché.

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    :
    ManagedByExtended            : {}
    OsType                       :
    DiskSizeGB                   : 128
    DiskSizeBytes                : 137438953472
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Name                         : myDemoDataDisk
    ```

### <a name="attach-the-data-disk"></a>Association du disque de données

Un disque de données doit être attaché à une machine virtuelle pour que celle-ci puisse y accéder. Suivez les étapes de cette section afin de créer une référence pour la machine virtuelle, connectez le disque, puis mettez à jour la configuration de la machine virtuelle.

1. Récupérez la machine virtuelle à laquelle vous allez attacher le disque de données. L’exemple de code suivant utilise l’applet de commande [Get-AzVM](/powershell/module/az.compute/get-azvm) pour créer une référence à la machine virtuelle.

    ```azurepowershell-interactive
    $vm = Get-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

1. Attachez ensuite le disque de données à la configuration de la machine virtuelle à l’aide de l’applet de commande [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).

    ```azurepowershell-interactive
    $vm = Add-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName `
        -CreateOption Attach `
        -ManagedDiskId $dataDisk.Id `
        -Lun 1
    ```

1. Enfin, mettez à jour la configuration de la machine virtuelle avec l’applet de commande [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk).

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    Après une courte pause, la sortie confirme la réussite de l’attachement.

    ```Output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

### <a name="initialize-the-data-disk"></a>Initialiser le disque de données

Une fois qu’un disque de données est attaché à la machine virtuelle, vous devez configurer l’OS pour qu’il puisse utiliser le disque. La section suivante fournit des conseils d’aide sur la connexion à la machine virtuelle distante et la configuration du premier disque ajouté.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Localisez la machine virtuelle à laquelle vous avez attaché le disque de données. Créez une connexion RDP (Remote Desktop Protocol), et connectez-vous en tant qu’administrateur local.

1. Après avoir établi une connexion RDP à la machine virtuelle distante, sélectionnez le menu **Démarrer** de Windows. Dans la zone de recherche, entrez **PowerShell**, puis sélectionnez **Windows PowerShell** pour ouvrir une fenêtre PowerShell.

    [![Image d’une fenêtre de connexion Bureau à distance.](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. Dans la fenêtre PowerShell ouverte, exécutez le script suivant.

    ```powershell
    Get-Disk | Where PartitionStyle -eq 'raw' |
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -AssignDriveLetter -UseMaximumSize |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDemoDataDisk" -Confirm:$false
    ```

    La sortie confirme la réussite de l’initialisation.

    ```Output
    DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining   Size
    ----------- --------------- ---------- --------- ------------ ----------------- -------------   ----
    F           myDemoDataDisk  NTFS       Fixed     Healthy      OK                    127.89 GB 128 GB
    ```

## <a name="expand-a-disk"></a>Étendre un disque

Vous pouvez étendre des disques Azure pour fournir une capacité de stockage supplémentaire quand votre machine virtuelle dispose de peu d’espace disque disponible.

Certains scénarios nécessitent le stockage des données sur le disque de l’OS. Par exemple, vous devrez peut-être prendre en charge les applications héritées qui installent des composants sur le lecteur de l’OS. Vous pouvez également être amené à migrer une machine virtuelle ou un PC physique local ayant un lecteur d’OS plus grand. Dans ce cas, il peut s’avérer nécessaire d’étendre le disque d’OS d’une machine virtuelle.

La réduction d’un disque existant n’est pas prise en charge. Elle risque d’entraîner une perte de données.

### <a name="update-the-disks-size"></a>Mettre à jour la taille du disque

Suivez les étapes ci-dessous pour redimensionner le disque d’OS ou un disque de données.

1. Sélectionnez la machine virtuelle qui contient le disque que vous allez redimensionner avec l’applet de commande `Get-AzVM`.

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. Pour pouvoir redimensionner le disque d’une machine virtuelle, vous devez arrêter cette dernière. Utilisez l’applet de commande `Stop-AzVM` pour arrêter la machine virtuelle. Une confirmation vous est demandée.

    > [!IMPORTANT]
    > Avant d’arrêter une machine virtuelle, vérifiez toujours qu’aucune ressource ou donnée importante ne peut être perdue.

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Après une courte pause, la sortie confirme que la machine s’est correctement arrêtée.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. Une fois la machine virtuelle arrêtée, vous pouvez obtenir une référence au disque d’OS ou au disque de données attaché à la machine virtuelle avec l’applet de commande `Get-AzDisk`.

    L’exemple suivant sélectionne le disque d’OS de la machine virtuelle.

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    L’exemple suivant sélectionne le premier disque de données de la machine virtuelle.

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. Une fois que vous avez une référence au disque, définissez sa taille à 250 Gio.

    > [!IMPORTANT]
    > La nouvelle taille doit être supérieure à la taille du disque actuelle. Le maximum autorisé est de 4 095 Gio pour les disques d’OS.

    ```azurepowershell-interactive
    $disk.DiskSizeGB = 250
    ```

1. Mettez ensuite à jour l’image de disque avec l’applet de commande `Update-AzDisk`.

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    L’image de disque est mise à jour, et la sortie confirme la nouvelle taille du disque.

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoVM
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    TimeCreated                  : 9/135/2021 6:41:10 PM
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationData
    DiskSizeGB                   : 250
    DiskSizeBytes                : 268435456000
    UniqueId                     : {GUID}
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Reserved
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/disks/myDemoDataDisk
    Name                         : myDemoDataDisk
    Type                         : Microsoft.Compute/disks
    Location                     : centralus

1. Finally, restart the VM with the `Start-AzVM` cmdlet.

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Après une courte pause, la sortie confirme que la machine a correctement démarré.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

### <a name="expand-the-disk-volume-in-the-os"></a>Étendre le volume de disque dans l’OS

Pour pouvoir tirer parti de la nouvelle taille de disque, vous devez étendre le volume dans l’OS. Suivez les étapes ci-dessous pour étendre le volume de disque et tirer parti de la nouvelle taille de disque.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Localisez la machine virtuelle à laquelle vous avez attaché le disque de données. Créez une connexion RDP (Remote Desktop Protocol), et connectez-vous. Si vous n’avez plus accès à un compte d’administration, créez un objet de justificatif pour un nom d’utilisateur et un mot de passe spécifiques à l’aide de l’applet de commande [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

1. Après avoir établi une connexion RDP à la machine virtuelle distante, sélectionnez le menu **Démarrer** de Windows. Dans la zone de recherche, entrez **PowerShell**, puis sélectionnez **Windows PowerShell** pour ouvrir une fenêtre PowerShell.

    [![Image d’une fenêtre de connexion Bureau à distance.](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. Ouvrez PowerShell et exécutez le script suivant. Changez la valeur de la variable `-DriveLetter` de manière appropriée. Par exemple, pour redimensionner la partition sur le lecteur **F:** , utilisez `$driveLetter = "F"`.

    ```powershell
    $driveLetter = "[Drive Letter]" `
    $size = (Get-PartitionSupportedSize -DriveLetter $driveLetter) `
    Resize-Partition `
        -DriveLetter $driveLetter `
        -Size $size.SizeMax
    ```

1. Réduisez la fenêtre RDP et revenez à Azure Cloud Shell. Utilisez l’applet de commande `Get-AzDisk` pour vérifier que le disque a été correctement redimensionné.

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup | Out-Host -Paging
    ```

## <a name="upgrade-a-disk"></a>Mettre à niveau un disque

Il existe plusieurs façons de répondre aux changements apportés aux charges de travail de votre organisation. Par exemple, vous pouvez choisir de mettre à niveau un disque HDD Standard vers un disque SSD Premium pour faire face à une demande accrue.

Suivez les étapes de cette section pour mettre à niveau un disque managé de Standard à Premium.

1. Sélectionnez la machine virtuelle qui contient le disque que vous allez mettre à niveau avec l’applet de commande `Get-AzVM`.

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. Pour pouvoir mettre à niveau le disque d’une machine virtuelle, vous devez arrêter cette dernière. Utilisez l’applet de commande `Stop-AzVM` pour arrêter la machine virtuelle. Une confirmation vous est demandée.

    > [!IMPORTANT]
    > Avant d’arrêter une machine virtuelle, vérifiez toujours qu’aucune ressource ou donnée importante ne peut être perdue.

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Après une courte pause, la sortie confirme que la machine s’est correctement arrêtée.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. Une fois la machine virtuelle arrêtée, vous pouvez obtenir une référence au disque d’OS ou au disque de données attaché à la machine virtuelle avec l’applet de commande `Get-AzDisk`.

    L’exemple suivant sélectionne le disque d’OS de la machine virtuelle.

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    L’exemple suivant sélectionne le premier disque de données de la machine virtuelle.

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. Une fois que vous avez une référence au disque, définissez son SKU à **Premium_LRS**.

    ```azurepowershell-interactive
    $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new('Premium_LRS')
    ```

1. Mettez ensuite à jour l’image de disque avec l’applet de commande `Update-AzDisk`.

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    L’image de disque est mise à jour. Utilisez l’exemple de code suivant pour vérifier que la référence SKU du disque a été mise à niveau.

    ```azurepowershell-interactive
    $disk.Sku.Name
    ```

    La sortie confirme la nouvelle référence SKU du disque.

    ```Output
    Premium_LRS
    ```

1. Enfin, redémarrez la machine virtuelle avec l’applet de commande `Start-AzVM`.

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Après une courte pause, la sortie confirme que la machine a correctement démarré.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

## <a name="detach-a-data-disk"></a>Détachement d'un disque de données

Vous pouvez détacher un disque de données d’une machine virtuelle quand vous souhaitez l’attacher à une autre machine virtuelle ou quand il n’est plus nécessaire. Par défaut, les disques détachés ne sont pas supprimés pour éviter toute perte de données involontaire. Un disque détaché continue d’entraîner des frais de stockage jusqu’à ce qu’il soit supprimé.

1. Tout d’abord, sélectionnez la machine virtuelle à laquelle le disque est attaché avec l’applet de commande `Get-AzVM`.

    ```azurepowershell-interactive
    $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. Détachez ensuite le disque de la machine virtuelle avec l’applet de commande `Remove-AzVMDataDisk`.

    ```azurepowershell-interactive
    Remove-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName
    ```

1. Mettez à jour l’état de la machine virtuelle avec l’applet de commande `Update-AzVM` pour supprimer le disque de données.

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    Après une courte pause, la sortie confirme que la machine virtuelle a correctement été mise à jour.

    ```output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

## <a name="delete-a-data-disk"></a>Supprimer un disque de données

Quand vous supprimez une machine virtuelle, les disques de données attachés à celle-ci restent provisionnés et continuent d’entraîner des frais jusqu’à ce qu’ils soient supprimés. Ce comportement par défaut permet d’éviter les pertes de données causées par une suppression involontaire.

Vous pouvez utiliser l’exemple de script PowerShell suivant pour supprimer les disques non attachés. La récupération des disques est limitée à **myDemoResourceGroup**, car le commutateur `-ResourceGroupName` est utilisé avec l’applet de commande `Get-AzDisk`.

```azurepowershell
# Get all disks in resource group $azResourceGroup
$allDisks = Get-AzDisk -ResourceGroupName $azResourceGroup

# Determine the number of disks in the collection
if($allDisks.Count -ne 0) {

    Write-Host "Found $($allDisks.Count) disks."

    # Iterate through the collection
    foreach ($disk in $allDisks) {

        # Use the disk's "ManagedBy" property to determine if it is unattached
        if($disk.ManagedBy -eq $null) {

            # Confirm that the disk can be deleted
            Write-Host "Deleting unattached disk $($disk.Name)."
            $confirm = Read-Host "Continue? (Y/N)"
            if ($confirm.ToUpper() -ne 'Y') { break }
            else {

                # Delete the disk
                $disk | Remove-AzDisk -Force 
                Write-Host "Unattached disk $($disk.Name) deleted."
            }
        }
    }
}
```

Le disque de données non attaché est supprimé, comme indiqué dans la sortie.

```output
Name      : abcd1234-ab12-cd34-ef56-abcdef123456
StartTime : 9/13/2021 10:14:05 AM
EndTime   : 9/13/2021 10:14:35 AM
Status    : Succeeded
Error     :
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées. Vous pouvez utiliser l’exemple de script PowerShell suivant pour supprimer le groupe de ressources créé plus tôt dans ce tutoriel.  

> [!CAUTION]
> Soyez prudent lors de la suppression d’un groupe de ressources. Pour éviter la perte de données importantes, vérifiez toujours qu’il n’existe aucune ressource ou donnée importante dans le groupe de ressources avant sa suppression.

```azurepowershell-interactive
    Remove-AzResourceGroup -Name $azResourceGroup
```

Vous êtes invité à confirmer l’opération. Après une courte pause, la réponse `True` confirme que **myDemoResourceGroup** a été correctement supprimé.
    
```Output
Confirm
Are you sure you want to remove resource group 'myDemoResourceGroup'
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
True
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer, attacher et initialiser un disque de données
> * Vérifier l’état d’un disque
> * Initialiser un disque
> * Étendre et mettre à niveau un disque
> * Détacher et supprimer un disque

Passez au tutoriel suivant pour découvrir comment automatiser la configuration des machines virtuelles.

> [!div class="nextstepaction"]
> [Automatiser la configuration de machine virtuelle](./tutorial-automate-vm-deployment.md)
