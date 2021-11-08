---
title: Archiver un objet blob
titleSuffix: Azure Storage
description: Découvrez comment créer un objet blob dans le niveau archive ou déplacer un objet blob existant vers le niveau archive.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: d83cb752eaaa2ed3edf58d3d1f31d6d7c5af76cd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433134"
---
# <a name="archive-a-blob"></a>Archiver un objet blob

Le niveau archive est un niveau hors connexion pour le stockage des données blob rarement sollicitées. Le niveau archive dispose des plus faibles coûts de stockage, mais les coûts et la latence d’extraction de données sont plus élevés par rapport aux niveaux en ligne (chaud et froid). Les données doivent rester dans le niveau archive pendant au moins 180 jours ; sinon, elles sont soumises à des frais de suppression anticipée. Pour plus d’informations sur le niveau archive, consultez [Niveau d’accès archive](access-tiers-overview.md#archive-access-tier).

Tant qu’un objet blob se trouve dans le niveau archive, il ne peut être ni lu ni modifié. Pour lire ou télécharger un objet blob dans le niveau archive, vous devez d’abord le réhydrater sur un niveau en ligne, chaud ou froid. La réhydratation des données dans le niveau archive peut prendre jusqu’à 15 heures, en fonction de la priorité que vous spécifiez pour l’opération de réhydratation. Pour plus d’informations sur la réhydratation des objets blob, consultez [Vue d’ensemble de la réhydratation d’objets blob à partir du niveau archive](archive-rehydrate-overview.md).

> [!CAUTION]
> Un objet blob dans le niveau archive est hors connexion &mdash; il ne peut pas être lu ou modifié &mdash; tant qu’il n’est pas réhydraté. Le processus de réhydratation peut prendre plusieurs heures et comporte des coûts. Avant de déplacer des données vers le niveau archive, déterminez si la mise hors connexion des données blob peut affecter vos workflows.

Vous pouvez utiliser le Portail Azure, PowerShell, Azure CLI ou l’une des bibliothèques clientes Stockage Azure pour gérer l’archivage des données.

## <a name="archive-blobs-on-upload"></a>Archiver les objets blob lors de leur chargement

Pour archiver un ou plusieurs objets blob lors de leur chargement, créez l’objet blob directement dans le niveau archive.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour archiver un objet blob ou un ensemble d’objets blob lors de leur chargement à partir du Portail Azure, procédez comme suit :

1. Accédez au conteneur cible.
1. Cliquez sur le bouton **Charger**.
1. Sélectionnez le ou les fichiers à charger.
1. Développez la section **Avancé** et définissez le **Niveau d’accès** sur *Archive*.
1. Cliquez sur le bouton **Charger**.

    :::image type="content" source="media/archive-blob/upload-blobs-archive-portal.png" alt-text="Capture d’écran montrant le chargement d’objets blobs sur le niveau archive dans le Portail Azure":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour archiver un objet blob ou un ensemble d’objets blob lors de leur chargement avec PowerShell, appelez la commande [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent), comme montré dans l’exemple suivant. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurepowershell
$rgName = <resource-group>
$storageAccount = <storage-account>
$containerName = <container>

# Get context object
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Create new container.
New-AzStorageContainer -Name $containerName -Context $ctx

# Upload a single file named blob1.txt to the Archive tier.
Set-AzStorageBlobContent -Container $containerName `
    -File "blob1.txt" `
    -Blob "blob1.txt" `
    -Context $ctx `
    -StandardBlobTier Archive

# Upload the contents of a sample-blobs directory to the Archive tier, recursively.
Get-ChildItem -Path "C:\sample-blobs" -File -Recurse | 
    Set-AzStorageBlobContent -Container $containerName `
        -Context $ctx `
        -StandardBlobTier Archive
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour archiver un objet blob lors de son chargement avec Azure CLI, appelez la commande [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload), comme montré dans l’exemple suivant. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Archive \
    --auth-mode login
```

Pour archiver un ensemble d’objets blob lors de leur chargement avec Azure CLI, appelez la commande [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch), comme montré dans l’exemple suivant. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="archive-an-existing-blob"></a>Archiver un objet blob existant

Vous pouvez déplacer un objet blob existant vers le niveau archive de l’une des deux manières suivantes :

- Vous pouvez modifier le niveau d’un objet blob à l’aide de l’opération [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier). L’opération **Définir le niveau du blob** déplace un seul objet blob d’un niveau vers un autre.

    Gardez à l’esprit que lorsque vous déplacez un objet blob vers le niveau archive avec **Définir le niveau du blob**, vous ne pouvez pas lire ou modifier les données de l’objet blob tant que vous n’avez pas réhydraté l’objet blob. Si vous devez lire ou modifier les données de l’objet blob avant l’expiration de l’intervalle de suppression anticipée, envisagez d’utiliser une opération **Copier l’objet blob** pour créer une copie de l’objet blob dans le niveau archive.

- Vous pouvez copier un objet blob dans un niveau en ligne vers le niveau archive à l’aide de l’opération [Copier l’objet blob](/rest/api/storageservices/copy-blob). Vous pouvez appeler l’opération **Copier l’objet blob** pour copier un objet blob à partir d’un niveau en ligne (chaud ou froid) vers le niveau archive. L’objet blob source reste dans le niveau en ligne et vous pouvez continuer à lire ou modifier ses données dans le niveau en ligne.

### <a name="archive-an-existing-blob-by-changing-its-tier"></a>Archiver un objet blob existant en modifiant son niveau

Utilisez l’opération **Définir le niveau du blob** pour déplacer un objet blob du niveau chaud ou froid vers le niveau archive. L’opération **Définir le niveau du blob** est idéale pour les scénarios où vous n’avez pas besoin d’accéder aux données archivées avant l’expiration de l’intervalle de suppression anticipée.

L’opération **Définir le niveau du blob** modifie le niveau d’un seul objet blob. Pour déplacer un ensemble d’objets blob vers le niveau archive avec des performances optimales, Microsoft recommande d’effectuer une opération d’archivage en bloc. L’opération d’archivage en bloc envoie un lot d’appels **Définir le niveau du blob** au service dans une seule transaction. Pour plus d’informations, consultez [Archivage en bloc](#bulk-archive).  

#### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour déplacer un objet blob existant vers le niveau archive dans le Portail Azure, procédez comme suit :

1. Accédez au conteneur de l’objet blob.
1. Sélectionnez l’objet blob à archiver.
1. Sélectionnez le bouton **Modifier le niveau**.
1. Sélectionnez *Archiver* dans la liste déroulante **Niveau d’accès**.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/archive-blob/set-blob-tier-archive-portal.png" alt-text="Capture d’écran montrant la définition du niveau d’un objet blob sur archive dans le Portail Azure":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour remplacer le niveau archive d’un objet blob par chaud ou froid avec PowerShell, utilisez la propriété **BlobClient** de l’objet blob pour retourner une référence .NET à l’objet blob, puis appelez la méthode **SetAccessTier** sur cette référence. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

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

# Change the blob's access tier to Archive.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Archive", $null)
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour remplacer le niveau chaud ou froid d’un objet blob par archive avec Azure CLI, appelez la commande [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier). N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Archive \
    --auth-mode login
```

---

### <a name="archive-an-existing-blob-with-a-copy-operation"></a>Archiver un objet blob existant avec une opération de copie

Utilisez l’opération [Copier l’objet blob](/rest/api/storageservices/copy-blob) pour copier un objet blob au niveau chaud ou froid vers le niveau archive. L’objet blob source reste au niveau chaud ou froid, tandis que l’objet blob de destination est créé dans le niveau archive.

L’opération **Copier l’objet blob** est idéale pour les scénarios où vous avez éventuellement besoin de lire ou modifier les données archivées avant l’expiration de l’intervalle de suppression anticipée. Vous pouvez accéder aux données de l’objet blob source sans avoir à réhydrater l’objet blob archivé.

#### <a name="portal"></a>[Portail](#tab/azure-portal)

N/A

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour copier un objet blob d’un niveau en ligne vers le niveau archive avec PowerShell, appelez la commande [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) et spécifiez le niveau archive. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

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
    -StandardBlobTier Archive `
    -Context $ctx
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour copier un objet blob d’un niveau en ligne vers le niveau archive avec Azure CLI, appelez la commande [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) et spécifiez le niveau archive. N’oubliez pas de remplacer les espaces réservés entre crochets par vos propres valeurs :

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="bulk-archive"></a>Archivage en bloc

Lorsque vous déplacez un grand nombre d’objets blob vers le niveau archive, utilisez une opération de traitement par lots pour bénéficier de performances optimales. Une opération de traitement par lots envoie plusieurs appels d’API au service avec une seule requête. Les sous-opérations prises en charge par l’opération [Traitement par lot des objets blob](/rest/api/storageservices/blob-batch) incluent [Supprimer l’objet blob](/rest/api/storageservices/delete-blob) et [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier).

Pour archiver des objets blob avec une opération de traitement par lots, utilisez l’une des bibliothèques clientes Stockage Azure. L’exemple de code suivant montre comment effectuer une opération de traitement par lots de base avec la bibliothèque cliente .NET :

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/AccessTiers.cs" id="Snippet_BulkArchiveContainerContents":::

Pour voir un exemple d’application détaillé qui montre comment modifier des niveaux à l’aide d’une opération de traitement par lots, consultez [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/).

## <a name="see-also"></a>Voir aussi

- [Niveaux d’accès chaud, froid et archive pour les données d’objet blob](access-tiers-overview.md)
- [Réhydratation d’objets blob à partir du niveau archive](archive-rehydrate-overview.md)
- [Réalimenter un objet blob archivé dans un niveau en ligne](archive-rehydrate-to-online-tier.md)
