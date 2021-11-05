---
title: Définir le niveau d’accès d’un blob
titleSuffix: Azure Storage
description: Découvrez comment spécifier le niveau d’accès d’un blob lorsque vous le chargez, ou comment changer le niveau d’accès d’un blob existant.
author: tamram
ms.author: tamram
ms.date: 10/25/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2e6a5cc63dceff6145f66fd5a23e4ffb89a3817f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097565"
---
# <a name="set-a-blobs-access-tier"></a>Définir le niveau d’accès d’un blob

Vous pouvez définir le niveau d’accès d’un blob de l’une des manières suivantes :

- En définissant le niveau d’accès en ligne par défaut (Chaud ou Froid) pour le compte de stockage. Les blobs du compte héritent de ce niveau d’accès, sauf si vous remplacez explicitement le paramètre pour un blob individuel.
- En définissant explicitement le niveau d’un blob lors du chargement. Vous pouvez créer un blob dans le niveau Chaud, Froid ou Archive.
- En changeant le niveau d’un blob existant avec une opération Définir le niveau du blob, généralement pour passer d’un niveau plus chaud à un niveau plus froid.
- En copiant un blob avec une opération Copier l’objet blob, généralement pour passer d’un niveau plus froid à un niveau plus chaud.

Cet article explique comment gérer un blob dans un niveau d’accès en ligne (Chaud ou Froid). Pour plus d’informations sur la façon de déplacer un blob vers le niveau Archive, consultez [Archiver un blob](archive-blob.md). Pour plus d’informations sur la façon de réhydrater un blob à partir du niveau Archive, consultez [Réhydrater un blob archivé sur un niveau en ligne](archive-rehydrate-to-online-tier.md).

Pour plus d’informations sur les niveaux d’accès des blobs, consultez [Niveaux d’accès chaud, froid et archive pour les données blob](access-tiers-overview.md).

## <a name="set-the-default-access-tier-for-a-storage-account"></a>Définir le niveau d’accès par défaut d’un compte de stockage

Le paramètre de niveau d’accès par défaut pour un compte de stockage v2 universel détermine dans quel niveau en ligne un nouveau blob est créé par défaut. Vous pouvez définir le niveau d’accès par défaut pour un compte de stockage v2 universel au moment où vous créez le compte ou en mettant à jour la configuration d’un compte existant.

Lorsque vous changez le paramètre de niveau d’accès par défaut pour un compte de stockage v2 universel existant, le changement s’applique à tous les blobs du compte pour lesquels un niveau d’accès n’a pas été défini explicitement. Le changement du niveau d’accès par défaut peut avoir un impact sur la facturation. Pour obtenir des détails, consultez [Paramètre de niveau d’accès du compte par défaut](access-tiers-overview.md#default-account-access-tier-setting).

#### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour définir le niveau d’accès par défaut pour un compte de stockage au moment de sa création dans le portail Azure, suivez ces étapes :

1. Accédez à la page **Comptes de stockage** et sélectionnez le bouton **Créer**.
1. Renseignez l’onglet **Informations de base**.
1. Sous l’onglet **Avancé**, sous **Stockage Blob**, définissez le **Niveau d’accès** sur *Chaud* ou *Froid*. Le paramètre par défaut est *Chaud*.
1. Sélectionnez **Vérifier + créer** pour valider vos paramètres et créer votre compte de stockage.

    :::image type="content" source="media/access-tiers-online-manage/set-default-access-tier-create-portal.png" alt-text="Capture d’écran montrant comment définir le niveau d’accès par défaut lors de la création d’un compte de stockage.":::

Pour mettre à jour le niveau d’accès par défaut pour un compte de stockage existant dans le portail Azure, suivez ces étapes :

1. Accédez au compte de stockage dans le portail Azure.
1. Sous **Paramètres**, sélectionnez **Configuration**.
1. Repérez le paramètre **Niveau d’accès (par défaut)** et sélectionnez *Chaud* ou *Froid*. Le paramètre par défaut est *Chaud*, si vous n’avez pas déjà défini cette propriété.
1. Enregistrez vos modifications.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour changer le paramètre de niveau d’accès par défaut pour un compte de stockage avec PowerShell, appelez la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount), en spécifiant le nouveau niveau d’accès par défaut.

```azurepowershell
$rgName = <resource-group>
$accountName = <storage-account>

# Change the storage account tier to Cool
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Cool
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour changer le paramètre de niveau d’accès par défaut pour un compte de stockage avec PowerShell, appelez la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount), en spécifiant le nouveau niveau d’accès par défaut.

```azurecli
# Change the storage account tier to Cool
az storage account update \
    --resource-group <resource-group> \
    --name <storage-account> \
    --access-tier Cool
