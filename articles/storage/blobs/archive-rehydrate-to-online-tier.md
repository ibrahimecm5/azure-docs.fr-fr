---
title: Réalimenter un objet blob archivé dans un niveau en ligne
titleSuffix: Azure Storage
description: Avant de pouvoir lire un objet blob qui se trouve dans le niveau archive, vous devez le réhydrater au niveau chaud ou froid. Vous pouvez réhydrater un objet blob soit en le copiant du niveau archive vers un niveau en ligne, soit en remplaçant son niveau archive par chaud ou froid.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/01/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: 0e24c058239d57cba1c66ebff06ba9d482bcb594
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433191"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Réalimenter un objet blob archivé dans un niveau en ligne

Pour lire un objet blob qui se trouve dans le niveau archive, vous devez d’abord le réhydrater vers un niveau en ligne (chaud ou froid). Vous pouvez réhydrater un blob d’une de ces deux manières :

- En le copiant dans un nouvel objet blob dans le niveau chaud ou froid avec l’opération [Copier l’objet blob](/rest/api/storageservices/copy-blob). Microsoft recommande cette option pour la plupart des scénarios.
- En remplaçant son niveau archive par chaud ou froid avec l’opération [Définir le niveau d’objet blob](/rest/api/storageservices/set-blob-tier).

Lorsque vous réhydratez un objet blob, vous pouvez spécifier la priorité de l’opération : standard ou élevée. Une opération de réhydratation de priorité standard peut prendre jusqu’à 15 heures. Une opération de priorité élevée est prioritaire par rapport aux demandes de priorité standard. Elle peut être effectuée en moins d’une heure pour les objets de taille inférieure à 10 Go. Vous pouvez remplacer la priorité de réhydratation *standard* par *élevée* pendant que l’opération est en attente.

Vous pouvez configurer Azure Event Grid pour déclencher un événement lorsque la réhydratation est terminée et exécuter le code d’application en réponse. Pour savoir comment gérer un événement qui exécute une fonction Azure quand l’opération de réhydratation de blob est terminée, consultez [Exécuter une fonction Azure en réponse à un événement de réhydratation de blob](archive-rehydrate-handle-event.md).

Pour plus d’informations sur la réhydratation d’un objet blob, consultez [Réhydratation d’objets blob à partir du niveau archive](archive-rehydrate-overview.md).

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>Réactiver un objet blob à l’aide d’une opération de copie

Pour réhydrater un objet blob à partir du niveau archive en le copiant sur un niveau en ligne, utilisez PowerShell, Azure CLI ou l’une des bibliothèques clientes de Stockage Azure. Gardez à l’esprit que lorsque vous copiez un blob archivé sur un niveau en ligne, les blob source et de destination doivent avoir des noms différents.

Une fois l’opération de copie terminée, l’objet blob de destination s’affiche dans le niveau archive. Le blob de destination est ensuite réhydraté au niveau en ligne que vous avez spécifié dans l’opération de copie. Lorsque le blob de destination est entièrement réhydraté, il devient disponible dans le nouveau niveau en ligne.

Les exemples suivants montrent comment copier un blob archivé avec PowerShell ou Azure CLI.

### <a name="portal"></a>[Portail](#tab/azure-portal)

N/A

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour copier un blob archivé sur un niveau en ligne avec PowerShell, appelez la commande [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) et spécifiez le niveau cible et la priorité de réhydratation. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$srcContainerName = "<source-container>"
$destContainerName = "<dest-container>"
$srcBlobName = "<source-blob>"
$destBlobName = "<dest-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Copy the source blob to a new destination blob in Hot tier with Standard priority.
Start-AzStorageBlobCopy -SrcContainer $srcContainerName `
    -SrcBlob $srcBlobName `
    -DestContainer $destContainerName `
    -DestBlob $destBlobName `
    -StandardBlobTier Hot `
    -RehydratePriority Standard `
    -Context $ctx
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour copier un blob archivé sur un niveau en ligne avec Azure CLI, appelez la commande [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) et spécifiez le niveau cible et la priorité de réhydratation. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --rehydrate-priority standard \
    --auth-mode login
