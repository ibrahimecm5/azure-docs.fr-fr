---
title: Développer des disques durs virtuels sur une machine virtuelle Linux
description: Découvrez comment développer des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8f54e1f74c5f4f6a8502285f5e4c36c09892ec71
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082604"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Étendre des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Cet article explique comment étendre des disques managés pour une machine virtuelle Linux avec Azure CLI. Vous pouvez [ajouter des disques de données](add-disk.md) afin d’offrir un espace de stockage supplémentaire, et vous pouvez également étendre un disque de données existant. La taille par défaut de disque dur virtuel pour le système d’exploitation est généralement de 30 Go sur une machine virtuelle Linux dans Azure. 

> [!WARNING]
> Assurez-vous que l’état d’intégrité de votre système de fichiers soit toujours sain, votre type de table de partition de disque prendra en charge la nouvelle taille. Veillez également à sauvegarder vos données avant de redimensionner les disques. Pour plus d’informations, consultez le [démarrage rapide de Sauvegarde Azure](../../backup/quick-backup-vm-portal.md). 

## <a name="expand-an-azure-managed-disk"></a>Étendre un disque managé Azure

### <a name="resize-without-downtime-preview"></a>Redimensionner sans temps d’arrêt (préversion)

Vous pouvez maintenant redimensionner vos disques managés sans libérer votre machine virtuelle.

Pour cette opération, la préversion présente les limitations suivantes :

- Disponible uniquement dans la région USA Ouest.
- Prise en charge uniquement pour les disques de données.
- Les disques de moins de 4 Tio ne peuvent pas être étendus à 4 Tio ou plus sans temps d’arrêt.
    - Une fois que vous avez augmenté la taille d’un disque à 4 Tio ou plus, il peut être étendu sans temps d’arrêt.
- Vous devez installer et utiliser la [dernière interface de ligne de commande Azure](/cli/azure/install-azure-cli), le [dernier module d’Azure PowerShell](/powershell/azure/install-az-ps), le portail Azure si vous y accédez par le biais de [https://aka.ms/iaasexp/DiskLiveResize](https://aka.ms/iaasexp/DiskLiveResize) ou un modèle Azure Resource Manager avec une version d’API 2021-04-01 ou plus récente.

Pour vous inscrire à la fonctionnalité, utilisez la commande suivante :

```azurecli
az feature register --namespace Microsoft.Compute --name LiveResize
```

L’inscription peut prendre quelques minutes. Pour vérifier que vous vous êtes inscrit, utilisez la commande suivante :

```azurecli
az feature show --namespace Microsoft.Compute --name LiveResize
```

### <a name="get-started"></a>Bien démarrer

Vérifiez que vous avez installé la dernière version [d’Azure CLI](/cli/azure/install-az-cli2) et que vous êtes connecté à un compte Azure avec la commande [az login](/cli/azure/reference-index#az_login).

Cet article nécessite une machine virtuelle existante dans Azure avec au moins un disque de données attaché et préparé. Si vous n’avez pas encore de machine virtuelle à utiliser, consultez [Create and prepare a VM with data disks](tutorial-manage-disks.md#create-and-attach-disks) (Créer et préparer une machine virtuelle avec des disques de données).

Dans les exemples ci-après, remplacez les exemples de nom de paramètre, tels que *myResourceGroup* et *myVM*, par vos propres valeurs.

> [!IMPORTANT]
> Si vous avez activé **LiveResize** et que votre disque répond aux exigences indiquées dans [Redimensionner sans temps d’arrêt (préversion)](#resize-without-downtime-preview), vous pouvez ignorer les étapes 1 et 3. 

1. Il est impossible d’effectuer des opérations sur les disques durs virtuels avec la machine virtuelle en cours d’exécution. Libérez la machine virtuelle avec la commande [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L’exemple suivant libère la machine virtuelle nommée *myVM* dans le groupe de ressources nommé *myResourceGroup* :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > La machine virtuelle doit être libérée pour développer le disque dur virtuel. L’arrêt de la machine virtuelle avec `az vm stop` ne libère pas les ressources de calcul. Pour publier les ressources de calcul, utilisez `az vm deallocate`.

1. Affichez la liste des disques managés dans un groupe de ressources avec la commande [az disk list](/cli/azure/disk#az_disk_list). L’exemple suivant affiche la liste des disques managés dans le groupe de ressources nommé *myResourceGroup* :

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Développez le disque requis avec la commande [az disk update](/cli/azure/disk#az_disk_update). L’exemple ci-après étend la taille du disque managé nommé *myDataDisk* à *200* Go :

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Lorsque vous étendez un disque managé, la taille mise à jour est arrondie à la taille de disque managé la plus proche. Pour obtenir un tableau des tailles et des niveaux de disques managés disponibles, consultez [Vue d’ensemble d’Azure Disques managés - Tarification et facturation](../managed-disks-overview.md).

1. Démarrez votre machine virtuelle avec [az vm start](/cli/azure/vm#az_vm_start). L’exemple suivant démarre la machine virtuelle nommée *myVM* dans le groupe de ressources nommé *myResourceGroup* :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Étendre une partition de disque et un système de fichiers
Pour utiliser un disque étendu, étendez la partition et le système de fichiers sous-jacents.

1. Établissez une connexion SSH à votre machine virtuelle à l’aide des informations d’identification appropriées. Vous pouvez visualiser l’adresse IP publique de votre machine virtuelle avec la commande [az vm show](/cli/azure/vm#az_vm_show) :

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Étendez la partition et le système de fichiers sous-jacents.

    a. Si le disque est déjà monté, démontez-le :

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Utilisez `parted` pour afficher des informations sur le disque et redimensionner la partition :

    ```bash
    sudo parted /dev/sdc
    ```

    Affichez les informations sur la disposition actuelle de la partition avec `print`. Le résultat est comparable à l’exemple ci-après, qui indique que le disque sous-jacent présente une taille de 215 Go :

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Développez la partition avec `resizepart`. Entrez le numéro de la partition, *1*, et la taille de la nouvelle partition :

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Pour quitter l’outil, entrez `quit`.

1. Une fois la partition redimensionnée, vérifiez la cohérence de la partition avec `e2fsck` :

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Redimensionnez le système de fichiers avec `resize2fs` :

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Montez la partition à l’emplacement souhaité, tel que `/datadrive` :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Pour vérifier que le disque de données a été redimensionné, utilisez `df -h`. L’exemple de sortie ci-après indique que le lecteur de données */dev/sdc1* présente désormais une taille de 200 Go :

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Étapes suivantes
* Si vous avez besoin de stockage supplémentaire, vous pouvez également [ajouter des disques de données à une machine virtuelle Linux](add-disk.md). 
* Pour plus d'informations sur le chiffrement de disque, consultez [Azure Disk Encryption pour les machines virtuelles Linux](disk-encryption-overview.md).