```

---

## <a name="set-a-blobs-tier-on-upload"></a>Définir le niveau d’un blob lors du chargement

Lorsque vous chargez un blob dans le Stockage Azure, vous avez le choix entre deux options pour définir le niveau du blob lors du chargement :

- Vous pouvez spécifier explicitement le niveau dans lequel le blob sera créé. Ce paramètre remplace le niveau d’accès par défaut pour le compte de stockage. Vous pouvez définir le niveau d’un blob ou d’un ensemble de blobs lors du chargement sur Chaud, Froid ou Archive.
- Vous pouvez charger un blob sans spécifier de niveau. Dans ce cas, le blob sera créé dans le niveau d’accès par défaut spécifié pour le compte de stockage (Chaud ou Froid).

Les sections suivantes décrivent comment spécifier qu’un blob est chargé sur le niveau Chaud ou Froid. Pour plus d’informations sur l’archivage d’un blob lors du chargement, consultez [Archiver des blobs lors du chargement](archive-blob.md#archive-blobs-on-upload).

### <a name="upload-a-blob-to-a-specific-online-tier"></a>Charger un blob sur un niveau en ligne spécifique

Pour créer un blob dans le niveau Chaud ou Froid, spécifiez ce niveau lorsque vous créez le blob. Le niveau d’accès spécifié lors du chargement remplace le niveau d’accès par défaut pour le compte de stockage.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour charger un blob ou un ensemble de blobs sur un niveau spécifique à partir du portail Azure, suivez ces étapes :

1. Accédez au conteneur cible.
1. Cliquez sur le bouton **Charger**.
1. Sélectionnez le ou les fichiers à charger.
1. Développez la section **Avancé** et définissez le **Niveau d’accès** sur *Chaud* ou *Froid*.
1. Cliquez sur le bouton **Charger**.

    :::image type="content" source="media/access-tiers-online-manage/upload-blob-to-online-tier-portal.png" alt-text="Capture d’écran montrant comment charger des blobs sur un niveau en ligne dans le portail Azure.":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour charger un blob ou un ensemble de blobs sur un niveau spécifique avec PowerShell, appelez la commande [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent), comme montré dans l’exemple suivant. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurepowershell
$rgName = <resource-group>
$storageAccount = <storage-account>
$containerName = <container>

# Get context object
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Create new container.
New-AzStorageContainer -Name $containerName -Context $ctx

# Upload a single file named blob1.txt to the Cool tier.
Set-AzStorageBlobContent -Container $containerName `
    -File "blob1.txt" `
    -Blob "blob1.txt" `
    -Context $ctx `
    -StandardBlobTier Cool

# Upload the contents of a sample-blobs directory to the Cool tier, recursively.
Get-ChildItem -Path "C:\sample-blobs" -File -Recurse | 
    Set-AzStorageBlobContent -Container $containerName `
        -Context $ctx `
        -StandardBlobTier Cool
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour charger un blob sur un niveau spécifique avec Azure CLI, appelez la commande [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload), comme montré dans l’exemple suivant. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Cool \
    --auth-mode login
```

Pour charger un ensemble de blobs sur un niveau spécifique avec Azure CLI, appelez la commande [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch), comme montré dans l’exemple suivant. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Cool \
    --auth-mode login
```

---

### <a name="upload-a-blob-to-the-default-tier"></a>Charger un blob sur le niveau par défaut

Les comptes de stockage disposent d’un paramètre de niveau d’accès par défaut qui indique le niveau en ligne dans lequel un nouveau blob est créé. Le paramètre de niveau d’accès par défaut peut être défini sur chaud ou froid. Le comportement de ce paramètre est légèrement différent selon le type de compte de stockage :

- Le niveau d’accès par défaut pour un nouveau compte de stockage v2 universel est défini sur le niveau Chaud par défaut. Vous pouvez modifier le paramètre de niveau d’accès par défaut lorsque vous créez un compte de stockage ou après sa création.
- Quand vous créez un compte de stockage Blob hérité, vous devez spécifier le paramètre du niveau d’accès par défaut sur Chaud ou Froid. Vous pouvez changer le paramètre du niveau d’accès par défaut pour le compte de stockage après sa création.

Tout objet blob ne disposant pas d’un niveau explicitement attribué déduit le niveau à partir du paramètre de niveau d’accès du compte par défaut. Vous pouvez déterminer si le niveau d’accès d’un blob est déduit en utilisant le portail Azure, PowerShell ou Azure CLI.

#### <a name="portal"></a>[Portail](#tab/azure-portal)

Si le niveau d’accès d’un objet blob est déduit du paramètre de niveau d’accès au compte par défaut, le portail Azure affiche le niveau d’accès comme **Chaud (inféré)** ou **Froid (inféré)** .

:::image type="content" source="media/access-tiers-online-manage/default-access-tier-portal.png" alt-text="Capture d’écran montrant les blobs avec le niveau d’accès par défaut dans le portail Azure.":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour déterminer le niveau d’accès d’un blob et s’il est déduit d’Azure PowerShell, récupérez le blob, puis vérifiez ses propriétés **AccessTier** et **AccessTierInferred**.

```azurepowershell
$rgName = "<resource-group>"
$storageAccount = "<storage-account>"
$containerName = "<container>"
$blobName = "<blob>"