```

---

## <a name="rehydrate-a-blob-by-changing-its-tier"></a>Réactiver un objet blob en modifiant son niveau

Pour réhydrater un objet blob en remplaçant son niveau archive par chaud ou froid, utilisez le portail Azure, PowerShell ou Azure CLI.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour remplacer le niveau archive d’un objet blob par chaud ou froid dans le portail Azure, procédez comme suit :

1. Localisez le blob à réhydrater dans le portail Azure.
1. Sélectionnez le bouton **Plus** à droite de la page.
1. Sélectionnez **Modifier le niveau**.
1. Sélectionnez le niveau d'accès cible à partir de la liste déroulante **Niveau d'accès**.
1. À partir de la liste déroulante **Priorité de réactivation**, sélectionnez la priorité de réhydratation souhaitée. Gardez à l’esprit que la définition de la priorité de réhydratation sur *Élevée* entraîne généralement une réhydratation plus rapide, mais également un coût plus élevé.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="Capture d’écran montrant comment réhydrater un objet blob à partir du niveau archive dans le portail Azure ":::

1. Sélectionnez le bouton **Enregistrer**.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour remplacer le niveau archive d’un objet blob par chaud ou froid avec PowerShell, utilisez la propriété **BlobClient** de l’objet blob pour retourner une référence .NET à l’objet blob, puis appelez la méthode **SetAccessTier** sur cette référence. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to Hot with Standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "Standard")
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour remplacer le niveau archive d’un objet blob par chaud ou froid avec Azure CLI, appelez la commande [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier). N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --rehydrate-priority Standard \
    --auth-mode login
```

---

## <a name="bulk-rehydrate-a-set-of-blobs"></a>Réhydrater en bloc un ensemble d’objets blob

Pour réhydrater un grand nombre d’objets BLOB à la fois, appelez l’opération [Blob Batch](/rest/api/storageservices/blob-batch) pour appeler [Set Blob Tier](/rest/api/storageservices/set-blob-tier) en tant qu’opération en bloc. Pour obtenir un exemple de code qui montre comment effectuer l’opération de traitement par lots, consultez [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/).

## <a name="check-the-status-of-a-rehydration-operation"></a>Vérifier l’état d’une opération de réhydratation

Lorsque le blob est en cours de réhydratation, vous pouvez vérifier son état et sa priorité de réhydratation à l’aide du portail Azure, de PowerShell ou d’Azure CLI. La propriété de l’état peut renvoyer *rehydrate-pending-to-hot* ou *rehydrate-pending-to-cool*, selon le niveau cible pour l’opération de réhydratation. La propriété de la priorité de réhydratation renvoie *Standard* ou *Élevé*.

N’oubliez pas que la réhydratation d’un blob archivé peut prendre jusqu’à 15 heures, et interroger de manière répétée l’état du blob pour déterminer si la réhydratation est terminée est inefficace. L’utilisation d’Azure Event Grid pour capturer l’événement qui se déclenche lorsque la réhydratation est terminée offre de meilleures performances et une optimisation des coûts. Pour savoir comment exécuter une fonction Azure quand un événement se déclenche sur la réhydratation de blob, consultez [Exécuter une fonction Azure en réponse à un événement de réhydratation de blob](archive-rehydrate-handle-event.md).

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour vérifier l’état et la priorité d’une opération de réhydratation en attente dans le portail Azure, affichez la boîte de dialogue **Modifier le niveau** pour le blob :

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="Capture d’écran montrant l’état de réhydratation d’un blob dans le portail Azure":::

Lorsque la réhydratation est terminée, vous pouvez voir dans le portail Azure que le blob entièrement réhydraté apparaît maintenant dans le niveau en ligne ciblé.

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="Capture d’écran montrant l’objet blob réhydraté dans le niveau froid et le blob de journal écrit par le gestionnaire d’événements":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour vérifier l’état et la priorité d’une opération de réhydratation en attente avec PowerShell, appelez la commande [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) et vérifiez les propriétés **ArchiveStatus** et **RehydratePriority** du blob. Si la réhydratation est une opération de copie, vérifiez ces propriétés sur le blob de destination. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurepowershell
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$rehydratingBlob.BlobProperties.ArchiveStatus
$rehydratingBlob.BlobProperties.RehydratePriority
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour vérifier l’état et la priorité d’une opération de réhydratation en attente avec Azure CLI, appelez la commande [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) et vérifiez les propriétés **rehydrationStatus** et **rehydratePriority** du blob de destination. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="change-the-rehydration-priority-of-a-pending-operation"></a>Modifier la priorité de réhydratation d’une opération en attente

Lorsqu’une opération de réhydratation de priorité standard est en attente, vous pouvez remplacer le paramètre de priorité de réhydratation *standard* d’un objet blob par *élevée* pour réhydrater cet objet blob plus rapidement.

