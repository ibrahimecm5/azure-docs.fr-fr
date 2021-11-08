---
title: Créer un instantané incrémentiel
description: Découvrez les instantanés incrémentiels pour les disques managés, et notamment comment les créer avec le Portail Azure, le module Azure PowerShell et Azure Resource Manager.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell, ignite-fall-2021
ms.openlocfilehash: ba03ec11522ea5a4e4a011d1e62fa09b25aec749
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022293"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Création d’un instantané incrémentiel pour les disques managés

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Restrictions

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez utiliser l’interface de ligne de commande Azure pour créer un instantané incrémentiel. Vous devez disposer de la dernière version de l’interface de ligne de commande Azure. Consultez les articles suivants pour savoir comment [installer](/cli/azure/install-azure-cli) ou [mettre à jour](/cli/azure/update-azure-cli) l’interface de ligne de commande Azure.

Le script suivant crée un instantané incrémentiel d’un disque particulier :

```azurecli
# Declare variables
diskName="yourDiskNameHere"
resourceGroupName="yourResourceGroupNameHere"
snapshotName="desiredSnapshotNameHere"

# Get the disk you need to backup
yourDiskID=$(az disk show -n $diskName -g $resourceGroupName --query "id" --output tsv)

# Create the snapshot
az snapshot create -g $resourceGroupName -n $snapshotName --source $yourDiskID --incremental true
```

Vous pouvez identifier des instantanés incrémentiels à partir du même disque avec la propriété `SourceResourceId` des instantanés. `SourceResourceId` est l’ID de ressource Azure Resource Manager du disque parent.

Vous pouvez utiliser `SourceResourceId` pour créer une liste de tous les instantanés associés à un disque particulier. Remplacez `yourResourceGroupNameHere` par votre valeur, puis utilisez l’exemple suivant pour lister vos instantanés incrémentiels existants :


```azurecli
# Declare variables and create snapshot list
subscriptionId="yourSubscriptionId"
resourceGroupName="yourResourceGroupNameHere"
diskName="yourDiskNameHere"

az account set --subscription $subscriptionId

diskId=$(az disk show -n $diskName -g $resourceGroupName --query [id] -o tsv)

az snapshot list --query "[?creationData.sourceResourceId=='$diskId' && incremental]" -g $resourceGroupName --output table
```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Vous pouvez utiliser le module Azure PowerShell pour créer un instantané incrémentiel. Vous devez utiliser la dernière version du module Azure PowerShell. La commande suivante vous permet de l’installer ou de mettre à jour votre installation existante avec la dernière version :

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Une fois la dernière version installée, connectez-vous à votre session PowerShell à l'aide de la commande `Connect-AzAccount`.

Pour créer un instantané incrémentiel avec Azure PowerShell, définissez la configuration avec [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) avec le paramètre `-Incremental`, puis passez-la en tant que variable à [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) par le biais du paramètre `-Snapshot`.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Vous pouvez identifier des instantanés incrémentiels à partir du même disque avec les propriétés `SourceResourceId` et `SourceUniqueId` des instantanés. `SourceResourceId` est l’ID de ressource Azure Resource Manager du disque parent. `SourceUniqueId` est la valeur héritée de la propriété `UniqueId` du disque. Si vous supprimez un disque et que vous créez ensuite un disque portant le même nom, la valeur de la propriété `UniqueId` change.

Vous pouvez utiliser `SourceResourceId` et `SourceUniqueId` pour créer une liste de tous les instantanés associés à un disque particulier. Remplacez `yourResourceGroupNameHere` par votre valeur, puis utilisez l’exemple suivant pour lister vos instantanés incrémentiels existants :

```PowerShell
$resourceGroupName = "yourResourceGroupNameHere"
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[Portail](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

Vous pouvez également utiliser des modèles Azure Resource Manager pour créer un instantané incrémentiel. Vous devez vous assurer que le paramètre apiVersion est défini sur **2019-03-01** et que la propriété incremental est définie sur true. L'extrait de code suivant montre comment créer un instantané incrémentiel à l'aide de modèles Resource Manager :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```
---

## <a name="cross-region-snapshot-copy-preview"></a>Copie de capture instantanée entre régions (version préliminaire)

Vous pouvez utiliser l’option CopyStart (préversion) pour initier une copie d’instantanés incrémentiels d’une région à une région de votre choix. Azure gère le processus de copie des instantanés incrémentiels et garantit que seules les modifications Delta depuis le dernier instantané sont copiées dans la région cible, ce qui réduit l’encombrement des données. Les clients peuvent vérifier la progression de la copie afin de savoir quand un instantané cible est prêt à restaurer les disques dans la région cible. Vous pouvez utiliser ce processus pour copier des instantanés vers un autre abonnement pour la rétention à long terme. Vous pouvez également l’utiliser pour copier des instantanés dans la même région, pour vous assurer que les instantanés sont entièrement renforcés sur le [stockage redondant](disks-redundancy.md#zone-redundant-storage-for-managed-disks) dans une zone et s’assurer que les captures instantanées sont disponibles en cas de défaillance zonale.

:::image type="content" source="media/disks-incremental-snapshots/cross-region-snapshot.png" alt-text="Diagramme de la copie inter-région d’Azure orchestrée des instantanés incrémentiels via l’option de clonage." lightbox="media/disks-incremental-snapshots/cross-region-snapshot.png":::

### <a name="pre-requisites"></a>Conditions préalables

Vous devez activer la fonctionnalité de votre abonnement pour pouvoir utiliser la fonctionnalité en version préliminaire. Utilisez la commande suivante pour inscrire la fonctionnalité :

```azurecli
az feature register --namespace Microsoft.Compute --name CreateOptionClone
```

L’inscription peut prendre quelques minutes, vous pouvez utiliser la commande suivante pour vérifier son état :

```azurecli
az feature show --namespace Microsoft.Compute --name CreateOptionClone
```

### <a name="restrictions"></a>Restrictions

- La copie d’instantané entre régions est actuellement disponible uniquement dans la région USA Est 2 et USA Centre-Ouest.
- Vous devez utiliser la version 2020-12-01 ou une version plus récente de l’API REST Azure Compute.

### <a name="get-started"></a>Bien démarrer

```azurecli
subscriptionId=<yourSubscriptionID>
resourceGroupName=<yourResourceGroupName>
name=<targetSnapshotName>
sourceSnapshotResourceId=<sourceSnapshotResourceId>
targetRegion=<validRegion>

az login
az account set --subscription $subscriptionId
az group deployment create -g $resourceGroupName \
--template-uri https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/CrossRegionCopyOfSnapshots/CopyStartIncrementalSnapshots.json \
--parameters "name=$name" "sourceSnapshotResourceId=$sourceSnapshotResourceId" "targetRegion=$targetRegion"
az resource show -n $name -g $resourceGroupName --namespace Microsoft.Compute --resource-type snapshots --api-version 2020-12-01 --query [properties.completionPercent] -o tsv
```

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez voir un exemple de code illustrant la capacité différentielle des instantanés incrémentiels à l’aide de .NET, consultez [Copy Azure Managed Disks backups to another region with differential capability of incremental snapshots](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots) (Copier des sauvegardes de disques managés Azure dans une autre région avec la fonctionnalité différentielle des instantanés incrémentiels).
