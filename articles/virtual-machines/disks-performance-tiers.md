---
title: Modifier les performances des disques managés Azure – CLI/PowerShell
description: Découvrez comment modifier les niveaux de performances des disques managés existants à l’aide du module Azure PowerShell ou de l’interface Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ebc655cc772f0d05ef44a453076d5e17d217a54d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038381"
---
# <a name="change-your-performance-tier-without-downtime-using-the-azure-powershell-module-or-the-azure-cli"></a>Changer votre niveau de performance sans temps d’arrêt à l’aide du module Azure PowerShell ou de l’interface Azure CLI

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Restrictions

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="prerequisites"></a>Prérequis
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Installez la dernière version d’[Azure CLI](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure avec [az login](/cli/azure/reference-index).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Installez la dernière [version d’Azure PowerShell](/powershell/azure/install-az-ps) et connectez-vous à un compte Azure à l’aide de `Connect-AzAccount`.

---

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Créez un disque de données vide avec un niveau supérieur au niveau de base de référence
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Créer un disque de système d’exploitation avec un niveau supérieur au niveau de base d’une image de la Place de marché Azure

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk-without-downtime"></a>Mettre à jour le niveau d’un disque sans temps d’arrêt

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Mettez à jour le niveau d’un disque même s’il est attaché à une machine virtuelle en cours d’exécution

    ```azurecli
    resourceGroupName=<yourResourceGroupNameHere>
    diskName=<yourDiskNameHere>
    performanceTier=<yourDesiredPerformanceTier>

    az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Mettez à jour le niveau d’un disque même s’il est attaché à une machine virtuelle en cours d’exécution

    ```azurepowershell
    $resourceGroupName='yourResourceGroupName'
    $diskName='yourDiskName'
    $performanceTier='P1'

    $diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

    Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
    ```
---

## <a name="show-the-tier-of-a-disk"></a>Afficher le niveau d’un disque

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin de redimensionner un disque pour tirer parti de niveaux de performance supérieurs, consultez les articles suivants :

- [Étendre des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI](linux/expand-disks.md)
- [Développer un disque managé attaché à une machine virtuelle Windows](windows/expand-os-disk.md)