Notez que le paramètre de priorité de réhydratation ne peut pas être abaissé du *niveau élevé* vers le *niveau standard* pour une opération en attente. Gardez également à l’esprit que la modification de la priorité de réhydratation peut avoir un impact sur la facturation. Pour plus d’informations, consultez [Réhydratation d’objets blob à partir du niveau archive](archive-rehydrate-overview.md).

### <a name="change-the-rehydration-priority-for-a-pending-set-blob-tier-operation"></a>Modifier la priorité de réhydratation pour une opération Définir le niveau d’objet blob en attente

Pour modifier la priorité de réhydratation pendant qu’une opération [Définir le niveau d’objet blob](/rest/api/storageservices/set-blob-tier) de priorité standard est en attente, utilisez le portail Azure, PowerShell, Azure CLI ou l’une des bibliothèques clientes de Stockage Azure.

#### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour modifier la priorité de réhydratation pour une opération en attente avec le portail Azure, procédez comme suit :

1. Accédez à l’objet blob dont vous souhaitez modifier la priorité de réhydratation, puis sélectionnez l’objet blob.
1. Sélectionnez le bouton **Modifier le niveau**.
1. Dans la boîte de dialogue **Modifier le niveau**, définissez le niveau d’accès sur le niveau d’accès en ligne cible pour l’objet blob de réhydratation (chaud ou froid). Le champ **État de l’archive** affiche le niveau en ligne cible.
1. Dans la liste déroulante **Priorité de réhydratation**, définissez la priorité sur *Élevée*.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/update-rehydration-priority-portal.png" alt-text="Capture d’écran montrant comment mettre à jour la priorité de réhydratation d’un objet blob de réhydratation dans le portail Azure":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour modifier la priorité de réactivation pour une opération en attente avec PowerShell, vérifiez que vous avez installé le module [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage), version 3.12.0 ou ultérieure. Ensuite, récupérez les propriétés de l’objet blob à partir du service. Cette étape est nécessaire pour s’assurer que vous disposez d’un objet avec les paramètres de propriété les plus récents. Enfin, utilisez la propriété **BlobClient** de l’objet blob pour retourner une référence .NET à l’objet blob, puis appelez la méthode **SetAccessTier** sur cette référence.

