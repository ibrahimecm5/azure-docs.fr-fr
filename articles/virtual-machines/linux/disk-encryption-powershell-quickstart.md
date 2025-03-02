---
title: Créer et chiffrer une machine virtuelle Linux avec Azure PowerShell
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser Azure PowerShell pour créer et chiffrer une machine virtuelle Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6b8286ca8cac5ec2a16015d2171f224a60a58fb3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596806"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Démarrage rapide : Créer et chiffrer une machine virtuelle Linux dans Azure avec Azure PowerShell

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande PowerShell ou dans des scripts. Ce guide de démarrage rapide vous montre comment utiliser le module Azure PowerShell pour créer une machine virtuelle Linux, créer un coffre de clés pour le stockage des clés de chiffrement, et chiffrer la machine virtuelle. Ce démarrage rapide utilise l’image du marketplace Ubuntu 16.04 LTS fournie par Canonical et une taille de machine virtuelle Standard_D2S_V3. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées :

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle Azure avec [New-AzVM](/powershell/module/az.compute/new-azvm), en lui passant l’objet de configuration de machine virtuelle que vous avez créé ci-dessus.

```powershell-interactive
$cred = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:18.04-LTS:latest -Size Standard_D2S_V3
```

Quelques minutes sont nécessaires pour le déploiement de votre machine virtuelle. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Créer un coffre de clés configuré pour les clés de chiffrement

Azure Disk Encryption stocke sa clé de chiffrement dans Azure Key Vault. Créez un coffre de clés avec [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Pour activer le stockage des clés de chiffrement dans Key Vault, utilisez le paramètre -EnabledForDiskEncryption.

> [!Important]
> Chaque coffre de clés doit avoir un nom unique dans Azure. Dans les exemples ci-dessous, remplacez \<your-unique-keyvault-name\> par le nom de votre choix.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Chiffrer la machine virtuelle

Chiffrez votre machine virtuelle avec [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension requiert des valeurs de votre objet de coffre de clés. Vous pouvez obtenir ces valeurs en passant le nom unique de votre coffre de clés à [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Après quelques minutes, le processus retourne les éléments suivants :

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Vous pouvez vérifier le processus de chiffrement en exécutant [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Lorsque le chiffrement est activé, vous verrez les éléments suivants dans la sortie retournée :

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une machine virtuelle, créé un coffre de clés qui a été activé pour les clés de chiffrement, et vous avez chiffré la machine virtuelle.  Passez à l’article suivant afin d’en savoir plus sur Azure Disk Encryption pour les machines virtuelles Linux.

> [!div class="nextstepaction"]
> [Vue d’ensemble d’Azure Disk Encryption](disk-encryption-overview.md)