# Get the storage account context.
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Get the blob from the service.
$blob = Get-AzStorageBlob -Context $ctx -Container $containerName -Blob $blobName

# Check the AccessTier and AccessTierInferred properties.
# If the access tier is inferred, that property returns true.
$blob.BlobProperties.AccessTier
$blob.BlobProperties.AccessTierInferred
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour déterminer le niveau d’accès d’un blob et s’il est déduit d’Azure CLI, récupérez le blob, puis vérifiez ses propriétés **blobTier** et **blobTierInferred**.

```azurecli
az storage blob show \
    --container-name <container> \
    --name <blob> \
    --account-name <storage-account> \
    --query '[properties.blobTier, properties.blobTierInferred]' \
    --output tsv \
    --auth-mode login 
```

---

## <a name="move-a-blob-to-a-different-online-tier"></a>Déplacer un blob dans un autre niveau en ligne

Vous pouvez changer le niveau d’un blob existant de l’une des deux manières suivantes :

- En appelant l’opération [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier), soit directement, soit via une stratégie de [gestion du cycle de vie](access-tiers-overview.md#blob-lifecycle-management).
- En appelant l’opération [Copier l’objet blob](/rest/api/storageservices/copy-blob) pour copier un objet blob d’un niveau à un autre. Dans ce cas, le blob source reste dans le niveau d’origine et un nouveau blob est créé dans le niveau cible.

Pour plus d’informations sur chacune de ces options, consultez [Définition ou changement du niveau d’un blob](access-tiers-overview.md#setting-or-changing-a-blobs-tier).

Utilisez PowerShell, Azure CLI ou l’une des bibliothèques de client Stockage Azure pour déplacer un blob dans un autre niveau.

### <a name="change-a-blobs-tier"></a>Changer le niveau d’un blob

Lorsque vous changez le niveau d’un blob, vous déplacez ce blob et toutes ses données dans le niveau cible. L’appel de [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier) est généralement la meilleure option quand vous changez le niveau d’un blob pour passer d’un niveau plus chaud à un niveau pus froid.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Pour changer le niveau d’un blob afin de passer de Chaud à Froid dans le portail Azure, suivez ces étapes :

1. Accédez au blob pour lequel vous souhaitez changer le niveau.
1. Sélectionnez le blob, puis le bouton **Changer de niveau**.
1. Dans la boîte de dialogue **Changer de niveau**, sélectionnez le niveau cible.
1. Sélectionnez le bouton **Enregistrer**.

    :::image type="content" source="media/access-tiers-online-manage/change-blob-tier-portal.png" alt-text="Capture d’écran montrant comment changer le niveau d’un blob dans le portail Azure":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour changer le niveau d’un blob afin de passer de Chaud à Froid avec PowerShell, utilisez la propriété **BlobClient** du blob pour retourner une référence .NET au blob, puis appelez la méthode **SetAccessTier** sur cette référence. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to Cool.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Cool", $null, "Standard")
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour changer le niveau d’un blob afin de passer de Chaud à Froid avec Azure CLI, appelez la commande [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier). N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Cool \
    --auth-mode login
```

---

### <a name="copy-a-blob-to-a-different-online-tier"></a>Copier un blob dans un autre niveau en ligne

Lorsque vous copiez un blob dans un autre niveau, vous déplacez ce blob et toutes ses données dans le niveau cible. L’appel de [Copier l’objet blob](/rest/api/storageservices/copy-blob) est recommandé pour la plupart des scénarios dans lesquels vous déplacez un blob du niveau Froid vers un niveau Chaud, ou réhydratez un blob depuis le niveau Archive.

# <a name="portal"></a>[Portail](#tab/azure-portal)

N/A

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour copier un blob d’un niveau Froid vers un niveau Chaud avec PowerShell, appelez la commande [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) et spécifiez le niveau cible. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

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
    -Context $ctx
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour copier un blob d’un niveau Froid vers un niveau Chaud avec Azure CLI, appelez la commande [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) et spécifiez le niveau cible. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --auth-mode login
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Comment gérer le niveau d’accès par défaut d’un compte de stockage Azure](../common/manage-account-default-access-tier.md)
- [Réalimenter un objet blob archivé dans un niveau en ligne](archive-rehydrate-to-online-tier.md)