```azurepowershell
# Get the blob from the service.
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

# Verify that the current rehydration priority is Standard. 
if ($rehydratingBlob.BlobProperties.RehydratePriority -eq "Standard")
{
    # Change rehydration priority to High, using the same target tier.
    if ($rehydratingBlob.BlobProperties.ArchiveStatus -eq "rehydrate-pending-to-hot")
    {
        $rehydratingBlob.BlobClient.SetAccessTier("Hot", $null, "High")
        "Changing rehydration priority to High for blob moving to Hot tier."
    }
    
    if ($rehydratingBlob.BlobProperties.ArchiveStatus -eq "rehydrate-pending-to-cool")
    {
        $rehydratingBlob.BlobClient.SetAccessTier("Cool", $null, "High")
        "Changing rehydration priority to High for blob moving to Cool tier."
    }
}
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour modifier la priorité de réactivation pour une opération en attente avec Azure CLI, vérifiez d’abord que vous avez installé Azure CLI version 2.29.2 ou ultérieure. Pour plus d’informations sur l'installation d’Azure CLI, consultez [Comment installer Azure CLI](/cli/azure/install-azure-cli).

Ensuite, appelez la commande [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) avec le paramètre `--rehydrate-priority` défini sur *High*. Le niveau cible (chaud ou froid) doit être le même que celui spécifié à l’origine pour l’opération de réhydratation. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
# Update the rehydration priority for a blob moving to the Hot tier.
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Hot \
    --rehydrate-priority High \
    --auth-mode login

# Show the updated property values.
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

### <a name="change-the-rehydration-priority-for-a-pending-copy-blob-operation"></a>Modifier la priorité de réhydratation d’une opération Copier l’objet blob en attente

Lorsque vous réhydratez un objet blob en copiant l’objet blob archivé sur un niveau en ligne, Stockage Azure crée immédiatement l’objet blob de destination dans le niveau archive. L’objet blob de destination est ensuite réhydraté dans le niveau cible avec la priorité spécifiée sur l’opération de copie. Pour plus d’informations sur la réhydratation d’un objet blob archivé à l’aide d’une opération de copie, consultez [Copier un objet blob archivé sur un niveau en ligne](archive-rehydrate-overview.md#copy-an-archived-blob-to-an-online-tier).

Pour exécuter l’opération de copie du niveau archive vers un niveau en ligne avec une priorité Standard, utilisez PowerShell, Azure CLI ou l’une des bibliothèques clientes Stockage Azure. Pour plus d’informations, consultez [Réhydrater un objet blob à l’aide d’une opération de copie](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation). Ensuite, pour remplacer la priorité *standard* de la réhydratation en attente par *élevée*, appelez **Définir le niveau d’objet blob** sur l’objet blob de destination et spécifiez le niveau cible.

#### <a name="portal"></a>[Portail](#tab/azure-portal)

Après avoir lancé l’opération de copie, vous verrez dans le portail Azure que l’objet blob source et l’objet blob de destination se trouvent dans le niveau archive. L’objet blob de destination est en cours de réhydratation avec une priorité standard.

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-properties-portal-standard-priority.png" alt-text="Capture d’écran montrant l’objet blob de destination dans le niveau archive en cours de réhydratation avec une priorité standard":::

Pour modifier la priorité de réhydratation de l’objet blob de destination, procédez comme suit :

1. Sélectionnez l’objet blob de destination.
1. Sélectionnez le bouton **Modifier le niveau**.
1. Dans la boîte de dialogue **Modifier le niveau**, définissez le niveau d’accès sur le niveau d’accès en ligne cible pour l’objet blob de réhydratation (chaud ou froid). Le champ **État de l’archive** affiche le niveau en ligne cible.
1. Dans la liste déroulante **Priorité de réhydratation**, définissez la priorité sur *Élevée*.
1. Sélectionnez **Enregistrer**.

La page de propriétés de l’objet blob de destination indique à présent qu’il est en cours de réhydratation avec une priorité élevée.

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-properties-portal-high-priority.png" alt-text="Capture d’écran montrant l’objet blob de destination dans le niveau archive en cours de réhydratation avec une priorité élevée":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Une fois l’opération de copie lancée, vérifiez les propriétés de l’objet blob de destination. Vous verrez que l’objet blob de destination se trouve dans le niveau archive et qu’il est en cours de réhydratation avec une priorité standard.

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$destContainerName = "<container>"
$destBlobName = "<destination-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Get properties for the destination blob.
$destinationBlob = Get-AzStorageBlob -Container $destContainerName `
    -Blob $destBlobName `
    -Context $ctx

$destinationBlob.BlobProperties.AccessTier
$destinationBlob.BlobProperties.ArchiveStatus
$destinationBlob.BlobProperties.RehydratePriority
```

Ensuite, appelez la méthode **SetAccessTier** via PowerShell pour remplacer la priorité de réhydratation de l’objet blob de destination par *élevée*, comme décrit dans [Modifier la priorité de réhydratation d’une opération Définir le niveau d’objet blob en attente](#change-the-rehydration-priority-for-a-pending-set-blob-tier-operation). Le niveau cible (chaud ou froid) doit être le même que celui spécifié à l’origine pour l’opération de réhydratation. Consultez à nouveau les propriétés pour vérifier que l’objet blob est maintenant en cours de réhydratation avec une priorité élevée.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Une fois l’opération de copie lancée, vérifiez les propriétés de l’objet blob de destination. Vous verrez que l’objet blob de destination se trouve dans le niveau archive et qu’il est en cours de réhydratation avec une priorité standard.

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

Ensuite, appelez la commande [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) avec le paramètre `--rehydrate-priority` défini sur *Élevé*, comme décrit dans [Modifier la priorité de réhydratation d’une opération Définir le niveau d’objet blob en attente](#change-the-rehydration-priority-for-a-pending-set-blob-tier-operation). Le niveau cible (chaud ou froid) doit être le même que celui spécifié à l’origine pour l’opération de réhydratation. Consultez à nouveau les propriétés pour vérifier que l’objet blob est maintenant en cours de réhydratation avec une priorité élevée.

---

## <a name="see-also"></a>Voir aussi

- [Niveaux d’accès chaud, froid et archive pour les données d’objet blob](access-tiers-overview.md).
- [Vue d’ensemble de la réhydratation des objets blob à partir du niveau archive](archive-rehydrate-overview.md)
- [Exécution d’une fonction Azure en réponse à un événement de réactivation d’objets blob](archive-rehydrate-handle-event.md)
- [Réaction aux événements de stockage Blob](storage-blob-event-overview.md)
