---
title: Activer le bursting de disque à la demande
description: Activez le bursting de disque à la demande sur votre disque managé.
author: roygara
ms.author: rogarana
ms.date: 11/09/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 27a832f7f02fd10cde7f3c9f603f38d0d5e466b6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136187"
---
# <a name="enable-on-demand-bursting"></a>Activer le bursting à la demande

Deux modèles de bursting sont disponibles pour les disques SSD Premium : le bursting basé sur les crédits et le bursting à la demande. Cet article explique comment basculer vers le bursting à la demande. Pour les disques qui utilisent le modèle à la demande, le bursting peut dépasser les capacités cibles provisionnées à l’origine. Le bursting à la demande se produit aussi souvent que l’exige la charge de travail, jusqu’à ce que la capacité cible maximale soit atteinte. Le bursting à la demande entraîne des frais supplémentaires.

Pour plus d’informations sur le bursting de disque, consultez [Mode rafale des disques managés](disk-bursting.md). 

Pour les cibles de rafale max sur chaque disque pris en charge, consultez [Objectifs d’évolutivité et de performances pour les disques de machine virtuelle](disks-scalability-targets.md#premium-ssd-managed-disks-per-disk-limits).

> [!IMPORTANT]
> Vous n’avez pas besoin de suivre les étapes décrites dans cet article pour utiliser le bursting basé sur les crédits. Le bursting basé sur les crédits est activé par défaut sur tous les disques éligibles.

Avant d’activer le bursting à la demande, vous devez comprendre les points suivants :

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Disponibilité régionale

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Bien démarrer

Le bursting à la demande peut être activé à l’aide du module Azure PowerShell, d’Azure CLI ou de modèles Azure Resource Manager. Les exemples suivants montrent comment créer un disque avec pour lequel le bursting à la demande est activé et comment activer le bursting à la demande sur des disques existants.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Les cmdlets de bursting à la demande sont disponibles dans la version 5.5.0 et les versions ultérieures du module Az. Vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com/).
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Créer un disque de données vide avec bursting à la demande

Le bursting à la demande peut être activé uniquement sur un disque managé de taille supérieure à 512 Gio. Remplacez les paramètres `<myResourceGroupDisk>` et `<myDataDisk>`, puis exécutez le script suivant pour créer un SSD Premium avec bursting à la demande :

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Activer le bursting à la demande sur un disque existant

Le bursting à la demande peut être activé uniquement sur un disque managé de taille supérieure à 512 Gio. Remplacez les paramètres `<myResourceGroupDisk>` et `<myDataDisk>`, puis exécutez cette commande pour activer le bursting à la demande sur un disque existant :

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Les cmdlets de bursting à la demande sont disponibles dans la version 2.19.0 et les versions ultérieures du [module Azure CLI](/cli/azure/install-azure-cli). Vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com/).

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Créer et attacher un disque de données avec bursting à la demande

Le bursting à la demande peut être activé uniquement sur un disque managé de taille supérieure à 512 Gio. Remplacez les paramètres `<yourDiskName>`, `<yourResourceGroup>` et `<yourVMName>`, puis exécutez les commandes suivantes pour créer un SSD Premium avec bursting à la demande :

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Activer le bursting à la demande sur un disque existant - CLI

Le bursting à la demande peut être activé uniquement sur un disque managé de taille supérieure à 512 Gio. Remplacez les paramètres `<myResourceGroupDisk>` et `<yourDiskName>`, puis exécutez cette commande pour activer le bursting à la demande sur un disque existant :

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Avec l’API de disque `2020-09-30`, vous pouvez activer le bursting à la demande sur des disques SSD Premium existants ou nouvellement créés de taille supérieure à 512 Gio. L’API `2020-09-30` a introduit la nouvelle propriété `burstingEnabled`. Par défaut, cette propriété a la valeur false. L’exemple de modèle suivant crée un SSD Premium de 1 Tio dans la région USA Centre-Ouest avec bursting de disque activé :

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment obtenir des insights sur vos ressources utilisant le bursting, consultez [Métriques de bursting de disque](disks-metrics.md).
